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

C-kieli ei ollut itselleni vahvin ohjelmointikieli, joten lähdin ensin selvittämään rivi riviltä selvittämään mitä ohjelma tekee

    #include "stdio.h"    // stdio-kirjasto, joka sisältää C-työkaluja kuten printf-funktion

    void print_scrambled(char *message)    // funktio, joka saa parametrina osoittimen merkkijonon
    {
      register int i = 3;    // muuttujan i arvoksi asetetaan kokonaisluku 3
      do {    // silmukka
        printf("%c", (*message)+i);     // tulostetaan nykyinen merkki ja lisätään siihen muuttuja i (3)
      } while (*++message);    // siirrytään seuraavaan merkkiin kunnes \0
      printf("\n");
    }
    
    int main()    // ohjelman suoritus
    {
      char * bad_message = NULL;    // osoitin, joka ei osoita kelpoon muistiosoitteeseen
      char * good_message = "Hello, world.";    // osoitin merkkijonoon "Hello, world"
    
      print_scrambled(good_message);    // tulostetaan good_message ("Hello, world")
      print_scrambled(bad_message);    // yritetään käsitellä NULL-osoitinta = virhe
    }

Ohjelma oli jaettu print_scrambled() -funktioon, joka käsittelee merkkijonoa ja main() -funktioon, joka aloittaa ohjelman.

#### main()

- Pääohjelma, jossa luodaan kaksi muuttujaa: _good_message_ "Hello, world" ja _bad_message_ NULL.
- ``char *`` tarkoittaa osoitinta merkkiin
   - _good_message_ kertoo, missä "Hello, world" sijaitsee muistissa
   - _bad_message_ NULL johtaa siihen, että osoitin ei osoita mihinkään kelvolliseen kohtaan

<br>

#### print_scrambled()

