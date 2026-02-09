#1c_bonus_d


aws route53 list-resource-record-sets \
--hosted-zone-id Z0717862367KSPKDBWGDE \
--query "ResourceRecordSets[?Name=='thedawgs2025.click.']"

[
    {
        "Name": "thedawgs2025.click.",
        "Type": "NS",
        "TTL": 172800,
        "ResourceRecords": [
            {
                "Value": "ns-1276.awsdns-31.org."
            },
            {
                "Value": "ns-1882.awsdns-43.co.uk."
            },
            {
                "Value": "ns-974.awsdns-57.net."
            },
            {
                "Value": "ns-185.awsdns-23.com."
            }
        ]
    },
    {
        "Name": "thedawgs2025.click.",
        "Type": "SOA",
        "TTL": 900,
        "ResourceRecords": [
            {
                "Value": "ns-1276.awsdns-31.org. awsdns-hostmaster.amazon.com. 1 7200 900 1209600 86400"
            }
        ]
    }
]


aws elbv2 describe-load-balancers \
--names lab-alb01 \
--query "LoadBalancers[0].LoadBalancerArn"


aws elbv2 describe-load-balancer-attributes \
--load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:778185677715:loadbalancer/app/lab-alb01/256b0b35246fc8a7


 aws s3 ls s3://cloud-front-lab-group//AWSLogs/778185677715/elasticloadbalancing/ --recursive | head


aws elbv2 describe-load-balancers \
--names lab-alb01 \
--query "LoadBalancers[0].LoadBalancerArn"

{
    "Attributes": [
        {
            "Key": "access_logs.s3.enabled",
            "Value": "false"
        },
        {
            "Key": "access_logs.s3.bucket",
            "Value": ""
        },
        {
            "Key": "access_logs.s3.prefix",
            "Value": ""
        },
        {
            "Key": "health_check_logs.s3.enabled",
            "Value": "false"
        },
        {
            "Key": "health_check_logs.s3.bucket",
            "Value": ""
        },
        {
            "Key": "health_check_logs.s3.prefix",
            "Value": ""
        },
        {
            "Key": "idle_timeout.timeout_seconds",
            "Value": "60"
        },
        {
            "Key": "deletion_protection.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http2.enabled",
            "Value": "true"
        },
        {
            "Key": "routing.http.drop_invalid_header_fields.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http.xff_client_port.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http.preserve_host_header.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http.xff_header_processing.mode",
            "Value": "append"
        },
        {
            "Key": "load_balancing.cross_zone.enabled",
            "Value": "true"
        },
        {
            "Key": "routing.http.desync_mitigation_mode",
            "Value": "defensive"
        },
        {
            "Key": "client_keep_alive.seconds",
            "Value": "3600"
        },
        {
            "Key": "waf.fail_open.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http.x_amzn_tls_version_and_cipher_suite.enabled",
            "Value": "false"
        },
        {
            "Key": "zonal_shift.config.enabled",
            "Value": "false"
        },
        {
            "Key": "connection_logs.s3.enabled",
            "Value": "false"
        },
        {
            "Key": "connection_logs.s3.bucket",
            "Value": ""
        },
        {
            "Key": "connection_logs.s3.prefix",
            "Value": ""
        }
    ]
}

 aws elbv2 describe-load-balancer-attributes \
--load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:778185677715:loadbalancer/app/lab-alb01/256b0b35246fc8a7

Output:

{
    "Attributes": [
        {
            "Key": "access_logs.s3.enabled",
            "Value": "false"
        },
        {
            "Key": "access_logs.s3.bucket",
            "Value": ""
        },
        {
            "Key": "access_logs.s3.prefix",
            "Value": ""
        },
        {
            "Key": "health_check_logs.s3.enabled",
            "Value": "false"
        },
        {
            "Key": "health_check_logs.s3.bucket",
            "Value": ""
        },
        {
            "Key": "health_check_logs.s3.prefix",
            "Value": ""
        },
        {
            "Key": "idle_timeout.timeout_seconds",
            "Value": "60"
        },
        {
            "Key": "deletion_protection.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http2.enabled",
            "Value": "true"
        },
        {
            "Key": "routing.http.drop_invalid_header_fields.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http.xff_client_port.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http.preserve_host_header.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http.xff_header_processing.mode",
            "Value": "append"
        },
        {
            "Key": "load_balancing.cross_zone.enabled",
            "Value": "true"
        },
        {
            "Key": "routing.http.desync_mitigation_mode",
            "Value": "defensive"
        },
        {
            "Key": "client_keep_alive.seconds",
            "Value": "3600"
        },
        {
            "Key": "waf.fail_open.enabled",
            "Value": "false"
        },
        {
            "Key": "routing.http.x_amzn_tls_version_and_cipher_suite.enabled",
            "Value": "false"
        },
        {
            "Key": "zonal_shift.config.enabled",
            "Value": "false"
        },
        {
            "Key": "connection_logs.s3.enabled",
            "Value": "false"
        },
        {
            "Key": "connection_logs.s3.bucket",
            "Value": ""
        },
        {
            "Key": "connection_logs.s3.prefix",
            "Value": ""
        }
    ]
}

