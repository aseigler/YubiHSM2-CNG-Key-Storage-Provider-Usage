### Installing Microsoft Active Directory Certificate Services with YubiHSM Key Storage Provider

Once the YubiHSM Key Storage Provider has been [installed and configured](../README.md), the AD CS Certification Authority can be installed.

When the cryptographic provider is configured to "RSA#YubiHSM Key Storage Provider", the private key for the CA will be generated and stored on the connected YubiHSM2 device.

Install the CA with the desired options, either using Server Manager, or PowerShell.  

#### If using PowerShell 

Include the "-CryptoProviderName" parameter and supply "RSA#YubiHSM Key Storage Provider" as the parameter value.  

For example:
```PowerShell
Install-AdcsCertificationAuthority -CAType EnterpriseRootCa -CryptoProviderName "RSA#YubiHSM Key Storage Provider" -KeyLength 2048 -HashAlgorithmName SHA256 -ValidityPeriod Years -ValidityPeriodUnits 5
```
#### If using Server Manager

On the Cryptography for CA page, under select a cryptographic provider, select "RSA#YubiHSM Key Storage Provider" from the drop down list:

![CA Installation GUI, Cryptography for CA page](../../assets/gui-ca-cryptographic-provider-selection.png)
