_Kurssi: Sovellusten hakkerointi ja haavoittuvuudet ICI012AS3A-3004_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 25.8.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on h1 Freedom of Action, Control, and Risk Mitigation. Opettajina toimivat Tero Karvinen & Lari Iso-Anttila._

________________________________________________________________________________________________________________________________________________________________________________________

# h1 Freedom of Action, Control, and Risk Mitigation



## ISMS Scope - kotiverkko & opiskeluympäristö


ISMS on tietoturvan johtamisjärjestelmä, jota käytetään riskien tunnistamiseen, tiedon suojaamiseen ja tietoturvan kehittämiseen. ISMS ei ole yksittäinen dokumentti tai ohjelmisto – vaan kokonaisvaltainen viitekehys, joka kokoaa kaiken tietoturvatyön yhteen (Pitkänen, T. 2025).

Sisällytän soveltamisalaan järjestelmät ja tiedot, joita käytetään kurssin suorittamiseen tai ovat harjoitteluympäristön kannalta oleellisia osia.

### Soveltamisalan kattavuus

#### Kotiverkon infrastruktuuri

- Reititin
- Wi-Fi
- LAN

#### Laitteet

- Lenovo ThinkPad kannettava tietokone (Windows)
- VirtualBoxissa Kali Linux
- Android matkapuhelin

#### Informaatio & Data

- Kurssimateriaali (Moodle, Tero Karvisen verkkosivut)
- GitHub-repositoriot
- Labraharjoitukset


<br>
<br>

### Soveltamisalan rajaus

#### Kotiverkon älylaitteet

- Älytelevisio
- PlayStation pelikonsoli
- IoT-laitteet (valaistus, kaiuttimet)

Kurssiin liittymättömät laitteet rajataan soveltamisalasta pois.

#### ISP:n oma verkko

ISP:n verkko ei ole omistuksessani eikä hallinnassani. 

#### Pilvipalveluiden infrastruktuuri

- Googlen & GitHubin palveluiden infra (ei hallintaa)

<br>
<br>


### Rajapinnat

**Kotiverkko <--> Internet**

Kotiverkon ja Internetin rajapinta on reitittimessä. Se välittää liikennettä ISP- ja kotiverkon välillä.

**Opiskeluympäristö <--> Pilvipalvelut**

Opiskeluympäristöstä muodostetaan yhteyksiä ulkoisiin pilvipalveluihin, kuten Googlen eri palveluihin & GitHubiin sekä oppilaitoksen järjestelmiin. 




<img width="824" height="469" alt="image" src="https://github.com/user-attachments/assets/aeed2046-05b8-4412-ba81-c1c9b4cd5e46" />


**Todisteet**

1. Kurssin [GitHub](https://github.com/sauna41/Sovellusten-hakkerointi/tree/main) dokumentaatio


2. VirtualBox, jossa Kali Linux
      <img width="899" height="437" alt="image" src="https://github.com/user-attachments/assets/a525dcb6-4823-4928-9c1b-d025e673009e" />

3. Reitittimen asetukset & kotiverkossa olleiden laitteiden inventaario
   <img width="622" height="512" alt="image" src="https://github.com/user-attachments/assets/99e18b59-8785-4894-9592-e96c2de1b85a" />

4. ISMS-dokumentaatio
   ISMS-dokumentti kertoo, mitä järjestelmiä, laitteita ja tietoja soveltamisalaan kuuluu ja mitä ei.

5. Verkkokaavio
   Kertoo soveltamisalan alueet ja niiden yhteydet toisiinsa
   ________________________________________________________________________________________________________________________________________________________________________________________


#### b) Linking the Assignment to the Standard. Identify at least two interested parties in the context of your home network.


| Kiinnostunut osapuoli | Vaatimus | ISO 27001 viite | Todiste |
|---|---|---|---|
| **Minä** | Oman datan tietoturvallinen säilöminen ja suojaaminen. Kurssitehtävien jatkuvuus | Suunnittelu / Toiminta | Kurssikohtainen GitHub repositorion dokumentaatio, varmuuskopiot ja dokumentoidut tehtävät |
| **ISP** | Palvelusopimuksen ja käyttöehtojen noudattaminen. Liittymää ei käytetä väärin esim. luvattomien julkisten palvelinten pystyttäminen | Toiminta | Ote ISP:n käyttöehdoista tai viittaus niihin; reitittimen konfiguraatio, joka osoittaa ettei porttiohjauksia/julkisia palveluita ole avattu sopimuksen vastaisesti | 
| **Google** | Palveluiden turvallinen käyttö: henkilökohtaisten tietojen suojaaminen. Monivaiheisen tunnistautumisen käyttö. Käyttöehtojen noudattaminen | Tuki / Toiminta | Vahva salasana, MFA, turvallisuusasetuksien säädöt |
| **Oppilaitos** | Tietoturvatehtävien vastuullisuus. Harjoitukset luvallisissa palveluissa / alustoissa. Akateeminen vastuu. | Suunnittelu / Toiminta | Harjoitteluympäristön rajaus ja sääntöjen dokumentointi. Kurssitehtävien ja käytännönharjoitusten dokumentointi. |

________________________________________________________________________________________________________________________________________________________________________________________



### Lähteet

Karvinen, T. Sovellusten hakkerointi kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/application-hacking/. Luettu 25.8.2026.

Barker, S. ISO 27001 Clauses Explained Simply. Luettavissa: https://hightable.io/iso-27001-clauses/. Luettu 25.8.2026.

Pitkänen, T. Mikä on ISMS? Opas tietoturvan hallintajärjestelmiin. 22.5.2025. Luettavissa: https://www.digiturvamalli.fi/blogi/mika-on-isms. Luettu 25.8.2026.

