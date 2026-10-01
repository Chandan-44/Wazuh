Here are the step-by-step instructions from **"How to Install Wazuh Agent on Windows & Linux"** :

## Part 1: Install Wazuh Agent on Windows

1. **Generate Agent Command from Wazuh Dashboard:** Retrieves installer script pre-configured with your Manager IP.
1. Log in to your **Wazuh Dashboard**.
2. Navigate to **Management** → **Endpoints** → **Deploy new agent**.
3. Select **Windows** as the OS, choose the architecture (64-bit), and enter your **Wazuh Manager IP address**.
4. Copy the auto-generated PowerShell command provided by the wizard.


2. **Run Installation via Elevated PowerShell:** Downloads and executes silent MSI installation.
1. Open **PowerShell as Administrator** on your Windows endpoint.
2. Paste and run the command copied from the dashboard (or execute the following template):

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.9.2-1.msi -OutFile wazuh-agent.msi; msiexec /i wazuh-agent.msi /q WAZUH_MANAGER="YOUR_WAZUH_MANAGER_IP"

```


3. **Start the Wazuh Service:** Registers endpoint with the server.
Execute the command to start the Wazuh agent service:

```powershell
NET START Wazuh

```


---

## Part 2: Install Wazuh Agent on Linux (Ubuntu / Debian / RHEL)

1. **Add GPG Key and Repository:** Ensures package authenticity from official Wazuh sources.
**For Ubuntu / Debian:**

```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
sudo apt-get update

```

**For RHEL / CentOS / AlmaLinux:**

```bash
sudo rpm --import https://packages.wazuh.com/key/GPG-KEY-WAZUH
sudo cat > /etc/yum.repos.d/wazuh.repo << 'EOF'
[wazuh]
gpgcheck=1
gpgkey=https://packages.wazuh.com/key/GPG-KEY-WAZUH
enabled=1
name=EL-Wazuh
baseurl=https://packages.wazuh.com/4.x/yum/
protect=1
EOF

```


2. **Install Wazuh Agent Package:** Passes Manager IP directly during package installation.
**For Ubuntu / Debian:**

```bash
sudo WAZUH_MANAGER="YOUR_WAZUH_MANAGER_IP" apt-get install wazuh-agent

```

**For RHEL / CentOS:**

```bash
sudo WAZUH_MANAGER="YOUR_WAZUH_MANAGER_IP" yum install wazuh-agent

```


3. **Enable and Start the Service:** Enables background daemon to persist across reboots.
Run the following systemctl commands:

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent

```


---

## Part 3: Verify Enrollment

1. Return to the **Wazuh Dashboard**.
2. Go to **Management** → **Endpoints** (or **Agents Summary**).
3. Confirm that both your Windows and Linux endpoints appear with status **Active**.
