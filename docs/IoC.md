# Indicateurs de compromission (IoC) et correspondance MITRE ATT&CK

## Tableau de synthèse détection

| Scénario | Technique MITRE | Source de détection | Règle / alerte |
|---|---|---|---|
| ARP spoofing / MITM | [T1557.002](https://attack.mitre.org/techniques/T1557/002/) | arpwatch (victime) + Wireshark | Wazuh `100100` (niveau 10) |
| Rogue DHCP | [T1557](https://attack.mitre.org/techniques/T1557/) | logs DHCP + Wireshark | Wazuh `100110` (niveau 12) |

## Indicateurs observables

### ARP spoofing / MITM
- Une IP (passerelle `10.10.10.1`) associée successivement à **deux adresses MAC**.
- Cache ARP de la victime : `10.10.10.1` pointe vers la MAC de l'attaquant.
- Rafale de **réponses ARP gratuites** non sollicitées.
- Journal arpwatch : `changed ethernet address` / `flip flop`.

### Rogue DHCP
- **Deux `DHCP OFFER`** pour un même `xid`, de deux `server-id` différents.
- Bail client avec passerelle/DNS ≠ pfSense légitime.
- Serveur DHCP répondant depuis une **MAC inconnue** de l'inventaire.

> Les valeurs réelles (adresses MAC, horodatages, captures) seront renseignées après
> rejeu dans la VM — voir les fichiers `captures/TODO-PREUVE_*.md`. Aucune valeur de ce
> tableau ne doit être inventée.
