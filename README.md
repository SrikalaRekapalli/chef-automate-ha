# Chef-Automate-HA

## Introduction

This document explains how to deploy Chef-Automate HA template using Azure Key-Vault through Azure CLI tool.

Azure Key Vault helps safeguard cryptographic keys and secrets used by cloud applications and services. By using Key Vault, you can encrypt keys and secrets (such as authentication keys, storage account keys, data encryption keys, .PFX files, and passwords).

Chef Automate-HA environment uses key vault to store the secrets like dbpassword, replication password, cluster token, cluster name, chefdeliveryuser, chefsecrets, chefdeliverypassword.

## Chef-Automate-HA Architecture Diagram

![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/chef-automate-ha-architecture.png)

## Deploying Chef-Automate-HA using Azure keyvault through Azure Cloud Shell

First we need to execute the below command before we launch the chef-automate-HA ARM template.

**Note:**  The User who is executing this command should have **Owner role**.

1. Go to Azure portal and open Azure Cloud Shell and execute the below command.

    **az ad sp create-for-rbac**

2.	After executing the above command, you will get **appId, dispalyName, name, password, tenantId**.

![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/1.png)

3.	Note down the **appId** and **password**, you will need to update these two in chef-automate-ha.parameters.json file.

4.	Also you need to update objectId in chef-automate-ha.parameters.json file, to get your objectId execute the below command.

    **az ad sp show --id <appId>**

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/2.png)

5.	Note down the Object ID and update the required input parameters in chef-automate-ha.parameters.json to deploy the chef-automate-ha.json template.

6.	Copy the chef-automate-ha.parameters.json file to Cloud Shell.

    **Link:** [chef-automate-ha.parameters.json](https://raw.githubusercontent.com/sysgain/chef-automate-HA/chef-ha-staging/chef-automate-ha.parameters.json?token=AT3pTZMOnKgsXY707BtfECCqeFKNX9tWks5ZL_BYwA%3D%3D)

    **vim chef-automate-ha.parameters.json**

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/3.png)

7.	You need to update **appId, password, objectId, firstname, lastname, emaild and organization name** etc in chef-automate-ha.parameters.json file.

8.	Create a resource group that serves as the container for the ARM template deployment resources.

    **az group create -n <RGNAME> -l <LOCATION>**

    **EX: az group create –n chef-automate-ha –l westus**

9.	Run the following command to deploy the ARM template.

    **az group deployment create --template-uri <https://raw.url/filename.json> --parameters @./<automatecluster.parameters.local.json> -g <RG Name> -n <deploymentName>**

    **Ex: az group deployment create --template-uri https://raw.githubusercontent.com/sysgain/chef-automate-HA/master/chef-automate-ha.json?token=AT3pTdWuHJENZfsbbl6LKnCqeWTSdQnxks5ZHWsvwA%3D%3D --parameters @./chef-automate-ha.parameters.json -g chef-automate-ha -n deploy**

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/4.png)

10.	The deployment will take a few minutes to complete. When it finishes, you see a message that includes the result as shown below:

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/5.png)
 
11.	After successful deployment you can see the adminusername, chef-server-URL, chef-server-fqdn, keyvaultName, chef-server-weblogin-username, chef-server-weblogin-password, chef-automate-URL and chef-automate-fqdn for chef-server, chef-backend and chef-automate in output section.

12.	Upload your sshprivatekey to cloudshell for logging into chef virtualmachines.

13.	To setup chef-automate, login to chef Automate server by using chef-automate-fqdn, adminusername mentioned in output section.

    **ssh –i <privatekeyfilename> <username>@chef-automate-fqdn**

    **EX: ssh –i privatekeyfilename chefuser@ chefautopnqll46s.westus.cloudapp.azure.com**

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/6.png)

14.	Now copy or upload your delivery.license file to chef-automate server through winscp or scp.

15.	In chef-automate virtual machine you will find one file named **command to execute** in /etc/delivery location.

16.	Open that file and execute that command to setup chef-automate.

    *There,*

    **•	AUTOMATE_LICENSE** is the full path and file name of your Chef Automate license file.

    **For example:** /root/automate.license

    **The --server-url** is the URL of your Chef server, which contains the fully-qualified domain name of the Chef server and the name of the organization you created when you created the delivery user.

    **•	AUTOMATE_SERVER_FQDN** is the external fully-qualified domain name of the Chef Automate server. This is just the name of the system, not a URL.

    **For example:** host.organization.co.

    **•	ENTERPRISE_NAME** is the name of your enterprise.

    **For example:** oraganization_inc.

    **EX: automate-ctl setup --license /home/chefuser/delivery.license --key /etc/delivery/chefautomatedeliveryuser.pem --server-url https://cheffepnqll46s.westus.cloudapp.azure.com/organizations/chef-automate-org --fqdn chefautopnqll46s.westus.cloudapp.azure.com --enterprise organization_inc**

17.	Click **yes** for chef automate server configuration 

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/7.png)

18.	Click **no** to add a runner.

19.	After the command getting successfully executed you can see the admin username, password and chef-automate web login url.

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/8.png)

20.	Now login to the chef-automate web page using that URL.

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/9.png)

21.	After signing in you can able to see chef automate web page.

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/10.png)

22.	Now you can login to the chef server web UI by using the chef-server web url, chef-server-webLogin-username mentioned in output section.

23.	Chef-server-webLogin-password is stored in the keyvault. You can be retrieve the password by running below command in Azure Cloud Shell.

    **az keyvault secret show --name chefuserpassword --vault-name < keyvaultname >**

Where keyvault name is displayed in the output section.

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/11.png)

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/12.png)

24.	To check the status of chef-server frontend & backend, login to the chef-server using chef-server-fqdn, adminusername mentioned in output section.

    **ssh –i <privatekeyfilename.pem> <username>@chef-server-fqdn**

    **Ex: ssh –i privatekeyfilename chefuser@ cheffepnqll46s.westus.cloudapp.azure.com**

25.	To run as an administrator use sudo –I, and then run the following command.

    **chef-server ctl status.**

26.	You should get the status of chef-server as follows.

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/13.png)

27.	Now login to the chef-backend leader server to check the cluster status.

28.	To login to the server, you can use the backend leader private ip in backend leader virtual machine (i.e., be0) properties in your resource group

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/14.png)

29.	Use the adminusername and privateIp to login to the server.

    **EX : ssh privatekeyfilename chefuser@10.0.1.4**

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/15.png)

30.	To run as an administrator use sudo –I, and then run the following command.

    **Chef-backend ctl status**

31.	You should get the chef-backend status as shown below:

    ![alt text](https://raw.githubusercontent.com/SrikalaRekapalli/chef-automate-ha/master/images/16.png)



