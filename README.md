
# YubiHSM2 CNG Key Storage Provider Usage

This respository exists to provide supplemental documentation with regard to using the Yubico YubiHSM2 device with the Yubico provided Cryptography API: Next Generation (CNG) Key Storage Provider (KSP).

YubiHSM2: https://www.yubico.com/products/yubihsm/

The CNG KSP for YubiHSM2 is included as part of the YubiHSM2 SDK found here: https://developers.yubico.com/YubiHSM2/Releases/

### Download and extract the Win64 version of the SDK, where the MSI installer is located

The MSI is digitally signed, be sure to verify the signature before deploying the package to target servers.

![MSI Authenticode signature validation](../assets/signature-validation.png)

### The CNG provider must be installed on all computers which are to be clients to the YubiHSM2 device
It does not need to be installed on the server device that the YubiHSM2 is physically installed into where the connector is running, unless the server is also going to be a client.  Install as you would any MSI using the command line or GUI.

```
msiexec.exe /i "yubihsm-cngprovider-windows-amd64.msi" /passive  ACCEPT=yes
```
![MSI GUI installation](../assets/msi-gui.png)


### Once the CNG provider is installed, it must be configured to connect to a YubiHSM connector
If you are running the client and server on the same machine, using the default port, and using the default authentication key (authkey) and the default password (**which of course should _NEVER_ be done in a production environment**), you would configure the the CNG provider using the below three registry values using PowerShell, regedit.exe, or any other means.

``` PowerShell
Set-ItemProperty -path HKLM:\SOFTWARE\Yubico\YubiHSM -name AuthKeysetID -Type DWord -Value 1
Set-ItemProperty -path HKLM:\SOFTWARE\Yubico\YubiHSM -name AuthKeysetPassword -Type String -Value password
Set-ItemProperty -path HKLM:\SOFTWARE\Yubico\YubiHSM -name ConnectorURL -Type String -Value http://127.0.0.1:12345
```
![YubiHSM CNG provider configuration via regedit.exe](../assets/regedit.png)

### The YubiHSM2 device has the capability of holding multiple authkey objects

See (https://developers.yubico.com/YubiHSM2/Concepts/Object.html).  These authkeys can be thought of as synonymous with user accounts in a Windows environment.  Each authkey can have a different password, just like each user on a Windows system can have a different password. 

### The YubiHSM2 device also has the concept of an object belonging to one or more "domains"

See (https://developers.yubico.com/YubiHSM2/Concepts/Domain.html). This has nothing at all to do with traditional Windows domains, but rather can be thought of as synonymous with security groups used in a Windows environment for authorization. Each authkey (or any other object) can belong to one or more domains. This provides logical seperation of keys when multiple applications are utilizing the same YubiHSM2 device.

For example, a single YubiHSM2 device might be physically installed into a server in a datacenter.  This server can be running any operating system supported by the YubiHSM connector service. Two or more authkeys might have been created (https://developers.yubico.com/YubiHSM2/Commands/Put_Authkey.html) with different domains specified. Two or more other Windows servers, perhaps virtual machines running on a hypervisor in the same datacenter, may have the Yubico CNG provider installed and registry values configured to connect to the server using the same ConnectorURL value, but different AuthKeysetID and AuthKeysetPassword values.  In this configuration, asymmetric keys (or any other objects) created in a session authenticated to the YubiHSM connector with an authkey associated with one domain cannot be manipulated in any way by a session authenticated to the same YubiHSM connector with an authkey not associated to same domain. This would be helpful in installations of something like [Active Directory Certificate Services](AD%20CS/README.md) where multiple Certificate Authorities are installed and connected to the same YubiHSM2 device. Conversely, if two or more servers are configured with the same authkey and password, or different authkeys that were created associated to the same domain, asymmetric keys (or any other objects) can be used by sessions on different servers simultaniously. This can be verify helpful in situations where a single key needs to be securely utilized by multiple load balanced servers in a farm or array, such as an [Active Directory Federation Services](AD%20FS/README.md) deployment or an [Online Certificate Status Protocol](OCSP/README.md) array deployment.
