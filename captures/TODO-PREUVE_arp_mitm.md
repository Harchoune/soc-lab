# TODO-PREUVE — ARP spoofing / MITM (T1557.002)

**Manip (depuis Kali-Lab, lab-net isolé, carte pontée OFF) :**
1. Relever les MAC/IP légitimes (victime + passerelle).
2. Jouer le scénario 1 décrit dans `attacks/README.md` en capturant un pcap.
3. Côté victime : `ip neigh` avant / après pour montrer l'entrée empoisonnée.
4. Côté SIEM : déclencher et relever l'alerte Wazuh 100100.

**Fichiers attendus :**
- `arp_mitm.pcap` : capture filtrée (ARP + trafic victime) du réseau du lab uniquement.
- `arp_wireshark_duplicate.png` : trame « duplicate address detected » annotée.
- `arp_cache_victime.txt` : sortie `ip neigh` avant/après.
- `wazuh_alert_100100.png` : alerte réelle dans le dashboard Wazuh.

**Ne rien inventer** : tant que ces fichiers ne sont pas produits, l'alerte reste « à valider ».
