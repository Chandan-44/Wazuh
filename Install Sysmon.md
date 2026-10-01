Here are the step-by-step instructions from **"How to Install Sysmon for Advanced Windows Security Logging"** :

1. **Download Sysmon from Microsoft Sysinternals:** Prerequisite setup.
1. Visit the official **Microsoft Sysinternals** page for Sysmon.
2. Download the `Sysmon.zip` package.
3. Extract the contents of the ZIP file to an easily accessible directory (e.g., `C:\Sysmon`).


2. **Obtain a Security-Focused XML Configuration File:** Filters noise and captures high-fidelity threat telemetry.
A raw Sysmon installation logs massive amounts of data. To make logging useful for SIEM tools (like Splunk or Sentinel), use a modular configuration:

1. Download a reliable community XML configuration file, such as **Olaf Hartong's `sysmon-modular**` or **SwiftOnSecurity's `sysmon-config**`.
2. Place the downloaded configuration file (e.g., `sysmonconfig.xml`) inside your `C:\Sysmon` directory alongside `Sysmon64.exe`.


3. **Open PowerShell as Administrator:** Elevated privileges are required to install system drivers.
1. Press `Windows Key + X` or search for **PowerShell** in the Start menu.
2. Right-click **Windows PowerShell** and select **Run as Administrator**.
3. Navigate to your extracted Sysmon folder:

```powershell
cd C:\Sysmon

```


4. **Install Sysmon Service and Driver:** Applies the configuration and accepts the EULA.
Run the following command in your elevated PowerShell window:

```powershell
.\Sysmon64.exe -i .\sysmonconfig.xml -accepteula

```

*If using 32-bit Windows, substitute `Sysmon64.exe` with `Sysmon.exe`.*


5. **Verify Installation & Event Logging:** Ensure the service and driver are operating correctly.
1. Press `Win + R`, type `eventvwr.msc`, and press **Enter** to open **Event Viewer**.
2. In the left menu, navigate to:
`Applications and Services Logs` → `Microsoft` → `Windows` → `Sysmon` → `Operational`
3. Confirm that events (such as **Event ID 1: Process Creation** or **Event ID 3: Network Connection**) are actively appearing.


6. **Update or Manage Sysmon Configuration:** Future tuning and updates.
* **Update configuration without reinstalling:**

```powershell
.\Sysmon64.exe -c .\sysmonconfig.xml

```

* **Uninstall Sysmon:**

```powershell
.\Sysmon64.exe -u

```
