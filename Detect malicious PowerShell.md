To detect malicious PowerShell activity using Wazuh, you need to enable Windows PowerShell event logging, configure the Wazuh Agent to collect those logs, and set up custom rules on the Wazuh Manager.

1. **Enable PowerShell Logging on Windows Target:** Prerequisite for capturing Event ID 4104.
1. Press `Win + R`, type `gpedit.msc`, and press **Enter** to open the Local Group Policy Editor.
2. Navigate to **Computer Configuration → Administrative Templates → Windows Components → Windows PowerShell**.
3. Double-click **Turn on PowerShell Script Block Logging**, set it to **Enabled**, and ensure **Log script block invocation start / stop events** is checked.
4. *(Optional)* Double-click **Turn on Module Logging**, set it to **Enabled**, click **Show...**, and enter `*` under Module Names to capture all executed module details.
5. Click **Apply** and **OK**.


2. **Configure Wazuh Agent to Forward PowerShell Logs:**
1. Open the Wazuh Agent configuration file (`C:\Program Files (x86)\ossec-agent\ossec.conf`) with Administrator privileges.
2. Add the `Microsoft-Windows-PowerShell/Operational` log channel inside the `<ossec_config>` block:

```xml
<localfile>
  <location>Microsoft-Windows-PowerShell/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>

```

3. Save the file and restart the Wazuh Agent service via PowerShell (`Restart-Service Wazuh`) or `services.msc`.


3. **Define Custom Detection Rules on Wazuh Manager:**
1. Access your Wazuh Manager CLI and open the custom rules file:

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml

```

2. Add a rule block to detect suspicious patterns in PowerShell Script Block events (Event ID 4104), such as `DownloadString`, `Invoke-Expression`, `EncodedCommand`, or execution policy bypasses (`-ep bypass`):

```xml
<group name="powershell,windows,">
  <rule id="100050" level="10">
    <if_sid>60000</if_sid>
    <field name="win.system.eventID">^4104$</field>
    <field name="win.eventdata.scriptBlockText" type="scregex">(?i)(DownloadString|Invoke-Expression|IEX|EncodedCommand|-ep bypass|-w hidden)</field>
    <description>Suspicious or Malicious PowerShell Script Execution Detected</description>
    <mitre>
      <id>T1059.001</id>
    </mitre>
  </rule>
</group>

```

3. Save the file and restart the Wazuh Manager to apply the updated rules:

```bash
sudo systemctl restart wazuh-manager

```


4. **Simulate PowerShell Execution and Verify Alerts:**
1. On the monitored Windows agent, run a benign test command in PowerShell to simulate a download cradle:

```powershell
powershell.exe -nop -w hidden -ep bypass -c "Write-Host 'Test DownloadString detection'"

```

2. Log into the **Wazuh Dashboard** and navigate to **Security Events** or **Threat Hunting**.
3. Filter by Rule ID `100050` or search for `win.eventdata.scriptBlockText` to verify that the executed script block was logged and flagged.


For visual walk-throughs and detailed demonstrations of this process, check out [How to Detect Malicious PowerShell Scripts in Wazuh](http://www.youtube.com/watch?v=P0W6GHkVoHY). This video demonstrates the step-by-step setup of PowerShell event channel logging and configuring custom Wazuh rules to trigger alerts when malicious commands run on an endpoint.
