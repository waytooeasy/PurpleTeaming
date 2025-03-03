	Powerview
		Desc:
			Enumerates shared folder, filters default C$ and IPC$ and checks if invoking user has access to it
		Usage:
			Import module "Powerview.ps1"
			Invoke-ShareFinder -domain domain -ExcludeStandard -CheckShareAccess
			lolbin findstr /s /i /m "oass" *.bat - Any extensions tbh
			matched files exlucde the /m param for inline stdout
			One obvious and yet uncommon search term is the `NetBIOS` name of the domain. Commands such as `runas` and `net` take passwords as a positional argument on the command line instead of passing them via `pass`, `pw`
			Usually defined as `/user:DOMAIN\USERNAME PASSWORD`