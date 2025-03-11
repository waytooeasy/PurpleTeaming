	Desc:
		The [Print Spooler](https://learn.microsoft.com/en-us/windows/win32/printdocs/print-spooler) is an old service enabled by default, even with the latest Windows Desktop and Servers versions. The service became a popular attack vector when in 2018, `Lee Christensen` found the `PrinterBug`. The functions [RpcRemoteFindFirstPrinterChangeNotification](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-rprn/b8b414d9-f1cd-4191-bb6b-87d09ab2fd83) and [RpcRemoteFindFirstPrinterChangeNotificationEx](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-rprn/eb66b221-1c1f-4249-b8bc-c5befec2314d) can be abused to force a remote machine to perform a connection to any other machine it can reach. Moreover, the `reverse` connection will carry authentication information as a `TGT`. Therefore, any domain user can coerce `RemoteServer$` to authenticate to any machine. Microsoft's stance on the `PrinterBug` was that it will not be fixed, as the issue is "by-design".
		
		The impact of `PrinterBug` is that any Domain Controller that has the Print Spooler enabled can be compromised in one of the following ways:
		
		1. Relay the connection to another DC and perform DCSync (if `SMB Signing` is disabled).
		2. Force the Domain Controller to connect to a machine configured for `Unconstrained Delegation` (`UD`) - this will cache the TGT in the memory of the UD server, which can be captured/exported with tools like `Rubeus` and `Mimikatz`.
		3. Relay the connection to `Active Directory Certificate Services` to obtain a certificate for the Domain Controller. Threat agents can then use the certificate on-demand to authenticate and pretend to be the Domain Controller (e.g., DCSync).
		4. Relay the connection to configure `Resource-Based Kerberos Delegation` for the relayed machine. We can then abuse the delegation to authenticate as any Administrator to that machine.
		
	Prevention:	
		Print Spooler should be disabled on all servers that are not printing servers. Domain Controllers and other core servers should never have additional roles/functionalities that open and widen the attack surface toward the core AD infrastructure.
		
		Additionally, there is an option to prevent the abuse of the `PrinterBug` while keeping the service running: when disabling the registry key `RegisterSpoolerRemoteRpcEndPoint`, any incoming remote requests get blocked; this acts as if the service was disabled for remote clients. Setting the registry key to 1 enables it, while 2 disables it

	 Detection

		Exploiting the `PrinterBug` will leave traces of network connections toward the Domain Controller; however, they are too generic to be used as a detection mechanism.
		
		In the case of using `NTLMRelayx` to perform DCSync, no event ID `4662` is generated, however, to obtain the hashes as DC1 from DC2, there will be a successful logon event for DC1. This event originates from the IP address of the Kali machine, not the Domain Controller, as we can see below:

	A suitable detection mechanism always correlates all logon attempts from core infrastructure servers to their respective IP addresses