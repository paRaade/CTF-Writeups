**Lab**:
 [Log Analysis – Compromised WordPress](https://blueteamlabs.online/home/challenge/log-analysis-compromised-wordpress-ce000f5b59)

**Scenario**:
One of our WordPress sites has been compromised but we're currently unsure how. The primary hypothesis is that an installed plugin was vulnerable to a remote code execution vulnerability which gave an attacker access to the underlying operating system of the server.

**Tools used**: 
Visual Studio Code,  grep

**Summary:**
Following the logic of the hypothesis in the scenario. The reasonable thing to look for in the access.log provided is to look for the wordpress login portal. Typically when exploiting remote code execution vulnerabilites in Wordpress it requires the attacker to first have an authenticated session.

A quick google search shows that wordpress login file name is: wp-login.php.
Using  **grep -n "wp-login.php" access.log** I can see multiple URL  requests to **/wp-login.php?itsec-hb-token=adminlogin**


