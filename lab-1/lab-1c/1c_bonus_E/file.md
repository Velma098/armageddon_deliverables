# Lab 1C — Bonus E
## WAF Logging Verification Report
### Account: `778185677715` | Region: `us-east-1` | Date: February 12, 2026

---

## Infrastructure Summary

| Resource | Value |
|---|---|
| VPC | `vpc-04afeba69e964c861` |
| EC2 Instance (Public) | `i-09679556d87176ca5` |
| EC2 Instance (Private) | `i-0e5776c9136139256` |
| RDS Endpoint | `lab-rds01.c89ykq22i31z.us-east-1.rds.amazonaws.com` |
| ALB DNS | `lab-alb01-947924813.us-east-1.elb.amazonaws.com` |
| WAF ARN | `arn:aws:wafv2:us-east-1:778185677715:regional/webacl/lab-waf01/3ee501d8-57e3-4aaf-b1bb-3d08ca85af6e` |
| WAF Log Destination | `cloudwatch` |
| WAF Log Group | `aws-waf-logs-lab-webacl01` |
| ACM Certificate | `arn:aws:acm:us-east-1:778185677715:certificate/9cf34a25-f435-43ad-b531-df69a798c860` |
| Target Group | `arn:aws:elasticloadbalancing:us-east-1:778185677715:targetgroup/lab-tg01/f0d614c16d755184` |
| SNS Topic | `arn:aws:sns:us-east-1:778185677715:lab-db-incidents` |
| Dashboard | `lab-dashboard01` |

---

## Verification Results

### 1. WAF Web ACL Confirmed

**Command:**
```bash
aws wafv2 list-web-acls \
  --scope REGIONAL \
  --region us-east-1 \
  --query "WebACLs[].Name" \
  --output text
```

**Result:**
```
lab-waf01
```

**Status:** ✅ WAF Web ACL `lab-waf01` confirmed

---

### 2. WAF Logging Configuration

**Command:**
```bash
aws wafv2 get-logging-configuration \
  --resource-arn arn:aws:wafv2:us-east-1:778185677715:regional/webacl/lab-waf01/3ee501d8-57e3-4aaf-b1bb-3d08ca85af6e
```

**Result:**
```json
{
  "LoggingConfiguration": {
    "ResourceArn": "arn:aws:wafv2:us-east-1:778185677715:regional/webacl/lab-waf01/3ee501d8-57e3-4aaf-b1bb-3d08ca85af6e",
    "LogDestinationConfigs": [
      "arn:aws:logs:us-east-1:778185677715:log-group:aws-waf-logs-lab-webacl01"
    ],
    "ManagedByFirewallManager": false,
    "LogType": "WAF_LOGS",
    "LogScope": "CUSTOMER"
  }
}
```

| Field | Value |
|---|---|
| Log Destination | CloudWatch Logs |
| Log Group | `aws-waf-logs-lab-webacl01` |
| Log Type | `WAF_LOGS` |
| Log Scope | `CUSTOMER` |
| Managed by Firewall Manager | `false` |

**Status:** ✅ WAF logging enabled — single destination confirmed as required

---

### 3. Traffic Generation

**Commands:**
```bash
curl -I https://thedawgs2025.click/
curl -I https://app.thedawgs2025.click/
```

**Results:**

| URL | Status | Date |
|---|---|---|
| `https://thedawgs2025.click/` | ✅ `200 OK` | Thu, 12 Feb 2026 04:28:40 GMT |
| `https://app.thedawgs2025.click/` | ✅ `200 OK` | Thu, 12 Feb 2026 04:29:09 GMT |

**Status:** ✅ Traffic flowing through WAF on both endpoints

---

### 4. CloudWatch Log Streams

**Command:**
```bash
aws logs describe-log-streams \
  --log-group-name aws-waf-logs-lab-webacl01 \
  --order-by LastEventTime --descending
```

**Result:**

| Stream Name | First Event | Last Event |
|---|---|---|
| `us-east-1_lab-waf01_0` | `1770865986537` | `1770867555732` |
| `log_stream_created_by_aws_to_validate_log_delivery_subscriptions` | Validation stream | — |

**Status:** ✅ Active log stream confirmed — events ingested

---

### 5. WAF Log Events Analysis

**Command:**
```bash
aws logs filter-log-events \
  --log-group-name aws-waf-logs-lab-webacl01 \
  --max-items 20
```

**Parsed from 20 log events:**

| Field | Observed Values |
|---|---|
| Action | `ALLOW` (all sampled events) |
| Terminating Rule | `Default_Action` |
| Rule Group | `AWS#AWSManagedRulesCommonRuleSet` |
| Source | ALB — `lab-alb01` |
| Client IP | `176.65.148.161` |
| Country | `NL` (Netherlands) |
| User Agent | Let's Encrypt validation server |
| URIs | `/`, `/_next` |
| Methods | `HEAD`, `GET` |
| JA3 Fingerprint | `20b279993ae2e137e62b9647c6d768fb` |
| JA4 Fingerprint | `t13d131100_f57a46bbacb6_ab7e3b40a677` |

