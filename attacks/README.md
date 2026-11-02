# Scénarios offensifs (reproductibles en lab isolé)

> ⚠️ **Cadre strict.** Ces manipulations ne doivent être exécutées que depuis `Kali-Lab`,
> à l'intérieur du réseau interne VirtualBox `lab-net`, **carte pontée déconnectée**
> (voir [`../topology/README.md`](../topology/README.md)). Elles servent uniquement à
> générer du trafic d'attaque pour **valider la détection** côté bleu. Ne jamais les
> utiliser sur un réseau que l'on ne possède pas.

Le but du dépôt n'est pas l'attaque en soi mais la **détection** : chaque scénario
ci-dessous renvoie aux règles Wazuh et filtres Wireshark de [`../detection/`](../detection/).

---

## Scénario 1 — ARP spoofing / MITM (MITRE T1557.002)

**Principe.** L'attaquant envoie des réponses ARP gratuites falsifiées pour associer sa
propre MAC à l'IP de la passerelle (et réciproquement), se plaçant en homme du milieu.

**Outils.** `dsniff` (arpspoof) ou `ettercap`, déjà présents sur Kali.

**Déroulé type.**
1. Relever les MAC/IP légitimes : `ip neigh` sur la victime, `arp -a`.
2. Activer le relais IP sur l'attaquant pour rester transparent :
   `sysctl -w net.ipv4.ip_forward=1`.
3. Lancer l'empoisonnement bidirectionnel (victime ⇄ passerelle) avec l'outil choisi,
   en ciblant **exclusivement** `10.10.10.10` et `10.10.10.1`.
4. Capturer en parallèle sur l'attaquant : `tcpdump -i eth0 -w out/arp_mitm.pcap arp or host 10.10.10.10`.
5. Arrêter l'outil (il ré-émet les associations correctes), remettre `ip_forward=0`.

**Ce que l'attaque produit (observable côté bleu).**
- Plusieurs réponses ARP associant **deux MAC différentes à la même IP** (passerelle).
- Entrée ARP de la victime pointant vers la MAC de l'attaquant.
- `arpwatch` / `arping -D` signalent un *changement d'adresse* (flip-flop).

→ Détection : [`../detection/wireshark/filtres.md`](../detection/wireshark/filtres.md) et
règle `100100` dans [`../detection/wazuh/local_rules.xml`](../detection/wazuh/local_rules.xml).

---

## Scénario 2 — Rogue DHCP (MITRE T1557)

**Principe.** Un second serveur DHCP non autorisé répond plus vite que le pfSense
légitime, distribuant une passerelle/DNS contrôlés par l'attaquant.

**Outils.** `dnsmasq` sur Kali configuré en serveur DHCP concurrent, ou le module
`dhcp` d'un outil de MITM.

**Déroulé type.**
1. Configurer une plage DHCP pirate (ex. passerelle = IP de l'attaquant, DNS = attaquant).
2. Démarrer le service **uniquement** sur l'interface `lab-net`.
3. Forcer un client à renouveler son bail (`dhclient -r && dhclient` côté victime).
4. Observer quel serveur a répondu (légitime vs pirate) dans le bail obtenu.
5. Arrêter le service pirate ; remettre la victime sur le DHCP légitime.

**Ce que l'attaque produit (observable côté bleu).**
- Deux `DHCP OFFER` pour une même requête, émis par deux IP/MAC serveurs différentes.
- Bail client dont la **passerelle/DNS** ne correspondent pas au pfSense légitime.
- Serveur DHCP répondant depuis une MAC inconnue du réseau.

→ Détection : filtres DHCP dans [`../detection/wireshark/filtres.md`](../detection/wireshark/filtres.md)
et règle `100110` dans [`../detection/wazuh/local_rules.xml`](../detection/wazuh/local_rules.xml).

---

Les commandes exactes (avec leurs paramètres réels) et leurs sorties doivent être
rejouées dans la VM puis consignées — voir les `TODO-PREUVE_*.md` dans
[`../captures/`](../captures/).
