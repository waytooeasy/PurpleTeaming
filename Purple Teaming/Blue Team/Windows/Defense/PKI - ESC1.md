	Description:
	
		After `SpectreOps` released the research paper [Certified Pre-Owned](https://specterops.io/wp-content/uploads/sites/3/2022/06/Certified_Pre-Owned.pdf), `Active Directory Certificate Services` (`AD CS`) became one of the most favorite attack vectors for threat agents due to many reasons, including:

	1. Using certificates for authentication has more advantages than regular username/password credentials.
	2. Most PKI servers were misconfigured/vulnerable to at least one of the eight attacks discovered by SpectreOps (various researchers have discovered more attacks since then).
	
	There are a plethora of advantages to using certificates and compromising the `Certificate Authority` (`CA`):
	
	- Users and machines certificates are valid for 1+ years.
	- Resetting a user password does not invalidate the certificate. With certificates, it doesn't matter how many times a user changes their password; the certificate will still be valid (unless expired or revoked).
	- Misconfigured templates allow for obtaining a certificate for any user.
	- Compromising the CA's private key results in forging `Golden Certificates`.
	
	These advantages make certificates the preferred method for long-term persistence. While SpectreOps disclosed eight privilege escalation techniques, we will examine the first, `ESC1`, to demonstrate how it works. The description of `ESC1` is:
	
	- `Domain escalation via No Issuance Requirements + Enrollable Client Authentication/Smart Card Logon OID templates + CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT`.

	 Prevention

		The attack would not be possible if the `CT_FLAG_ENROLLEE_SUPPLIES_SUBJECT` flag is not enabled in the certificate template. Another method to thwart this attack is to require `CA certificate manager approval` before issuing certificates; this will ensure that no certificates on potentially dangerous templates are issued without manual approval (which hopefully correlates that the request originated from a legit user).
		
		Because there are many different privilege escalation techniques, it is highly advised to regularly scan the environment with `Certify` or other similar tools to find potential PKI issues.

	 Detection

		When the CA generates the certificate, two events will be logged, one for the received request and one for the issued certificate, if it succeeds. Those events have the IDs of `4886` and `4887`
		Note that events `4886` and `4887` will be generated on the machine issuing the certificate rather than the domain controller. If GUI access is not available, we can use PSSession to interact with the PKI machine, and the `Get-WinEvent` cmdlet to search for the events
		Get-WINEvent -FilterHashtable @{Logname='Security'; ID='4886'} or Get-WINEvent -FilterHashtable @{Logname='Security'; ID='4887'}

		To view the full audit log of the events, we can pipe the output into `Format-List` , or save the events in an array and check them individually.
		[pki]: PS C:\Users\htb-student\Documents> $events = Get-WinEvent -FilterHashtable @{Logname='Security'; ID='4886'} 
		[pki]: PS C:\Users\htb-student\Documents> $events[0] | Format-List -Property * 