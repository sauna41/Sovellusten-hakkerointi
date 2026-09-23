_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) --> GNU Debugger

_Päivämäärä: 17.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on **h5 Binääri tässä, missä koodit?**. Opettajana toimi Lari Iso-Anttila.

________________________________________________________________________________________________________________________________________________________________________________________


## h5 Binääri tässä, missä koodit? (Lari)


### main.cpp


GNU Debugger eli GDB mahdollistaa ohjelman sisälle kurkkaamisen sitä ajattaessa. Sen avulla voidaan tutkia mitä ohjelmassa tapahtuu kun se ajetaan tai missä kohtaa ohjelma kaatuu. DBG:lla voidaan tutkia ohjelmaa, joka pyörii joko natiivisti host-koneella tai toisella koneella (remote). 

Debuggerilla pystyy käynnistämään ohjelman, pysäyttämään sen määritetyillä ehdoilla, analysoimaan mitä tapahtuu tai muuttamaan ohjelmaa. [SourceWare](https://sourceware.org/gdb/)

    
- run
  - Käynnistää ohjelman ajamisen ohjelman alusta
- break
  - Asettaa breakpointin haluttuun kohtaan. Ohjelman ajo pysähtyy merkattuun kohtaan.
        - ``break 10`` riville 10, ```break main``` main-lohkon alkuun
- clear
  - Poistaa tietyn breakpointin
        - ``clear <rivinumero>`` 
- delete
    - Poistaa kaikki asetetut breakpointit
- watch
  - Pysäyttää ohjelman kun määritetty ehto täyttyy
- next
  - Suorittaa seuraavan koodirivin
- continue
  - Ohjelman ajoa jatketaan seuraavaan break- tai watchpointtiin asti

[YouLinux](https://www.yolinux.com/TUTORIALS/GDB-Commands.html)

________________________________________________________________________________________________________________________________________________________________________________________

### Lab0.zip - Harjoitellaan tunnilla itsenäisesti debuggerin käyttöä. Etsitään virhe ja pyritään korjaamaan se

Avasin GDB:n ```layout split``` näkymän, jotta pystyin samanaikaisesti analysoimaan lähdekoodia sekä debuggeria. 

<img width="1563" height="856" alt="LAYOUT SPLIT" src="https://github.com/user-attachments/assets/3c3cc23b-060c-4184-8a7d-ed5fd069ad72" />

Virhe löytyi for-loopin sisältä:

    > for (int i = 0; i <= size; i++) // <= tulisi olla pelkkä <

Taulukon indeksit alkavat nollasta, joten koko taulukon koko on ``size - 1``. <= aiheuttaa sen, että silmukka suoritetaan vielä arvolla i == size, jolloin ohjelma käsittelee taulukon rajojen ulkopuolella olevaa kohtaa. Tämä saattaa johtaa puskuriylivuotoon tai muuhun ei-toivottuun toimintaan. 

#### Ohjelman korjaaminen

Ongelmaan oli yksinkertainen ratkaisu: loin lähdekoodista kopion, jonne korjasin vertailuoperaattorin kuntoon. Lopuksi käänsin lähdekohdin ajettavaksi ohjelmaksi.

    > cp buggy_program.c fixed_program.c    // kopiointi
    > micro fixed_program.c   // muokattiin lähdekoodin vertailuoperaattori
    > gcc fixed_program.c -o fixed_program    // korjatun lähdekoodin kääntäminen uudeksi ohjelmaksi
    
  
<img width="1350" height="373" alt="BUGGY_TULOSTE" src="https://github.com/user-attachments/assets/7799bfcb-0afb-4880-b21f-bea159c5b719" />

<img width="1393" height="352" alt="FIXED_TULOSTE" src="https://github.com/user-attachments/assets/9ae332a4-2608-4a4c-951e-653b3a727345" />



Rikkinäisen _buggy_program_ & korjatun _fixed_program_ tulosteet vierekkäin:
<br>

<img width="1719" height="288" alt="image" src="https://github.com/user-attachments/assets/7b48922a-b8e6-49de-8944-c6db3cd196d4" />



________________________________________________________________________________________________________________________________________________________________________________________

### Lab1.zip - Harjoitellaan tunnilla itsenäisesti. Etsitään, miksi ohjelma kaatuu ja voidaanko se korjata.

    

Avasin jälleen ``gdb ./gdb_example1`` komennolla Debuggerin työhakemistosta ja aloin tutkimaan koodia.

Ohjelma oli jaettu print_scrambled() -funktioon, joka käsittelee merkkijonoa ja main() -funktioon, joka aloittaa ohjelman.

#### main()

- Pääohjelma, jossa luodaan kaksi muuttujaa: _good_message_ "Hello, world" ja _bad_message_ NULL.
- ``char *`` tarkoittaa osoitinta merkkiin
   - _good_message_ kertoo, missä "Hello, world" sijaitsee muistissa
   - _bad_message_ NULL tarkoittaa, että osoitin ei osoita mihinkään kelvolliseen kohtaan

<br>

#### print_scrambled()

- funktio, joka tulostaa joko _good_messagen_ tai _bad_messagen_.
- ``printf("%c2, (*message)+1);`` 
- - _bad_message_ oli NULL, joten sen tulostaminen aiheuttaa ongelman. 


#### GNU debugger tutkiminen

Ensin asetin breakpointin _print_scrambled_ -funktioon ja käynnistin ohjelman.

Ensimmäinen _print_scrambled()_ -kutsu tulosti tulosteen "Hello, world" normaalisti. 

Toinen kutsu ei kuitenkaan tuottanut tulostetta vaan ohjelma kaatui. Tämä johtui siis siitä, että tulostettava _bad_message_ osoittaa NULL-osoitteeseen. do..while -loopin sisällä ohjelma yrittää hakea merkin kohdasta _*message_ mutta _message_ sisältää NULL-arvon. Tämä johtaa siihen, että ohjelma yrittää lukea muistia virheellisestä osoitteesta. Tällöin ohjelma kaatuu ennen kuin toinen viesti ehtii tulostua. 


[good_message](https://github.com/user-attachments/assets/6fb3efc2-ced4-4b28-9bad-15557dcc1dc2)
<br>

<img width="803" height="194" alt="BAD_MESSAGE" src="https://github.com/user-attachments/assets/083f6586-30f9-4852-82c0-9b5ef8650259" />
<br>

#### Korjaaminen

Kun ongelmakohta oli löydetty, se oli helppo korjata. Lisäämällä ehto 

    if (message == NULL) { 
        return; 
    } 
ohjelma tarkastaa alussa, onko sille annettu NULL-osoitin. Jos on, niin funktio lopettaa toimintansa eikä yritä käsitellä epäkelpoa muistiosoitetta.  

________________________________________________________________________________________________________________________________________________________________________________________

### Lab2.zip - kotitehtävä. Ohjelma on käännetty, mutta koodit ovat päässeet katoamaan. Tehtävänä on löytää ohjelman kysymä uusi salasana ja ohjelman tulostama lippu. 

Olen dokumentoinut ratkaisun toisen tehtävän yhteydessä. Linkki dokumentointiin löytyy [täältä](https://github.com/sauna41/Sovellusten-hakkerointi/blob/main/h3%20No%20Strings%20Attached.md#a-strings-download-ezbin-challengeszip-run-passtr-find-the-correct-password-using-strings-also-find-the-flag-preferably-without-looking-at-the-source-if-you-can)
________________________________________________________________________________________________________________________________________________________________________________________

### Kirjoita dokumentti siitä, miten sait nämä selville. Sekä mitä uutta opit GNU Debuggerista, että mitä et oppinut tunnilla

GNU Debugger oli entuudestaan täysin tuntematon, joten kaikki opitut asiat luennolla tulivat uusina. Myös C oli kielenä vieras, joten sen tulkitsiminen vaati ajatustyötä. 

Kun jatkoin harjoitusten suorittamista myöhemmin, opin hyödyntämään luennolla opittuja asioita sekä tutuistin itsenäisesti uusiin komentoihin. 

Ohjelman etenemistä oli helppo seurata "reaaliajassa" Debuggerin avulla. 
________________________________________________________________________________________________________________________________________________________________________________________

### Lab3.zip - Nora Crackme -haasteita

Olen dokumentoinut aiemmin ratkomiani Nora Crackme -haasteita aiemmissa tehtävissä. Ne ovat saatavilla linkin takaa:

[Nora Crackme -haasteita](https://github.com/sauna41/Sovellusten-hakkerointi/blob/main/h4%20Some%20Disassembly%20Required.md#d-nora-crackme-compile-to-binaries-tindall-2023-noracodes--crackmes-read-readmemd-dont-look-at-the-source-code-unless-you-need-training-wheels-in-these-tasks-binaries-are-reverse-engineered-binaries-are-not-modified-because-otherwise-the-solution-to-every-task-would-be-to-change-the-return-value-to-return-0)
________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet

Iso-Anttila L, Karvinen T. Haaga-Helian Sovellusten hakkerointi ja -haavoittuvuudet kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/application-hacking/. Luettu 17.9.2026.

What is GDB? GDB: The GNU Project Debugger. Luettavissa: https://sourceware.org/gdb/. Luettu 17.9.2026.

GNU GDB Debugger Command Cheat Sheet. YouLinux.com Luettavissa: https://www.yolinux.com/TUTORIALS/GDB-Commands.html. Luettu 17.9.2026.


