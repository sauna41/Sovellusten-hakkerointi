_Kurssi: Sovellusten hakkerointi- ja haavoittuvuudet ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) --> GNU Debugger

_Päivämäärä: 1.10.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi- ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on **H8: Bonus**. Opettajina toimivat Tero Karvinen & Lari Iso-Anttila._

________________________________________________________________________________________________________________________________________________________________________________________

### h2

#### Solve Portswigger Academy's "Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data".

Labraharjoituksessa oli tehtävä saada näkyviin tuotteita, joita sivuston ei pitäisi näyttää käyttäjälle käyttäen SQL injektiota. Labra kertoi, että SQL-tiedustelu kulki seuraavasti: ``SELECT * FROM products WHERE category = 'Gifts' AND released = 1``.

Kun sivustoa tutki kategorioittain muuttui URL-osoite sen mukaan: esimerkiksi "Gifts" -kategoria tuotti URLiksi _https://0add00950424bc23803c8ac5005d00a9.web-security-academy.net/filter?category=Gifts_. Tällöin esillä oli vain Gifts-kategorian julkaistut tuotteet (3). Nähtävillä oli siis kyseisen kategorian tuotteet, joiden release-status oli 1. 

<img width="1094" height="516" alt="GIFTS" src="https://github.com/user-attachments/assets/2706b57a-da6c-4426-b889-16ea66b1e2f3" />
<br>


SQL-injektio suoritettiin juurikin URL-kenttään: muokattiin URLin ``category=Gifts`` osa muotoon ``category=' OR 1=1--``. 
- hipsukka (') sulkee alkuperäisen merkkijonon
- ``OR 1=1`` lisää ehdon, joka on aina tosi (1 on aina 1)
- kaksi viivaa ``--`` aloittaa SQL-kommentin --> ``AND RELEASED = 1`` muuttuu kommentiksi eikä ehtoa tarkasteta

Injektio johtaa siihen, että _kaikki_ tuotteet täyttävät WHERE-ehdon: 

    category = ''  → FALSE
    1=1             → TRUE
    
    FALSE OR TRUE → TRUE


<img width="715" height="1097" alt="ALL GIFTS" src="https://github.com/user-attachments/assets/42450dda-ab60-41af-9883-50439dbdd9b2" />


h) Optional. Introductory exercise that helps solve 010-staff-only. Solve Portswigger Academy's "Lab: SQL injection vulnerability allowing login bypass"

________________________________________________________________________________________________________________________________________________________________________________________

### h3 No Strings Attached 
Optional bonus: Cryptopals. Crypto Challenge Set 1. This can be done as a bonus over several weeks. If you solve items 1 .. "4. Detect single-character XOR", you've already stepped into the world of cryptography.


________________________________________________________________________________________________________________________________________________________________________________________

### h4 Some Disassembly Required


Optional: And beyond. Crackme01 has multiple solutions. How many can you find? Why?
h) Optional: Unsolicited. Crackme02 has two solutions. Can you find both?
i) Optional, slightly more challenging: A ray. Nora crackme02e. Solve the binary.

________________________________________________________________________________________________________________________________________________________________________________________


