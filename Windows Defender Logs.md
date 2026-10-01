Here are the step-by-step instructions from **"How to Collect Windows Defender Logs in Wazuh :

1. **Configure Windows Defender Log Channel:** Can be configured locally on the agent or via centralized group policies.
Choose one of the following methods to add the Windows Defender log channel to your Wazuh configuration:

* **Method 1: Local Configuration (on Windows Endpoint)**
1. Open **PowerShell** or **Notepad** as Administrator.
2. Open `C:\Program Files (x86)\ossec-agent\ossec.conf`.


* **Method 2: Centralized Configuration (via Wazuh Dashboard)**
1. Log in to your **Wazuh Dashboard**.
2. Go to **Management** → **Groups** → Select your **Windows** group.
3. Navigate to **Files** → Edit `agent.conf`.



Add the following XML block inside the `<ossec_config>` section:

```xml
<localfile>
  <location>Microsoft-Windows-Windows Defender/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>

```


2. **Restart the Wazuh Agent Service:** Applies the new event channel configuration.
If configured locally on the Windows endpoint, open **PowerShell as Administrator** and restart the service:

```powershell
Restart-Service Wazuh

```

*(If configured via Centralized Group Configuration on the Wazuh Dashboard, the agent will automatically pull the updated `agent.conf` and restart its log collector engine.)*


3. **Simulate Windows Defender Activity (Optional):** Generates security events to test detection rules.
To verify log collection and alert generation:

1. Download a standard benign test file (such as the **EICAR test file**) or run a quick scan using Windows Defender.
2. Windows Defender will log the detection event to the `Microsoft-Windows-Windows Defender/Operational` event log.


4. **Verify Event Ingestion on Wazuh Dashboard:** Built-in rules (0600-win-wdefender_rules.xml) process the logs.
1. Open your **Wazuh Dashboard**.
2. Navigate to **Threat Intelligence** → **Security Events** (or **Discover**).
3. Apply the following query filter to view ingested Defender logs:

```text
data.win.system.providerName: "Microsoft-Windows-Windows Defender"

```

4. Confirm that malware detection, threat quarantine, or scan activity events appear with full parsed metadata (`data.win.eventdata`).
