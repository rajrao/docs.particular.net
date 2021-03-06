

#### Using the NServiceBus PowerShell Cmdlet

The [NServiceBus PowerShell Module](/nservicebus/operations/management-using-powershell.md) includes a Cmdlet for importing the Platform License into the `HKEY_LOCAL_MACHINE` registry. 

For 64-bit operating systems the license is written to both the 32-bit and 64-bit registry. The license is stored is `HKEY_LOCAL_MACHINE\Software\ParticularSoftware\License` and `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\ParticularSoftware`.


#### Other Manual Options

These following instructions cover installing the license file without using NServiceBus PowerShell Module. These options give a bit more flexibility as they support storing the the license in `HKEY_CURRENT_USER`. If the licenses is stored in `HKEY_CURRENT_USER` it is only accessible to the current user.


#### Using PowerShell Command Prompt

* Open an administrative PowerShell prompt.
* Change the current working directory to where the license.xml file is.
* Run the following script

```ps
$content = Get-Content license.xml | Out-String
Set-ItemProperty -Path HKLM:\Software\ParticularSoftware -Name License -Force -Value $content
```

NOTE: For 64 bit operating systems repeat the process in both the PowerShell prompt and the PowerShell(x86) console prompt. This will ensure the license is imported into both the 32 bit and 64 bit registry keys.


#### Using Registry Editor

 * Start the [Registry Editor](https://technet.microsoft.com/en-us/library/cc755256.aspx)
 * Go to `HKEY_LOCAL_MACHINE\Software\ParticularSoftware` or `HKEY_CURRENT_USER\Software\ParticularSoftware`
 * Create a new Multi-String Value (`REG_MULTI_SZ`) named `License`
 * Paste the contents of the license file.

If `HKEY_LOCAL_MACHINE` is the chosen license location and the operating system is 64-bit then repeat the import process for the `HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\ParticularSoftware` key to support 32-bit clients.

It is safe to ignore any warnings regarding empty strings.