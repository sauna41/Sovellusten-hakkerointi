_Kurssi: Sovellusten hakkerointi ja haavoittuvuudet ICI012AS3A-3004_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 27.8.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on h2 Break & Unbreak. Opettajana toimivat Tero Karvinen_

________________________________________________________________________________________________________________________________________________________________________________________

### x) Read/watch/listen and summarize

[OWASP: OWASP Top 10: A01 Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)

- Broken Access Control on tilanne, jossa hyökkääjien on mahdollista saada pääsy tietoon tai toimintoihin joihin ei pitäisi olla oikeutta.
- Yleisiä haavoittuvuksia ovat esimerkiksi:
  - admin oikeuksiin käsiksi pääsy
  - URL-osoitteiden manipulointi
  - autentikoiduille sivuille pääseminen ilman riittävää autentikointia
- Käyttöoikeuksia voi muokata HTML puolella, joten niiden tarkastaminen palvelinpuolella on turvallisempaa
- deny by default on turvallinen oletus. Tällöin pääsy on kielletty, jollei tosin todisteta.

[Karvinen 2023: Find Hidden Web Directories - Fuzz URLs with ffuf](https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/)

- Palvelimilla voi olla piilotettuja sivuja, joihin ei ole selkeitä näkyviä viittauksia. Niitä voidaan kuitenkin etsiä fuzzaamalla
- ffuf on nopea verkkofuzzeri joka tarvitsee vain sanalistan toimiakseen
- Tuloksia voidaan suodattaa erilaisin parametrein, kuten koon, rivimäärän tai sanamäärän mukaan.
- _FUZZ_ toimii paikkamerkkinä, jonka ffuf korvaa hakusanalla

