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




