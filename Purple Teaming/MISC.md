	xfreerdp3
		reizable screen and ESPlAT keyboard
			 xfreerdp3 /v:10.129.86.7 /u:eagle\\bob +dynamic-resolution /kbd:layout:0x080A

	smbclient:
		 smbclient \\\\10.129.204.151\\Share -U eagle/administrator%Slavi123

   	winr:
    		Invoke-Command -ComputerName "Target-PC" -ScriptBlock { net share /delete "ShareName" /y } -Credential (Get-Credential)

	logoff users:
		query user /server:ServerName
		logoff 3 /server:DC01