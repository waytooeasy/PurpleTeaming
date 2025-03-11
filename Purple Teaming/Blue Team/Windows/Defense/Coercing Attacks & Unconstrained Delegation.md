	Description

		Coercing attacks have become a `one-stop shop` for escalating privileges from any user to Domain Administrator. Nearly every organization with a default AD infrastructure is vulnerable. We've just tasted coercing attacks when we discussed the `PrinterBug`. However, several other RPC functions can perform the same functionality. Therefore, any domain user can coerce `RemoteServer$` to authenticate to any machine in the domain. Eventually, the [Coercer](https://github.com/p0dalirius/Coercer) tool was developed to exploit all known vulnerable RPC functions simultaneously.
		
		Similar to the `PrinterBug`, an attacker can choose from several "follow up" options with the reverse connection, which, as mentioned before, are:
		
		1. Relay the connection to another DC and perform DCSync (if `SMB Signing` is disabled).
		2. Force the Domain Controller to connect to a machine configured for `Unconstrained Delegation` (`UD`) - this will cache the TGT in the memory of the UD server, which can be captured/exported with tools like `Rubeus` and `Mimikatz`.
		3. Relay the connection to `Active Directory Certificate Services` to obtain a certificate for the Domain Controller. Threat agents can then use the certificate on-demand to authenticate and pretend to be the Domain Controller (e.g., DCSync).
		4. Relay the connection to configure Resource-Based Kerberos Delegation for the relayed machine. We can then abuse the delegation to authenticate as any Administrator to that machine.

	Prevention

		Windows does not offer granular visibility and control over RPC calls to allow discovering what is being used and block certain functions. Therefore, an out-of-the-box solution for preventing this attack does not exist currently. However, there are two different general approaches to preventing coercing attacks:
		
		1. Implementing a third-party RPC firewall, such as the one from [zero networks](https://github.com/zeronetworks/rpcfirewall), and using it to block dangerous RPC functions. This tool also comes up with an audit mode, allowing monitoring and gaining visibility on whether business disruptions may occur by using it or not. Moreover, it goes a step further by providing the functionality of blocking RPC functions if the dangerous `OPNUM` associated with coercing is present in the request. (Note that in this option, for every newly discovered RPC function in the future, we will have to modify the firewall's configuration file to include it.)
		2. Block Domain Controllers and other core infrastructure servers from connecting to outbound ports `139` and `445`, `except` to machines that are required for AD (as well for business operations). One example is that while we block general outbound traffic to ports 139 and 445, we still should allow it for cross Domain Controllers; otherwise, domain replication will fail. (The benefit of this solution is that it will also work against newly discovered vulnerable RPC functions or other coercing methods.)

	Detection

		As mentioned, Windows does not provide an out-of-the-box solution for monitoring RPC activity. The RPC Firewall from [zero networks](https://github.com/zeronetworks/rpcfirewall) is an excellent method of detecting the abuse of these functions and can indicate immediate signs of compromise; however, if we follow the general recommendations to not install third-party software on Domain Controllers then firewall logs are our best chance.