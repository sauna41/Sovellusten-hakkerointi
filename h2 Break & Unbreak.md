_Kurssi: Sovellusten hakkerointi ja haavoittuvuudet ICI012AS3A-3004_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 27.8.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on h1 Break & Unbreak. Opettajana toimivat Tero Karvinen_

________________________________________________________________________________________________________________________________________________________________________________________



Remember systematic working methods and report as you go. Also reflect: Where could this vulnerability be common? How could this mistake be avoided? What did I learn from this?

x) Read/watch/listen and summarize. (In this x-subsection, you don't need to do tests on a computer; just reading or listening and a summary is enough. A few bullet points are sufficient for the summary.)
OWASP: OWASP Top 10: A01 Broken Access Control
Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf
PortSwigger: Access control vulnerabilities and privilege escalation
Karvinen 2006: Report Writing (in Finnish)
Optional: PortSwigger 2020: What is SQL injection? - Web Security Academy (about 10 min video)


________________________________________________________________________________________________________________________________________________________________________________________

a) Break into 010-staff-only. See Karvinen 2024: Hack'n Fix

________________________________________________________________________________________________________________________________________________________________________________________

b) Fix the 010-staff-only vulnerability from source code. Demonstrate with a test that your solution works.

________________________________________________________________________________________________________________________________________________________________________________________

c) Solve dirfuzt-1 from the article Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf. This helps in solving 020-your-eyes-only.

________________________________________________________________________________________________________________________________________________________________________________________

d) Break into 020-your-eyes-only. See Karvinen 2024: Hack'n Fix


________________________________________________________________________________________________________________________________________________________________________________________

e) Fix the 020-your-eyes-only vulnerability. Demonstrate with a test that your solution works.

________________________________________________________________________________________________________________________________________________________________________________________

g) Optional. Introductory exercise that helps solve 010-staff-only. Solve Portswigger Academy's "Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data".
h) Optional. Introductory exercise that helps solve 010-staff-only. Solve Portswigger Academy's "Lab: SQL injection vulnerability allowing login bypass"
