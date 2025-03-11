	Description

		In Active Directory, [Access Control Lists (ACLs)](https://learn.microsoft.com/en-us/windows/win32/secauthz/access-control-lists) are tables, or simple lists, that define the trustees who have access to a specific object and their access type. A trustee may be any security principal, such as a user account, group, or login session. Each `access control list` has a set of `access control entries` (`ACE`), and each ACE defines the trustee and the type of access the trustee has. Therefore, an object can be accessed by multiple trustees since there can be various ACEs. Access control lists are also used for auditing purposes, such as recording the number of access attempts to a securable object and the type of access. A securable object is any named object in Active Directory that contains a security descriptor, which has the security information about the object, which includes ACLs.
		
		An example of an `Access Control Entry` is that, by default, AD gives Domain Admins the right to modify the password of every object. However, rights can also be delegated to certain users or groups that can perform a specific action on other objects; this can be password resets, modification of group membership, or deletion of objects. In large organizations, if it is virtually impossible to avoid non-privileged users ending up with delegated rights, they should eliminate human error and have well-defined process documentation. For example, suppose an employee was to (accidentally/intentionally) change their department from IT to Security Operations. In that case, the organization must have a process to revoke all rights and access to systems and applications. In real-life AD environments, we will often encounter cases such as:
		
		- All Domain users added as Administrators to all Servers
		- Everyone can modify all objects (having full rights to them).
		- All Domain users have access to the computer's extended properties containing the LAPS passwords.

	Prevention

		There are three things we can do:
		
		- Begin `continuous assessment` to detect if this is a problem in the AD environment.
		- `Educate` employees with high privileges to avoid doing this.
		- `Automate` as much as possible from the access management process, and only assign privileged access to administrative accounts; this ensures that administrators don't manually edit accounts which reduces the risk of introducing delegated rights to unprivileged users.

	Detection
	
		Fortunately, we have several ways to detect if AD objects are modified. Unfortunately, the events generated for modified objects are incomplete, as they do not provide granular visibility over what was changed. For example, in the first case described above, Bob modified Anni by adding an SPN value. By doing so, Bob will have the means to perform Kerberoasting against Anni. When the SPN value gets added, an event with the ID `4738`, "A user account was changed", is generated. However, this event does not demonstrate all modified user properties, including the SPN. Therefore, the event only notifies about the modification of a user but does not specify what exactly was changed ( although it does have a fair amount of fields that can be useful). Below is the event that will be generated if Bob adds any bogus SPN value to Anni's User Object:

		However, using this event, we can tell if a non-privileged user performs privileged actions on another user. If, for example, all privileged users have a naming convention that begins with "adminxxxx", then any change not associated with "adminxxxx" is suspicious. If an ACL abuse leads to a password reset, the event ID `4724` will be logged.

		Similarly, if Bob were to perform the second scenario, an event with ID `4742` would be generated, which is also unfortunately limited in the information it can provide; however, it notifies about the action that the user account Bob is compromised and used maliciously. The following was the event ID `4742` generated when Bob modified Server01:

	Honeypot
		
		Misconfigured ACLs can be an effective mechanism of detection for suspicious behavior. There are two ways to approach this:
		
		1. Assign relatively high ACLs to a user account used as a honeypot via a previously discussed technique—for example, a user whose `fake` credentials are exposed in the description field. Having ACLs assigned to the account may provoke an adversary to attempt and verify if the account's exposed password is valid as it holds high potential.
		2. Have an account that `everyone` or many users can modify. This user will ideally be a honeypot user, with some activity to mimic real users. Any changes occurring on this honeypot user are malicious since there is no valid reason for anyone to perform any actions on it (except admins, that may occasionally need to reset the account's password to make the account look realistic). Therefore, any event ID `4738` associated with the honeypot user should trigger an alert. Additionally, mature organizations may immediately disable the user performing the change and initiate a forensic investigation on the source device.
		---