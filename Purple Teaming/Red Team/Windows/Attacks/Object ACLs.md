	Desc:
		Identify abusable ACLS

	Usage:
		Use [BloodHound](https://github.com/BloodHoundAD/BloodHound) to graph the relationships between the objects and [SharpHound](https://github.com/BloodHoundAD/SharpHound) to scan the environment and pass `All` to the `-c` parameter (short version of `CollectionMethod`):

		.\SharpHound.exe -c All
`