# Azure-Database-Migration

## Table of Contents
- Project Brief <br>
- Tools Used  <br>
- Project Description  <br>
  - Set Up the Production Environment <br>
    -  Set up a Windows Virtual Machine (VM)
    -  Connect to a Windows VM
    -  Install SQL Server and SQL Server Management Studio (SSMS) on the VM
    -  Create the production database by restoring it from this backup file
  - Migrate to Azure SQL Database <br>
    - Set Up Azure SQL Database
    - Install and configure Azure Data Studio on your production Windows VM
    - Using Azure Data Studio, establish a connection to the newly created Azure SQL Database and the Local SQL Database
    - Install the SQL Server Schema Compare extension within Azure Data Studio and carry out the Schema Compare
    - Install Azure SQL Migration extension with Azure Data Studio and carry out the Database Migration
    - To confirm the success of the database migration process, carry out a comprehensive validation
  - Data Backup and Restore <br>
    - Backup the On-Premise Database
    - Upload Backup To Data Storage
    - Restore Database on Development Environment
    - Automate Backups for Development Database
  - Disaster Recovery Simulation <br>
    - Mimic Data Loss in Production Environment
    - Restore Database from Azure SQL Database Backup
  - Geo Replication and Failover <br>
    - Set Up Geo-Replication for Azure SQL Database
    - Test Failover and Failback
  - Microsoft Entra Directory Integration <br>
    - Configure Microsoft Entra ID for Azure SQL Database
    - Create DB Reader User
- Usage Instructions  <br>
- File Structure  <br>

## Description of the Project
### Set Up the Production Environment
#### Set up a Windows Virtual Machine (VM) 

Go to the Azure CLI to create a Virtual Machine (VM). In the Azure portal homepage, select the Create a resource button from the top left corner of the dashboard. Then, select Virtual Machine from the Azure services. Select the subscription you will be using, this can include a billing and management container that holds and organizes resources and their associated billing information for an azure customer. Create a new resource group called my-vm-rg and allocate the VM to a specific resource group.

Complete the mandatory fields which are the VM name, Region, Image and Size. The Region will be whichever is closest to you, the Image will be Windows 11 Pro and the Size will be whichever is appropriate to your VM. Using password-based authentication for Windows virtual machines in Azure, you can specify username and password that will be used to log in to the VM’s operating system. The password should be strong and secured. Determine the inbound port rules which are used to control incoming network traffic to a VM. Use the default Azure settings which block all incoming traffic to VMs. Enable RDP access on Windows by allowing the RDP port 3389 in the NSG inbound rules for the VM. Finalize the provisioning of the Windows VM by clicking I confirm I have an eligible Windows 10/11 license with multi-tenant hosting rights. Then, use the Review + Create button to validate the VM’s configuration and once the validation has passed, click create.

#### Connect to a Windows VM

In the Connect panel for an RDP connection, download the RDP connection files using the Download RDP file button. Open Remote Desktop Connection on Windows, drag and drop the downloaded file to open the .rdp file. Log in to the VM using the credentials using the details created during VM provisioning and this will connect to the Windows VM.

#### Install SQL Server and SQL Server Management Studio (SSMS) on the VM

Download SQL Server from the Microsoft Download Center. Run the SQL Server Installation Wizard, select the Basic option for installation type and begin a quick and simple SQL Server installation. Accept the License Terms after reading them, and specify the location of the SQL Server install. Install the selected features using the SQL Server installation process. Next, using the install SSMS button from the SQL Server installation wizard you will be redirected to the Microsoft website. Download SSMS from the Microsoft Download Center and run the SSMS installer on the Azure VM and follow the on-screen instructions to install the tool. After installation, ensure that you can connect to SQL Server using SSMS or other client tools to confirm a successful setup.

#### Create the production database by restoring it from this backup file

