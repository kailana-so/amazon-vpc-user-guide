# VPC that supports IPv6 addressing<a name="get-started-ipv6"></a>

The following steps describe how to create a nondefault VPC that supports IPv6 addressing\.

To complete this exercise, do the following:
+ Create a nondefault VPC with an IPv6 CIDR block and a single public subnet\. Subnets enable you to group instances based on your security and operational needs\. A public subnet is a subnet that has access to the internet through an internet gateway\.
+ Create a security group for your instance that allows traffic only through specific ports\.
+ Launch an Amazon EC2 instance into your subnet, and associate an IPv6 address with your instance during launch\. An IPv6 address is globally unique, and allows your instance to communicate with the internet\.
+ You can request an IPv6 CIDR block for the VPC\. When you select this option, you can set the network border group, which is the location from which we advertise the IPv6 CIDR block\. Setting the network border group limits the CIDR block to this group\.

For more information about IPv4 and IPv6 addressing, see [IP addressing in your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html)\.

**Topics**
+ [Step 1: Create the VPC](#get-started-ipv6-vpc)
+ [Step 2: Create a security group](#get-started-ipv6-sg)
+ [Step 3: Launch an instance](#get-started-ipv6-instance)

## Step 1: Create the VPC<a name="get-started-ipv6-vpc"></a>

Follow the steps in this section to create a VPC\.

**To create a VPC:**

1. Follow the steps in [Create a VPC, subnets, and other VPC resources](working-with-vpcs.md#create-vpc-and-other-resources) to create the VPC and additional resources\. When you create the VPC, do the following:
   + For **Number of Availability Zones \(AZs\)**, choose **1**\.
   + For **Number of public subnets**, choose **1**\.
   + For **Number of private subnets**, choose **0**\.
   + For **NAT gateways**, choose **None**\.
   + For **VPC endpoints**, choose **None**\.
   + For **DNS options**, choose **Enable DNS hostnames** and **Enable DNS resolution**\.

1. Choose **Create VPC**\.

1. The **Your VPCs** page displays your default VPC and the VPC that you just created\.

### View information about your VPC<a name="verify-vpc-components-ipv6"></a>

After you've created the VPC, you can view information about the subnet, internet gateway, and route tables\. The VPC that you created has two route tables — a main route table that all VPCs have by default, and a custom route table that was created by Amazon VPC\. The custom route table is associated with your subnet, which means that the routes in that table determine how the traffic for the subnet flows\. If you add a new subnet to your VPC, it uses the main route table by default\.

**To view information about your VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Your VPCs**\. Take note of the name and the ID of the VPC that you created \(look in the **Name** and **VPC ID** columns\)\. You use this information to identify the components that are associated with your VPC\. 

1. In the navigation pane, choose **Subnets**\. The console displays the subnet that was created when you created your VPC\. You can identify the subnet by its name in **Name** column, or you can use the VPC information that you obtained in the previous step and look in the **VPC** column\.

1. In the navigation pane, choose **Internet Gateways**\. You can find the internet gateway that's attached to your VPC by looking at the **VPC** column, which displays the ID and the name \(if applicable\) of the VPC\.

1. In the navigation pane, choose **Route Tables**\. There are two route tables associated with the VPC\. Select the custom route table \(the **Main** column displays **No**\), and then choose the **Routes** tab to display the route information in the details pane:
   + The first two rows in the table are the local routes, which enable instances within the VPC to communicate over IPv4 and IPv6\. You can't remove these routes\.
   + The next row shows the route that enables traffic destined for an IPv4 address outside the VPC \(`0.0.0.0/0`\) to flow from the subnet to the internet gateway\. 
   + The next row shows the route that enables traffic destined for an IPv6 address outside the VPC \(`::/0`\) to flow from the subnet to the internet gateway\. 

1. Select the main route table\. The main route table has a local route, but no other routes\. 

## Step 2: Create a security group<a name="get-started-ipv6-sg"></a>

A security group acts as a virtual firewall to control the traffic for its associated instances\. To use a security group, add the inbound rules to control incoming traffic to the instance, and outbound rules to control the outgoing traffic from your instance\. To associate a security group with an instance, specify the security group when you launch the instance\. 

Your VPC comes with a *default security group*\. Any instance not associated with another security group during launch is associated with the default security group\. In this exercise, you create a new security group, `WebServerSG`, and specify this security group when you launch an instance into your VPC\.

### Create your WebServerSG security group<a name="getting-started-ipv6-create-group"></a>

You can create your security group using the Amazon VPC console\.

**To create the WebServerSG security group and add rules**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. In the navigation pane, choose **Security Groups**, **Create Security Group**\.

1. For **Group name**, enter `WebServerSG` as the name of the security group and provide a description\. You can optionally use the **Name tag** field to create a tag for the security group with a key of `Name` and a value that you specify\.

1. Select the ID of your VPC from the **VPC** menu and choose **Yes, Create**\.

1. Select the `WebServerSG` security group that you just created \(you can view its name in the **Group Name** column\)\.

1. On the **Inbound Rules** tab, choose **Edit** and add rules for inbound traffic as follows:

   1. For **Type**, choose **HTTP** and enter `::/0` in the **Source** field\.

   1. Choose **Add another rule**, For **Type**, choose **HTTPS**, and then enter `::/0` in the **Source** field\.

   1. Choose **Add another rule**\. If you're launching a Linux instance, choose **SSH** for **Type**, or if you're launching a Windows instance, choose **RDP**\. Enter your network's public IPv6 address range in the **Source** field\. If you don't know this address range, you can use `::/0` for this exercise\.
**Important**  
If you use `::/0`, you enable all IPv6 addresses to access your instance using SSH or RDP\. This is acceptable for the short exercise, but it's unsafe for production environments\. In production, authorize only a specific IP address or range of addresses to access your instance\.

   1. Choose **Save**\.

## Step 3: Launch an instance<a name="get-started-ipv6-instance"></a>

When you launch an EC2 instance into a VPC, you must specify the subnet in which to launch the instance\. In this case, you'll launch an instance into the public subnet of the VPC you created\. Use the Amazon EC2 launch wizard in the Amazon EC2 console to launch your instance\. Not all of the Amazon EC2 launch wizard options are detailed here\. For more information, see [Launching an instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/launching-instance.html) in the *Amazon EC2 User Guide for Linux Instances*\.

To ensure that your instance is accessible from the internet, assign an IPv6 address from the subnet range to the instance during launch\. This ensures that your instance can communicate with the internet over IPv6\.

**To launch an EC2 instance into a VPC**

Before you launch the EC2 instance into the VPC, configure the subnet of the VPC to automatically assign IPv6 IP addresses\. For more information, see [Modify the IPv6 addressing attribute for your subnet](working-with-subnets.md#subnet-ipv6)\.

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. In the navigation bar, on the top\-right, ensure that you select the same Region in which you created your VPC and security group\. 

1. From the dashboard, choose **Launch Instance**\.

1. On the first page of the wizard, choose the AMI to use\. For this exercise, we recommend that you choose an Amazon Linux AMI or a Windows AMI\.

1. On the **Choose an Instance Type** page, you can select the hardware configuration and size of the instance to launch\. By default, the wizard selects the first available instance type based on the AMI that you selected\. You can leave the default selection and choose **Next: Configure Instance Details**\.

1. On the **Configure Instance Details** page, select the VPC that you created from the **Network** list and the subnet from the **Subnet** list\. 

1. For **Auto\-assign IPv6 IP**, choose **Enable**\.

1. Leave the rest of the default settings, and go through the next pages of the wizard until you get to the **Add Tags** page\.

1. On the **Add Tags** page, you can tag your instance with a `Name` tag; for example `Name=MyWebServer`\. This helps you to identify your instance in the Amazon EC2 console after you've launched it\. Choose **Next: Configure Security Group** when you are done\. 

1. On the **Configure Security Group** page, the wizard automatically defines the launch\-wizard\-*x* security group to allow you to connect to your instance\. Instead, choose the **Select an existing security group** option, select the **WebServerSG** group that you created previously, and then choose **Review and Launch**\.

1. On the **Review Instance Launch** page, check the details of your instance and choose **Launch**\.

1. In the **Select an existing key pair or create a new key pair** dialog box, you can choose an existing key pair, or create a new one\. If you create a new key pair, ensure that you download the file and store it in a secure location\. You need the contents of the private key to connect to your instance after it's launched\. 

   To launch your instance, select the acknowledgment check box and choose **Launch Instances**\.

1. On the confirmation page, choose **View Instances** to view your instance on the **Instances** page\. Select your instance, and view its details in the **Description** tab\. The **Private IPs** field displays the private IPv4 address that's assigned to your instance from the range of IPv4 addresses in your subnet\. The **IPv6 IPs** field displays the IPv6 address that's assigned to your instance from the range of IPv6 addresses in your subnet\. 

You can connect to your instance through its IPv6 address using SSH or Remote Desktop from your home network\. Your local computer must have an IPv6 address and must be configured to use IPv6\. For more information about how to connect to a Linux instance, see [Connecting to your Linux instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstances.html) in the *Amazon EC2 User Guide for Linux Instances*\. For more information about how to connect to a Windows instance, see [Connect to your Windows instance using RDP](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html) in the *Amazon EC2 User Guide for Windows Instances*\.

**Note**  
If you also want your instance to be accessible via an IPv4 address over the internet, SSH, or RDP, you must associate an Elastic IP address \(a static public IPv4 address\) to your instance, and you must adjust your security group rules to allow access over IPv4\. For more information, see [Get started with Amazon VPC](vpc-getting-started.md)\.