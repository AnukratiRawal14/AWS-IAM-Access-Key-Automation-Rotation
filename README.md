## AWS IAM Key Rotation
This set of CloudFormation templates and Python scripts will set up an auto-rotation function that will automatically rotate your AWS IAM User Access Keys every 90 days. At 100 days it will then disable the old Access Keys. And finally at 110 days it will delete the old Access Keys. It will also set up a secret inside AWS Secrets Manager to store the new Access Keys, with a resource policy that permits only the AWS IAM User access to them. There is also automation to send emails with a custome email template via SES that will alert account owners when rotation occurs. 

**Simple Email Service (SES) Setup:**
1. Move the Amazon Simple Email Service (SES) service out of sandbox mode<br/>
a.	https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html<br/>
b.	Note: There is about a 24 hour wait for approval
2. While in Amazon Simple Email Service (SES), verify the senders address or the sender domain that you will use as the email source.<br/>
a.	This is the email that will be in the ‘Sender’ section of the email sent to your end users.
3. The AWS resources needed for this tool will deploy with the main CloudFormation template.

**Steps for deployment**
Central/Management Account Deployment Steps:
1. Go to organization -> Services -> Go to cloudformation stackset -> Enable
   
![image](https://github.com/user-attachments/assets/92a81bcc-51fb-4a06-9ecf-ff22474eab47)

3. Go to StackSet, set delegated administrator to deployment account. Refer  Refer https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-orgs-delegated-admin.html
4. Deploy stack in respective account. Refer this https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-prereqs-self-managed.html

**Setup Required**
1. Create Identity in SES.
2. Required VPC, having Private subnet and NAT gateway
3. Create SMTP user and store credentials in SSM parameter store -> Password (SecureString), Username(String)
 ![image](https://github.com/user-attachments/assets/2379bbf0-ce83-4b01-9bef-fec090834a3e)
4. Crete s3 bucket, inside this create folder asa, and sub folder asa-iam-rotation and upload folders
   ![image](https://github.com/user-attachments/assets/5ac1d350-e183-4962-92a3-671a17fb837d)



**Deploy Template in Deployment Account**
1. Create CFT template in deployment account which will create lambdas: Account Inventory, AWS-IAM-Key-Rotation, ASA-Notifier, CloudwatchEvents

![image](https://github.com/user-attachments/assets/fb653efe-dc67-4e67-86ad-df9c8e44322d)
![image](https://github.com/user-attachments/assets/aa8cdcf2-5b06-41e8-be58-977daee540a1)


2. Create Service-Managed StackSet

![image](https://github.com/user-attachments/assets/436bef34-9ff3-4be6-a123-dc3f78a970e2)

**Go to Central Management Account and deploy CFT which create IAM role**

![image](https://github.com/user-attachments/assets/4a91789b-6ba8-4d19-910b-c9029110015f)

**Come back to Deployment Account, deploy VPC-Endpoints CFT**

![image](https://github.com/user-attachments/assets/acd90730-6f45-4f23-b18d-acfe1a32ea32)

**Test the Setup** <br>
Go to lambda, test manually ASA-Account-Inventory, which will invoke ASA-IAM-Access-Key-Rotation-Function once successful then will invoke ASA-Notifier




