# Lab : DHCP Starvation sur routeur Cisco (GNS3)

Rapport de laboratoire réalisé dans le cadre du **Mentorat Cybersécurité CYB6 (FORCE N)**.
Cette version reproduit l'attaque **DHCP Starvation** sur un **véritable routeur Cisco** émulé
sous **GNS3** (image IOS `c7200`), dans la maquette d'origine du cours. Elle complète la
version où le serveur DHCP était une machine **VyOS** sous VirtualBox.

> ⚠️ Toutes les manipulations ont été effectuées sur une topologie GNS3 locale et isolée, sans
> accès à un réseau de production, à des fins strictement pédagogiques. N'effectuez jamais ces
> techniques sur un système sans autorisation écrite.

## Environnement

| Équipement | Rôle | IP |
|---|---|---|
| R1 (Cisco c7200) | Serveur DHCP / passerelle | `192.168.6.5` |
| Switch1 | Switch (Ethernet switch GNS3) | — |
| vm-kali | Attaquant (client DHCP) | `192.168.6.11` |

Topologie : `R1 (c7200) — Switch1 — vm-kali`, réseau `192.168.6.0/24`. Le routeur est émulé à
partir de l'image IOS `c7200-adventerprisek9-mz.153-3.XB12` fournie par l'encadrant ; la Kali
est une VM VirtualBox intégrée à GNS3.

## Contenu du lab

1. **Tâche 1. Mise en place de la topologie GNS3** : import de l'image IOS c7200, intégration
   de la VM Kali, câblage de la topologie.
2. **Tâche 2. Configuration du serveur DHCP Cisco** : configuration du routeur (pool L3SIMAC),
   vérification que Kali reçoit une adresse, et tests de connectivité dans les deux sens.
3. **Tâche 3. Installation de yersinia et Wireshark** : procédure d'installation (Partie B) et
   usage de l'interface texte `yersinia -I`.
4. **Tâche 4. Attaque DHCP Starvation** : flood de DISCOVER avec yersinia, preuve de
   l'épuisement du pool via `show ip dhcp binding`, analyse Wireshark, et comparaison du
   comportement Cisco et VyOS/Kea.
5. **Annexe** : récapitulatif des commandes utilisées, de leurs rôles et de leurs options.

## Structure du dépôt

```
.
├── Rapport_Lab_DHCP_Starvation_Cisco.tex       # Source LaTeX du rapport
├── Rapport_Lab_DHCP_Starvation_Cisco.pdf       # Rapport compilé
├── Rapport_Lab_DHCP_Starvation_Cisco.pdf.asc   # Signature GPG détachée du PDF
├── signature.tex                               # Bloc signature (auteur + empreinte GPG)
├── HAMADY_CISSE_public_key.asc                 # Clé publique GPG de l'auteur
├── images/                                     # Captures d'écran du lab
├── LICENSE
└── README.md
```

## Compilation

```bash
pdflatex Rapport_Lab_DHCP_Starvation_Cisco.tex
pdflatex Rapport_Lab_DHCP_Starvation_Cisco.tex   # 2e passe pour le sommaire et l'annexe
```

## Vérifier l'authenticité

Le PDF du rapport est signé numériquement (GPG) :

```bash
gpg --import HAMADY_CISSE_public_key.asc
gpg --verify Rapport_Lab_DHCP_Starvation_Cisco.pdf.asc Rapport_Lab_DHCP_Starvation_Cisco.pdf
```

La vérification doit afficher `Good signature from "HAMADY CISSE ..."`. L'empreinte de la clé
est :

```
A6F4 0DFD EE3E B7AE 80B3  6E77 361C 9E02 B790 303E
```

## Auteur

HAMADY CISSE, Mentorat Cybersécurité CYB6, Groupe 3.

## Licence

© 2026 HAMADY CISSE. Ce travail est publié sous licence
**[Creative Commons Attribution - Pas d'Utilisation Commerciale 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/)**.
Voir le fichier [`LICENSE`](LICENSE) pour les termes complets.
