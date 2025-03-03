	Desc:
		Stores logon scripts, gp data and other domain-wide data
		32-byte AES encrypted privete key
		\\<DOMAIN>\SYSVOL\<DOMAIN>\Policies\`
		[AES private key on MSDN](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-gppref/2c15cbf0-f086-4c74-8b70-1f2fa45dd4be?redirectedfrom=MSDN):
		patch (`KB2962486)`
		Released in 2014, old directories may still contain data since patch does not clear the old data

	Detection:
			Auduting the directory for user reads, generates EVENT 4664
			Honeypot user with old password ceration metadata, dummy scheduled data to ensure recent logon attempts
			event ids to correlate with the dummy user failed auths