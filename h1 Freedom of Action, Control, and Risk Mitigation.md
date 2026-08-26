_Kurssi: Sovellusten hakkerointi ja haavoittuvuudet ICI012AS3A-3004_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 25.8.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on h1 Freedom of Action, Control, and Risk Mitigation. Opettajina toimivat Tero Karvinen & Lari Iso-Anttila._

________________________________________________________________________________________________________________________________________________________________________________________

# h1 Freedom of Action, Control, and Risk Mitigation



## ISMS Scope - kotiverkko & opiskeluympäristö

a) Basic Level. Define the ISMS scope for your home network and study lab (½–1 page). Describe at least the following:
a1) What is included in the scope
Basic home network infrastructure: router, Wi-Fi, possible network shares or NAS, printer, and IoT devices where relevant.
Devices used for the course exercises: laptop or workstation, possible virtual machines, a lab server such as a Linux VM, and a phone if you use it for MFA.
Information and data: course materials, personal notes, repositories, lab materials, and possible credentials or cryptographic keys.

ISMS on tietoturvan johtamisjärjestelmä, jota käytetään riskien tunnistamiseen, tiedon suojaamiseen ja tietoturvan kehittämiseen. ISMS ei ole yksittäinen dokumentti tai ohjelmisto – vaan kokonaisvaltainen viitekehys, joka kokoaa kaiken tietoturvatyön yhteen (Pitkänen, T. 2025).

Tässä kohdassa ISMS koskee omaa kotiverkkoani ja opiskeluympäristöäni. Ympäristö sisältää niin fyysisiä kuin virtuaalisia järjestelmiä. 

### Soveltamisalan kattavuus

#### Kotiverkon infrastruktuuri

- Reititin
- Wi-Fi
- LAN
- Erinäiset IoT-laitteet

#### Laitteet

- Lenovo ThinkPad kannettava tietokone (Windows)
- VirtualBoxissa Kali Linux
- Android matkapuhelin

#### Informaatio & Data

- Kurssimateriaali (Moodle, Tero Karvisen verkkosivut)
- GitHub-repositoriot
- Labraharjoitukset




a2) What is excluded from the scope and why
Examples of exclusions: devices belonging to other family members, smart TVs, game consoles, an employer-managed computer, and the ISP’s network on the internet side of your router.
Justify the exclusions based on factors such as ownership, manageability, lack of relevance to the course, or risk acceptance.

### Soveltamisalan rajaus

#### Kotiverkon älylaitteet

- Älytelevisio
- PlayStation pelikonsoli
- IoT-laitteet (valaistus, kaiuttimet)

Nämä eivät ole oleellisia kurssiharjoitusten osalta

#### ISP:n oma verkko

- Muodostaa ulkoisen ympäristön, johon minulla ei ole hallintaa

### Pilvipalveluiden infrastruktuuri

- Googlen & GitHubin palvelimet (ei hallintaa)



### Key interfaces and boundaries


Kotiverkko <--> Internet

Kotiverkon ja Internetin välinen rajapinta on reitittimessä. Se vastaa liikenteen reitittämisestä sisäverkon ja ISP:n välillä WAN:in avulla. 
Router toimii tärkeänä rajapintana.
Firewall kontrolloi liikennettä sisäverkon ja Internetin välillä.
WAN-yhteys yhdistää kotiverkon ISP:n verkkoon.


**Study environment ↔ Cloud services**

Opiskeluympäristö
GitHub / GitLab
Google Drive
Oppilaitoksen LMS
Mahdolliset muut kurssipalvelut

Näissä käytetään esimerkiksi:

HTTPS
MFA
SSH
API-avaimia tarvittaessa
Remote connections

Mahdollisia yhteyksiä:

VPN
SSH
RDP

Näiden yhteydessä pitäisi dokumentoida:

mistä yhteys tulee
mihin se päättyy
mitä järjestelmää se käyttää
miten yhteys suojataan
Suppliers / service providers
ISP
Router/device manufacturer
Cloud service providers
Educational institution

Näitä ei välttämättä sisällytetä omaan ISMS-scopeen kokonaisuudessaan, mutta ne muodostavat ulkoisia riippuvuuksia ja rajapintoja.

Deliverables
A scope description of ½–1 page.
One simple network and interface diagram. Boxes and arrows are sufficient. The diagram must show:
The “Home Network / Study Lab” area (in scope).
The “External Environment” (out of scope).
The interfaces and boundaries, such as internet, cloud services, and remote connections.

**Evidence Addendum**

1. Kurssin [GitHub](https://github.com/sauna41/Sovellusten-hakkerointi/tree/main) dokumentaatio


2. VirtualBox, jossa Kali Linux
      <img width="899" height="437" alt="image" src="https://github.com/user-attachments/assets/a525dcb6-4823-4928-9c1b-d025e673009e" />
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

