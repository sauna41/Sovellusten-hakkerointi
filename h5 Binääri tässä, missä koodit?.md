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


Nyt ``<=`` aiheutti tilanteen, jossa YLIVUOTO.

Ongelmaan oli yksinkertainen ratkaisu: loin lähdekoodista kopion, jonne korjasin vertailuoperaattorin kuntoon. Lopuksi käänsin lähdekohdin ajettavaksi ohjelmaksi.

    > cp buggy_program.c fixed_program.c
    > micro fixed_program.c   // muokattiin lähdekoodin vertailuoperaattori
    > gcc fixed_program.c -o fixed_program
    
  
<img width="1350" height="373" alt="BUGGY_TULOSTE" src="https://github.com/user-attachments/assets/7799bfcb-0afb-4880-b21f-bea159c5b719" />

<img width="1393" height="352" alt="FIXED_TULOSTE" src="https://github.com/user-attachments/assets/9ae332a4-2608-4a4c-951e-653b3a727345" />



Rikkinäisen _buggy_program_ & korjatun _fixed_program_ tulosteet vierekkäin:
<br>
<img width="1719" height="288" alt="image" src="https://github.com/user-attachments/assets/7b48922a-b8e6-49de-8944-c6db3cd196d4" />



________________________________________________________________________________________________________________________________________________________________________________________

### Lab1.zip - Harjoitellaan tunnilla itsenäisesti. Etsitään, miksi ohjelma kaatuu ja voidaanko se korjata.

________________________________________________________________________________________________________________________________________________________________________________________

### Lab2.zip - kotitehtävä. Ohjelma on käännetty, mutta koodit ovat päässeet katoamaan. Tehtävänä on löytää ohjelman kysymä uusi salasana ja ohjelman tulostama lippu. 


________________________________________________________________________________________________________________________________________________________________________________________

### Kirjoita dokumentti siitä, miten sait nämä selville. Sekä mitä uutta opit GNU Debuggerista, että mitä et oppinut tunnilla

GNU Debugger oli entuudestaan täysin tuntematon, joten kaikki opitut asiat luennolla tulivat uusina. Kun jatkoin harjoitusten suorittamista myöhemmin, opin hyödyntämään luennolla opittuja asioita sekä 
________________________________________________________________________________________________________________________________________________________________________________________

### Lab3.zip - Tiedostossa on Nora Crackme -haasteita. Valitse yksi tiedosto ja yritä ratkaista binäärin salasana. Kirjoita tästä dokumentti, miten sait salasanan selville.


________________________________________________________________________________________________________________________________________________________________________________________

### Lab4.zip - Vapaaehtoinen tehtävä. Ratkaise tämän binäärin salasana ja kirjoita siitä dokumentaatio.


________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet

Iso-Anttila L, Karvinen T. Haaga-Helian Sovellusten hakkerointi ja -haavoittuvuudet kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/application-hacking/. Luettu 17.9.2026.

What is GDB? GDB: The GNU Project Debugger. Luettavissa: https://sourceware.org/gdb/. Luettu 17.9.2026.

GNU GDB Debugger Command Cheat Sheet. YouLinux.com Luettavissa: https://www.yolinux.com/TUTORIALS/GDB-Commands.html. Luettu 17.9.2026.


