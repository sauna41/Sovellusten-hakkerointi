_Kurssi: Sovellusten hakkerointi- ja haavoittuvuudet ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) --> GNU Debugger

_Päivämäärä: 1.10.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi- ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on **H8: Bonus**. Opettajina toimivat Tero Karvinen & Lari Iso-Anttila._

________________________________________________________________________________________________________________________________________________________________________________________


# h8: BONUS

Tähän raporttiin on kerätty kaikki vapaaehtoiset tehtävät kotitehtävistä h1-h7.

________________________________________________________________________________________________________________________________________________________________________________________

## h2

### Solve Portswigger Academy's "Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data".

Labraharjoituksessa oli tehtävä saada näkyviin tuotteita, joita sivuston ei pitäisi näyttää käyttäjälle käyttäen SQL injektiota. Labra kertoi, että SQL-tiedustelu kulki seuraavasti: ``SELECT * FROM products WHERE category = 'Gifts' AND released = 1``.

Kun sivustoa tutki kategorioittain muuttui URL-osoite sen mukaan: esimerkiksi "Gifts" -kategoria tuotti URLiksi _https://0add00950424bc23803c8ac5005d00a9.web-security-academy.net/filter?category=Gifts_. Tällöin esillä oli vain Gifts-kategorian julkaistut tuotteet (3). Nähtävillä oli siis kyseisen kategorian tuotteet, joiden release-status oli 1. 

<img width="1094" height="516" alt="GIFTS" src="https://github.com/user-attachments/assets/2706b57a-da6c-4426-b889-16ea66b1e2f3" />

_gifts-kategorian julkaistut tuotteet_
<br>


SQL-injektio suoritettiin juurikin URL-kenttään: muokattiin URLin ``category=Gifts`` osa muotoon ``category=' OR 1=1--``
- hipsukka (') sulkee alkuperäisen merkkijonon
- ``OR 1=1`` lisää ehdon, joka on aina tosi (1 on aina 1)
- kaksi viivaa ``--`` aloittaa SQL-kommentin --> ``AND RELEASED = 1`` muuttuu kommentiksi eikä ehtoa tarkasteta

Injektio johtaa siihen, että _kaikki_ tuotteet täyttävät WHERE-ehdon: 

    category = ''  → FALSE
    1=1             → TRUE
    
    FALSE OR TRUE → TRUE


<img width="715" height="1097" alt="ALL GIFTS" src="https://github.com/user-attachments/assets/42450dda-ab60-41af-9883-50439dbdd9b2" />

_kaikki tuotteet näkyvissä_
<br>

________________________________________________________________________________________________________________________________________________________________________________________


### Solve Portswigger Academy's "Lab: SQL injection vulnerability allowing login bypass"

Toisessa PortSwigger harjoituksessa haavoittuvuus sijaitsi kirjautumislomakkeessa. Tehtävänä oli kirjautua sisään _administrator_ -käyttäjänä sisään. 

Login-lomake löytyi "Login" klikkauksen päästä. Todennäköisesti SQL-kysely oli suurinpiirtein mallia: **SELECT * FROM users WHERE username = 'administrator' AND password = 'password'**


SQL-injektio syötettiin lomakkeeseen samalla ajatuksella kuin aiemmassa tehtävässä: kommentoitiin kyselystä ei-toivottuja osia pois. 
- Käyttäjänimeksi asetettiin _administrator_
- kommentoitiin salasana kysely pois kommenttimuotoon ``--`` --> tietokanta ei käsittele sitä ehtona
    - salasanakenttään pystyi täten syöttämään mitä vain, sillä se oli enää pelkkä kommentti

<img width="702" height="310" alt="LOGIN" src="https://github.com/user-attachments/assets/6f003a28-33b8-4b0e-81eb-568a239fa19b" />

_sql injektio lomakkeessa_
<br>

SQL-injektio syötettiin sisään kun selain lähetti kirjautumispyynnön HTTPS:n kautta palvelimelle ja palvelin käsitteli syötteen osana SQL-kyselyä. Tällöin SQL-käsittely näki vain, että käyttäjä on _administrator_ eikä salasanaehtoa ollut enää, sillä se oli muutettu kommentiksi.


<img width="628" height="417" alt="logged in" src="https://github.com/user-attachments/assets/ddcad490-5a76-4a6d-9199-5b8102d0483f" />




________________________________________________________________________________________________________________________________________________________________________________________

### h3 No Strings Attached 
Optional bonus: Cryptopals. Crypto Challenge Set 1. This can be done as a bonus over several weeks. If you solve items 1 .. "4. Detect single-character XOR", you've already stepped into the world of cryptography.


________________________________________________________________________________________________________________________________________________________________________________________

### h4 Some Disassembly Required


Optional: And beyond. Crackme01 has multiple solutions. How many can you find? Why?
h) Optional: Unsolicited. Crackme02 has two solutions. Can you find both?
i) Optional, slightly more challenging: A ray. Nora crackme02e. Solve the binary.

________________________________________________________________________________________________________________________________________________________________________________________


