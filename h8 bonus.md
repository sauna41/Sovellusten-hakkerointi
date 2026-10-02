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

## h4 Some Disassembly Required

_h4: [Some Disassembly Required -raportissani](https://github.com/sauna41/Sovellusten-hakkerointi/blob/main/h4%20Some%20Disassembly%20Required.md) on aiempaa dokumentointia crackme01 & crackme02 haasteista_


### And beyond. Crackme01 has multiple solutions. How many can you find? Why?

Tehtävänanto vihjaa, että jostain syystä ratkaisuja on useita. Tutkin ohjelmaa tarkemmin Ghidrassa. Merkkijonon vertailu tapahtui seuraavalla tavalla:


<img width="823" height="761" alt="STRCMP 9" src="https://github.com/user-attachments/assets/8f244ce1-a11f-41bf-bf86-91caa88c1573" />

_Vertailu ghidrassa_

Pseudokoodi analysoituna:

    iVar1 = strncmp(__s1,"password1",9);    // lue käyttäjän syöte, vertaa "password1" merkkijonon ensimmäiseen yhdeksään merkkiin
    if (iVar1 == 0) {    // jos salasanasyöte palauttaa 0
      printf("Yes, %s is correct!\n",__s1);    // syötetty salasana oli oikein

Ohjelman virhe on kohdassa ``strncmp(__s1,"password1",9);``: se tarkistaa, onko käyttäjän syötteessä oikean salasanan "_password1_" ensimmäiset yhdeksän merkkiä, ei salasanan pituutta. Jos tämä on totta, siirrytään tulostamaan, että salasana oikein. Se ei kuitenkaan tarkista mahdollisia merkkejä yhdeksännen jälkeen. Tämä johtaa tilanteeseen, jossa käyttäjä voi syöttää rajattomasti merkkejä sillä loppuosaa ei tarkasteta ollenkaan:


<img width="759" height="228" alt=">9 MERKKIÄ PASSU" src="https://github.com/user-attachments/assets/18ca36e5-5e1f-43dc-bce5-0b1be441e0da" />
_Rajattomasti oikeita salasanoja_

"Oikeita" salasanoja on siis käytännössä rajaton määrä.

<br>
<br>


### Optional: Unsolicited. Crackme02 has two solutions. Can you find both?

Löysin haasteeseen yhden ratkaisun aiemmassa dokumentaatiossani ja nyt oli aika etsiä se toinen.

Jälleen pseudokoodi auki ghidrassa ja tutkimaan. Tässä vaiheessa ghidra ja sen esittämä koodi oli jo jokseenkin tutumpaa, joten pseudokoodia oli helpompi analysoida. Melko nopeasti havaitsin kiinnostavia rivejä:

<img width="513" height="712" alt="image" src="https://github.com/user-attachments/assets/37bf7c27-c094-41fb-bc2c-f21a9c02566d" />

_ghidran pseudokoodia_
<br>


Huomionarvoisia olivat seuraavat rivit:

        if (*pcVar4 == '\0') break;    // jos syöte on tyhjä, silmukka keskeytyy ennen yhdenkään merkin vertailua ja ohjelma jatkaa eteenpäin palauttaen 0. 
        
                    ↓    
                    
    printf("Yes, %s is correct!\n",pcVar1);    // tulosta käyttäjän syöte ja kerro salasanan olevan oikein


Tämä tarkoitti, että jos käyttäjä syöttää tyhjän merkkijonon ``""``, silmukka katkeaa ja hyppää eteenpäin tulostukseen:


<img width="539" height="79" alt="TYHJÄ VASTAUS" src="https://github.com/user-attachments/assets/65744bd9-8405-4565-b10f-e4081b2e0fab" />
<br>


________________________________________________________________________________________________________________________________________________________________________________________


### slightly more challenging: A ray. Nora crackme02e. Solve the binary.

Ghidraan auki: 

<img width="592" height="713" alt="PSEUDO MAIN" src="https://github.com/user-attachments/assets/8e0e60d4-5ec5-4fdf-b5b8-cdeca3059a2c" />

_main-lohko ghidrassa_
<br>

Pseukoodin analysointi:

     undefined8 main(int param_1,long param_2)    // pääfunktio: param_1 on annettujen argumenttien määrä, long_param2 sisältää argumentit

    {
      char *pcVar1;    // käyttäjän syöte
      char cVar2;    // odotettu merkki
      undefined8 uVar3;    // lopullinen arvo
      char *pcVar4;    // osoittaa käyttäjän syötteen nykyiseen merkkiin
      char *pcVar5;    // osoittaa vertailun nykyisen merkkiin
      
      if (param_1 == 2) {    // tarkastus, että käyttäjä syöttää jotain
        pcVar1 = *(char **)(param_2 + 8);    // haetaan käyttäjän ensimmäinen argumentti
        pcVar5 = "uvmnpoi";    // asetetaan osoitin merkkijonoon "uvmnpoi"
        cVar2 = 'y';    // ensimmäinen odotettu vertailumerkki on "y"
        pcVar4 = pcVar1;    // osoitin käyttäjän ensimmäiseen merkkiin
        do {    // silmukan käynnistys
          if (*pcVar4 == '\0') break;    // tarkistetaan, että syöte on loppunut
          if (cVar2 + -2 != (int)*pcVar4) {    // otetaan odotettu merkki (y) ja vähennetään sen ASCII-arvosta 2 (w)
            printf("No, %s is not correct.\n",pcVar1);    // jos väärä syöte, tulostetaan
            return 1;    // palautetaan 1
          }
          cVar2 = *pcVar5;    // jos ensimmäinen merkki on oikea, cVar2:ksi otetaan pcVar5 arvo
          pcVar4 = pcVar4 + 1;    // siirrytään käyttäjän syötettä eteenpäin
          pcVar5 = pcVar5 + 1;    // siirrytään vertailumerkissä eteenpäin
        } while (cVar2 != '\0');    // jatketaan niin kauan että syöte on käsitelty
        printf("Yes, %s is correct!\n",pcVar1);    // tulostetaan, että syöte on oikein
        uVar3 = 0;    // palautusarvo 0
      }
      else { 
        puts("Need exactly one argument.");    // jos argumentteja != 1, virheilmoitus
        uVar3 = 0xffffffff;
      }
      return uVar3;    // palautetaan lopullinen exit-status


Ohjelman logiikka on siis vähentää jokaisesta odotetusta merkistä ASCII-arvo 2. Ohjelma käy näin kaikki syötteen merkit salasanan pituudelta. Ghidrasta löytyi merkkijono "uvmnpoi", josta oli helppo laskea ASCII-arvoja:

    y - 2 = w
    u - 2 = s
    v - 2 = t
    m - 2 = k
    n - 2 = l
    p - 2 = n
    o - 2 = m
    i - 2 = g

= **wstklnmg**

<img width="547" height="78" alt="password found" src="https://github.com/user-attachments/assets/1c82fa5f-10b3-4e69-9e8a-e77294edbe80" />

_salasana löytyi_
<br>

Myös "tyhjä" syöte käyttäjältä toimi, samalla logiikalla kuin edellisessä tehtävässä.

<img width="412" height="82" alt="image" src="https://github.com/user-attachments/assets/bf02c5a2-d930-4399-b4ef-24ba6ee0c35e" />
<br>


________________________________________________________________________________________________________________________________________________________________________________________

## h5 Binääri tässä, missä koodit? 


### Lab4.zip - Ratkaise tämän binäärin salasana ja kirjoita siitä dokumentaatio


Lab4.zip saatiin ladattua kurssimateriaaleista ja purettua ``unzip lab4.zip`` komennolla. Tarkastelin ohjelmaa ensin "ulkoapäin":

    file crackme
    strings crackme

Salasanaa ei vielä näillä kräkätty mutta ``strings crackme | grep check`` tuotti kuitenkin tulosta: "_GLOBAL__sub_I__Z13checkPasswordNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEE". Tämä paljasti, että ohjelmasta löytyy checkPassord -symboli, mikä viittasi siihen, että ohjelmasta löytyisi salasanaa tarkistava funktio. Lähdin tämän perusteella etsimään lisää tietoa Ghidrasta.

<img width="933" height="77" alt="GREPPAUS" src="https://github.com/user-attachments/assets/64b35ead-a9e7-44e6-b2dd-c645a62fd626" />

_checkPassword löytö_
<br>


#### Ghidra analyysi

main-lohkon sisältä löytyi pseudokoodia, jonka sisältöä en osannut vielä tulkita sen syvellisemmin.

<img width="695" height="461" alt="image" src="https://github.com/user-attachments/assets/5bd101a3-5fd2-4ab0-9e01-470ec415e797" />

_main-lohko & checkPassword_
<br>

Tuttu ``checkPassword`` kuitenkin löytyi, joten siirryin sen osoitteeseen tutkimaan tarkemmin. Siirtyminen kyseiseen funktioon kävi helposti tuplaklikkaamalla.


<img width="693" height="587" alt="checkPasswordPSEUDO" src="https://github.com/user-attachments/assets/9b4b55de-42ad-47ca-aa77-e730d3f2ac7f" /> 

_checkPassword funktion pseudokoodi_
<br>

Pseudokoodin tulkitseminen oli huomattavasti haastavampaa kuin aiemmissa tehtävissä. Selkeimmät osuudet olivat merkkijonot "dec" riviltä 21 sekä merkkijonot "k" ja "car" riveiltä 30 & 31.

- Rivillä 21 "dec" merkkijono lisättiin vertailujonoon ensimmäisenä.
- Rivien 30 ja 31 ``operator+=(local_68,"k")`` ja ``operator+=(local_68,"car");`` operaattorit selkeästi lisäsivät merkkejä (k ja car) 

Nämä olivat oikeastaan ainoat ymmärtämäni vaiheet, mutta niillä pääsi alkuun. Kokeilin syöttää näiden yhdistelmää salasanaksi ohjelmaan:


<img width="779" height="229" alt="yrityksiä" src="https://github.com/user-attachments/assets/ddae5c79-af2b-4fb0-929a-b8c03fb3005a" />

_hakuammunta yrityksiä_
<br>

Ghidraa hetken tuijoteltuani ja ``std::`` merkintöjä seuratessa huomasin myös ``std::reverse<>`` osuuden. Reverse kääntää merkkijonon käänteiseen järjestykseen, jolloin **deccark = cracked**. Tämä tuskin oli sattumaa, joten kokeilin seuraavaksi _cracked_ -salasanaa.


<img width="829" height="114" alt="CRACKED" src="https://github.com/user-attachments/assets/89b2d3b7-720b-47a7-bb9c-13129207f1c5" />

_Login successful_
<br>


________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet











