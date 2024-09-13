## Azure Cloud Detection Lab Project

**Overview:**
SIEM (Security Information and Event Management) allows security professionals to analyze log data from various network sources such as firewalls, IDS/IPS, identity solutions, and more. This provides real-time monitoring, prioritization, and threat remediation capabilities.

**Microsoft Sentinel** is a cloud-based SIEM/SOAR solution offering security analytics, threat intelligence, and response on a unified platform.

### What is Detection?
Detection in cybersecurity involves analyzing an environment for anomalous activities that could lead to a network compromise.

### Lab Content:
In this lab, participants will:
- Configure and deploy Azure resources like Log Analytics Workspace, Virtual Machines, and Azure Sentinel.
- Implement security best practices for networks and virtual machines.
- Use data connectors to bring data into Sentinel for analysis.
- Understand Windows Security Event logs.
- Configure Windows Security Policies.
- Utilize KQL to query logs.
- Write custom analytic rules to detect Microsoft security events.
- Map adversary tactics and techniques using MITRE ATT&CK.

### (Please Read Before Getting Started):
- This lab is cloud-based and can be completed for free by signing up for a [free Azure subscription](https://azure.microsoft.com/en-us/free/) and utilizing the 30-day Microsoft Sentinel trial. 
- Remember to turn off VMs when not in use and delete non-free resources to avoid extra costs.

---

### Setup Instructions:

**Step 1: Create Free Azure Account**
- Use [this link](https://azure.microsoft.com/en-us/free/) to create your free Azure account.

**Step 2: Create a Resource Group**
- Resource groups in Azure act as containers for resources. For this lab, the resource group will include:
  - Windows 10 VM
  - Log Analytics Workspace
  - Azure Sentinel Resource.

  Search “Resource Group” in the Azure Portal, follow the prompts, and create your resource group.
  ![Resource Group](https://imgur.com/MCumdOE.png)

**Step 3: Deploy a Virtual Machine**
- Search "Virtual Machine" in the Azure Portal and follow the steps to deploy a Windows VM.
  ![Deploy VM](https://imgur.com/eGzsoWW.png)

  Ensure you remember your admin credentials. Stick to default settings for now.

**Network and Virtual Machine Security**
- When deploying a VM, it's placed on a Virtual Network (vnet) with a Network Security Group (NSG). NSGs act as firewalls, filtering traffic based on security rules.
  
- Enable Just-in-Time (JIT) access to reduce the attack surface. JIT limits access to specific IPs and time-based restrictions, reducing exposure to brute force or password spray attacks.

**To Enable JIT:**
1. Search for **Microsoft Defender for Cloud** in the Azure Portal.
2. Enable all Defender plans (free trial) and navigate to **Workload Protections**.
   ![Workload Protections](https://imgur.com/6uriXUT.png)
3. Enable **Just-in-Time VM Access**.
   ![Enable JIT](https://imgur.com/g5bSs28.png)

4. Use the default JIT settings for RDP, which restrict access to a three-hour window after approval.
   ![JIT Settings](https://imgur.com/I76dPoF.png)

5. Once JIT is enabled, go to the VM’s networking tab, and request access under **Connect**. This will update RDP access to your IP and block other requests.
   ![Request Access](https://imgur.com/IcpMLlF.png)

---

### Step 4: Create Log Analytics Workspace and Deploy Sentinel

To manage log data in Azure, configure a **Log Analytics Workspace**:

1. In the Azure Portal, search for **Microsoft Sentinel** and follow prompts to set up a Log Analytics workspace.
   ![Log Analytics](https://imgur.com/gdz42Qz.png)

2. Use the same resource group as the VM to keep everything organized.
   ![Workspace Setup](https://imgur.com/nmqiM5d.png)

---

### Part 2: Getting Data into Sentinel

After deploying Sentinel, if you check the **Incidents** tab on the left, you'll notice there are no incidents yet, as no data is being fed into Sentinel.

**Troubleshooting: Data Connectors Not Visible**

I found that the **Data Connectors** tab was initially empty, which meant the required connectors weren’t available. Here’s how I resolved it:

1. **Check Content Hub:** 
   Navigate to **Content Hub** in Azure Sentinel or click **‘Click Here’** to ensure that all necessary content is available.
   ![Click Here](https://imgur.com/KWrCTAy.png)

2. **Install Missing Content:**
   Click **‘Install/Update’** to add the necessary data connectors.
   ![Install/Update](https://imgur.com/HmmGli5.png)

   ![Installed](https://imgur.com/tJcwXYl.png)


3. **Install Windows Security Events Data Connector:**
   I had to go back and install the **Windows Security Events** connector as it wasn’t included with the earlier content.

   ![Windows Security Events](https://imgur.com/mdbhWVZ.png)

4. **Verify Installation:**
   After installation, return to the **Data Connectors** tab. The **Windows Security Events** connector should now appear.

5. **Configure Windows Security Events Data Connector:**
   - Go to **Data Connectors**, search for **‘Windows Security Events via AMA’**.
   - Select it and click **‘Open Connector Page’**.
   ![Open Connector Page](https://imgur.com/zgdv5Ol.png)

   - Click **‘Create Data Collection Rule’**.
     ![Create Data Collection Rule](https://imgur.com/tR6dPOD.png)

   - Name the rule and connect it to the resource group used for all resources up to this point.
   
    ![Configure Rule](https://imgur.com/DvMZcHl.png)

  - Select the VM created earlier

  ![Select VM](https://imgur.com/nFEpiaJ.png)

- Select **“All Security Events”** to ensure comprehensive data collection.


- The data collection rule should show a **“Validation Passed”** screen, indicating that the configuration is correct.


- Click **Create** to finalize the setup and start collecting data from the Windows 10 VM.

- Refresh the **Data Connectors** page until the status for the **Windows Security Events** connector shows as **“Connected”**. This confirms that the data collection is active and functioning correctly.

![Connected](https://imgur.com/8q51PaL.png)

### Part 3: Generating Security Events

Now that our VM is connected to Sentinel and our Log Analytics Workspace, we need to generate data from our logs. To achieve this, perform actions on the Windows 10 VM to create security alerts.

Windows logs a variety of security events, including privileged use, logon events, process activity, policy changes, and more. Generating these events will help us observe and analyze security alerts.

#### Step 1: Start the Virtual Machine

1. Navigate to the Azure Portal and locate the VM created earlier in the lab.

2. If the VM is not already running, click **“Start”** at the top of the page to power it on.
   ![Start VM](https://imgur.com/0QdWPZ6.png)

3. If prompted, enable **Just-in-Time Access** to ensure secure access.


### Step 2: Access the Virtual Machine via RDP

1. Under **Networking**, locate the public IP assigned to your VM.

![Public IP Networking](https://imgur.com/b5DIqB5.png)

2. Use **Remote Desktop Connection** or a similar RDP client on your PC to connect to the VM using the public IP address.  
   (Tip: You may need to refresh the Azure portal after starting the virtual machine to see the public IP.)

   ![RDP Connection](https://imgur.com/klAhNLF.png)

3. When prompted, enter the **username** and **password** you created when setting up the VM.

4. Once authenticated and logged into the virtual machine, search for **Event Viewer** in the Windows search bar and open the program.

   ![Event Viewer](https://imgur.com/Z4kOqyU.png)

### Step 3: Exploring Windows Security Events

1. Inside **Event Viewer**, you'll notice several types of logs that Windows collects, such as:
   - **Application Logs**
   - **Security Logs**
   - **Setup Logs**
   - **System Logs**
   - **Forwarded Events**
          
![Event Viewer Windows Logs](https://imgur.com/2SaTSt8.png)


   For this lab, we will focus specifically on **Windows Security events**.

2. Click on the **Security** log to display a list of security events.
   
   ![Security Logs](https://i.imgur.com/XVVpK4G.png)

4. You will see various security events listed here. Use the **Find** option and search for **Event ID 4624**.
   
   - **Event ID 4624** indicates a **successful logon**.  
   - Select the event to view more detailed information about the logon event, such as the account used and logon type.

   ![Event ID 4624](https://i.imgur.com/xB1Qree.png)

5. Review the event details and familiarize yourself with the information presented, as this is vital for detecting and analyzing security activity.

![Event Details](https://i.imgur.com/0Iuelyh.png)

By completing this step, you will gain insight into how Windows security events are captured and displayed in **Event Viewer**, a crucial tool in understanding the security posture of your VM.

## Part 4: Kusto Query Language (KQL)

A SIEM like Azure Sentinel centralizes data from various sources, making it easier for analysts to gather critical information quickly. In an enterprise setting, you'd want data from all endpoints and virtual machines to be accessible in one place.

Let’s return to Azure Sentinel and pull the event we identified from our Sentinel Logs.

![Sentinel](https://i.imgur.com/gQYN6Pw.png)

1. **Access Logs:**
   - On the **Sentinel Main Page**, click on **Logs**.
   ![Logs Page](https://i.imgur.com/9BEhniF.png)

2. **Execute the Query:**
   - In the **Logs** page, under **‘Type your query’**, enter the following KQL (Kusto Query Language) logic:
   
     ```kql
     SecurityEvent
     | where EventID == 4624
     | project TimeGenerated, Computer, AccountName
     ```
   Every SIEM has its own search language for extracting data from logs. In Sentinel, this language is KQL, or **Kusto Query Language**. While KQL offers a variety of syntax rules and query options, we’ll start with a basic command to extract relevant data.

3. **Understanding the Query:**
   - **SecurityEvent**: Refers to the table storing the event data. It contains all the events we viewed in the Event Viewer.
   - **Where**: Filters the events to show only those with **EventID == 4624**, which corresponds to successful logons.
   - **Project**: Specifies which data fields to display when the query is run. In this case, we're focusing on the **TimeGenerated**, **Computer**, and **AccountName** fields.

4. **Review the Results:**
   - When the query is run, you'll see a list of successful logon events on the VM, showing the **time**, **computer name**, and **account** that generated the event.
   ![Query Results](https://i.imgur.com/5V63KTr.png)

   However, notice that the **AccountName** field may be empty. Sentinel doesn’t automatically populate this data. Later in the lab, we’ll create an analytics rule to resolve this and properly display the **Account Name** field.

## Part 5: Writing an Analytic Rule and Generating a Scheduled Task

We can set up analytic rules to monitor specific events and generate alerts when those events occur. An analytic rule monitors the VM for activity matching its logic and provides details that help analysts investigate whether the alert is a **false positive** or a **true positive**.

![Microsoft Sentinel Analytics](https://i.imgur.com/DeDGf4F.png)

### Built-in SIEM Alerts

SIEM tools come with a list of built-in alerts that can be enabled. You can explore these by clicking **Create a Rule** and following the on-screen instructions. However, alerts will only trigger if the logic matches a security event on your VM.

### Scheduled Tasks and Persistence Techniques

For the final part of this lab, we'll create a custom rule to detect potentially malicious scheduled tasks on our VM. A **scheduled task** automates certain activities on a machine. For example, you can set one up to open Google Chrome at a specific time every day. Although many scheduled tasks are harmless, malicious actors can use them for persistence.

According to the MITRE ATT&CK framework, adversaries may exploit task scheduling to execute malicious code at specified intervals.

In this lab, we’ll set up a scheduled task that opens **Internet Explorer** at a certain time, simulating how this feature could be used for persistence. We’ll also create an **analytic rule** in Sentinel to monitor for this action.

### Step 1: Enable Logging for Scheduled Task Events

The **Windows Security Event ID 4698** corresponds to scheduled task creation, but these events aren't logged by default. We need to enable logging by modifying the **Local Security Policy** on your VM.

1. Open **Local Security Policy** in your Windows 10 VM.
2. Expand **Advanced Audit Policy Configuration**.

![Local Security Policy - Advanced Audit Policy Configuration](https://i.imgur.com/fwrHGeg.png)


3. Navigate to **System Audit Policies** > **Object Access**.

![System Audit Policies](https://i.imgur.com/vWgDhd9.png)

4. Enable **Audit Other Object Access Events** for both **Success** and **Failure**.

![Audit Other Object Access](https://i.imgur.com/TUDK4hr.png)


![Success and Failure Enabled](https://i.imgur.com/mg5lTfm.png)
Logging is now enabled for scheduled task events.

### Step 2: Creating a Scheduled Task

To detect a scheduled task, we need to generate activity on our VM:

1. Open **Task Scheduler** and click **Create Task**.
2. Add a task name and set **Configure for** to **Windows 10**.

![Task Scheduler](https://i.imgur.com/KJJRBxg.png)

3. Under **Triggers**, click **New** and schedule the task for a time close to the current time. Click **OK**.

![Triggers New](https://i.imgur.com/YeIyU9G.png)

4. Under **Actions**, select **Start a program**.
5. Choose **Internet Explorer** as the program to run.

![Actions Internet Explorer](https://i.imgur.com/Z7ih2bK.png)

6. Leave the **Conditions** and **Settings** tabs as they are, and click **OK**.

This will create the scheduled task. You can now search for **Event ID 4698** in the **Event Viewer** under the **Security Logs**.

![Event ID 4698](https://i.imgur.com/VXMXvpS.png)

### Step 3: Writing the Analytic Rule

Now, let’s write a KQL query to alert us when a scheduled task is created:

1. Go to the **Sentinel Home Page**, click **Analytics Rules**, and then click **Create**. Choose the **Scheduled Query** option.

![Scheduled Query Creation](https://i.imgur.com/WpuAjFh.png)

Fill out the Analytics rule details:

![Analytics Rule Details](https://i.imgur.com/R5fGShD.png)

For the MITRE ATT&CK Options:

![MITRE ATT&CK OPTIONS](https://i.imgur.com/nFYyhp2.png)


2. In the **Rule Logic** tab, use the following KQL query to pull instances of scheduled task creation:

    ```kql
    SecurityEvent
    | where EventID == 4698
    ```

   This query will show us scheduled task creation events from our logs.

3. Expand the log for the scheduled task you created and look at the **Event Data** category. You'll find useful details like the task name, process ID, and the username that created the task.

4. To display these fields, modify the query using **parse** and **project**:

    ```kql
    SecurityEvent
    | where EventID == 4698
    | parse EventData with * '<Data Name="SubjectUserName">' User '</Data>' *
    | parse EventData with * '<Data Name="TaskName">' TaskName '</Data>' *
    | parse EventData with * '<Data Name="ClientProcessId">' ProcessID '</Data>' *
    | project Computer, TimeGenerated, ProcessID, TaskName, User
    ```

   This query extracts and displays key information such as **User**, **TaskName**, and **ProcessID**.

![Rule Query](https://i.imgur.com/E9PuEnK.png)

### Step 4: Enhancing the Alert

1. In the **Alert Enhancement** section, you can add context to the alert by mapping data fields like the **User** and **TaskName**.
2. Set the query to run at intervals, adjusting the frequency and time window.
3. Click **Review and Create** to finalize the analytic rule.

![Alert Enhancement](https://i.imgur.com/TRQNMOO.png)

![Query Scheduling](https://i.imgur.com/r50W3PY.png)

![Rule Created](https://i.imgur.com/JQjdyYV.png)



### Step 5: Triggering the Alert

Once the rule is created, trigger the alert by creating another scheduled task in your VM. The alert should appear in Sentinel within 10 minutes.

Refresh the **Incidents Page** until the alert triggers. When it does, you'll see all the necessary information, such as the **host machine**, **user account**, **process ID**, and **task name**.

While this scheduled task is non-malicious, an analyst would investigate similar tasks with security tools like EDR to determine if they are a threat.

![Incident](https://imgur.com/n8wxgpC.png)

