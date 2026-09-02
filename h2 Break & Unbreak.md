_Kurssi: Sovellusten hakkerointi ja haavoittuvuudet ICI012AS3A-3004_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 27.8.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on h2 Break & Unbreak. Opettajana toimivat Tero Karvinen_

________________________________________________________________________________________________________________________________________________________________________________________

### x) Read/watch/listen and summarize

OWASP: OWASP Top 10: A01 Broken Access Control

Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf

PortSwigger: Access control vulnerabilities and privilege escalation

Karvinen 2006: Report Writing (in Finnish)

________________________________________________________________________________________________________________________________________________________________________________________

### a) Break into 010-staff-only. See Karvinen 2024: Hack'n Fix

Latasin Tero Karvisen [Hack'n Fix](https://terokarvinen.com/hack-n-fix/) sivun ohjeiden mukaisesti _teros-challenge.zip_ -tiedoston ja purin sen. Sisältönä oli erilaisia haasteita. 


Käynnistin ohjelman ```python3 staff-only.py``` komennolla, jonka tulosteesta selvisi IP-osoite. 

<img width="724" height="179" alt="image" src="https://github.com/user-attachments/assets/aa57856e-8fca-4893-bef2-3d773522080b" />


Navigoin Firefoxin verkkoselaimessa saatuun IP-osoitteeseen ja pääsin kirjautumissivulle. Syöttämällä tekstikenttään kerrottu PIN-koodi 123, sivusto generoi salasanan _Somedude_ ja kaikki muut kokeilemani numeroyhdistelmät palauttivat _(not found)_. Jos kenttään yritti syöttää muuta kun numeroita, virheilmoitus pyysi käyttämään vain numeroita. 


<img width="745" height="394" alt="image" src="https://github.com/user-attachments/assets/4141fe04-00f0-4293-a03d-a89c1dcbafe0" />

Avasin viereen toisen terminaalin ja tarkastelin sivustoa sieltä. Ajoin seuraavaksi ```curl -X POST http://127.0.0.1:5000/ -d "pin=' OR '1'='1"```, joka syötti SQL-injektion palvelimelle. Injektio toteutti SQL-kyselyn mallia ```SELECT password FROM pins WHERE pin='' OR pin='11112222333' -- ';```, joka palautti admin-salasanan:

**SUPERADMIN%%rootALL-FLAG{Tero-e45f8764675e4463db969473b6d0fcdd}**

<img width="757" height="307" alt="image" src="https://github.com/user-attachments/assets/1663aadd-a319-4b35-becc-944b8d448aef" />

________________________________________________________________________________________________________________________________________________________________________________________

### b) Fix the 010-staff-only vulnerability from source code

Loin uuden kopion staff-only.py -tiedostosta ja lähdin tarkastelemaan lähdekoodia komennolla ```micro FIX-staff-only.py```. 

Muokkasin lähdekoodista KOHTAA, joka muokkasi nyt käyttäjän syöttämää arvoa käsiteltäväksi arvona eikä osana SQL-koodia. 

<img width="578" height="130" alt="image" src="https://github.com/user-attachments/assets/faf84b9a-a985-4c5a-b74e-f54ab512b856" />


Käynnistin sovelluksen uudelleen ja kokeilin ajaa aiemman SQL-haavoittuvuuden uudelleen. Muokkauksien jälkeen tulos oli kuitenkin _"Your password | SUPERADMIN | not found"_


<img width="830" height="190" alt="image" src="https://github.com/user-attachments/assets/1c1d340e-e6b8-4f32-9f50-91782d91c600" />

________________________________________________________________________________________________________________________________________________________________________________________

### c) Solve dirfuzt-1 from the article Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf

Latasin taas Tero Karvisen [Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/) sivulta ohjeiden mukaisesti dirfuzt-0 -tiedoston. Aloitin ensimmäisenä lataamalla ffufin ```sudo apt-get install ffuf``` ja navigoimalla dirfuztin sivustolle.

Seuraavaksi latasin kattavan sanalistan ```wget https://raw.githubusercontent.com/danielmiessler/SecLists/master/Discovery/Web-Content/common.txt```, jonka ajoin ffufilla läpi ```ffuf -w common.txt -u http://127.0.0.2:8000/FUZZ```. ffuf kävi läpi erittäin pitkän sanalistan, joissa kaikissa oli sama tuloste. Tämä ei luonnollisestikaan vähentänyt mahdollisia vaihtoehtoja, joten sorttasin tulosteesta saatujen tietojen perusteella vain ne tulosteet, jotka olivat suurempia kuin 132. 

Tämä palauttikin vaan enää yhden tuloksen: adminin. Lisäämällä _/admin_ URLin perään, löydettiin ratkaisu. 


<img width="347" height="131" alt="image" src="https://github.com/user-attachments/assets/48f5788a-e471-4b76-8914-770231e08b53" />

________________________________________________________________________________________________________________________________________________________________________________________

### d) Break into 020-your-eyes-only. See Karvinen 2024: Hack'n Fix




________________________________________________________________________________________________________________________________________________________________________________________

### e) Fix the 020-your-eyes-only vulnerability. Demonstrate with a test that your solution works.

________________________________________________________________________________________________________________________________________________________________________________________

g) Optional. Introductory exercise that helps solve 010-staff-only. Solve Portswigger Academy's "Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data".
h) Optional. Introductory exercise that helps solve 010-staff-only. Solve Portswigger Academy's "Lab: SQL injection vulnerability allowing login bypass"

________________________________________________________________________________________________________________________________________________________________________________________


#### Lähteet:

Karvinen, T. Sovellusten hakkerointi kurssimateriaali. Luettavissa: https://terokarvinen.com/application-hacking/#homework. Luettu 27.8.2026.

Karvinen, T. Hack'n Fix. 2026. Luettavissa: https://terokarvinen.com/hack-n-fix/. Luettu 27.8.2026.

Karvinen, T. Find Hidden Web Directories - Fuzz URLs with ffuf. 2026. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/. Luettu 27.8.2026.


