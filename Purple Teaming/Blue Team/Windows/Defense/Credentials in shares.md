	Desc:
	
		Shared with credential related data exposed through shared folders, accesed granted to users are wrongfully configured
		
	Prevention:
	
		The best practice to prevent these attacks is to lock down every share in the domain so there are no loose permissions.
		Technically, there is no way to prevent what users leave behind them in scripts or other exposed files, so performing regular scans (e.g., weekly) on AD environments to identify any new open shares or credentials exposed in older ones is necessary

	Detection:
	
		Understanding and analyzing users' behavior is the best detection technique for abusing discovered credentials in shares. Suppose we know the time and location of users' login via data analysis. In that case, it will be effortless to alert on seemingly suspicious behaviors—for example, the discovered account 'Administrator' in the attack described above. If we were a mature organization that used `Privileged Access Workstation`, we would be alert to privileged users not authenticating from those machines. These would be alerts on event IDs `4624`/`4625` (failed and successful logon) and `4768` (Kerberos TGT requested).
		
	Another detection technique is discovering the `one-to-many` connections, for example, when `Invoke-ShareFinder` scans every domain device to obtain a list of its network shares. It would be abnormal for a workstation to connect to 100s or even 1000s of other devices simultaneously.

	Honeypot:
	
		- `service account` that was created 2+ years ago. The last password change should be at least one year ago.
		- The last modification time of the file containing the `fake` password must be after the last password change of the account. Because it is a fake password, there is no risk of a threat agent compromising the account.
		- The account is still active in the environment.
		- The script containing the credentials should be realistic. (For example, if we choose an `MSSQL service account`, a `connection string` can expose the credentials.)
		- 
		Because the provided password is wrong, we would primarily expect failed logon attempts. Three event IDs (`4625`, `4771`, and `4776`) can indicate this. Here is how they look from our playground environment if an attacker is attempting to authenticate with the account `svc-iis` and a wrong password: