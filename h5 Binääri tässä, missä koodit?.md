_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)

_Päivämäärä: 17.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on **h5 Binääri tässä, missä koodit?**. Opettajana toimi Tero Karvinen.

________________________________________________________________________________________________________________________________________________________________________________________


## h5 Binääri tässä, missä koodit? (Lari)


### main.cpp - käytiin yhdessä tunnilla läpi GDB:n perusasiat.

________________________________________________________________________________________________________________________________________________________________________________________

### Lab0.zip - Harjoitellaan tunnilla itsenäisesti debuggerin käyttöä. Etsitään virhe ja pyritään korjaamaan se

Avasin GDB:n ```layout split``` näkymän, jotta pystyin samanaikaisesti analysoimaan lähdekoodia sekä debuggeria. 

<img width="1563" height="856" alt="LAYOUT SPLIT" src="https://github.com/user-attachments/assets/3c3cc23b-060c-4184-8a7d-ed5fd069ad72" />

Virhe löytyi for-loopin sisältä:

    for (int i = 0; i <= size; i++) // <= tulisi olla pelkkä <


Nyt ``<=`` aiheutti tilanteen, jossa YLIVUOTO.

Ongelmaan oli yksinkertainen ratkaisu: loin lähdekoodista kopion, jonne korjasin vertailuoperaattorin kuntoon. Lopuksi käänsin lähdekohdin ajettavaksi ohjelmaksi.

    > cp buggy_program.c fixed_program.c
    > micro fixed_program.c   // muokattiin lähdekoodin vertailuoperaattori
    > gcc fixed_program.c -o fixed_program
    
  
<img width="1350" height="373" alt="BUGGY_TULOSTE" src="https://github.com/user-attachments/assets/7799bfcb-0afb-4880-b21f-bea159c5b719" />

<img width="1393" height="352" alt="FIXED_TULOSTE" src="https://github.com/user-attachments/assets/9ae332a4-2608-4a4c-951e-653b3a727345" />



________________________________________________________________________________________________________________________________________________________________________________________

### Lab1.zip - Harjoitellaan tunnilla itsenäisesti. Etsitään, miksi ohjelma kaatuu ja voidaanko se korjata.

________________________________________________________________________________________________________________________________________________________________________________________

### Lab2.zip - kotitehtävä. Ohjelma on käännetty, mutta koodit ovat päässeet katoamaan. Tehtävänä on löytää ohjelman kysymä uusi salasana ja ohjelman tulostama lippu. 


________________________________________________________________________________________________________________________________________________________________________________________

### Kirjoita dokumentti siitä, miten sait nämä selville. Sekä mitä uutta opit GNU Debuggerista, että mitä et oppinut tunnilla

________________________________________________________________________________________________________________________________________________________________________________________

### Lab3.zip - Tiedostossa on Nora Crackme -haasteita. Valitse yksi tiedosto ja yritä ratkaista binäärin salasana. Kirjoita tästä dokumentti, miten sait salasanan selville.


________________________________________________________________________________________________________________________________________________________________________________________

### Lab4.zip - Vapaaehtoinen tehtävä. Ratkaise tämän binäärin salasana ja kirjoita siitä dokumentaatio.


________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet



