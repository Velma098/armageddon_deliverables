sessions_manager_walkthrough

# instructions to use sessions manager

Step 1)
Get Instance details: 
```
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[].[InstanceId,Tags[?Key==`Name`]| [0].Value]'\
  --output table
```
----------------------------------------------
|              DescribeInstances             |
+----------------------+---------------------+
|  i-XXXXXXXXXXXXXXXXX |  <profile-name>     |
|  i-XXXXXXXXXXXXXXXXX |  <profile-name>     |
+----------------------+---------------------+

Instance state:

```
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[].[InstanceId,State.Name,Tags[?Key==`Name`]|[0].Value]' \
  --output table
```
---------------------------------------------------------
|                   DescribeInstances                   |
+----------------------+----------+---------------------+
|  i-xxxxxxxxxxxxxxxxx |  running |  ec2-name1          |
|  i-xxxxxxxxxxxxxxxxx |  running |  ec2-name-2         |
+----------------------+----------+---------------------+




Get Instance ARN:
```
aws ec2 describe-instances \
  --instance-ids i-xxxx \
  --query "Reservations[0].Instances[0].IamInstanceProfile.Arn" \
  --output text
```
arn:aws:iam::<account-no>5:instance-profile/<profile-name>

Get Instance role
```
aws iam get-instance-profile \
  --instance-profile-name lab-ec201-private \
  --query "InstanceProfile.Roles[0].RoleName" \
  --output text
```


Step 2)
Launch session

```
aws ssm start-session --target i-xxxxx
```


lab-instance-profile01

aws iam list-attached-role-policies --role-name lab-ec2-role01
sessions_manager_walkthrough

# instructions to use sessions manager

Step 1)
Get Instance details: 
```
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[].[InstanceId,Tags[?Key==`Name`]| [0].Value]'\
  --output table
```
----------------------------------------------
|              DescribeInstances             |
+----------------------+---------------------+
|  i-XXXXXXXXXXXXXXXXX |  <profile-name>     |
|  i-XXXXXXXXXXXXXXXXX |  <profile-name>     |
+----------------------+---------------------+

Instance state:

```
aws ec2 describe-instances \
  --query 'Reservations[*].Instances[].[InstanceId,State.Name,Tags[?Key==`Name`]|[0].Value]' \
  --output table
```
---------------------------------------------------------
|                   DescribeInstances                   |
+----------------------+----------+---------------------+
|  i-xxxxxxxxxxxxxxxxx |  running |  ec2-name1          |
|  i-xxxxxxxxxxxxxxxxx |  running |  ec2-name-2         |
+----------------------+----------+---------------------+




Get Instance ARN:
```
aws ec2 describe-instances \
  --instance-ids i-xxxx \
  --query "Reservations[0].Instances[0].IamInstanceProfile.Arn" \
  --output text
```
arn:aws:iam::<account-no>5:instance-profile/<profile-name>

Get Instance role
```
aws iam get-instance-profile \
  --instance-profile-name lab-ec201-private \
  --query "InstanceProfile.Roles[0].RoleName" \
  --output text
```


Step 2)
Launch session

```
aws ssm start-session --target i-xxxxx
```


lab-instance-profile01

aws iam list-attached-role-policies --role-name lab-ec2-role01
