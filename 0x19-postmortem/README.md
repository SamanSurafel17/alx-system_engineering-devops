#Postmortem

# Issue Summary:
on 1/20/2022 at 8:34 pm EST, 100% of the website's service was down for a total 10 minutes, with service reinstated at 8:44 pm EST. Users universally experienced a response with a status code of 404(Page not Found). the root cause of the outage was a renaming file name, file name change from 'Public_Html' as 'Publics_Html'.

## Timeline for 1/20/22 (EST):
**8:34 pm:** After deploying a WebSite Update, a Junior engineer noticed that the website returning a 404 status code.

**8:35 pm:** All running processes on a particular server were checked using `ps auxf`. Apache2 and MySQL were found to be running as expected, indicating an error with server.

**8:36 pm:** The Server configuration file `/var/www/html/` was edited to enable debug mode.

**8:37 pm:** The website was curled to reveal a fatal error, a missing file `/var/www/html/Publics_html` required in `/var/www/html/`. The nonexistent `Public_html` indicated a potential typographical error.

**8:38 pm:** `ls` was used to check the contents of  `/var/www/html/`.  It was discovered that the file `/var/www/html/Publics_html ` existed, confirming a typographical error was made.

**8:39 pm:** The typographical error was fixed on the individual server using `sed -i 's/Publics/Public/' /var/www/html/`. 

**8:40 pm:** Website service was then tested once more, with content being served as expected.

**8:42 pm:** A puppet manifest was developed to fix this issue on a large scale.

**8:44 pm:** The puppet manifest was deployed on all remaining servers, bringing website service back to 100%. 

## Root cause and resolution:
The root cause of this outage was a typo made in the Html file `/var/www/html/Publics_html` in which the file `/var/www/html/Public_html` was required. The filename of `Publics` was a typographical error, meant to be `Public`, a fatal error was raised, preventing content from being served. Since this code was deployed on all servers, this error caused a 100% outage. A puppet manifest to fix the typographical error was developed and deployed on all servers, reinstating service within 10 minutes of the outage.

## Corrective and preventative measures:
To prevent wide-scale issues like this from occurring in the future, code should never be deployed on all servers before testing. Some things to consider for the future are: the development of company-wide testing protocol, setting up isolated docker containers for testing purposes, and the implementation of a two-person sign-off before major deployment.
