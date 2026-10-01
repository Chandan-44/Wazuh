Here are the step-by-step instructions for **Automating Windows Firewall Blocks via Wazuh Active Response for RDP Attackers**:

1. **Configure Custom RDP Brute-Force Detection Rule:** Path: /var/ossec/etc/rules/local_rules.xml on Wazuh Manager.
1. Log in to your **Wazuh Manager** server via SSH or terminal.
2. Open the local custom rules file:

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml

```

3. Add a rule block to detect multiple failed RDP authentication attempts (Windows Event ID 4625 / SID 60122) within a specific timeframe:

```xml
<group name="rdp,windows,">
  <rule id="100002" level="10">
    <if_matched_sid>60122</if_matched_sid>
    <same_source_ip />
    <frequency>5</frequency>
    <timeframe>120</timeframe>
    <description>Multiple RDP Failed Logins Detected - Possible Brute Force Attack</description>
    <mitre>
      <id>T1110</id>
    </mitre>
  </rule>
</group>

```


2. **Configure Active Response in Wazuh Manager:** Path: /var/ossec/etc/ossec.conf on Wazuh Manager.
1. Open the main configuration file on the Wazuh Manager:

```bash
sudo nano /var/ossec/etc/ossec.conf

```

2. Add the `<active-response>` block inside the `<ossec_config>` section, binding the built-in `netsh` command to your RDP brute-force rule:

```xml
<active-response>
  <command>netsh</command>
  <location>local</location>
  <rules_id>100002</rules_id>
  <timeout>600</timeout>
</active-response>

```

* **`command`**: Uses the built-in `netsh` command script on Windows agents to add an inbound block rule in Windows Defender Firewall.
* **`location`**: Set to `local` to execute on the Windows endpoint that suffered the attack.
* **`timeout`**: `600` automatically unblocks the IP after 10 minutes (stateful response).


3. **Restart Wazuh Manager:** Loads the new detection rule and active-response binding.
Restart the Wazuh Manager service to apply the configuration:

```bash
sudo systemctl restart wazuh-manager

```


4. **Verify Windows Agent Execution Permissions:** Ensures agent can execute netsh commands.
1. Ensure the **Wazuh Agent** service on the Windows target runs under the `LocalSystem` account (default setting).
2. Verify that `netsh.exe` is present in `C:\Program Files (x86)\ossec-agent\active-response\bin\`.


5. **Simulate RDP Brute-Force Attack:** Triggers detection threshold from an external host.
From an attacker machine (e.g., Kali Linux), launch an RDP brute-force test against the Windows host using Hydra:

```bash
hydra -L usernames.txt -P passwords.txt rdp://<TARGET_WINDOWS_IP>

```


6. **Verify Block in Windows Firewall & Wazuh Dashboard:** Confirms automated firewall rule insertion and remediation.
1. **Check Windows Target:** Open PowerShell as Administrator on the target machine and check the active response log:

```powershell
Get-Content "C:\Program Files (x86)\ossec-agent\active-responses.log" -Tail 20

```

You should see `netsh` adding an inbound firewall rule blocking the attacker's IP.
2. **Check Wazuh Dashboard:** Go to **Security Events** → **Threat Hunting** and look for Rule `100002` and Active Response execution alerts.


For a full lab demonstration showing this process in action, see [Automating Windows Firewall Blocks via Wazuh Active Response for RDP Attackers](http://www.youtube.com/watch?v=vP_FGEcnSCg).
