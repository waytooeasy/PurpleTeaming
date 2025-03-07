	Desc:
		Impersonate a user with perms:
			- `Replicating Directory Changes`
			- `Replicating Directory Changes All`
			
		Requires already using being logged in as said user
		
	Usage:
		Subsequently, we need to use `Mimikatz`, one of the tools with an implementation for performing DCSync. We can run it by specifying the username whose password hash we want to obtain if the attack is successful.
		We can perform `pass-the-hash` with the obtained hash and authenticate against any Domain Controller