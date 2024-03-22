# RHEL Study Guide: Analyze and Store Logs

[RHEL Study Guide - Table of Contents](https://github.com/pslucas0212/RHEL-Study-Guide) 

In this section change the time zone on an existing server and configure a new log file for all events for authentication failures.

Select appropriate timezone for America/Jamaica
```
# tzselect
Please identify a location so that time zone rules can be set correctly.
Please select a continent, ocean, "coord", or "TZ".
 1) Africa
 2) Americas
 3) Antarctica
 4) Asia
 5) Atlantic Ocean
 6) Australia
 7) Europe
 8) Indian Ocean
 9) Pacific Ocean
10) coord - I want to use geographical coordinates.
11) TZ - I want to specify the timezone using the Posix TZ format.
#? 2
Please select a country whose clocks agree with yours.
 1) Anguilla		  15) Costa Rica	    29) Honduras	      43) St Martin (French)
 2) Antigua & Barbuda	  16) Cuba		    30) Jamaica		      44) St Pierre & Miquelon
 3) Argentina		  17) Curaçao		    31) Martinique	      45) St Vincent
 4) Aruba		  18) Dominica		    32) Mexico		      46) Suriname
 5) Bahamas		  19) Dominican Republic    33) Montserrat	      47) Trinidad & Tobago
 6) Barbados		  20) Ecuador		    34) Nicaragua	      48) Turks & Caicos Is
 7) Belize		  21) El Salvador	    35) Panama		      49) United States
 8) Bolivia		  22) French Guiana	    36) Paraguay	      50) Uruguay
 9) Brazil		  23) Greenland		    37) Peru		      51) Venezuela
10) Canada		  24) Grenada		    38) Puerto Rico	      52) Virgin Islands (UK)
11) Caribbean NL	  25) Guadeloupe	    39) St Barthelemy	      53) Virgin Islands (US)
12) Cayman Islands	  26) Guatemala		    40) St Kitts & Nevis
13) Chile		  27) Guyana		    41) St Lucia
14) Colombia		  28) Haiti		    42) St Maarten (Dutch)
#? 30

The following information has been given:

	Jamaica

Therefore TZ='America/Jamaica' will be used.
Selected time is now:	Fri Mar 22 16:12:06 EST 2024.
Universal Time is now:	Fri Mar 22 21:12:06 UTC 2024.
Is the above information OK?
1) Yes
2) No
#? 1

You can make this change permanent for yourself by appending the line
	TZ='America/Jamaica'; export TZ
to the file '.profile' in your home directory; then log out and log in again.

Here is that TZ value again, this time on standard output so that you
can use the /bin/tzselect command in shell scripts:
America/Jamaica
```
Udpate serverb to the timezone America/Jamaica
```
# timedatectl set-timezone America/Jamaica
```
Check the current timezone setting
```
# timedatectl
               Local time: Fri 2024-03-22 16:15:40 EST
           Universal time: Fri 2024-03-22 21:15:40 UTC
                 RTC time: Fri 2024-03-22 21:15:40
                Time zone: America/Jamaica (EST, -0500)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

View recorded log events for the last 30 minutes
```
# date
Fri Mar 22 04:16:26 PM EST 2024
# date -d "-30 minutes"
Fri Mar 22 03:46:34 PM EST 2024
# journalctl --since 15:46 --until 16:16
Mar 22 15:47:55 serverb.lab.example.com sshd[4483]: Accepted publickey for root from 172.25.250.9 port 5384>
Mar 22 15:47:55 serverb.lab.example.com systemd[1]: Created slice User Slice of UID 0.
Mar 22 15:47:55 serverb.lab.example.com systemd[1]: Starting User Runtime Directory /run/user/0...
...
```

Configure the rsyslog service to write the authpriv.alert messages to the /var/log/auth-errors file.
```
# vi /etc/rsyslog.d/auth-errors.conf
authpriv.alert /var/log/auth-errors
```

Restart rsyslog service
```
# systemctl restart rsyslog
```
Test the update
```
# logger -p authpriv.alert "Logging test authpriv.alert"
# tail /var/log/auth-errors 
Mar 22 16:23:16 serverb root[5251]: Logging test authpriv.alert
```

We are finished.


```