Download the AdventureWorks2022.bak file to your Download folder and open SSMS with an established connection to the Azure VM. Right-click on the Database node of the Server and select Restore Database. In the Restore Database dialog, select Device as the source for the backup file, click on the ‘...’  button, locate the AdventureWorks2022.bak file using the Add button and click OK to start the restore process. A confirmation dialog may appear when restoration is complete, review the restore details and click OK to proceed.

Verify that the restored database is accessible and operational in the Azure VM and you can check the data and run tests to ensure the database is working correctly. You can also now connect to this local database using Azure Data Studio.

### Migrate to Azure SQL Database

#### Set Up Azure SQL Database

Create a new SQL Database Server using the Create New button on the Azure SQL Servers page. You will need to fill the credentials for a Server Name and Region and Authentication Methods. The Authentication Method will be SQL Server Authentication which will require the creation of a username and password. Finally, click OK to create the server which will redirect you back to the SQL Database page.

Confire compute and storage for the SQL Database Server under the Compute + Storage pane. Select Basic Compute for the Storage Tier and click Apply and finally click Review + Create and once the validations have passed, click Create.
Enter SQL Databases in the search bar of the Azure Portal, select its corresponding page  and click the Create button, filling in the required information. This will be the Subscription, Resource Group and Database Name. Select the SQL Server previously created and click Review + Create.

#### Install and configure Azure Data Studio on your production Windows VM

Download and install Azure Data studio from this website: https://colab.research.google.com/corgiredirector?site=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fsql%2Fazure-data-studio%2Fdownload-azure-data-studio. Choose the appropriate download version for Windows. Once the download is complete, run the installer and follow the on-screen instruction to install Azure Data Studio and tick Create Desktop Icon. After installing Azure Data Studio, launch it.

#### Using Azure Data Studio, establish a connection to the newly created Azure SQL Database and the Local SQL Database

To connect Azure Data Studio to your loca SQL Server Database, click on Server icon in the Activity Bar, select New Connection to open the Connection Dialog, select Microsoft SQL Server as the Server Type, enter the connection details such as server name (localhost), authentication type (Windows Authentication) and login credentials, click Enable Trust Server Certificate and click Connect.

Connect Azure Data Studio to your Azure SQL Database using the Server icon in the Activity Bar, select New Connection to open the Connection Dialog and select Microsoft SQL Server as the server type. Enter the connection details such as server name, authentication type and login credentials which can be gathered from the SQL Database Overview page in Azure Data Studio. In the Overview page of the Azure SQL Database, you can find the IP of the VM under Public IP Address. Copy this IP Address and go to the SQL Servers service page. Select the SQL server where you want to host your Azure SQL Database. Go to the left-hand side panel, access the Networking page to Add a Firewall Rule. Give the rule a name and then paste the VM IP address on the Start IP and End IP fields and click Save at the bottom of the page. This will allow you to connect to the VM. Use SQL Login for the authentication type, and fill in the username and password credentials from the Azure SQL Server. Select the Azure SQL Database from the drop-down menu under the Database pane. 

#### Install the SQL Server Schema Compare extension within Azure Data Studio and carry out the Schema Compare

Click on the Extensions icon in the Activity Bar of Azure Data Studio. Search for SQL Server Schema Compare and install it, click on the Server icon in the Activity Bar, select your local SQL Server database from the list of connected servers, right-click on the database and choose Schema Compare to create a new migration project. In the Schema Compare dialog, configure the source connection to your local SQL Server Database and the target connection to your Azure SQL Database by clicking the ‘...’, choose the Source and Target databases for comparison and click OK. Click on the Compare button at the top of the Schema Compare page and select the specific schema changes you want to apply to the Azure SQL Database. Apply the selected schema changes to synchronize the schema between the local database and the Azure SQL Database by clicking on the Apply button at the top of the Schema Compare page. When prompted to update the target, press Yes, which will apply the schema compare changes.

#### Install Azure SQL Migration extension with Azure Data Studio and carry out the Database Migration

In Azure Data Studio, go to Extensions and search for Azure SQL Migration and install it. Click on the Server icon and select your desired local SQL Server database from the list of connected servers. Right-click on the server and select manage, under General, click on Azure SQL Migration, click on Migrate to Azure SQL and select the database you want to migrate. Select the Azure SQL Database as our target, press View/Select. Confirm the database you want to migrate to, configure the Azure SQL target by selecting the subscription, resource group and SQL Database Server. Provide login credentials used for the Azure SQL Database, select the desired target database. Search for Azure Database Migration Services in the Azure Portal, click on Create to create a new service, click on Select and select the respective Resource Group, Region and Name.

Download and install integration runtime by following the link provided, select the latest version to download and run the file. Use one of the two keys provided in Azure Data Studio and register the integration runtime. To finalize setting up Runtime Integration click Launch Configuration Manager, click Next and fill in your login details for Windows Authentication to the Local SQL Server. Click Edit and Select tables to select the tables you want to migrate, then click Update and click Run validation. Once the validation is successful, press Start Migration to complete the migration process.

#### To confirm the success of the database migration process, carry out a comprehensive validation

Thoroughly inspect the data, schema, and any configurations of the migrated database, ensuring that the migration has been executed successfully and adheres to principles of data integrity.

Once the migration status goes to Succeeded, the data should be checked within the Azure server connections. Right-click on any table and press Select Top 1000 to see the data stored in the Azure SQL Database.

### Data Backup and Restore

#### Backup the On-Premise Database

Open SQL SSMS and connect to the SQL Server, right-click on the database you want to back by selecting Tasks then Back Up. Choose a destination folder for the file and Select the desired backup type as Full and click OK.

#### Upload Backup To Data Storage

Navigate to the Storage page in Azure and click Create and in the Create Storage Account and fill in the details for Subscription and Resource Group, Storage Account Name and Region and click Review + Create. On the Storage Account page, click on Upload and drag and drop the Backup File from your local machine, select the container you want to store the backup file in and wait for the upload to complete. Verify that the backup file is now in Azure Blob Storage by checking its contents.

#### Restore Database on Development Environment

Open SSMS with an established connection to the Azure VM. Right-click on the Database node of the Server and select Restore Database. In the Restore Database dialog, select Device as the source for the backup file, click on the ‘...’  button, locate the file using the Add button and click OK to start the restore process. A confirmation dialog may appear when restoration is complete, review the restore details and click OK to proceed.

Verify that the restored database is accessible and operational in the Azure VM and you can check the data and run tests to ensure the database is working correctly. You can also now connect to this local database using Azure Data Studio.

#### Automate Backups for Development Database

Open SSMS and connect to the SQL Server. In the Object Explorer, right-click on the SQL Server Agent node and then click Start. Press Yes to proceed past the prompt ‘Are you sure you want to start the SQL Server Agent?’ and expand the SQL Server Agent node. Create SQL Server Credentials by right-clicking the Server Name and selecting New Query then execute the following T-SQL command to create the credentials:

      CREATE CREDENTIAL [YourCredentialName]   <br>
      WITH IDENTITY = '[Your Azure Storage Account Name]', <br>
      SECRET = 'Access Key'; <br>

Find the Access keys by navigating to Security + Networking > Access Keys, then the SQL Server Credentials will be showing under Security > Credentials. In the Object Explorer, expand the Management node, right-click on Maintenance Plans and select Maintenance Plan Wizard. Fill in the details for the name of the plan and the description. Set up a weekly back up by clicking Change on the schedule and click Next to continue. Select Back Up Database (Full) to configure the backup task, then click Next. In the Back Up Database Task, select the databases you want to back up and choose URL as the backup destination. In the Destination tab, you will first have to select the SQL Server Credentials from a drop-down list, here you should select the credentials you have provisioned to access the Azure Storage Account. Fill in the details for the Azure Storage Container including the name of the container that the backups should be uploaded to. Click Next until the final page and click Finish to create a maintenance plan.

