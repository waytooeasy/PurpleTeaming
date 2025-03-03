	Desc:
		GPOs that can modify 'Authenticated Users' or 'Domain Users'
		OU Infection

	Detection:
		Event 5136, user not trusted and object modified object value/key

	Prevention:
		Lock down the GPO permissions to be modified by a particular group of users only or by a specific account