[PortSwigger: Access control vulnerabilities and privilege escalation](https://portswigger.net/web-security/access-control)

- Access Control määrittää mitä käyttäjä saa tehdä ja mitä ei
- Authentication varmistaa henkilöllisyyden
-  Priviledge Escalationit tarkoittavat tilannetta, jossa käyttäjä pääsee käsiksi oikeuksiin joita tälle ei kuuluisi
-  Käyttöoikeuksia voidaan kiertää esimerkiksi URL:n ja HTTP pyyntöjen avulla
-  Käyttöoikeudet tulisi tarkistaa palvelimelta, sillä käyttäjä voi HTML:n kautta kiertää niitä
-  Käyttöoikeudet tulisi aina testata kattavasti

[Karvinen 2006: Report Writing (in Finnish)](https://terokarvinen.com/2006/raportin-kirjoittaminen-4/)

Hyvö raportti on

- Toistettava: Toisen opiskelijan pitäisi päästä samaan lopputulemaan raportin pohjalta.
- Täsmällinen: Mitä komentoja käytettiin, mitä ne tekivät, tuliko virheilmoituksia?
- Helppolukuinen: Väliotsikot, sopiva kielioppi
- Lähteet: lähdeviitteet ja lähdeluettelo saatavilla
- Mokia tai kiellettyä on asioiden keksiminen päästä ja plagiointi niin tekstin kuin kuvien suhteen

________________________________________________________________________________________________________________________________________________________________________________________

### a) Break into 010-staff-only. See Karvinen 2024: Hack'n Fix

Latasin Tero Karvisen [Hack'n Fix](https://terokarvinen.com/hack-n-fix/) sivun ohjeiden mukaisesti _teros-challenge.zip_ -tiedoston ja purin sen. Sieltä löytyi 010-staff-only -tiedosto, jonka tavoitteena oli saada tietoon admin-käyttäjän salasana, joka sisälsi merkkijonon **SUPERADMIN.** 

Käynnistin ohjelman ```python3 staff-only.py``` komennolla, jonka tulosteesta selvisi IP-osoite. 

<img width="724" height="179" alt="image" src="https://github.com/user-attachments/assets/aa57856e-8fca-4893-bef2-3d773522080b" />

<br/><br/>

Navigoin Firefoxin verkkoselaimessa saatuun IP-osoitteeseen ja pääsin kirjautumissivulle. Syöttämällä tekstikenttään sivulla kerrottu PIN-koodi 123, sivusto generoi salasanan _Somedude_. 

<img width="745" height="394" alt="image" src="https://github.com/user-attachments/assets/4141fe04-00f0-4293-a03d-a89c1dcbafe0" />

<br/><br/>

Kaikki muut kokeilemani numeroyhdistelmät palauttivat _(not found)_. Jos kenttään yritti syöttää muuta kun numeroita, virheilmoitus pyysi käyttämään vain numeroita. SQL-injektio suoraan lomakkeeseen ei täten tuntunut onnistuvan. Tarkastelin HTML-sivustoa tarkemmin dev-toolsseilla ja havaitsin, että lomakkeen HTML-elementti oli mallia _input type="number"_, joka pakotti syötteen numeroiksi. Muokkaamalla tämän tekstiksi, kykenin syöttämään SQL-injektion suoraan lomakkeeseen. Syöteellä ```' OR 1=1; --``` onnistuin saamaan esiin _foo_ salasanan. Tämä oli edistystä mutta ei kuitenkaan vielä haettu admin-salasana. 

Tutkin eri lähteitä SQL-injektioista mutta en keksinyt miten saisin edettyä tehtävässä. Sain Karvisen sivulta vinkin, että LIMIT -parametrilla saisin rajattua tuloksia, joten tutustuin LIMITiin [W3 Schoolin](https://www.w3schools.com/sql/sql_top.asp) kautta ja päädyin lopulta kokeilemaan summamutikassa eri arvoja. Lopulta ```' OR 1=1 LIMIT 2,1; --``` toimi ja sain pääsyn admin-salasanaan.





<img width="975" height="585" alt="image" src="https://github.com/user-attachments/assets/6d6bde6f-35db-4615-b1c1-6f97e52973ba" />

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

### d) Break into 020-your-eyes-only

Seuraamalla jälleen [Karvisen sivulta](https://terokarvinen.com/hack-n-fix/) löytyviä ohjeita, aloitin luomalla virtuaaliympäristön _virtualenv_. 


Päästiin sivulle:

<img width="959" height="288" alt="image" src="https://github.com/user-attachments/assets/93875d9e-c8f2-4478-b076-9c22b570ad76" />

Yritin luoda uuden käyttäjän mutta jostain syystä sivusto palautti minut aina vaan "Welcome back" näkymään eikä kirjautuminen onnistunut. Syväluotaavaan tutkimisen jälkeen selvisi, että koska selaimessani oli dark mode päällä, en nähnyt rekisteröitymissivun tekstiä, mikä kertoi, ettei käyttäjätunnukseni/salasani olleet sopivat... Mitä tästä opittiin? Jos sivusto näyttää heti alkuun rikkinäiseltä, kannattaa tutkailla käyttöliittymää / CSS:ää ennen kuin siirtyy backendin puolelle.

<br/><br/>

<img width="549" height="720" alt="image" src="https://github.com/user-attachments/assets/cbefd76b-bb36-4d57-bff0-38f39c6e0705" />

<br/><br/>

Kun vihdoin sain luotua uuden käyttäjän kokeilin klikkailla eri linkkejä sivustolla. Pääsin tarkastelemaan omaa dataa mutta _Admin dashboard_ oli 403 forbidden. Ffuffasin sivun, joka palautti yhden tuloksen: _admin-console._ Kokeilin sisäänkirjautuneena lisätä tämän URLiin, joka johdatti salaiselle Admin sivustolle ja tehtävä oli ratkaistu.


<img width="983" height="283" alt="image" src="https://github.com/user-attachments/assets/0fa5086f-6090-4595-93e8-7bf5869bdb06" />

________________________________________________________________________________________________________________________________________________________________________________________


### e) Fix the 020-your-eyes-only vulnerability

Lähdin tutkimaan ensin mistä päin lähdekoodia löytäisin haavoittuvuuden. Tiesin suunnilleen mitä etsin, joten oikea tiedosto löytyi suht nopeasti. Avasin halutun tiedoston microlla ```micro views.py```. Sieltä löysin, että _AdminShowAllView_ näytti admin konsolin kunhan käyttäjä oli olemassa. Muokkasin riviä lisäämällä lauseen "and self.request.user.is_staff". Tämä lisäsi ehdoksi, että käyttäjän tulee olla myös admin, jotta konsolin saa esiin. Tässä kohtaa käytin oikean koodin luomiseen ChatGPT:tä promptaamalla: "Korjaa rivi niin, että tavallinen käyttäjä ei saa näkymää esiin".


<img width="817" height="103" alt="image" src="https://github.com/user-attachments/assets/db6134a1-2135-46f5-8de7-555fae0c4914" />


Varmistin asian vielä navigoimalla uudelleen admin-console -sivulle. Tällä kertaa pääsy sinne oli kuitenkin estetty ja tuloksena oli _403 Forbidden_

<img width="743" height="128" alt="image" src="https://github.com/user-attachments/assets/05a4ee5d-1287-46f0-b215-ca79614d5157" />


________________________________________________________________________________________________________________________________________________________________________________________



#### Lähteet:

Karvinen, T. Sovellusten hakkerointi kurssimateriaali. Luettavissa: https://terokarvinen.com/application-hacking/#homework. Luettu 27.8.2026.

Karvinen, T. Hack'n Fix. 2024. Luettavissa: https://terokarvinen.com/hack-n-fix/. Luettu 27.8.2026.

Karvinen, T. Find Hidden Web Directories - Fuzz URLs with ffuf. 2023. Luettavissa: https://terokarvinen.com/2023/fuzz-urls-find-hidden-directories/. Luettu 27.8.2026.

Karvinen, T. Raportin kirjoittaminen. 2006. Luettavissa: https://terokarvinen.com/2006/raportin-kirjoittaminen-4/. Luettu 27.8.2026.

ChatGPT. Käytettävissä: chatgpt.com. Käytetty 27.8.2026.

SQL SELECT TOP, LIMIT and FETCH FIRST. W3 Schools. https://www.w3schools.com/sql/sql_top.asp. Luettu 27.8.2026.


