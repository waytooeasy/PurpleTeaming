	JOHN
		TGS:  sudo john ./out.txt --fork=4 --format=krb5tgs --wordlist=/usr/share/wordlists/rockyou.txt --pot=results.pot
	HASHCAT
		ASREP:
			Edit and add 23$ after `$krb5asrep$`
			sudo hashcat -m 18200 -a 0 asrep.txt passwords.txt --outfile asrepcrack.txt --force
