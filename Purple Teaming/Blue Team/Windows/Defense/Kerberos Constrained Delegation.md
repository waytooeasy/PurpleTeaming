	Desc:
		`Kerberos Delegation` enables an application to access resources hosted on a different server; for example, instead of giving the service account running the web server access to the database directly, we can allow the account to be delegated to the SQL server service. Once a user logs into the website, the web server service account will request access to the SQL server service on behalf of that user, allowing the user to get access to the content in the database that they’ve been provisioned to without having to assign any access to the web server service account itself.

	We can configure three types of delegations in Active Directory:
	
	- `Unconstrained Delegation` (most permissive/broad)
	- `Constrained Delegation`
	- `Resource-based Delegation`
	
	Knowing and understanding that `any` type of delegation is a possible security risk is paramount, and we should avoid it unless necessary.
	
	As the name suggests, `unconstrained delegation` is the most permissive, allowing an account to delegate to any service. In `constrained delegation`, a user account will have its properties configured to specify which service(s) they can delegate. For `resource-based delegation`, the configuration is within the computer object to whom delegation occurs. In that case, the computer is configured as `I trust only this/these accounts`. It is rare to see `Resource-based delegation` configured by an Administrator in production environments ( threat agents often abuse it to compromise devices). However, `Unconstrained` and `Constrained` delegations are commonly encountered in production environments.
	
	Prevention
	
		Fortunately, when designing Kerberos Delegation, Microsoft implemented several protection mechanisms; however, it did not enable them by default to any user account. There are two direct ways to prevent a ticket from being issued for a user via delegation:
		
		- Configure the property `Account is sensitive and cannot be delegated` for all privileged users.
		- Add privileged users to the `Protected Users` group: this membership automatically applies the protection mentioned above (however, it is not recommended to use `Protected Users` without first understanding its potential implications).
		
		We should treat any account configured for delegation as extremely privileged, regardless of its actual privileges (such as being only a Domain user). Cryptographically secure passwords are a must, as we don't want `Kerberoasting` giving threat agents an account with delegation privileges.
		
	 Detection

		Correlating users' behavior is the best technique to detect `constrained delegation` abuse. Suppose we know the location and time a user regularly uses to log in. In that case, it will be easy to alert on other (suspicious) behaviors—for example, consider the account 'Administrator' in the attack described above. If a mature organization uses Privileged Access Workstations (PAWs), they should be alert to any privileged users not authenticating from those machines, proactively monitoring events with the ID `4624` (successful logon).
		
		In some occasions, a successful logon attempt with a delegated ticket will contain information about the ticket's issuer under the `Transited Services` attribute in the events log. This attribute is normally populated if the logon resulted from an `S4U` (`Service For User`) logon process.
		
		S4U is a Microsoft extension to the Kerberos protocol that allows an application service to obtain a Kerberos service ticket on behalf of a user; if we recall from the attack flow when utilizing `Rubeus`, we specified this `S4U` extension.