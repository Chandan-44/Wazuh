To configure Wazuh Active Response to automatically delete malicious files detected by VirusTotal, you link Wazuh's built-in `remove-threat` active response script to the VirusTotal malicious alert rule (**Rule ID 87105**).

1. **Define the Active Response Command:** Path: /var/ossec/etc/ossec.conf on Wazuh Manager.
1. Open the main configuration file on your **Wazuh Manager** server:

```bash
sudo nano /var/ossec/etc/ossec.conf

```

2. Add the `<command>` block inside the `<ossec_config>` section to register the built-in threat removal executable:

```xml
<command>
  <name>remove-threat</name>
  <executable>remove-threat</executable>
  <timeout_allowed>no</timeout_allowed>
</command>

```


2. **Link Active Response to VirusTotal Rule ID 87105:** Triggers automatically when VirusTotal flags a file as malicious.
In the same `ossec.conf` file on the Wazuh Manager, add the `<active-response>` block directly below your `<command>` block:

```xml
<active-response>
  <command>remove-threat</command>
  <location>local</location>
  <rules_id>87105</rules_id>
</active-response>

```

* **`command`**: Refers to the `remove-threat` command defined in Step 1.
* **`location`**: Set to `local` so the response script executes directly on the agent endpoint where the malicious file was detected.
* **`rules_id`**: `87105` is the default Wazuh rule ID triggered when VirusTotal detects a malicious file signature.


3. **Verify Active Response Script Executable on Agents:** Default built-in scripts located in agent active-response directory.
Wazuh agents ship with built-in active response scripts, but ensure permissions and availability match your operating systems:

* **Windows Agents:** The script runs via `C:\Program Files (x86)\ossec-agent\active-response\bin\remove-threat.exe` (or `.cmd`). No extra setup is required.
* **Linux Agents:** Ensure `/var/ossec/active-response/bin/remove-threat` exists and is executable:

```bash
sudo chmod +x /var/ossec/active-response/bin/remove-threat

```


4. **Restart Wazuh Manager:** Loads the newly configured command and trigger.
Restart the Wazuh Manager service to apply the active response configuration:

```bash
sudo systemctl restart wazuh-manager

```


5. **Test Auto-Deletion & Audit Logs:** Confirms automated execution and file remediation.
1. **Download Test Sample:** Download an EICAR test file into a monitored directory on an endpoint.
2. **Observe Removal:** VirusTotal will analyze the file hash, trigger Rule 87105, and the `remove-threat` script will automatically delete the file from disk within seconds.
3. **Check Agent Logs:** Verify execution on the agent by checking active response log entries:
* **Linux Agent:** `/var/ossec/logs/active-responses.log`
* **Windows Agent:** `C:\Program Files (x86)\ossec-agent\active-responses.log`