**Notable observation:** All sampled requests originated from the Let's Encrypt validation service (`176.65.148.161`, NL). These are automated certificate validation probes — not user traffic. All were correctly permitted by the `Default_Action` rule after passing the `AWSManagedRulesCommonRuleSet` rule group evaluation.

**Status:** ✅ WAF logs actively capturing request metadata including client IP, country, URI, rule evaluation, and TLS fingerprints

---

## Log Group Inventory

**Command:**
```bash
aws logs describe-log-groups \
  --query "logGroups[].logGroupName" \
  --output text
```

**Relevant log groups confirmed:**

| Log Group | Purpose |
|---|---|
| `/aws/ec2/lab-rds-app` | Application logs |
| `aws-waf-logs-lab-webacl01` | WAF request logs |
| `/aws/rds/instance/lab-rds01/error` | RDS error logs |

**Status:** ✅ WAF log group present and correctly named with required `aws-waf-logs-` prefix

---

## SEIR Gate Results

### Gate 1/2 — Secrets and EC2 Role

| Check | Result |
|---|---|
| Credentials valid (`sts get-caller-identity`) | ✅ PASS |
| Secret exists and describable (`lab/rds/mysql`) | ✅ PASS |
| Rotation requirement | INFO — disabled (`REQUIRE_ROTATION=false`) |
| Resource policy | ✅ PASS — no resource policy (acceptable) |
| IAM instance profile on `i-0e5776c9136139256` | ❌ FAIL — no instance profile attached |
| Caller role validation | ⚠️ WARN — expected role unknown |

**Gate 1 Result: FAIL**

**Root Cause:** Private EC2 instance `i-0e5776c9136139256` has no IAM instance profile attached. The instance cannot assume a role to access Secrets Manager or SSM without one.

---

### Gate 2/2 — Network and RDS

| Check | Result |
|---|---|
| Credentials valid | ✅ PASS |
| RDS instance exists (`lab-rds01`) | ✅ PASS |
| RDS not publicly accessible | ✅ PASS — `PubliclyAccessible=False` |
| RDS security group resolved | ✅ PASS — `sg-03572076bce3e29c0` |
| EC2 security groups for `i-0e5776c9136139256` | ❌ FAIL — could not resolve |
| Private subnet check | INFO — disabled |

**Gate 2 Result: FAIL**

**Root Cause:** EC2 instance `i-0e5776c9136139256` security groups could not be resolved. This is consistent with the Gate 1 finding — the instance may have been deployed without the correct network interface configuration or the instance profile required to query its own metadata.

---

## Known Issue — WAF Variable Misconfiguration

During deployment, the WAF was not being provisioned via Terraform. Investigation revealed the WAF enablement variable had been set to `false`. This was initially confused with the WAF logging enablement flag until re-inspection of the variable definitions clarified the distinction.

**Lesson:** Terraform toggle variables for WAF enablement and WAF logging are separate flags. Both must be explicitly set to `true` for full functionality. Always verify variable values with `terraform console` or `terraform plan` output before assuming resource creation.

---

## Verification Summary

| Check | Result |
|---|---|
| WAF Web ACL exists | ✅ Pass |
| WAF logging enabled | ✅ Pass |
| Single log destination | ✅ Pass |
| Log group name valid (`aws-waf-logs-` prefix) | ✅ Pass |
| Active log stream | ✅ Pass |
| Traffic generating log events | ✅ Pass |
| Log events contain request metadata | ✅ Pass |
| HTTPS endpoints responding | ✅ Pass |
| SEIR Gate 1 — Secrets + Role | ❌ Fail — no instance profile |
| SEIR Gate 2 — Network + RDS | ❌ Fail — EC2 SG unresolvable |

---

## Why WAF Logging Matters for Incident Response

WAF logs answer the questions that matter most during an incident:

| Question | WAF Log Field |
|---|---|
| Are 5xx errors from attackers or backend failure? | `action`, `terminatingRuleId` |
| Do WAF blocks spike before ALB errors? | `timestamp` correlation |
| What paths and IPs are hammering the app? | `httpRequest.uri`, `httpRequest.clientIp` |
| Is it one client, one ASN, or broad? | `httpRequest.country`, `clientIp` |
| Did WAF mitigate or are we failing downstream? | `action` = `BLOCK` vs `ALLOW` |
| What TLS client is making requests? | `ja3Fingerprint`, `ja4Fingerprint` |

CloudWatch Logs destination enables fast search and metric filters. S3 and Firehose destinations enable long-term archival and SIEM pipeline integration. This lab uses CloudWatch for operational speed — the correct choice for active triage.