# Script to send an email when there is RDS alert

This is a script to send notifications through e-mail when a RDS alert is generated.

**Step 1** <br />
Create a SNS subscription/topic. Make sure you change the parameter ***--notification-endpoint***.
```
region="ap-northeast-1"
RDSNotification=$(aws sns create-topic --name "RDS-Notification" --region $region --query "TopicArn" --output=text)
aws sns subscribe --topic-arn $RDSNotification  \
 --protocol "email" \
 --notification-endpoint "my_email@mail.com" \
 --region $region
 ```
 <br />
 
**Step 2** <br />
Create a Events Subscription. Make sure you change the variable ***db="rds_instance"***.
```
db="rds_instance"
echo "Creating RDS Event Subscription RDS-Notification"
aws rds create-event-subscription \
 --subscription-name "RDS-Notification-"$db \
 --sns-topic-arn $RDSNotification \
 --source-type "db-instance" \
 --source-ids $db \
 --event-categories "availability" "backup" "configuration change" "maintenance" "notification" "recovery" "deletion" "failover" "failure" "low storage" \
 --enabled  \
 --region $region
 ```
 <br />

***Step 3** <br />
You can use this option just in case you have a SNS topic created already. 
Make sure you change the variables/parameter below:  <br />
***region="us-west-1"*** <br />
***db="rds_instance"*** <br />
***sns_topic="my_sns_topic_name"*** <br />

```bash
db="rds_instance"
region="us-west-1"
RDSNotification=$(aws sns create-topic --name $sns_topic --region $region --query "TopicArn" --output=text)
echo "Creating RDS Event Subscription RDS-Notification"
aws rds create-event-subscription \
 --subscription-name "RDS-Notification-"$db \
 --sns-topic-arn $RDSNotification \
 --source-type "db-instance" \


 --source-ids $db \
 --event-categories "availability" "backup" "configuration change" "maintenance" "notification" "recovery" "deletion" "failover" "failure" "low storage" \
 --enabled  \
 --region $region
```
