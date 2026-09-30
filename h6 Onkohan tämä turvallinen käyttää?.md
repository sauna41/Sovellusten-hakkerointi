_Kurssi: Tunkeutumistestaus ICI005AS3A-3007_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox) --> GNU Debugger

_Päivämäärä: 24.9.2026_

_Tämä raportti on osa Haaga-Helian Tunkeutumistestaus -kurssia syksyllä 2026. Tehtävänanto on **H6: Onkohan tämä turvallinen käyttää?**. Opettajana toimi Lari Iso-Anttila._

________________________________________________________________________________________________________________________________________________________________________________________


### Tutki kotona Tapo C200 -kameran ohjelmiston turvallisuutta ja käytä kaikkia menetelmiä, joita olet oppinut tällä kurssilla analyysin tekemiseen.
_Kirjoita tutkimuksestasi raportti, josta selviää, mitä löysit ja miten löysit mahdolliset ongelmat. Onko mahdollista käyttää hyödyksi löytämiäsi haavoittuvuuksia._
<br>
<br>

Latasin kurssimateriaaleina annetut Tapo C200 v3 Dump filen & TP-link Decrypt -tiedostot.


<img width="824" height="582" alt="image" src="https://github.com/user-attachments/assets/33603050-8a6b-4a1f-be4d-8a9ffc17b549" />
<br>

### Firmware kuvan purkaminen

Latasin kurssimateriaalin ohjeiden mukaan Tapo V3 firmware binäärin:


    aws s3 cp s3://download.tplinkcloud.com/firmware/Tapo_C200v3_en_1.4.2_Build_250313_Rel.40499n_up_boot-signed_1747894968535.bin Tapo_C200v4_en_1.4.2.bin --no-sign-request

Siirsin sen TP-link Decryptin kanssa samaan hakemistoon ja ajoin ``./bin/tp-link-decrypt Tapo_C200v4_en_1.4.2.bin``

<img width="580" height="354" alt="image" src="https://github.com/user-attachments/assets/e222cab6-92b8-4e09-8a4b-728d7e488b6e" />
<br>

Työkalu tunnisti firmware-kuvan ja löysi myös headerin sekä RSA-2048 salausmenetelmän. Kuvan verifiointi onnistui.

Purettu firmware kirjoitettiin tiedostoon ``Tapo_C200v4_en_1.4.2.bin.dec`` josta analyysiä voitiin jatkaa puretulla firmware-kuvalla.


### Image-tiedoston analysointi

<img width="844" height="324" alt="image" src="https://github.com/user-attachments/assets/05a17959-b1ec-490a-9d83-c11b17cdd9ae" />
<br>


Seuraavaksi purettu firmware analysoitiin ``binwalk`` työkalulla:

    binwalk Tapo_C200v4_en_1.4.2.bin.dec

Analyysi paljasti useita löytöjä, kuten Linux-kernelin, LZO-, XZ & LZMA-pakattua dataa sekä SquashFS-tiedostojärjestelmän. Tehtävänannon kannalta merkittävin löytö oli 


      4063744     0x3E0200     Squashfs filesystem, 
                               little endian, version 4.0, 
                               compression:xz, size: 3032084 bytes,
                               96 inodes, blocksize: 65536 bytes, 
                               created: 2025-03-13 03:15:05


### Rootfs irrottaminen dump-tiedosta

SquashFS irrotettiin dumpista 

    dd if=dump-tapo-c200v3-1.4.2.bin \
    of=rootfs-dump/rootfs.squashfs \
    bs=1 \
    skip=4456448 \
    count=3032084 \
    status=progress

jonka tuloksena oli eriytetty SquashFS-tiedostojärjestelmä. Se purettiin vielä omaan hakemistoonsa

    unsquashfs -d rootfs-dump/rootfs rootfs-dump/rootfs.squashfs

Purkamalla saatiin rootFS:stä tiedosto- ja hakemistorakenne.



### Rootfs irrottaminen image-tiedosta

Firmware-kuvasta löydetty SquashFS erotettiin samalla tavalla kuin dumpista löytynyt tiedostojärjestelmä. Loin sillekin oman hakemistonsa ja irrotin tiedot oikeaan paikkaan

    dd if=Tapo_C200v4_en_1.4.2.bin.dec \
     of=rootfs-image/rootfs.squashfs \
     bs=1 \
     skip=4063744 \
     count=3032084 \
     status=progress

Myös firmware-kuvasta oli täten saatu erillinen purettu rootfs hakemistonsa.



### Firmwaren sovellukset


Kaikki ajettavat tiedostot voitiin listata komennolla ``find rootfs-image/rootfs -type f -executable``


<img width="679" height="892" alt="SOVELLUKSET" src="https://github.com/user-attachments/assets/aae119e2-3dcc-4e5a-a495-1ce1a259e52d" />
<br>


Kiinnostavin tulos oli ``rootfs-image/rootfs/bin/main``, sillä se todennäköisesti sisältäisi keskeistä binääriä liittyen kameran toiminnallisuuteen, käyttäjähallintaan ja kirjautumiseen. Myös ``rootfs-image/rootfs/bin/gdbserver`` saattaisi olla hyödyllinen dynaamisessa tutkimisessa.



### Analyysi ja rootin löytäminen




________________________________________________________________________________________________________________________________________________________________________________________


### Lähteet

Sovellusten hakkerointi ja haavoittuvuudet kurssimateriaali. Luettavissa: https://terokarvinen.com/application-hacking/. Luettu 24.9.2026.

Rooting the TP-Link Tapo C200 Rev.5. qkaiser. 2025. Luettavissa: https://quentinkaiser.be/security/2025/07/25/rooting-tapo-c200/. Luettu 24.9.2026.

