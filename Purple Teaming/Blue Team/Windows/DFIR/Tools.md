#### USN Journal

	`USN`, or `Update Sequence Number`, is a vital component of the NTFS file system in Windows. The USN Journal is essentially a change journal feature that meticulously logs alterations to files and directories on an NTFS volume.
	
	For those in digital forensics, the USN Journal is a goldmine. It enables us to monitor operations such as File Creation, Rename, Deletion, and Data Overwrite.

#### Windows Event Logs Parsing Using EvtxECmd (EZ-Tool)

	`EvtxECmd` (available at `C:\Users\johndoe\Desktop\Get-ZimmermanTools\net6\EvtxeCmd`) is another brainchild of Eric Zimmerman, tailored for Windows Event Log files (EVTX files). With this tool at our disposal, we can extract specific event logs or a range of events from an EVTX file, converting them into more digestible formats like JSON, XML, or CSV.

Maps in EvtxECmd

	Maps in `EvtxECmd` are pivotal. They metamorphose customized data into standardized fields in the CSV (and JSON) data. This granularity and precision are indispensable in forensic investigations, enabling analysts to interpret and extract salient information from Windows Event Logs with finesse.
	
	Standardized fields in maps:
	
	- `UserName`: Contains information about user and/or domain found in various event logs
	- `ExecutableInfo`: Contains information about process command line, scheduled tasks etc.
	- `PayloadData1,2,3,4,5,6`: Additional fields to extract and put contextual data from event logs
	- `RemoteHost`: Contains information about IP address
	
	`EvtxECmd` plays a significant role in:
	
	- Converting the unique part of an event, known as EventData, into a more standardized and human-readable format.
	- Ensuring that the map files are tailored to specific event logs, such as Security, Application, or custom logs, to handle differences in event structures and data.
	- Using a unique identifier, the Channel element, to specify which event log a particular map file is designed for, preventing confusion when event IDs are reused across different logs.

#### Program Execution Artifacts

	When we talk about `execution artifacts` in digital forensics, we're referring to the traces and evidence left behind on a computer system or device when a program runs. These little bits of information can clue us in on the activities and behaviors of software, users, and even those with malicious intent. If we want to piece together what went down on a computer, diving into these execution artifacts is a must.
	
	You might stumble upon some well-known execution artifacts in these Windows components:
	
	- `Prefetch`
	- `ShimCache`
	- `Amcache`
	- `BAM (Background Activity Moderator)`

#### Investigation of Prefetch

	`Prefetch` is a Windows operating system feature that helps optimize the loading of applications by preloading certain components and data. Prefetch files are created for every program that is executed on a Windows system, and this includes both installed applications and standalone executables. The naming convention of Prefetch files is indeed based on the original name of the executable file, followed by a hexadecimal value of the path where the executable file resides, and it ends with the `.pf` file extension.
	
	In digital forensics, the Prefetch folder and associated files can provide valuable insights into the applications that have been executed on a Windows system. Forensic analysts can examine Prefetch files to determine which applications have been run, how often they were executed, and when they were last run.
	
	In general, prefetch files are stored in the `C:\Windows\Prefetch\` directory.

#### Investigation of ShimCache (Application Compatibility Cache)

	`ShimCache` (also known as AppCompatCache) is a Windows mechanism used by the Windows operating systems in order to identify application compatibility issues. This database records information about executed applications, and is stored in the Windows Registry. This information can be used by developers to track compatibility issues with executed programs.
	
	In the `AppCompatCache` cache entries, we can see the information such as:
	
	- Full file paths
	- Timestamps
	    - Last modified time ($Standard_Information)
	    - Last updated time (Shimcache)
	- Process execution flag
	- Cache entry position
	
	Forensic investigators can use this information to detect the execution of potentially malicious files.
	
	The `AppCompatCache` key is located at the `HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\ControlSet001\Control\Session Manager\AppCompatCache` registry location.
	
	Let's load the `SYSTEM` registry hive

#### Investigation of Amcache

	`AmCache` refers to a Windows registry file which is used to store evidence related to program execution. It serves as a valuable resource for digital forensics and security investigations, helping analysts understand the history of application execution and detect signs of any suspicious execution.
	
	The information that it contains include the execution path, first executed time, deleted time, and first installation. It also provides the file hash for the executables.
	
	On Windows OS the AmCache hive is located at `C:\Windows\AppCompat\Programs\AmCache.hve`
	
	AmCache-related files harvested from KAPE are typically housed in `<KAPE_output_folder>\Windows\AppCompat\Programs`.
	
	Let's load `C:\Users\johndoe\Desktop\forensic_data\kape_output\D\Windows\AppCompat\Programs\AmCache.hve`

#### Investigation of Windows BAM (Background Activity Moderator)

	The `Background Activity Moderator` (BAM) is a component in the Windows operating system that tracks and logs the execution of certain types of background or scheduled tasks. BAM is actually a kernel device driver as shown in the below screenshot.

	It is primarily responsible for controlling the activity of background applications but it can help us in providing the evidence of program execution which it lists under the bam registry hive. The BAM key is located at the below registry location. `HKEY_LOCAL_MACHINE\SYSTEM\ControlSet001\Services\bam\State\UserSettings\{USER-SID}`

	Using Registry Explorer, we can browse this inside the SYSTEM hive to see the executable names. Registry explorer already has a bookmark for `bam`.

Reviewing PowerShell-related activity in detail can be instrumental during investigations.

	Here are some recommended guidelines when handling PowerShell data.
	
	- `Unusual Commands`: Look for PowerShell commands that are not typical in your environment or are commonly associated with malicious activities. For example, commands to download files from the internet (Invoke-WebRequest or wget), commands that manipulate the registry, or those that involve creating scheduled tasks.
	- `Script Execution`: Check for the execution of PowerShell scripts, especially if they are not signed or come from untrusted sources. Scripts can be used to automate malicious actions.
	- Encoded Commands: Malicious actors often use encoded or obfuscated PowerShell commands to evade detection. Look for signs of encoded commands in transcripts.
	- `Privilege Escalation`: Commands that attempt to escalate privileges, change user permissions, or perform actions typically restricted to administrators can be suspicious.
	- `File Operations`: Check for PowerShell commands that involve creating, moving, or deleting files, especially in sensitive system locations.
	- `Network Activity`: Look for commands related to network activity, such as making HTTP requests or initiating network connections. These may be indicative of command and control (C2) communications.
	- `Registry Manipulation`: Check for commands that involve modifying the Windows Registry, as this can be a common tactic for malware persistence.
	- `Use of Uncommon Modules`: If a PowerShell script or command uses uncommon or non-standard modules, it could be a sign of suspicious activity.
	- `User Account Activity`: Look for changes to user accounts, including creation, modification, or deletion. Malicious actors may attempt to create or manipulate user accounts for persistence.
	- `Scheduled Tasks`: Investigate the creation or modification of scheduled tasks through PowerShell. This can be a common method for persistence.
	- `Repeated or Unusual Patterns`: Analyze the patterns of PowerShell commands. Repeated, identical commands or unusual sequences of commands may indicate automation or malicious behavior.
	- `Execution of Unsigned Scripts`: The execution of unsigned scripts can be a sign of suspicious activity, especially if script execution policies are set to restrict this.