# NBA Game Day Notifications / Sports Alerts System

## **Project Goal**
<p>This project is an alert system that sends real-time NBA game day score notifications to subscribed users via SMS/Email. It leverages **Amazon SNS**, **AWS Lambda and Python**, **Amazon EvenBridge** and **NBA APIs** to provide sports fans with up-to-date game information. The project demonstrates cloud computing principles and efficient notification mechanisms. 

To complete this project, I will utilize the CLI (aws cli) as much as possible. After being able to configure infrastructure and/or AWS services via AWS console, I make a huge effort to achive the same results from CLI.</p>

---

## **Features**
- Fetches live NBA game scores using an external API.
- Sends formatted score updates to subscribers via SMS/Email using Amazon SNS.
- Scheduled automation for regular updates using Amazon EventBridge.
- Designed with security in mind, following the principle of least privilege for IAM roles.

## **Prerequisites**
- Free account with subscription and API Key at [sportsdata.io](https://sportsdata.io/)
- Personal AWS account with basic understanding of AWS and Python

---

## **Technologies**
- **Cloud Provider**: AWS
- **Core Services**: SNS, Lambda, EventBridge
- **External API**: NBA Game API (SportsData.io)
- **Programming Language**: Python 3.x
- **IAM Security**:
  - Least privilege policies for Lambda, SNS, and EventBridge.

---

## **Technical Architecture**
![game-day notification architecture diagram](https://github.com/user-attachments/assets/636a402c-a12b-4806-82f1-871c40cf397d)

---




## **Setup Instructions**

### **1. Create an SNS Topic**

```aws sns create-topic --name gd_topic```

![createSNSTopic](https://github.com/user-attachments/assets/6042f857-894f-4a8f-8035-1b40128008a5)


### **2. Add Subscriptions to the SNS Topic**

```aws sns subscribe --topic-arn arn:aws:sns:us-east-1:XXXXXXXXXXXX:gd_topic --protocol email --notification-endpoint XXXXXXXXXX@gmail.com```

![Add_SubscriptionToSNSTopic](https://github.com/user-attachments/assets/1f9417e5-5c03-4ab1-925b-6adae15f85ab)


>Notification to subscribe to the SNS Topic
![subcsription_email3](https://github.com/user-attachments/assets/08ce8672-61b1-4320-9333-1623756fba15)

![sub_confirmed4](https://github.com/user-attachments/assets/530f6337-04e3-44d4-8685-5f1a45264d77)

### **3. Create the SNS Publish Policy**

```aws iam create-policy --policy-name gd_sns_policy --policy-document file://gd_sns_policy.json```
>Must be in the directory/folder of the gd_sns_policy.json. Also, be sure to update the local policy json file has the ARN


![createSNSPublishPolicy4](https://github.com/user-attachments/assets/bf2d9ddd-96f0-4f88-b554-e864fae3a0ab)


### **4. Create an IAM Role for Lambda**

<code> aws iam create-role --role-name gd_lambda_role --assume-role-policy-document file://gd_sns_policy.json</code>

<code>aws iam attach-role-policy --role-name gd_lambda_role --policy-arn arn:aws:iam::aws:policy/service-role/AWSLambdaBasicExecutionRole</code>
>attach pre-existing service-role 'AWSLambdaBasicExecutionRole' to the newly created 'gd_lambda_role'

![createIAM_LambdaRole5](https://github.com/user-attachments/assets/a0789683-0ec7-4271-8675-c1ffb88773b8)


### **5. Deploy the Lambda Function**

```aws lambda create-function --function-name gd_notifications --runtime python3.13 --zip-file fileb://gd_notifications.zip --handler gd_notifications.lambda_handler --role arn:aws:iam::XXXXXXXXXXXX:role/gd_lambda_role --environment Variables="{NBA_API_KEY=XXXXXXXX,SNS_TOPIC_ARN=XXXXXXXXXXX}"![image](https://github.com/user-attachments/assets/5de405c7-dc1c-4fc5-86e0-73f0bcb437b5)
```
>In order to successfully execute this command, be sure to zip the lambda function python file in the correct directory/folder.
>Update the ARN for --role part of the command
>Update the env variables with API KEY & ARN

![DeployLambda6](https://github.com/user-attachments/assets/063429a2-b1ee-4ba8-85b1-493637d000b3)

> Test the Lambda Function
> Lambda Function successfully executed and API request & respond was successful
![Test_Lambda function7](https://github.com/user-attachments/assets/19ad4de5-773f-4073-ba95-e4ee2efa926d)


### *6. *Set Up Automation with Eventbridge**

>create Eventbridge Scheduler rule with a cronjob to automate the updates

``````aws events put-rule --name "gd_rule" --schedule-expression "cron(0 9-23/2,0-2/2 * * ?*)"```

>This command add permissions for the event rule toevent the Lambda Function per the cronjob specifications

```aws lambda add-permission --function-name gd_notifications --statement-id gdRulePermissions  --action 'lambda:InvokeFunction'  --principal events.amazonaws.com  --source-arn arn:aws:events:us-east-1:405971315646:rule/gd_rule```

>Identifying eventbridge rule & file(Lambda Function)

```aws events put-targets --rule gd_rule-rule --targets file://gd_notifications.py```


![setup_eventbridge8](https://github.com/user-attachments/assets/8c4cbb9c-1213-4ddc-94ee-edeaa763615a)


### **Final Result**

-Eventrbridge rule/cronjob triggered and sent the desired NBA notification
![eventbridge_cronjob_triggered10](https://github.com/user-attachments/assets/8359c978-ed0a-4254-b347-a411d92352ee)

### **Project Overview**
1. Designing a notification system with AWS SNS and Lambda.
2. Securing AWS services with least privilege IAM policies.
3. Automating workflows using EventBridge.
4. Integrating external APIs into cloud-based workflows.
