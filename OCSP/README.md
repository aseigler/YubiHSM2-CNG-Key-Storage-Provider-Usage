### Installing Microsoft Online Certificate Status Protocol with YubiHSM Key Storage Provider

Once the YubiHSM Key Storage Provider has been [installed and configured](../README.md), the OCSP keys can be generated.

#### On the first OCSP responder (array controller), generate the OCSP response certificate

By either:

- using certreq.exe with an inf file with "RSA, YubiHSM Key Storage Provider" specified as the CSP
- using certificate MMC custom enrollment with "RSA, YubiHSM Key Storage Provider" specified as the CSP
- using using certificate MMC and submit directly to a CA using a template configured with CSP only "RSA, YubiHSM Key Storage Provider"

If using CSR, get it signed and import the CA response file.

Use OCSP MMC or PowerShell to configure the OCSP responder.  Export the public key and copy to second and subsequent responder array members.

#### On the second and subsequent OCSP responder array members

Import the public key exported from the first node to the computer My store

Run:
```
certutil.exe -store my
```
Note the serial number of OCSP signing certificate

Run:

```
certutil.exe -f -csp "YubiHSM Key Storage Provider" -repairstore my SERIAL
```
Where SERIAL is the serial number of the OCSP signing certificate.  This associates the public key that was imported with the private key stored on the YubiHSM2 device through the YubiHSM KSP.
