# Filtres d'affichage Wireshark et notes d'analyse

Capture sur l'interface `lab-net` de la victime (`Ubuntu-Lab`) ou en mode promiscuité
depuis le SIEM. Les filtres ci-dessous sont des **filtres d'affichage** (display filters).

## ARP spoofing / MITM (T1557.002)

| Objectif | Filtre |
|---|---|
| Toutes les trames ARP | `arp` |
| Réponses ARP uniquement | `arp.opcode == 2` |
| Conflit d'adresse détecté par Wireshark | `arp.duplicate-address-detected` |
| Réponses ARP non sollicitées (gratuites) | `arp.opcode == 2 && !arp.isgratuitous == 0` |
| Trafic ARP émis par la MAC suspecte | `eth.src == <MAC_attaquant>` |

**Signes à relever**
- Une même IP (la passerelle `10.10.10.1`) annoncée successivement avec deux MAC
  différentes → Wireshark affiche l'alerte *« duplicate use of … detected »*.
- Fréquence anormale de réponses ARP gratuites (plusieurs par seconde).
- Noter dans l'analyse : IP/MAC légitimes de référence, MAC de l'attaquant, horodatage
  du premier empoisonnement.

## Rogue DHCP (T1557)

| Objectif | Filtre |
|---|---|
| Tout le trafic DHCP | `dhcp` (ou `bootp`) |
| OFFERs seulement | `dhcp.option.dhcp == 2` |
| ACKs seulement | `dhcp.option.dhcp == 5` |
| Serveur DHCP identifié | `dhcp.option.dhcp_server_id == <IP_serveur>` |

**Signes à relever**
- Deux `DHCP OFFER` en réponse au même `DHCP DISCOVER` (champ `xid` identique),
  avec deux `dhcp.option.dhcp_server_id` distincts.
- OFFER dont la passerelle (`option 3`) ou le DNS (`option 6`) ≠ configuration pfSense.
- MAC source du serveur pirate absente de l'inventaire du lab.

## Export

Enregistrer les captures filtrées (parole du lab uniquement) au format `.pcap` dans
`captures/`, et une capture d'écran annotée de la trame caractéristique. Voir
`captures/TODO-PREUVE_*.md`.
