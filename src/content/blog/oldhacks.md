---
description: We think we have it bad with some of the vulnerabilities out now, most
  recently Palo Alto and Cisco have had major SSL VPN vulnerabilities, but at least
  we have some basic security.
pubDate: '2024-04-27'
title: 'Old Vulnerabilities:  PHF'
---


We think we have it bad with some of the vulnerabilities out now, most recently Palo Alto and Cisco have had major SSL VPN vulnerabilities, but at least we have some basic security.

1990s security was just about non-existent.  [The phf-cgi](https://insecure.org/sploits/phf-cgi.html) vulnerability is a good example.

Linux and other Unix OS's used to come with many services running, and if you installed the apache web server, it would also have some neat scripts available by default.

The scripts would be usually written in perl and reside in a cgi-bin directory (Common Gateway Interface).  An awesome script usually included was named phf.

I'm not sure what the purpose of this script was, but most people would use it to cat files on the system, usually the password file.

It's just crazy comparing security back then to security now.