- funktio käsittelee sille annettua merkkiä yksi kerrallaan
- ``printf("%c2, (*message)+1);`` hakee _message_ -osoittimen merkin ja lisää sen arvoon muuttujan **i** arvon (3)
- do...while -silmukka jatkaa seuraavaan merkkiin aina niin kauan kunnes merkkijono loppuu (\0)

Ongelma syntyy siis silloin, kun funktiolle annetaan _bad_message_ joka on NULL. NULL ei osoita mihinkään muistiosoitteeseen, joten kun ohjelma yrittää suorittaa ***message** eli lukea merkkiä muistista, se kohtaa virheellisen muistiosoitteeen ja ohjelma kaatuu.
<br>


#### GNU Debugger tutkiminen

Ensin asetin breakpointin _print_scrambled_ -funktioon ja käynnistin ohjelman. ``print`` -komennoilla pystyin tutkimaan muuttujien ja osoittimien arvoja haluamissani kohdissa pitkin suoritusta.

Asetin ensin breakpoitin -funktioon. Ensimmäinen _print_scrambled()_ -kutsu käsitteli _good_messagen_ normaalisti ja tulosti "Hello, world". Toinen kutsu ei tuottanut tulostetta vaan havaitsin, että message-parametrin arvo oli 0x0, eli NULL. Tämä johti seuraavaksi ohjelman kaatumisen. Tämä siis varmisti, että ohjelman ongelma liittyi NULL-osoittimeen.

<img width="931" height="140" alt="image" src="https://github.com/user-attachments/assets/0cf7e606-3053-422c-aac1-3a410311dae7" />
<br>

<img width="803" height="194" alt="BAD_MESSAGE" src="https://github.com/user-attachments/assets/083f6586-30f9-4852-82c0-9b5ef8650259" />
<br>

Lisäsin vielä toisen breakpointin ennen ongelmallista _printf()_ kohtaa. Tutkin tulosteita good_messagen tulostuksen kohdalta josta oli helppo havaita, kuinka ohjelma eteni merkki kerrallaan.

Kun edettiin toiseen kutsuun, huomattiin, että osoitin osoittaa osoitteeseen 0x0 (NULL) ja GDB kertoo ("No symbol "message" in current context") eli message-nimistä muuttujaa ole näkyvissä. 

<img width="692" height="162" alt="image" src="https://github.com/user-attachments/assets/738ce103-c291-491b-b211-95b6ca393dca" />

<br>

#### Korjaaminen

Kun ongelmakohta oli löydetty, se oli helppo korjata. Lisäämällä ehto 

    if (message == NULL) { 
        return; 
    } 
    
ohjelma tarkastaa alussa, onko sille annettu NULL-osoitin. Jos on, niin funktio lopettaa toimintansa eikä yritä käsitellä epäkelpoa muistiosoitetta.  

________________________________________________________________________________________________________________________________________________________________________________________

### Lab2.zip - kotitehtävä. Ohjelma on käännetty, mutta koodit ovat päässeet katoamaan. Tehtävänä on löytää ohjelman kysymä uusi salasana ja ohjelman tulostama lippu. 

_Olen dokumentoinut yhdenlaista ratkaisua jo toisen tehtävän yhteydessä. Linkki tähän dokumentointiin löytyy [täältä](https://github.com/sauna41/Sovellusten-hakkerointi/blob/main/h3%20No%20Strings%20Attached.md#a-strings-download-ezbin-challengeszip-run-passtr-find-the-correct-password-using-strings-also-find-the-flag-preferably-without-looking-at-the-source-if-you-can)_

##### Tiedoston analysointia

Ennen kuin lähdin tutkimaan debuggeria, yritin kartoittaa mitä ohjelman sisältä löytyy. Otin ensimmäisenä tarkasteluun ohjelman sisältämiä funktioiden ja muuttujien nimiä komennolla

        nm ./passtr2o

Tästä selvisi, että ohjelma sisältää ainakin _main, EaseAs, check paswword & mAsdf3a_ -funktiot. Myös niiden sijainnit muistissa olivat nähtävillä (_0000000001080_)
 - main - pääfunktio
 - check_password - liittyy todennäköisesti salasanan tarkistamiseen

<img width="613" height="607" alt="NM NIMIÄ" src="https://github.com/user-attachments/assets/9b034bae-89ab-47ac-98ee-13cf88344b19" />
<br>

Nähtävillä oli myös **U** symboleja, joiden takana oli komentoja kuten printf & scanf. **U** tarkoittaa _undefined_, eli ne tulevat ulkoisesta kirjastosta eivätkä ole osa ohjelman omaa koodia. LÄHDE TÄNNE-



#### check_password

_check_password_ -funktio oli löydetty, joten lähdin pilkkomaan sitä pienempiin paloihin debuggerin sisällä. 

    disassemble check_pasword

<img width="938" height="467" alt="DISASSEMBLE" src="https://github.com/user-attachments/assets/14182389-6d2d-4ea3-8565-1fbccd64a2de" />
<br>

_check_password_ funktio sisältä löytyi ``XOR %eax, %eax`` sekä ``ret``. Pienellä selvittelyllä tulkitsin tämän tarkoittamaan käytännössä XOR 0 = 0, eli XOR asettaa EAX-rekistgerin arvoksi 0 ja ``ret`` eli return palauttaa funktion kutsujalle. Käytännössä siis return 0;. Hypoteesi tässä kohtaa oli, että salasana tarkistetaan muualla ja tämä funktio vain palauttaa tietyissä oloissa 0. 
<br>

#### main

Seuraavaksi main() -funktio purettiin palasiin:

    disassemble main
<br>
<img width="761" height="589" alt="MAIN" src="https://github.com/user-attachments/assets/2285cc28-cdc5-4696-83b7-ad2e5588a66c" />
<br>

Ensimmäinen havainto oli 0x1060 ``<_isoc99_scanf@plt>``, jonka uskoin olevan käyttäjän syötteen lukeminen. Hetkeä myöhemmin syöte siirtyy ``mov``:n avulla toiselle funktiolle, jolla syötettyä salasanaa todennäköisesti verrataan oikeaan. 

Tarkastin, millä formaatilla ``scanf`` lukee syötteen:

        gdb -batch -ex 'x/s 0x2019' ./passtr2o    // komento
        0x2019: "%19s"    // tuloste

Komento x/s eli _examine_ oli siis käytännössä, että _"tutki muistia osoitteesta 0x2019 ja lue sieltä merkkijono."_ Osoite 0x2019 saatiin aiemmasta main() -funktion tutkimisesta, jossa ohjelma lisää ``lea    0xf2d(%rip),rdi        # 0x2019`` rekisteriin juuri tuon osoitteen.

Tulos kertoi, että merkkijono luetaan käyttäjän syötteestä (%s) ja että se on enintään 19 merkkiä pitkä. Tiedossa oli, että main() -funktio kutsuu <mAsdf3a> -funktiota heti syötteen lukemisen jälkeen, joten siirryin tutkimaan sitä tarkemmin.



#### mAsdf3a

    disassemble mAsdf3a

<img width="670" height="588" alt="image" src="https://github.com/user-attachments/assets/a5fa6cd9-88ab-4f2f-a4c0-8c06510286d9" />
<br>

Funktion sisältä löytyi ``strlen, comp, jne``, eli uskoin vahvasti, että tämä on se funktio, jossa itse vertailu suoritetaan
    - strlen = merkkijonon pituden selvittelyä
    - comp = vertailee arvoja
    - jne = jump if not equal

Funktion alussa ``strlen`` -funktiota käytetään pariin otteeseen. Tämän perusteella vertailtavien merkkijonojen pituudet vertaillaan näissä kohdissa. Tämän jälkeen se alkaa käymään merkki kerrallaan käymään merkkijonon merkkejä läpi. 

Tässä kohtaa tiesin entuudestaan, että kyseinen ohjelma muuttaa merkkijonoa ennen vertailua. Tämän perusteella osasin päätellä, että debuggerin rivit

    test al,0x1
    je 0x1299
    sub edx,0x7
    ... 
    add edx,0x3

toteuttavat todennäköisesti tämän merkkijonojen muuntamisen. Näistä oli pääteltävissä, että merkin sijainnin perusteella siihen joko lisättiin 3 (add) tai vähennettiin 7 (sub).

Lopussa ``mov`` palauttaa EAX-rekisterin arvoksi 1, jos kaikki tarkistukset menivät läpi. Jos eivät, palautus on -1. 
<br>

<img width="698" height="104" alt="RATKAISU" src="https://github.com/user-attachments/assets/0484e83d-3aec-494f-bdd6-2286b8e78635" />
<br>

#### Merkkijonon selvittäminen

Viimeisenä piti selvittää, mitä merkkijonoa ``mAsdf3a`` -funktio käyttää vertailussa käyttäjän syötteen suhteen.

main() -funktiosta löytyi käsky

    movabs $0x3875346a544c6e61,%rax

jossa ohjelma tallentaa tämän arvon myöhemmin pinoon. Pysäytin ohjelman tähän kohtaan ja GDB:n avulla oli mahdollista katsoa, miltä kyseinen arvo näytti merkkijonona. Asetin siis breakpointin juuri ennen arvon tallentamista 

    break *main+83
    run

Ja suoritin assembler-käskyn ``nexti``. 
    - next vs nexti: next suorittaa yhden lähdekoodirivin, nexti suorittaa yhden konekielikäskyn

Tämä asetti arvon, jonka jälkeen muistista löytyi

<img width="518" height="127" alt="X/S RSP+1" src="https://github.com/user-attachments/assets/889836d7-1025-44f9-9d32-18ff2c7bc028" />
<br>

Tuloste kertoi, että **anLTj4u8** oli merkkijono, jota ohjelma käytti salasana tarkastuksen lähtökohtana. Ohjelma siis otti vastaan käyttäjän syötteen, muutti sitä lisäämällä tai vähentämällä merkkijonon merkkien arvoja ASCII-taulukon mukaisesti ja vertasi lopputulosta tuohon merkkijonoon. 

Muuttamalla tämän tietäen anLTj4u8 jokainen merkki saatiin salasanaksi **dgOMm-x1**. 

<img width="713" height="115" alt="FLAG" src="https://github.com/user-attachments/assets/67201050-d157-4108-8733-627c1e1e40a5" />


________________________________________________________________________________________________________________________________________________________________________________________


### Lab3.zip - Nora Crackme -haasteita

Olen dokumentoinut aiemmin ratkomiani Nora Crackme -haasteita aiemmissa tehtävissä. Ne ovat saatavilla linkin takaa:

[Nora Crackme -haasteita](https://github.com/sauna41/Sovellusten-hakkerointi/blob/main/h4%20Some%20Disassembly%20Required.md#d-nora-crackme-compile-to-binaries-tindall-2023-noracodes--crackmes-read-readmemd-dont-look-at-the-source-code-unless-you-need-training-wheels-in-these-tasks-binaries-are-reverse-engineered-binaries-are-not-modified-because-otherwise-the-solution-to-every-task-would-be-to-change-the-return-value-to-return-0)

________________________________________________________________________________________________________________________________________________________________________________________

### Kirjoita dokumentti siitä, miten sait nämä selville. Sekä mitä uutta opit GNU Debuggerista, että mitä et oppinut tunnilla

GNU Debugger oli entuudestaan täysin tuntematon, joten kaikki opitut asiat luennolla tulivat uusina. Myös C oli kielenä vieras, joten sen tulkitsiminen vaati ajatustyötä. Ensimmäisen luennon aikan opin kuintekin Debuggerin peruskäytön ja miten ohjelman suoritusta voi tutkia vaiheittain.

Labraharjoitusten aikana opin hyödyntämään luennolla opittuja komentoja ja tutuistuin myös itsenäisesti GNU debuggerin muihin toimintoihin. 

Opin esimerkiksi ymmärtämään sekä asettamaan breakpointteja, suorittamaan ohjelmaa vaihe vaiheelta ja analysoimaan ohjelman toimintaa näissä vaiheissa. Myös muuttujien ja muistiosoitteiden arvoja oli mahdollista seurata ohjelman suorituksen edetessä.

Ohjelman etenemistä oli helppo seurata "reaaliajassa". Oli hyödyllistä kun ohjelman pystyi pysäyttämään haluttuun kohtaan, jolloin sen hetkistä tilannetta pystyi tutkimaan tarkemmin tai tulostamaan sen hetkiset ``print messaget``. Lähdekoodin ja debuggerin yhdistäminen tukivat toisiaan, sillä vaikka ongelmakohtaa ei tajuaisi suoraan toisesta, pystyi niitä tutkimaan ristiin ja tutkimaan hypoteesejä. 

Tehtävissä oli saatavilla lähdekoodit mutta GNU:ta pystyi hyödyntämään ilmankin. Konekäskyjen opettelu sekä niiden vaikutukset toisiinsa vaativat huomattavasti ajatustyötä. Muisti rekisterien tuntui erityisen haastavalta sijaintien ollessa "pitkiä" ja jokseenkin hankalia seurata.

________________________________________________________________________________________________________________________________________________________________________________________


### Lähteet

Iso-Anttila L, Karvinen T. Haaga-Helian Sovellusten hakkerointi ja -haavoittuvuudet kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/application-hacking/. Luettu 17.9.2026.

What is GDB? GDB: The GNU Project Debugger. Luettavissa: https://sourceware.org/gdb/. Luettu 17.9.2026.

GNU GDB Debugger Command Cheat Sheet. YouLinux.com Luettavissa: https://www.yolinux.com/TUTORIALS/GDB-Commands.html. Luettu 17.9.2026.

ChatGPT. Saatavilla: chatgpt.com. Käytetty 23.9.2026.


