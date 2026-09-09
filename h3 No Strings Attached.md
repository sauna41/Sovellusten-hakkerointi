_Kurssi: Sovellusten hakkerointi ja haavoittuvuudet ICI012AS3A-3004_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 7.9.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on h3 No Strings Attached. Opettajana toimi Tero Karvinen_

________________________________________________________________________________________________________________________________________________________________________________________

### a) Strings. Download ezbin-challenges.zip. Run 'passtr'. Find the correct password using 'strings'. Also find the flag. (Preferably without looking at the source, if you can.)

Latasin [Karvisen kurssisivulta](https://terokarvinen.com/application-hacking/) tehtävänannon kansion ja purin sen. 

Tarkastelin aluksi tiedostoa: ```file passtr```. 

<br> <img width="966" height="108" alt="FILE PASSTR" src="https://github.com/user-attachments/assets/980db5e1-0140-4c67-91c7-40df51015932" /> <br>

Flag löytyi yllättävänkin helposti. Komennolla ```strings passtr``` tulostui kaikki ohjelmat merkkijonot, jota tutkimalla löytyi 

<img width="794" height="424" alt="FLAG" src="https://github.com/user-attachments/assets/e2cae3bf-6e9f-4edc-b0c9-4228e7f29dd8" />

________________________________________________________________________________________________________________________________________________________________________________________


### b) Make a new version of the passtr.c program where the password doesn't appear directly as-is in the binary. Demonstrate with a test that the password doesn't appear. (Obfuscation is sufficient.)

Tutustuin obfuskointiin [FreeCodeCampin artikkelin](https://www.freecodecamp.org/news/make-your-code-secure-with-obfuscation/) pohjalta. Obfuskoinnissa tarkoituksena on "hämärtää" koodia niin, että vaikka tietokone pystyy edelleen käsittelemään sitä, ihminen tai yksinkertainen analyysityökalu ei kykene lukemaan sitä helposti. Edellisessä tehtävässä hakkerin salasana paljastui yksinkertaisena merkkijonona mutta obfuskoinnin avulla sama salasana tulostuisi jonain aivan muuna.

Loin itselleni varmuuskopiot alkuperäisestä _passtr_ ohjelmasta ja lähdekoodista komennoilla ```cp passtr ~/<Hakemisto>/COPYpasstr``` ja ```cp passtr.c ~/<Hakemisto>/COPYpasstr.c```. Sen jälkeen lähdin tutkimaan lähdekoodia.



<br> <img width="927" height="449" alt="FIXED SOURCECODE" src="https://github.com/user-attachments/assets/605064b7-27b2-4de8-9434-a8b64e7e2bf2" /> <br>


Verrattiin tulostuksia:

    strings COPYpasstr | grep "sala-hakkeri-321"
    strings passtr | grep "sala-hakkeri-321"

Alkuperäinen ohjelma palautti salasanan merkkijonona kun taas muokattu ohjelma ei tulostanut mitään. 

<br> <img width="882" height="100" alt="FIXED PROOF" src="https://github.com/user-attachments/assets/fc5a576a-32f3-4357-b9ca-cfdc295d9a64" /> <br>


________________________________________________________________________________________________________________________________________________________________________________________


### c) Packd. Run 'packd' from the package ezbin-challenges.zip. What is the password? What is the flag?

Lähdin lähestymään tehtävää samanlailla kuin edellistä. Ensin kokeilin ```strings packd``` -komentoa, joka palautti epäselvää dataa mutta myös ohjelman käyttöliittymän merkkijonot sekä osa, katkenut pala lippua. Lisäksi tulosteesta löytyi maininta UPX-pakkauksesta. Tästä + tehtävännimestä päättelin, että binääri saattaisi olla UPX-pakattu mikä vaikeuttaa yksinkertaista _strings_ analyysia.

<br> <img width="901" height="300" alt="UPX FIND" src="https://github.com/user-attachments/assets/a9e61ce4-6384-4961-b376-4a1bc6f11a49" /> <br>

Tutustuin UPX-pakkauksiin ja löysin, että komennolla ```upx .d <tiedosto>``` saisi purettua UPX-pakatun tiedoston, joten lähdin kokeilemaan tätä.

<br> <img width="837" height="212" alt="UNPACKED" src="https://github.com/user-attachments/assets/0af78430-8f1f-4b26-aa24-1b5cef12dcc7" /> <br>

Tämän jälkeen yritin uudelleen strings -komentoa. Tällä kertaa merkkijonot olivat näkyvissä, jolloin salasana & lippu saatiin tietoon.


<br> <img width="766" height="86" alt="FLAG FOUND" src="https://github.com/user-attachments/assets/0bb31d21-20e8-448c-8f52-02f85f935003" /> <br>


________________________________________________________________________________________________________________________________________________________________________________________

### Lähteet

Karvinen, T. Sovellusten hakkerointi kurssimateriaali. Luettavissa: https://terokarvinen.com/application-hacking/. Luettu 7.9.2026.

Kovacevic, A. What is Code Obfuscation? How to Disguise Your Code to Make it More Secure. FreeCodeCamp. 2020. Luettavissa: https://www.freecodecamp.org/news/make-your-code-secure-with-obfuscation/. Luettu 7.9.2026.