Refresh the Maintenance Plans node to see the newly created plan, test it by right-clicking on it and clicking Execute. If it executes successfully, the schedule can be modified by right-clicking on the plan and selecting Modify. Click on Calendar icon to open the New Job Schedule to modify the schedule of the maintenance plan.

### Disaster Recovery Simulation

#### Mimic Data Loss in Production Environment

Open Azure Data Studio on the production VM and identify the previous Azure SQL Database. Open a query window by right-clicking on the production database and selecting New Query, select all the entries in the Database’s table and click Run to execute it. Scroll down in the Results and observe the number of entries in the table, write and execute SQL queries to intentionally delete or corrupt specific data:

      -- Intentional Deletion <br>
      DELETE TOP (100) <br>
      FROM dbo.[table_name]; <br>
      -- Data Corruption <br>
      UPDATE TOP (100) dbo.[table_name] <br>
      SET product_price = NULL <br>

Verify the data loss by querying the affected table to ensure that the data has been intentionally deleted or corrupted. For example, use the query that deleted the first 100 rows in the table.

#### Restore Database from Azure SQL Database Backup

In the Azure SQL Database dashboard, select the target database that needs restoration, select the Restore Option at the top bar on the page to open a Restore Database and select a Restore Point. Choose a new Database Name, click Review + Create and click Create. To verify the restored database, check the number of entries for the table which indicates that the restoration worked.

### Geo Replication and Failover

#### Set Up Geo-Replication for Azure SQL Database

Go to the Azure SQL Database dashboard, select the primary database to be replicated, click on Replicas in the left-hand menu under Data Management and click on Create Replica to begin the process and click Create New in the server pane. Fill in the details for the secondary region, select SQL authentication, fill in the SQL login credentials and click OK. Locate the new server details under the Server Field, click Review + Create and click Create to commence the replication process. To verify the replication, go to the resource page to find the replicated database.

#### Test Failover and Failback

Go to the Azure SQL Database dashboard, select the SQL server, select Failover Groups under Data Management and then select Add Group. On the Failover Group page, fill in the details for name and server, leave the rest of the details as default and click Create. Go to Azure SQL Server, under Failover Groups, initiate a planned failover by selecting Failover and click Yes to continue. Verify that the failover succeeded by evaluating the swapped servers, and select Failover to fail the servers back to their original role.

### Microsoft Entra Directory Integration

#### Configure Microsoft Entra ID for Azure SQL Database

Locate the SQL Server under the Security section, click Microsoft Entre, click Set Admin, click Users and select the User as the admin of the database. Click Select to continue and Save to save the changes to the SQL Server. Connect to the primary database by double-clicking it, set the authentication type as Azure Active Directory - Universal with MFA Support, click Add an Account… from the drop-down menu and click Connect.

#### Create DB Reader User

Create a new user with the name DB_Reader with a password using the ‘Let me create the password’ field. Open Azure Data Studio and connect to the database using the Microsoft Entra Admin details, right-click on the server and select New Query. Run the following SQL query to grant db_datareader role to the DB_Reader:
      
      CREATE USER [DB_Reader@yourdomain.com] FROM EXTERNAL PROVIDER; <br>
      ALTER ROLE db_datareader ADD MEMBER [DB_Reader@yourdomain.com]; <br>

To verify the connection, re-connect to the Azure SQL Server by double-clicking it, right-clicking on the server name and selecting Edit Connection. Here, under Account, click Add Account and fill in the details for the login, also creating a new password and click Connect. Verify the user’s read-only access by right-clicking one of the tables and selecting Select Top 1000 to receive the correct output.

## Usage Instructions
Install the required software listed in the description of the project and carry out the steps in the description. This is a comprehensive guide to Azure Database Migration.

## File Structure
README.md
