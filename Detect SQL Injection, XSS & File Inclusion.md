Here is the step-by-step process for detecting SQL Injection (SQLi), Cross-Site Scripting (XSS), and File Inclusion (LFI/RFI) attacks using Wazuh SIEM.

1. **Prepare the Web Server and Target Application:** Prerequisite setup on the monitored host.
Install a web server (such as Apache or Nginx) on the target endpoint and host a target web application (or DVWA) to generate HTTP logs.

```bash
sudo apt update
sudo apt install apache2

```


2. **Configure the Wazuh Agent for Web Access Log Ingestion:**
Configure the Wazuh Agent on the web server to monitor the web access log file where incoming HTTP GET and POST requests are recorded.

1. Open the Wazuh Agent configuration file:

```bash
sudo nano /var/ossec/etc/ossec.conf

```

2. Add a `localfile` block pointing to your web access log:

```xml
<localfile>
  <log_format>apache</log_format>
  <location>/var/log/apache2/access.log</location>
</localfile>

```

3. Restart the Wazuh Agent to apply the configuration:

```bash
sudo systemctl restart wazuh-agent

```


3. **Verify Built-In Web Attack Detection Rules:**
Wazuh features out-of-the-box security rules to detect common web application attack signatures recorded in web logs:

* **Rule ID 31103 / 31106:** SQL Injection attempt
* **Rule ID 31104:** Cross-Site Scripting (XSS) attempt
* **Rule ID 31105:** Path Traversal / Local File Inclusion attempt

If custom patterns are required, you can add tailored detection logic inside `/var/ossec/etc/rules/local_rules.xml` on the Wazuh Manager and restart the manager service.


4. **Simulate Attacks to Generate Security Events:**
Send web requests containing attack payloads to the target web application to verify that the web log records the activity.

* **SQL Injection (SQLi):**

```bash
curl -XGET "http://SERVER_IP/index.php?id=SELECT+*+FROM+users"

```

* **Cross-Site Scripting (XSS):**

```bash
curl -XGET "http://SERVER_IP/index.php?name=<script>alert(1)</script>"

```

* **Local File Inclusion (LFI):**

```bash
curl -XGET "http://SERVER_IP/index.php?file=../../../../etc/passwd"

```


5. **Analyze and Filter Alerts in the Wazuh Dashboard:**
View and analyze the detected web attack alerts in the central console.

1. Log into the Wazuh Web UI and navigate to **Threat Hunting** or **Security Events**.
2. Filter the events using the rule IDs:
* `rule.id: 31103 OR rule.id: 31106` (SQL Injection)
* `rule.id: 31104` (XSS)
* `rule.id: 31105` (File Inclusion / Traversal)


3. Examine event details such as the source IP, payload string, severity score, and MITRE ATT&CK mapping.


For a complete hands-on demonstration showing how these attacks are generated and visualized inside the console, view [Detecting SQL Injection, XSS & File Inclusion with Wazuh SIEM](https://www.youtube.com/watch?v=BsAI4b9G5GQ), which provides a full lab walkthrough of this detection workflow.
