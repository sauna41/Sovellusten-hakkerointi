_Kurssi: Sovellusten hakkerointi ja haavoittuvuudet ICI012AS3A-3004_

_Tekijä: Henri Äikäs_

_Alusta: Windows 11 / Kali Linux (VirtualBox)_

_Päivämäärä: 25.8.2026_

_Tämä raportti on osa Haaga-Helian Sovellusten hakkerointi ja haavoittuvuudet -kurssia syksyllä 2026. Tehtävänanto on h0. Opettajina toimivat Tero Karvinen & Lari Iso-Anttila._

________________________________________________________________________________________________________________________________________________________________________________________

The target environment is your own home network and the computer you use to complete the course exercises. In practice, the “organisation” consists of your household and your own IT environment, including devices, router/Wi-Fi, cloud services, lab machines, a possible NAS, and so on.

Objectives

Define a reasonable ISMS scope for your own environment.
Identify the different network boundaries and interfaces, and document your IT environment.
Aim to produce documentation detailed enough to allow your environment to be audited on paper.
Tasks

a) Basic Level. Define the ISMS scope for your home network and study lab (½–1 page). Describe at least the following:
a1) What is included in the scope
Basic home network infrastructure: router, Wi-Fi, possible network shares or NAS, printer, and IoT devices where relevant.
Devices used for the course exercises: laptop or workstation, possible virtual machines, a lab server such as a Linux VM, and a phone if you use it for MFA.
Information and data: course materials, personal notes, repositories, lab materials, and possible credentials or cryptographic keys.

a2) What is excluded from the scope and why
Examples of exclusions: devices belonging to other family members, smart TVs, game consoles, an employer-managed computer, and the ISP’s network on the internet side of your router.
Justify the exclusions based on factors such as ownership, manageability, lack of relevance to the course, or risk acceptance.

a3) Key interfaces and boundaries
Cloud services, such as GitHub/GitLab, Google Drive/OneDrive, and the institution’s learning management system (LMS).
Remote connections, such as VPN, SSH, and RDP, as well as the boundary between the home network and the internet, including the router and firewall.
Suppliers and service providers: internet service provider (ISP), device vendors, and cloud service providers.

Deliverables
A scope description of ½–1 page.
One simple network and interface diagram. Boxes and arrows are sufficient. The diagram must show:
The “Home Network / Study Lab” area (in scope).
The “External Environment” (out of scope).
The interfaces and boundaries, such as internet, cloud services, and remote connections.

Evidence Addendum
Write 1–3 lines for each item under the heading: "What evidence could I present?". Examples include a screenshot of the router configuration page, a device inventory, a list of virtual machines, a repository link, and backup configuration settings.

________________________________________________________________________________________________________________________________________________________________________________________


b) Linking the Assignment to the Standard. Identify at least two interested parties in the context of your home network.

For each interested party, describe:
- Their need, expectation, or requirement, such as security, privacy, or availability.
- The ISO 27001 requirement area to which it relates: Context, Leadership, Planning, Support, Operation, Performance Evaluation, or Improvement.
- How you would demonstrate that the requirement has been fulfilled (evidence).
- 
Examples of Interested Parties. Select the parties that are relevant to your environment:
- You – continuity of the course exercises and preservation of your data.
- Family members or housemates – privacy and assurance that the study lab does not disrupt everyday activities.
- Internet service provider – compliance with the service agreement and device usage terms.
- Cloud service providers, such as GitHub, Google, or Microsoft – account security, MFA, and compliance with the terms of service.
- Educational institution or course organiser – academic integrity and assurance that no harmful activities are conducted on the network.
- Employer, if you use the same computer or network – separation of environments and protection of employer information.
Authorities or regulators, at a general level – lawful use and appropriate processing of personal data.

Deliverable. Create a table with the following columns:
- Interested Party
- Need or Requirement
- ISO 27001 Reference (Requirement Area)
- How Compliance Is Demonstrated (Evidence)

________________________________________________________________________________________________________________________________________________________________________________________



### Lähteet

Karvinen, T. Sovellusten hakkerointi kurssimateriaali. 2026. Luettavissa: https://terokarvinen.com/application-hacking/. Luettu 25.8.2026.

