# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

![alt text](https://github.com/juan-desu/Attack-Defense-Analysis-of-a-Vulnerable-Network/blob/main/flags/Nmap%20result%20scans.png)


This scan identifies the services below as potential points of entry:
- Target 1
  - Port 22 (SSH)
  - Port 80 (HTTP)
  - Port 111 (rpcbind)
  - Port 139 (netbios/smb)
  - Port 445 (netbios/smb)

The following vulnerabilities were identified on each target:
- Target 1
   - SSH with Password
-Users are able to ssh into the machine with simply a password, rather than requiring an SSH key.
-User michael had an incredibly weak password (same as his username).
   - python can run with sudo
- User steven has the ability to run python with sudo
- Python can execute arbitrary code on the system, so it is trivial to get a shell with root access using this loophole
   - Database credentials in plain text
- Database credentials for the wordpress site were found written in plain text, and stored in the /var/www/html/wp_config.php.
- This allowed us to access the mysql database used for the site, and extract password hashes and other confidential information.
   - CVE-2017-3167
- Authentication bypass is possible on the version of Apache running on the server
   - CVE-2017-7494
- Version of samba running on the server is insecure and allows for remote code execution

### Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: b9bbcb33e11b80be759c4e844862482d
    - **Exploit Used**
      -Weak Password / SSH with password
	-After SSHing into the host with michael's credentials, we were able to search the /var/www/html directory for flag1. 
      - Command run
	-ssh michael@192.168.1.100
-cd /var/www/html
-grep -ER flag1
![alt text](https://github.com/juan-desu/Attack-Defense-Analysis-of-a-Vulnerable-Network/blob/main/flags/flag%201.png)
  - `flag2.txt`: fc3fd58dcdad9ab23faca6e9a36e581c
    - **Exploit Used**
      -Weak Password / SSH with password
	-After SSHing into the host with michael's credentials, flag2 was found right in /var/www
      - Command run_
	- ssh michael@192.168.1.100
- cd /var/www
- cat flag2.txt
![alt text](https://github.com/juan-desu/Attack-Defense-Analysis-of-a-Vulnerable-Network/blob/main/flags/Flag%202.png)
  - `flag3.txt`: afc01ab56b50591e7dccf93122770cd2
    - **Exploit Used**
      - Database credentials in plain text
	- After getting the database credentials from /var/www/html/wp_config.php, we connected to the mysql database and searched for the flag.
      - Command run
	- ssh michael@192.168.1.100
- less /var/www/html/wp_config.php
- mysql --user root --password # Password is R@v3nSecurity
-mysql> SELECT post_title, post_content FROM wp_posts WHERE post_title LIKE "flag%";
- This returned the value for flag 3
![alt text](https://github.com/juan-desu/Attack-Defense-Analysis-of-a-Vulnerable-Network/blob/main/flags/Flag%203-4.png)
  - `flag4.txt`: flag4.txt: 715dea6c055b9fe3337544932f2941ce
    - **Exploit Used**
      - python can run with sudo
	-After cracking steven's password using john and the hash found in the database, we determined that user steven could run python with sudo permissions.
-This allows us to use python as sudo to execute a shell program, thereby granting us access to the root account.
- flag4.txt was found in the /root directory, the root account's home directory.

      - Commands run
- python -c 'import os; os.system("/bin/sh")'
- cat flag4.txt
![alt text](https://github.com/juan-desu/Attack-Defense-Analysis-of-a-Vulnerable-Network/blob/main/flags/Flag%204.png)
