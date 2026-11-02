# TODO-PREUVE — Déploiement et validation Wazuh

**Préalable manquant :** aucune VM/conteneur Wazuh n'existe encore dans l'inventaire
VirtualBox (seules Ubuntu-Lab, Kali-Lab, pfSense-lab, DC, Windows-LTCS-lab sont présentes).

**Manip :**
1. Installer un manager Wazuh (VM dédiée ou conteneur Docker) sur lab-net (10.10.10.20).
2. Enrôler l'agent sur `Ubuntu-Lab` ; vérifier la remontée des logs.
3. Installer `arpwatch` sur la victime (`sudo apt install arpwatch`).
4. Déposer `detection/wazuh/local_rules.xml` et `decoders.xml`, créer la liste
   `etc/lists/dhcp_authorized`, redémarrer le manager.
5. Rejouer les 2 scénarios et **vérifier que les décodeurs/règles matchent réellement**
   (ajuster les regex au format de log observé avec `wazuh-logtest`).

**Fichiers attendus :**
- `wazuh_logtest.txt` : sortie de `wazuh-logtest` prouvant le match des règles.
- captures d'alertes citées dans les autres TODO-PREUVE.
