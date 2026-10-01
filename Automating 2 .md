Here are the step-by-step instructions for **Automating Wazuh Active Response to Disable Unauthorized New User Accounts**:

1. **Verify or Create Account Creation Detection Rule:** Path: /var/ossec/etc/rules/local_rules.xml on Wazuh Manager.
1. Log in to your **Wazuh Manager** server via SSH or terminal.
2. Open the custom rules file:

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml

```

3. Ensure you have a rule targeting Windows Event ID **4720** ("A user account was created") or use default Wazuh Rule ID `60109`:

```xml
<group name="windows,account_management,">
  <rule id="100003" level="10">
    <if_sid>60109</if_sid>
    <description>Unauthorized Local User Account Created on Windows Host</description>
    <mitre>
      <id>T1136.001</id>
    </mitre>
  </rule>
</group>

```


2. **Verify Account Disablement & Dashboard Alerts:** Confirms account state and audit logs.

For a full step-by-step walkthrough demonstration, check out [Automating Wazuh Active Response to Disable Unauthorized New User Accounts](http://www.youtube.com/watch?v=k58KbP1DUyc).
