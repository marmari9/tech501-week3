### Create a dashboard:
* From VM overview choose monitoring and pin the desired metrics. choose create new dashboard and shared. 
* From dashboard hub edit metrics to view in one line.
* Azure provides a 1-minute interval checks
* Change Local time to last 30 minutes and save.
  
# Steps to Create an Action Group in Azure

## 1. Create an Action Group
- Go to **Azure Portal** → Navigate to **Monitor**.
- Select **"Alerts"** → Click on **"Manage actions"**.
- Click **"Create action group"**.
- Fill in:
  - **Subscription**: Choose your Azure subscription.
  - **Resource group**: Select tech501.
  - **Action group name**: (e.g., `CPU_Alert_Group`).
  - **Display name**: (e.g., `CPU Alert`).

## 2. Add an Email Notification
- Under **Notifications**, click **"Add notification"**.
- Choose **"Email/SMS message/Push/Voice"**.
- Enter your **email address**.
- Click **OK**.

## 3. Save and Use the Action Group
- Click **"Review + Create"**, then **"Create"**.
- Now, when creating your **CPU usage alert**, select this action group.

# Steps to Create an Alert Rule in Azure Monitor

## 1. Create an Alert Rule in Azure Monitor
- Go to **Azure Portal** → Navigate to **Monitor**.
- Select **"Alerts"** → Click on **"Create"** → **"Alert rule"**.
- **Select the Target Resource**:
  - Click **"Select scope"**.
  - Choose your **Virtual Machine (VM) or relevant resource**.
  - Click **"Apply"**.

## 2. Define the Condition
- Click **"Add condition"**.
- Select **"Percentage CPU"** under **"Signals"**.
- Set the **Threshold value** to **greater than 3%**.
- Configure **Aggregation type** (Average).
- Set **Evaluation frequency** (e.g., every 1 minute).
- Click **"Done"**.

## 3. Configure the Action Group (Email Notification)
- Under **Actions**, click **"Select action group"**.
- Click **"tech501-maram-action-group"**

## 4. Configure Alert Rule Details
- Name the alert rule (e.g., `High_CPU_Alert`).
- Set the **Severity level** (e.g., Informational, Warning, Critical).
- *(Optional)* Enable **"Automatically resolve alerts"**.
- Click **"Create alert rule"**.

## 5. Installing Apache Bench:
* on our local machine ssh to the vm and run this code to install Apache: 
  ```
    sudo apt-get install apache2-utils 
    ```

* to create a stress test and overwork the CPU with requests to trigger the alert:

  ```
    ab -n 1000 -c 100 http://172.187.147.115/
    ```
    - notice how CPU utilization has gone up on the dashboard
    - ![alt text](<Screenshot 2025-02-04 170344-1.png>)


    - Check if the **email notification** is received.

![alt text](<Screenshot 2025-02-04 173350.png>)

![alt text](<Screenshot 2025-02-04 173446.png>)