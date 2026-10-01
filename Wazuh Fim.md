Here are the step-by-step instructions for **Wazuh FIM (File Integrity Monitoring) Configuration** :

## Part 1: Configure FIM for Windows Endpoints

1. **Access Windows Group Configuration:** Navigates to group configuration in Wazuh Dashboard.
1. Log in to your **Wazuh Dashboard**.
2. Click the top-left **Menu** icon → **Management** → **Groups**.
3. Select your **Windows** group, click **Files**, and click **Edit** on `agent.conf`.


2. **Add FIM Configuration Block for Windows:** Uses a wildcard path to monitor all user accounts simultaneously.
Add the `<syscheck>` configuration block inside `<ossec_config>`:

```xml
<syscheck>
  <disabled>no</disabled>
  <scan_on_start>yes</scan_on_start>
  <frequency>60</frequency>
  <directories realtime="yes">C:\Users\*\Downloads</directories>
</syscheck>

```

* **`scan_on_start="yes"`**: Initiates a file integrity scan whenever the system reboots.
* **`frequency="60"`**: Sets the polling scan interval to 60 seconds.
* **`realtime="yes"`**: Enables real-time file change monitoring.
* **`C:\Users\*\Downloads`**: Uses the wildcard `*` to automatically monitor the `Downloads` folder for all user profiles across all Windows agents.

Click **Save**.


---

## Part 2: Configure FIM for Linux Endpoints

1. **Access Linux Group Configuration:** Targets Linux agent group settings.
1. Return to **Management** → **Groups** and select your **Linux** group.
2. Click **Files** and select **Edit** on `agent.conf`.


2. **Add FIM Configuration Block for Linux:** Monitors sensitive Linux directories in real time.
Add the Linux FIM block to `agent.conf`:

```xml
<syscheck>
  <disabled>no</disabled>
  <scan_on_start>yes</scan_on_start>
  <frequency>60</frequency>
  <directories realtime="yes">/home/*/Desktop/secret</directories>
</syscheck>

```

Click **Save**.


---

## Part 3: Apply & Verify FIM Operations

1. **Restart Wazuh Manager:** Pushes updated group configurations to connected agents.
Restart the Wazuh Manager service so it synchronizes the new `syscheck` configuration to all endpoints.


2. **Simulate File Changes:** Performs file operations on target endpoints.
* **On Windows:** Create, modify, or delete files inside the `Downloads` directory.
* **On Linux:** Create, edit, or remove files inside the monitored directory (e.g., `/home/<user>/Desktop/secret`).


3. **Verify Alerts in Wazuh Dashboard:** Monitors real-time security alerts.
1. Open the **Wazuh Dashboard** and navigate to **Threat Hunting** → **Events**.
2. Confirm that file integrity events are generated for file additions, modifications, and deletions.
3. Verify event details, including the action performed, full file path, and user context.
