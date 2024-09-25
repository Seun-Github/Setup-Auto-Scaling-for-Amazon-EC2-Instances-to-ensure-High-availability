# Setup-Auto-Scaling-for-Amazon-EC2-Instances-to-ensure-High-availability
In this project, I will describe how to create a set of EC2 instances using Auto Scaling.
1.	Launch an EC2 launch template with a minimum of 2 instances and a maximum of 4 instances.
2.	Launch Auto Scaling group to span 2 Availability zone with 1 subnet in each Availability zone.
3.	Simulate a failure by terminating 1 instance and confirm the instance is replaced.
   
Amazon EC2 instances is a virtual computing capacity in the AWS cloud. Amazon is responsible for the physical hardware while you pay for use of the virtual computing capacity

Auto-scaling group is used to configure EC2 instance(s) such that a minimum number is/are maintained and in the event of traffic increase or decrease, the system automatically scales out or in as required but not exceeding a maximum/minimum number of instances specified. Auto-scaling does the work of monitoring the metrics of the instances and triggers scaling once the set thresholds are met. Hence the system can maintain service availability at an optimum cost.

**Prerequisites**

•	An AWS account

•	A key pair

## Step 1.
Log into my AWS account, search EC2 in the search bar. 
On the EC2 dashboard click on Launch Templates under INSTANCES on the left pane.

From here click Create Launch Template button.
![image](https://github.com/user-attachments/assets/45f8fb2d-063c-4335-8c27-cf887c8d2343)


I named my template and gave a Template version description. Also, I clicked the checkbox for auto-scaling guidance.  
![image](https://github.com/user-attachments/assets/5085a018-668d-4962-9ed5-0d6ae5f84fb7)



Here, We will select an AMI (Amazon Machine Image) to create a Launch template. I chose Amazon Linux 2023 AMI 64-bit ( free tier eligible)
![image](https://github.com/user-attachments/assets/71df335c-6d20-4ecc-9655-156790abcda8)


Here we will select the instance type with the adequate configuration (CPU, Memory,…) for intended purpose. I chose t2 micro (free tier eligible)
![image](https://github.com/user-attachments/assets/f0a71405-d07a-4f17-bd64-8171a40288aa)


Next, we would select/create a key pair to use in gaining CLI access to our instance. I choose an existing key I had earlier created.
![image](https://github.com/user-attachments/assets/80b9d64c-ec89-442c-b69b-8e9479684d41)


In the Network Settings Area, we have the option of creating or selecting an existing security group, choosing subnet and VPC . I will be creating a new security group. I also chose the default VPC to launch the template and opted not to include subnet in the Launch template (subnet will be defined in the Autoscaling Group)
![image](https://github.com/user-attachments/assets/411825cf-c8d5-4d94-9754-40fcbad1a60c)


Here, in the Inbound security group rules, we will grant protocols that our application is required to communicate effectively. Our web server requires HTTP for internet access and SSH for CLI terminal access. For the HTTP rule, we set the source to anyone so that it can be reached by any one on the internet. For the SSH rule, we set the source to anywhere however, for enhance security, always set it to only the IP(s) meant to access the Instance.
![image](https://github.com/user-attachments/assets/23dd1bc1-6c2a-44ac-a413-1550b94ea1d2)


For the storage section below, I left it with the default settings
![image](https://github.com/user-attachments/assets/3efb5d77-2d48-4477-a0ca-4f3a04fb57fa)


Scroll down to the bottom of the page and expand Advanced settings. Here, all setting will remain default except the User data section where I pasted a bootstrap script.
![image](https://github.com/user-attachments/assets/c79e35fa-0786-4d4a-8b36-2b8e668db604)
![image](https://github.com/user-attachments/assets/2dcabcaf-6d9b-4b17-a557-a14b622b958b)

The script above is meant to: 1.	Update our Server, 2.	Install Apache Webserver, 3.	Enable the Apache Webserver and update the index.html file.
Finally, I clicked on the Create Launch template to conclude the step

![image](https://github.com/user-attachments/assets/f9a05ca8-7f12-4572-942d-2dc608be2c6f)

## STEP2 :
In the EC2 Dashboard locate Auto Scaling at the left Pane. Click create Create Auto Scaling Group
![image](https://github.com/user-attachments/assets/95d0e637-aa50-4435-9e44-47124a976c92)


Specify a suitable name for the Auto Scaling group 
![image](https://github.com/user-attachments/assets/85dd86f7-2a35-4335-9433-8946b6a9200c)


In the Launch Template section, select the template created in step1
![image](https://github.com/user-attachments/assets/f9a3d614-6974-429a-a094-4cb26d57ba5a)


In the Network section, I selected the default VPC in the VPC option and 2 public subnets ( 1 from each availability zones)  where the Auto scaling group will deploy instances
![image](https://github.com/user-attachments/assets/a361d092-8608-4a75-a1ca-93f6363050fb)

Skip the “Configure Advanced Options step” by clicking NEXT button at the bottom of the page.

Next  “Configure group size and scaling policies”. 
Under group size I entered 2 as my desired Instance capacity, 
![image](https://github.com/user-attachments/assets/734791f6-02c4-4dc0-b253-4e1e861aadb1)

Under SCALING. I entered 2 as my minimum and 4 as my maximum desired capacity. 
This will determine the number of instances running depending on the scaling threshold exceeded
Leave every other parameter as default on this page
Then click on the Next button at the bottom of the Page
![image](https://github.com/user-attachments/assets/0a280ed4-1385-45f2-9136-25b014ab0df9)

I won’t be adding tags or notifications to this project, hence click next. 
Review the details of your Auto Scaling group to ensure correctness. 
Then click Create Auto Scaling Group
![image](https://github.com/user-attachments/assets/52fc6197-62e0-426c-89ea-2b66834b4d88)


Status below indicated that Auto scaling group is updating its capacity
![image](https://github.com/user-attachments/assets/9c11ef69-4f66-4041-9508-88b2ed2d25d0)


Navigate to your EC2 instance dashboard, click Instance in the left pane. It would show the instances being initialized.
![image](https://github.com/user-attachments/assets/e4139fa2-f964-435d-8640-f30931032c29)

## Step 3:
To check if our webpage is running successfully, we will copy and paste the IPv4 address of any of the instances on our web browser
![image](https://github.com/user-attachments/assets/53fca99f-d009-4985-8ba7-b2976040adc7)

Now to test our Auto-Scaling group we will simulate an instance failure! I did this by terminating one of our instances. 
Click the check box for an instance, at the top, click Instance state, and select Terminate instance.
![image](https://github.com/user-attachments/assets/3b266fc5-5c7e-4eae-a748-9232869fac31)
![image](https://github.com/user-attachments/assets/d2b33065-0766-40ef-abdb-e00c7ab46ad5)

Our minimum instance was set at 2, hence, within few minutes, auto scaling group will sense the failure in one instance and initiate a new instance to replace it
New Instance now operational as captured below
![image](https://github.com/user-attachments/assets/d53a0afd-e380-4358-85cb-1fca5ee3b98a)

To confirm functionality of the new Instance, I copied the IPv4 address from the instance summary and pasted on a browser. 
The result below confirms functionality of the instance with the webserver operational
![image](https://github.com/user-attachments/assets/2d1b0c99-0b20-48b7-bec1-e78dc44df9f3)

This highlights the importance and functionality of Auto scaling in a real-life scenario. Desired capacity is restored automatically within minutes.
To avoid holding down resources or been charged, I recommend deleting the Auto Scale group which will also terminate any instances associated with it. Thanks

























