# TODO-PREUVE — Rogue DHCP (T1557)

**Manip (depuis Kali-Lab, lab-net isolé) :**
1. Démarrer un serveur DHCP concurrent (scénario 2 de `attacks/README.md`).
2. Forcer un renouvellement de bail côté victime.
3. Capturer les deux OFFER (légitime + pirate) sous Wireshark.
4. Relever l'alerte Wazuh 100110.

**Fichiers attendus :**
- `rogue_dhcp.pcap` : capture montrant les deux DHCP OFFER (même xid).
- `rogue_dhcp_wireshark.png` : comparaison des deux server-id.
- `bail_victime.txt` : bail obtenu (passerelle/DNS pirates).
- `wazuh_alert_100110.png` : alerte réelle dans le dashboard Wazuh.
