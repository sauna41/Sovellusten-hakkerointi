_Kurssi: Sovellusten hakkerointi ja haavoittuvuudet ICI012AS3A-3004_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 13.9.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on h4 Some Disssembly Required. Opettajana toimi Tero Karvinen_

________________________________________________________________________________________________________________________________________________________________________________________


### a) Install Ghidra.

Ghidran asentamista varten tarvittiin lisäksi Java Development Kit (JDK), koska Java-pohjainen Ghidra tarvitsee Java ympäristön toimiakseen. Asensin Kaliin OpenJDK 25 ja tarkistin asennuksen ```java --version``` ja ````javac --version```` -komennoilla. Molemmat versiot olivat mallia 25.0.4. [Ghidra Docs](https://ghidradocs.com/9.1_PUBLIC/docs/InstallationGuide.html)

Kun Java-ympäristö oli kunnossa, Ghidran asennus tapahtui ```sudo apt install ghidra``` komennolla.

________________________________________________________________________________________________________________________________________________________________________________________


### b) rever-C. Reverse engineer the packd binary to C language with Ghidra. Find the main program. Give variables descriptive names. Explain the program's operation. Solve the task from the binary, without the original source code.
#### Uusi projekti

Avattiin Ghridra ja alettiin käsittelemään aiemmista tehtävistä tuttua _packd_ sisältöä. 

Ghidrasta luotiin uusi projekti: _File_ --> _New Project_ --> _Non-Shared Project_ --> _Hakemisto & nimi_. Projektin sisälle importattiin _packd_ -tiedosto.

<img width="329" height="142" alt="PACKD PROJEKTI" src="https://github.com/user-attachments/assets/302ce771-22a1-4337-9300-6b868ca688ca" />

Klikkaamalla saatiin auki CodeBrowser ja Ghidran ilmoitus: "_packd has not been analyzed. Would you like to analyze it now?_" johon vastattiin kyllä. Analyysi-ikkunasta en muuttanut asetuksia vaan suoritin analyysin vakioasetuksilla. 

<img width="841" height="687" alt="ANALYZE WINDOW" src="https://github.com/user-attachments/assets/534ba90f-66ae-4a82-85de-9f1db093002c" />


#### Main-lohkon etsintä

Aloitin tutkimaan analyysiä etsimällä _Main_ -lohkon. _Symbol Tree_ valikosta sitä ei löytynyt suoraan, joten kokeilin etsiä tiedettyä merkkijonoa _Search_ -toiminnolla. 

<br>

<img width="1130" height="425" alt="image" src="https://github.com/user-attachments/assets/972f79e3-fa26-42f3-900a-47dfb5644891" />

<br>
<br>

Tällä löytyi "_What's the password?_" merkkijonon sijainti, jonka tiesin olevan main-lohkon sisällä. Aikani ihmeteltyä erilaisia _FUN_ funktioita, tajusin, että käsittelyssä oleva paketti on vielä .upx pakattu. Pakkaus lisäsi siihen liittyvää sisältöä, joka monimutkaisti varsinaisen ohjelmalogiikan analysointia, joten tein binääristä puretun version. Analyysi kohdistui silti tehtävässä annettuun packd-binääriin eikä lähdekoodiin.

Loin uuden projektin, johon lisäsin puretun packd -tiedoston. Samoilla askelilla löytyi main-lohko ja sen löytämä pseudokoodi helposti: 

<img width="274" height="336" alt="image" src="https://github.com/user-attachments/assets/0ebb1614-a636-499e-9ec9-2ce1d26e1f41" />

#### **Ohjelman toiminta** pähkinänkuoressa
1. Kysytään käyttäjältä salasanaa tulostamalla _"What's the password?"_
2. ````scanf()```` lukee käyttäjän syötteen muuttujaan local_28
3. ````strcmp()```` vertaa käyttäjän syötettä oikeaan salasanaan "_piilos-AnAnAs_"
4. ````strcmp()```` palauttaa 0 jos merkkijonot täsmäävät
5. Jos tulos on 0, ohjelma ilmoittaa oikeasta salasanasta ja tulostaa lipun
6. Muissa tapauksissa tulostetaan "_Sorry, no bonus_" 

<br>
<br>

#### Muuttujanimet kuntoon

Lähdin harjoittamaan **reverse engineringiä** nimeämällä automaattiset muuttujat helpommin ymmärrettäväksi:
- iVar1 --> int passwordComparison;
- char local_28 --> char password

#### Salasana

Oikea salasana esiintyy strcmp()-funktion parametrina, se voidaan ratkaista ilman lähdekoodia. Se olisi helppo testata antamalla se syötteeksi, mikä palauttaisi lipun ja täten todistaisi, että tehtävän voi ratkaista ilman alkuperäistä lähdekoodia pelkän reverse engineeratun binäärin avulla. 

________________________________________________________________________________________________________________________________________________________________________________________

### c) If backwards. Modify the passtr program's binary (without the original source code) so that it accepts all passwords except the correct one. Demonstrate with tests that the program works.

#### Analyysi

Alkuun samat askeleet kuin aiemmin. Ghidraan uusi projekti, johon importattiin _passtr_ ohjelma. Main-lohkon paikantaminen sujui tällä kertaa helpommin ja koodi saatiin tutkittavaksi:


<br>
<br>
<img width="685" height="402" alt="image" src="https://github.com/user-attachments/assets/c20bf1c9-dbb5-4fb2-8549-08e04d8a22b8" />
<br>
<br>

Tarkoitukseni oli siis muuttaa koodi niin, että kaikki paitsi oikea salasana tulostaisi lipun. Ajattelin aluksi, että voisin yksinkertaisesti vaihtaa ehdon muotoon ````if (iVarl != 0) //hyväksy kaikki väärät```` mutta tajusin, että varsinainen muutos tulisi tehdä assemblyssä. Tämä lähti liikkeelle etsimällä binääristä kohta, jossa vertailu tapahtuu. Oikealle riville pääsi helposti klikkaamalla koodista haluttua kohtaa. 

<br>
<br>
<img width="789" height="227" alt="image" src="https://github.com/user-attachments/assets/19e3073b-ab66-4c23-9b5a-040eaf665c26" />
<br>
<br>

Assemblyssä näkyy CALL (kutsuu strcmp-funktiota), jonka jälkeen TEST (tarkistaa, onko palautusarvo 0) ja JNZ (Jump if Not Zero) hyppää kohtaan "_Sorry, no bonus_", jos tulos ei ole nolla. Lähdin siis muuttamaan JNZ --> JZ (hypätään nollaan, eli lipun tulostukseen).

#### Binäärin patchaaminen

Right-klikkaamalla 001011a3 75 11 JNZ --> Patch Instruction päästiin muokkaamaan JNZ --> JZ. 

<br>
<br>
<img width="567" height="108" alt="image" src="https://github.com/user-attachments/assets/4dc9654e-1634-4db1-ae9b-7329fae6700c" />
<br>
<br>

#### Ohjelman export ja todennus

Binäärin logiikka oli nyt muokattu ja oli aika exportata se testiajoon. Export tapahtui _File_ --> _Export File_. Tiedostomuotona pidin alkuperäisen. Kun ohjelma oli exportattu, oli aika kokeilla ajaa se "väärällä" että "oikealla" salasanalla:

<br>
<br>
<img width="724" height="122" alt="image" src="https://github.com/user-attachments/assets/820af198-aa96-4c43-a6ac-983846bda7c9" />
<br>
<br>

<img width="508" height="121" alt="image" src="https://github.com/user-attachments/assets/287dc757-00b3-4915-b05e-7ab35f97b159" />
<br>
<br>
________________________________________________________________________________________________________________________________________________________________________________________


### d) Nora CrackMe: Compile to binaries Tindall 2023: NoraCodes / crackmes. Read README.md: don't look at the source code unless you need training wheels. In these tasks, binaries are reverse engineered. Binaries are not modified, because otherwise the solution to every task would be to change the return value to "return 0".

Latasin CracMe haasteet Kaliin komennolla ````git clone https://github.com/NoraCodes/crackmes.git````. README-tiedosta löytyi lisäohjeistusta: komennolla ````make <name>```` saatiin käännettyä crackme01 & crackme02 binääri. 

Sain kuitenkin virheilmoituksen, että _lcrypt_ puuttuu. Perehdyin aiheeseen ja käsitykseni mukaan GCC toimii kääntäjänä mutta tarvitsee linkkerin yhdistämään ohjelman ja kirjastot joita se käyttää. Linkitysvaiheessa objektit ja kirjastot yhdistetään suoritettavaksi ohjelmaksi. [GNU.org](https://gcc.gnu.org/onlinedocs/gcc/Link-Options.html)

<br>
<br>
<img width="851" height="123" alt="image" src="https://github.com/user-attachments/assets/477db322-8a16-490a-802e-56dd26844767" />
<br>
<br>

Latasin siis libcrypt-dev -paketin, joka sisälsi vaadittavat tiedostot tehtävissä kannalta linkittämiseen. (```sudo apt install libcrypt-dev```)

Uusi yritys ja tällä kertaa saatiin ajettava ohjelma.

<br>
<br>
<img width="705" height="63" alt="image" src="https://github.com/user-attachments/assets/610a0b29-08c7-4fe1-ac94-c09a4bd7167a" />
<br>
<br>


________________________________________________________________________________________________________________________________________________________________________________________

### e) Nora crackme01. Solve the binary.

Tehtävän tarkoitukseni oli siis päättää ohjelma exit statukseen 0.

Avasin jälleen ohjelman Ghidrassa. Main-lohkon löytäminen kävi tässä vaiheessa jo helposti aiempien tehtävien pohjalta. Pseudokoodista pystyi tulkitsemaan, ohjelma palauttaa arvon 0 kun syötetty salasana täsmää ohjelmaan tallennettuun salasanaan. Myös tämä oikea salasana oli helposti nähtävillä: _password1_. 
<br>
<br>
<img width="591" height="488" alt="PSEUDO CODE" src="https://github.com/user-attachments/assets/dedd6454-ad46-434a-a1fa-01332bd2d428" />
<br>
<br>

Ratkaisua oli helppo kokeilla selvitetyllä syötteellä: ````./crackme01.64 password1````:

<img width="426" height="76" alt="SOLVE" src="https://github.com/user-attachments/assets/4144f268-e3b1-4308-9b60-86d8787b0c43" />

________________________________________________________________________________________________________________________________________________________________________________________

### e) Nora crackme01e. Solve the binary.

Jälleen Ghidraan auki ja tulkitsemaan. Pseudokoodi oli hyvin samankaltainen kuin aiemmassa tehtävässä mutta ``strcmp()`` sijaan funktiona toimi ``strncmp()``. Tämä vertaili merkkijonosta **enintään** annetun määrän merkkejä (10). 
<br>
<img width="511" height="591" alt="CRACKME01E BINARY" src="https://github.com/user-attachments/assets/2ec30c03-e20c-42c0-89a7-fc9c4721db5d" />

Ajamalla '_slm!paas.k_' päästiin exit-status 0 pisteeseen. 

<br>
<img width="456" height="82" alt="CRACKME01E SOLVE" src="https://github.com/user-attachments/assets/31b34070-6b5a-4be9-8841-cd8939dd7c32" />

________________________________________________________________________________________________________________________________________________________________________________________

### f) Nora crackme02. Name the main program's variables from the reverse-engineered binary and explain the program's operation. Solve the binary.

Vielä kerran Ghidraan ja tutkimaan. Tällä kertaa merkkijonoja eri vertailtu strcmp() -funktiolla, vaan merkkejä vertailtiin silmukan sisällä. 

<br>
<br>
<img width="509" height="690" alt="CRACKME02 BINARY" src="https://github.com/user-attachments/assets/e2e66839-25b2-495d-bbd8-9b5395fe5b01" />
<br>
<br>

1. Ohjelma vaatii yhden argumentin
2. Asetetaan pcVar5 = "password1     // vertailumerkkijono
3. cVar2 = vertailun ensimmäinen merkki 'p'
4. pcVar4 = käyttäjän syötteen ensimmäinen merkki
5. Silmukka, jossa on itse kikkailu tapahtuu: (_koodin tulkintaan käytetty ChatGPT tekoälyä promptilla "Selitä koodi rivi riviltä"_)

        do {        // aloittaa silmukan
            pcVar5 = pcVar5 + 1;     // osoittaa merkkijonon toiseen indeksiin 'a'
        
            if (*pcVar4 == '\0')         // tarkastetaan, loppuiko käyttäjän syöte
                break;        // jos syöte loppuu, lopettaa
        
            if (cVar2 + -1 != (int)*pcVar4) {         // Jos käyttäjän syöttämä merkki on 
                                                      // -1 ASCII-arvoa pienempi kuin vertailumerkkijono (password1)
   
                printf("No, %s is not correct.\n",pcVar1);        // jos merkki on väärä, tulostetaan
                return 1;        // lopetetaan ja palautetaan exit-status 1
            }
        
            cVar2 = *pcVar5;        // siirrytään seuraavaan odotettuun merkkiin
            pcVar4 = pcVar4 + 1;        // siirrytään vertailemaan seuraavaa merkkiä käyttäjän syötteestä
    
        } while (cVar2 != '\0');        // tarkastetaan, onko odotettu salasana päättynyt

        // PÄHKINÄNKUORESSA: odotettu oikea merkki -1 = käyttäjän merkki

Eli "salasanan" ollessa password1, on odotettu käyttäjän syöte jokaisen merkin kohdalla -1 ASCII = **o`rrvnqc0**    [ASCII Table](https://www.ascii-code.com/)

Kokeiltiin yllä mainittua ja törmättiin backstick ongelmaan. 

<br>
<img width="440" height="72" alt="bquote" src="https://github.com/user-attachments/assets/40e116d9-e107-43e2-8c8c-032359a41f3d" />
<br>
<br>

Tämä oli helppo ratkaista laittamalla salasana hipsujen sisään. 

<br>
<img width="420" height="73" alt="CRACKME02 SOLVE" src="https://github.com/user-attachments/assets/cf11f4ee-6010-49d5-87cd-cc4784e462b6" />
<br>

________________________________________________________________________________________________________________________________________________________________________________________


### x) Read/watch/listen and summarize

[Hammond 2022: Ghidra for Reverse Engineering (PicoCTF 2022 #42 'bbbloat')](https://www.youtube.com/watch?v=oTD_ki86c9I)

Video esittelee 2022 PicoCTF reverse-engineering tehtävää "Bloat". Tarkoitus on selvittää ohjelman toiminta ilman lähdekoodia binäärin avulla ja löytää oikea syöte, jolla lippu saadaan kaapattua.
- Ensin selvitetään perustiedot: file -komento, 64-bit ELF, PIE.
- Ennen Ghidraa kokeiltiin komentorivityökaluja: ltrace, strace, objdump, strings. Näillä saadaan jonkin sortin käsitys siitä, mitä ohjelma tekee.
- Ghidraan luodaan uusi projekti, importataan binääri, suoritetaan analyysi ja etsitään "_What is my favorite number_" merkkijono.
- Tutkitaan Decompiler pseudokoodia: tulostetaan kysymys, luetaan syöte, verrataan syötettä arvoon ja tuloksesta riippuen jatketaan tai lopetetaan.
- Vertailuarvo löydettiin hexadesimaalilukuna ja se muutettiin desimaaliluvuksi
- Oikea desimaali tulostaa lipun

<br>

Tein tiivistelmän tämän raportin viimeisenä osana. Videolla käytettiin samoja työkaluja ja toimintatapoja kuin itse käytin aiempien osuuksien suorittamiseen. Binääristä etsittiin tunnettu merkkijono, josta päästiin tutkimaan Decompilerin pseudokoodia. Pseudokoodista voidaan päätellä miten ohjelma saattaisi toimia, jolloin voidaan lähteä kokeilemaan ratkaisuja alkuperäisellä binäärillä muokkaamatta mitään. Videosta jäi erityisesti mieleen se, miten paljon eri komentorivityökaluja kokeiltiin ennen Ghidra osuuden aloittamista. Kyseiset työkalut eivät olleet vielä tässä kohtaa itselleni tuttuja ``strings`` -työkalua lukuunottamatta mutta näinhän näitä oppii.

________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet:

Karvinen, T. Sovellusten hakkerointi kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/application-hacking/#homework. Luettu 16.9.2026.

Hammond, J. GHIDRA for Reverse Engineering (PicoCTF 2022 #42 'bbbloat'). 2022. Saatavilla: https://www.youtube.com/watch?v=oTD_ki86c9I. Katsottu 16.9.2026.

Ghidra Installation Guide. Ghidra Docs. Luettavissa: https://ghidradocs.com/9.1_PUBLIC/docs/InstallationGuide.html. Luettu 16.9.2026.

NoraCodes _crackmes_. Github. Saatavilla: https://github.com/NoraCodes/crackmes. Ladattu 16.9.2026.

ChatGPT. Prompt: "Selitä koodi rivi riviltä." Saatavilla: chatgpt.com. Käytetty 16.9.2026.

ASCII Table. ASCII code. Luettavissa: https://www.ascii-code.com/. Luettu 16.9.2026.


3.16 Options for Linking. GNU.org. Luettavissa: https://gcc.gnu.org/onlinedocs/gcc/Link-Options.html. Luettu 16.9.2026.
