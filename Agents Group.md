Here are the step-by-step instructions for **Configuring Centralized Agent Groups in Wazuh** without timestamps:

1. **Access Group Management & Create Groups:** Organizes agents by operating system or policy requirements.
1. Log in to your **Wazuh Dashboard**.
2. Click the top-left **Menu** icon (three horizontal lines) → **Management** → **Agent Management** → **Groups**.
3. Click **Add new group** to create OS-specific groups (e.g., `Windows` and `Linux`).


2. **Assign Agents to Created Groups:** Binds endpoints to their central group policies.
1. Click on the newly created **Windows** group.
2. Click **Manage Agents**, select your Windows endpoint(s) (e.g., `desktop`), and click **Add selected items** → **Apply changes**.
3. Return to the **Groups** overview, select the **Linux** group, click **Manage Agents**, select your Linux endpoint(s), and click **Add selected items** → **Apply changes**.


3. **Configure Group Rules in Wazuh Dashboard:** Centralized policy distribution via agent.conf.
1. Select the **Windows** group, navigate to **Files**, and click **Edit** on `agent.conf`.
2. Paste your Windows-specific XML configuration (such as registry monitoring or PowerShell command logging rules) and click **Save**.
3. Select the **Linux** group, click **Files** → **Edit**, paste your Linux-specific XML configuration (such as `sudo` command monitoring or file integrity checks), and click **Save**.


4. **Verify Configuration on Windows Endpoint:** Checks local agent directory on C: drive.
1. On the target Windows endpoint, navigate to the local agent directory:

```text
C:\Program Files (x86)\ossec-agent\shared\

```

2. Open `agent.conf` and verify that the XML rules pushed from the Wazuh Manager have automatically synchronized to the machine.


5. **Verify Configuration on Linux Endpoint:** Checks local agent directory under /var/ossec.
1. Open a terminal on the Linux target endpoint and switch to `root`.
2. Inspect the shared configuration file:

```bash
cat /var/ossec/etc/shared/agent.conf

```

3. Confirm that the Linux-specific XML rules configured on the Wazuh Manager are active locally.
