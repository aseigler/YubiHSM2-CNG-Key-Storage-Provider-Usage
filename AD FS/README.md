### Installing Microsoft Active Directory Federation Services with YubiHSM Key Storage Provider

Once the YubiHSM Key Storage Provider has been [installed and configured](../README.md), the AD FS signing and encrypting keys can be generated.

#### On the first AD FS node, generate the signing and encryption CSRs

By either:

- using certreq.exe with an inf file with "RSA, YubiHSM Key Storage Provider" specified as the CSP
- using PowerShell:

Token Signing:
``` PowerShell
New-SelfSignedCertificate -Provider "YubiHSM Key Storage Provider" -KeyLength 2048 -HashAlgorithm SHA256 -KeyUsage DigitalSignature,KeyEncipherment -NotAfter (Get-Date).AddYears(5) -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") -KeyAlgorithm RSA -KeyExportPolicy NonExportable -KeyUsageProperty Sign -Subject "CN=some string of your choosing here"
```
Token Decrypting:
``` PowerShell
New-SelfSignedCertificate -Provider "YubiHSM Key Storage Provider" -KeyLength 2048 -HashAlgorithm SHA256 -KeyUsage DataEncipherment,KeyEncipherment -NotAfter (Get-Date).AddYears(5) -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1") -KeyAlgorithm RSA -KeyExportPolicy NonExportable -KeyUsageProperty Decrypt -Subject "CN=some string of your choosing here"
```
- using using certificate MMC and submit directly to a CA using a template configured with CSP only "RSA, YubiHSM Key Storage Provider"

If using CSR, get it signed and import the CA response file.

Use ADFS MMC or PowerShell Set-AdfsCertificate cmdlet to specify the token-signing and token-decrypting certificates for the farm.  Export the public keys for the signing and encryption certificates and copy them to the second and subsequent nodes.

#### On the second or subsequent AD FS node

Import the public key exported from the first node to the computer My store
Run:
```
certutil.exe -store my
```
Note the serial number of the signing and of the encryption certificates

For each certificate run:

```
certutil.exe -f -csp "YubiHSM Key Storage Provider" -repairstore my SERIAL
```
Where SERIAL is the serial number of the certificate.  This associates the public key that was imported with the private key stored on the YubiHSM2 device through the YubiHSM KSP.
