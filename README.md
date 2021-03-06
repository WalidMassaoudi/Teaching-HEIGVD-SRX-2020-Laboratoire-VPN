# Teaching-HEIGVD-SRX-2020-Laboratoire-VPN

**Ce travail de laboratoire est à faire en équipes de 3 personnes**

**Pour ce travail de laboratoire, il est votre responsabilité de chercher vous-même sur internet, le support du cours ou toute autre source (vous avez aussi le droit de communiquer avec les autres équipes), toute information relative au sujet VPN, le logiciel eve-ng, les routeur Cisco, etc que vous ne comprenez pas !**

**ATTENTION : Commencez par créer un Fork de ce repo et travaillez sur votre fork.**

Clonez le repo sur votre machine. Vous pouvez répondre aux questions en modifiant directement votre clone du README.md ou avec un fichier pdf que vous pourrez uploader sur votre fork.

**Le rendu consiste simplement à répondre à toutes les questions clairement identifiées dans le text avec la mention "Question" et à les accompagner avec des captures. Le rendu doit se faire par une "pull request". Envoyer également le hash du dernier commit et votre username GitHub par email au professeur et à l'assistant**

**N'oubliez pas de spécifier les noms des membres du groupes dans la Pull Request ainsi que dans le mail de rendu !!!**


## Echéance 

Ce travail devra être rendu le dimanche après la fin de la 2ème séance de laboratoire, soit au plus tard, **le 11 mai 2020, à 23h59.**


## Introduction

Dans ce travail de laboratoire, vous allez configurer des routeurs Cisco émulés, afin de mettre en œuvre une infrastructure sécurisée utilisant des tunnels IPSec.

### Les aspects abordés

-	Contrôle de fonctionnement de l’infrastructure
-	Contrôle du DHCP serveur hébergé sur le routeur
-	Gestion des routeurs en console
-	Capture Sniffer avec filtres précis sur la communication à épier
-	Activation du mode « debug » pour certaines fonctions du routeur
-	Observation des protocoles IPSec
 
 
## Matériel

La manière la plus simple de faire ce laboratoire est dans les machines des salles de labo. Le logiciel d'émulation c'est eve-ng. Vous trouverez un [guide très condensé](files/Fonctionnement_EVE-NG.pdf) pour l'utilisation de eve-ng ici.

Vous pouvez faire fonctionner ce labo sur vos propres machines à condition de copier la VM eve-ng. A présent, la manière la plus simple d'utiliser eve-ng est de l'installer sur Windows (mais, il est possible de le faire fonctionner sur Mac OS et sur Linux...).

**Tuto d'installation** de la VM eve-ng : https://www.eve-ng.net/index.php/documentation/installation/virtual-machine-install/

**Récupération de la VM pré-configurée** (vous ne pouvez pas utiliser la versión qui se trouve sur le site de eve-ng) : vous la trouverez sur \\eistore1\cours\iict\SRX\LaboVPn

Il est conseillé de passer la VM en mode "Bridge" si vous avez des problèmes. Le mode NAT **devrait** aussi fonctionner.

Les user-password en mode terminal sont : "root" | "eve"

Les user-password en mode navigateur sont : "admin" | "eve"

Ensuite, terminez la configuration de la VM, connectez vous et récupérez l'adresse ip de la machine virtuelle.

Utilisez un navigateur internet (hors VM) et tapez l'adresse IP de la VM.


## Fichiers nécessaires 

Tout ce qu'il vous faut c'est un [fichier de projet eve-ng](files/eve-ng_Labo_VPN_SRX.zip), que vous pourrez importer directement dans votre environnement de travail.


## Mise en place

Voici la topologie qui sera simulée. Elle comprend deux routeurs interconnectés par l'Internet. Les deux réseaux LAN utilisent les services du tunnel IPSec établi entre les deux routeurs pour communiquer.

Les "machines" du LAN1 (connecté au ISP1) sont simulées avec l'interface loopback du routeur. Les "machines" du LAN2 sont représentées par un seul ordinateur.  

![Topologie du réseau](images/topologie.png)

Voici le projet eve-ng utilisé pour implémenter la topologie. Le réseau Internet (nuage) est simulé par un routeur. 

![Topologie eve-ng](images/topologie-eve-ng.png)


## Manipulations

- Commencer par importer le projet dans eve-ng.
- Prenez un peu de temps pour vous familiariser avec la topologie présentée dans ce guide et comparez-la au projet eve-ng. Identifiez les éléments, les interconnexions et les adresses IPs.
- À tout moment, il vous est possible de sauvegarder la configuration dans la mémoire de vos routeurs :
	- Au Shell privilégié (symbole #) entrer la commande suivante pour sauvegarder la configuration actuelle dans la mémoire nvram du routeur : ```wr```
	- Vous **devez** faire des sauvegardes de la configuration (exporter) dans un fichier - c.f. [document guide eve-ng](files/Fonctionnement_EVE-NG.pdf) 


### Vérification de la configuration de base des routeurs
Objectifs:

Vérifier que le projet a été importé correctement. Pour cela, nous allons contrôler certains paramètres :

- Etat des interfaces (`show interface`)
- Connectivité (`ping`, `show arp`)
- Contrôle du DHCP serveur hébergé sur R2


### A faire...

- Contrôlez l’état de toutes vos interfaces dans les deux routeurs et le routeur qui simule l'Internet - Pour contrôler l’état de vos interfaces (dans R1, par exmeple) les commandes suivantes sont utiles :

```
R1# show ip interface brief
R1# show interface <interface-name>
R1# show ip interface <interface-name>
```

Un « status » différent de `up` indique très souvent que l’interface n’est pas active.

Un « protocol » différent de `up` indique la plupart du temps que l’interface n’est pas connectée correctement (en tout cas pour Ethernet).

**Question 1: Avez-vous rencontré des problèmes ? Si oui, qu’avez-vous fait pour les résoudre ?**

---

**Réponse :** Non. La configuration démarré est correcte. L'interface eth0/0 de RX1 est up ainsi que son protocole. 
Les interfaces eth0/0 et eth0/1 sont up ainsi que leurs protocoles. 

---


- Contrôlez que votre serveur DHCP sur R2 est fonctionnel - Contrôlez que le serveur DHCP préconfiguré pour vous sur R2 a bien distribué une adresse IP à votre station « VPC ».


Les commandes utiles sont les suivantes :

```
R2# show ip dhcp pool 
R2# show ip dhcp binding
```

Côté station (VPC) vous pouvez valider les paramètres reçus avec la commande `show ip`. Si votre station n’a pas reçu d’adresse IP, utilisez la commande `ip dhcp`.

- Contrôlez la connectivité sur toutes les interfaces à l’aide de la commande ping.

Pour contrôler la connectivité les commandes suivantes sont utiles :

```
R2# ping ip-address
R2# show arp (utile si un firewall est actif)
```

Pour votre topologie il est utile de contrôler la connectivité entre :

- R1 vers ISP1 (193.100.100.254)
- R2 vers ISP2 (193.200.200.254)
- R2 (193.200.200.1) vers RX1 (193.100.100.1) via Internet
- R2 (172.17.1.1) et votre poste « VPC »

**Question 2: Tous vos pings ont-ils passé ? Si non, est-ce normal ? Dans ce cas, trouvez la source du problème et corrigez-la.**

---

**Réponse :**  Oui, la configuration est correcte, après que VPC prend une adresse ip.

---

- Activation de « debug » et analyse des messages ping.

Maintenant que vous êtes familier avec les commandes « show » nous allons travailler avec les commandes de « debug ». A titre de référence, vous allez capturer les messages envoyés lors d’un ping entre votre « poste utilisateur » et un routeur. Trouvez ci-dessous la commande de « debug » à activer.

Activer les messages relatif aux paquets ICMP émis par les routeurs (repérer dans ces messages les type de paquets ICMP émis - < ICMP: echo xxx sent …>)

```
R2# debug ip icmp
```
Pour déclencher et pratiquer les captures vous allez « pinger » votre routeur R1 avec son IP=193.100.100.1 depuis votre « VPC ». Durant cette opération vous tenterez d’obtenir en simultané les informations suivantes :

-	Une trace sniffer (Wireshark) à la sortie du routeur R2 vers Internet. Si vous ne savez pas utiliser Wireshark avec eve-ng, référez-vous au document explicatif eve-ng. Le filtre de **capture** (attention, c'est un filtre de **capture** et pas un filtre d'affichage) suivant peut vous aider avec votre capture : `ip host 193.100.100.1`. 
-	Les messages de R1 avec `debug ip icmp`.


**Question 3: Montrez vous captures**

---

**Screenshots :**  ![screen3](images/pingVPC_R1_wireshar_R1Debug.png)

---

## Configuration VPN LAN 2 LAN

**Il est votre responsabilité de chercher vous-même sur internet toute information relative à la configuration que vous ne comprenez pas ! La documentation CISCO en ligne est extrêmement complète et le temps pour rendre le labo est plus que suffisant !**

Nous allons établir un VPN IKE/IPsec entre le réseau de votre « loopback 1 » sur R1 (172.16.1.0/24) et le réseau de votre « VPC » R2 (172.17.1.0/24). La terminologie Cisco est assez « particulière » ; elle est listée ici, avec les étapes de configuration, qui seront les suivantes :

- Configuration des « proposals » IKE sur les deux routeurs (policy)
- Configuration des clefs « preshared » pour l’authentification IKE (key)
- Activation des « keepalive » IKE
- Configuration du mode de chiffrement IPsec
- Configuration du trafic à chiffrer (access list)
- Activation du chiffrement (crypto map)


### Configuration IKE

Sur le routeur R1 nous activons un « proposal » IKE. Il s’agit de la configuration utilisée pour la phase 1 du protocole IKE. Le « proposal » utilise les éléments suivants :

| Element          | Value                                                                                                        |
|------------------|----------------------------------------------------------------------------------------------------------------------|
| Encryption       | AES 256 bits    
| Signature        | Basée sur SHA-1                                                                                                      |
| Authentification | Preshared Key                                                                                                        |
| Diffie-Hellman   | avec des nombres premiers sur 1536 bits                                                                              |
| Renouvellement   | des SA de la Phase I toutes les 30 minutes                                                                           |
| Keepalive        | toutes les 30 secondes avec 3 « retry »                                                                              |
| Preshared-Key    | pour l’IP du distant avec le texte « cisco-1 », Notez que dans la réalité nous utiliserions un texte plus compliqué. |


Les commandes de configurations sur R1 ressembleront à ce qui suit :

```
crypto isakmp policy 20     * Définir une stratégie IKE et entrer dans  le mode de  configuration config-isakmp ,le numéro est unique ,il indentifie la politique du sécurité  *
  encr aes 256              * Spécifie l'algorithme de chiffrement (sans spécification DES est utilisée par defaut).ici en utilise AES 256 bits.
  authentication pre-share  * Spécifiez la méthode d'authentification: clés pré-partagées (preshare), nonces chiffrés RSA1 (rsa-encr) ou signatures RSA (rsa-slg). Cet exemple configure les clés pré-partagées. La valeur par défaut est les signatures RSA.*
  hash sha                  * Spécifiez l'algorithme de hachage: Message Digest 5 (MD5 [md5]) ou Secure Hash Algorithm (SHA [sha]). Cet exemple configure SHA, qui est la valeur par défaut.*
  group 5                   * Les groupes Diffie-Hellman (DH) déterminent la force de la clé utilisée dans le processus d'échange de clés. Des numéros de groupe plus élevés que 1024 sont plus sûrs ,ici c'est le groupe 5 donc 1536-bit*
  lifetime 1800             * Spécifiez la durée de vie de l'association de sécurité - en secondes (la durée minimum recommendée est plus que 900 secondes)*
crypto isakmp key cisco-1 address 193.200.200.1 no-xauth * Spécifie au niveau du pair local la clé partagée à utiliser avec un pair distant particulier.Puisque  l'homologue distant a spécifié son identité ISAKMP avec une adresse,on va utiliser le mot clé address, sinon on utilise le mot-clé hostname .no-xauth - Empêche le routeur de demander au pair des informations Xauth. *

crypto isakmp keepalive 30 3
```

Sur le routeur R2 nous activons un « proposal » IKE supplémentaire comme suit :

```
crypto isakmp policy 10
  encr 3des
  authentication pre-share
  hash md5
  group 2
  lifetime 1800
crypto isakmp policy 20
  encr aes 256
  authentication pre-share
  hash sha
  group 5
  lifetime 1800
crypto isakmp key cisco-1 address 193.100.100.1 no-xauth
crypto isakmp keepalive 30 3
```

Vous pouvez consulter l’état de votre configuration IKE avec les commandes suivantes. Faites part de vos remarques :

**Question 4: Utilisez la commande `show crypto isakmp policy` et faites part de vos remarques :**

---

**Réponse :** 
R1:
```
RX1(config)#do show crypto isakmp policy

Global IKE policy
Protection suite of priority 20
        encryption algorithm:   AES - Advanced Encryption Standard (256 bit keys        ).
        hash algorithm:         Secure Hash Standard
        authentication method:  Pre-Shared Key
        Diffie-Hellman group:   #5 (1536 bit)
        lifetime:               1800 seconds, no volume limit
```
R2 :
```
RX2(config)#do show crypto isakmp policy

Global IKE policy
Protection suite of priority 10
        encryption algorithm:   Three key triple DES
        hash algorithm:         Message Digest 5
        authentication method:  Pre-Shared Key
        Diffie-Hellman group:   #2 (1024 bit)
        lifetime:               1800 seconds, no volume limit
Protection suite of priority 20
        encryption algorithm:   AES - Advanced Encryption Standard (256 bit keys                         ).
        hash algorithm:         Secure Hash Standard
        authentication method:  Pre-Shared Key
        Diffie-Hellman group:   #5 (1536 bit)
        lifetime:               1800 seconds, no volume limit
```
La commande affiche les Policy IKE. Le résultat obtenu correspond à nos
configurations.

Nos remarques : 

- La première Policy de R2 ne sert à rien ici. On va utiliser la deuxième
puisque, c’est elle qui correspond à la policy de R1.

- MD5 est cassé.

- Diffie-Hellman group 5 n’est pas recommandé, vaut mieux utiliser le group 14.

---


**Question 5: Utilisez la commande `show crypto isakmp key` et faites part de vos remarques :**

---

**Réponse :** 
R1:
```
RX1(config)#do show crypto isakmp key
Keyring      Hostname/Address                            Preshared Key

default      193.200.200.1                               cisco-1

```
R2:
```
RX2(config)#do   show crypto isakmp key
Keyring      Hostname/Address                            Preshared Key

default      193.100.100.1                               cisco-1

```
 L'utilisation de la même clé en clair dans les 2 routeurs n'est pas recommendé à notre avis.
 
---

## Configuration IPsec

Nous allons maintenant configurer IPsec de manière identique sur les deux routeurs. Pour IPsec nous allons utiliser les paramètres suivants :

| Paramètre      | Valeur                                  |
|----------------|-----------------------------------------|
| IPsec avec IKE | IPsec utilisera IKE pour générer ses SA |
| Encryption     | AES 192 bits                            |
| Signature      | Basée sur SHA-1                         |
| Proxy ID R1    | 172.16.1.0/24                           |
| Proxy ID R2    | 172.17.1.0/24                           |

Changement de SA toutes les 5 minutes ou tous les 2.6MB

Si inactifs les SA devront être effacés après 15 minutes

Les commandes de configurations sur R1 ressembleront à ce qui suit :

```
crypto ipsec security-association lifetime kilobytes 2560
crypto ipsec security-association lifetime seconds 300
crypto ipsec transform-set STRONG esp-aes 192 esp-sha-hmac 
  ip access-list extended TO-CRYPT
  permit ip 172.16.1.0 0.0.0.255 172.17.1.0 0.0.0.255
crypto map MY-CRYPTO 10 ipsec-isakmp 
  set peer 193.200.200.1
  set security-association idle-time 900
  set transform-set STRONG 
  match address TO-CRYPT
```

Les commandes de configurations sur R2 ressembleront à ce qui suit :

```
crypto ipsec security-association lifetime kilobytes 2560
crypto ipsec security-association lifetime seconds 300
crypto ipsec transform-set STRONG esp-aes 192 esp-sha-hmac 
  mode tunnel
  ip access-list extended TO-CRYPT
  permit ip 172.17.1.0 0.0.0.255 172.16.1.0 0.0.0.255
crypto map MY-CRYPTO 10 ipsec-isakmp 
  set peer 193.100.100.1
  set security-association idle-time 900
  set transform-set STRONG 
  match address TO-CRYPT
```

Vous pouvez contrôler votre configuration IPsec avec les commandes suivantes :

```
show crypto ipsec security-association
show crypto ipsec transform-set
show access-list TO-CRYPT
show crypto map
```

## Activation IPsec & test

Pour activer cette configuration IKE & IPsec il faut appliquer le « crypto map » sur l’interface de sortie du trafic où vous voulez que l’encryption prenne place. 

Sur R1 il s’agit, selon le schéma, de l’interface « Ethernet0/0 » et la configuration sera :

```
interface Ethernet0/0
  crypto map MY-CRYPTO
```

Sur R2 il s’agit, selon le schéma, de l’interface « Ethernet0/0 » et la configuration sera :

```
interface Ethernet0/0
  crypto map MY-CRYPTO
```


Après avoir entré cette commande, normalement le routeur vous indique que IKE (ISAKMP) est activé. Vous pouvez contrôler que votre « crypto map » est bien appliquée sur une interface avec la commande `show crypto map`.

```

RX1#show crypto map
Crypto Map IPv4 "MY-CRYPTO" 10 ipsec-isakmp
        Peer = 193.200.200.1
        Extended IP access list TO-CRYPT
            access-list TO-CRYPT permit ip 172.16.1.0 0.0.0.255 172.17.1.0 0.0.0.255
        Current peer: 193.200.200.1
        Security association lifetime: 2560 kilobytes/300 seconds
        Security association idletime: 900 seconds
        Responder-Only (Y/N): N
        PFS (Y/N): N
        Mixed-mode : Disabled
        Transform sets={
                STRONG:  { esp-192-aes esp-sha-hmac  } ,
        }
        Interfaces using crypto map MY-CRYPTO:
                Ethernet0/0

        Interfaces using crypto map NiStTeSt1:
RX2#show crypto map
Crypto Map IPv4 "MY-CRYPTO" 10 ipsec-isakmp
        Peer = 193.100.100.1
        Extended IP access list TO-CRYPT
            access-list TO-CRYPT permit ip 172.17.1.0 0.0.0.255 172.16.1.0 0.0.0.255
        Current peer: 193.100.100.1
        Security association lifetime: 2560 kilobytes/300 seconds
        Security association idletime: 900 seconds
        Responder-Only (Y/N): N
        PFS (Y/N): N
        Mixed-mode : Disabled
        Transform sets={
                STRONG:  { esp-192-aes esp-sha-hmac  } ,
        }
        Interfaces using crypto map MY-CRYPTO:
                Ethernet0/0

        Interfaces using crypto map NiStTeSt1:

```

Pour tester si votre VPN est correctement configuré vous pouvez maintenant lancer un « ping » sur la « loopback 1 » de votre routeur RX1 (172.16.1.1) depuis votre poste utilisateur (172.17.1.100). De manière à recevoir toutes les notifications possibles pour des paquets ICMP envoyés à un routeur comme RX1 vous pouvez activer un « debug » pour cela. La commande serait :

```
debug ip icmp
```

Pensez à démarrer votre sniffer sur la sortie du routeur R2 vers internet avant de démarrer votre ping, collectez aussi les éventuels messages à la console des différents routeurs. 

**Question 6: Ensuite faites part de vos remarques dans votre rapport. :**

---

**Réponse :**  
 ![screenQ6](images/q6screen.png)

Nos remarques:
- Les paquets ISAKMP correspondent à la phase d'échange de clées entre les deux routeurs.
- Les paquets ESP correspondent aux pings désormais chiffré et encapsulé.
- Pour améliorer les perfomances, il vaudrait mieux augmenté les durées de vie (taille et temps).
---

**Question 7: Reportez dans votre rapport une petite explication concernant les différents « timers » utilisés par IKE et IPsec dans cet exercice (recherche Web). :**

---

**Réponse :**  
```     
Chaque clé est sensible à Brute Force.Si tout ce qu'on utilise est une seule clé et qu'un attaquant parvient à forcer notre seule clé,alors toutes
les  données que nous  avons envoyées ou reçues sont compromises.Pour limiter la portée du compromis potentiel, IPsec effectue des opérations de "rekey",
de sorte que si une force brute réussit, au mieux seulement pendant  8 heures de vos données sont compromises.  
IKE : 
    - lifetime : Spécifier la durée de vie de l'association de sécurité - en secondes (la durée minimum recommendée est plus que 900 secondes).
    - keepalive: Définir un intervalle nat keepalive à utiliser avec les homologues IOS ,le premier numéro indique le nombre de secondes entre  keep alives (minimum 10 secondes), le deuxième est utilisé 
                 pour calculer le nombre de secondes que le routeur doivent attendre avant que les IKE's se rénogocient .
IPsec :
    - lifetime : Un routeur CISCO permet actuellement la configuration des durées de vie des SA IPsec. Les durées de vie peuvent être configuré globalement
               ou par carte cryptographique. Il existe deux durées de vie: une durée de vie «temporisée» et un durée de vie.«volume de trafic» Une association 
               de sécurité expire après la première de ces durées de vie.
    -idle-time : la durée pendant laquelle la connexion vpn est inactive, c'est-à-dire. aucune activité vue sur le tunnel, avant qu'il ne soit déconnecté.
  ```
---


# Synthèse d’IPsec

En vous appuyant sur les notions vues en cours et vos observations en laboratoire, essayez de répondre aux questions. À chaque fois, expliquez comment vous avez fait pour déterminer la réponse exacte (capture, config, théorie, ou autre).


**Question 8: Déterminez quel(s) type(s) de protocole VPN a (ont) été mis en œuvre (IKE, ESP, AH, ou autre).**

---

**Réponse : 

D'après la configuration effectué pour les routeurs RX1 et RX2, on a utilisé le protocole IKE (en utilisant ISAKMP pour la phase de négotiation des clés , et __cisco-1__ commre preshared key). Pour la phase d'authentification,intégrité, et chiffrement de données on a utilisé ESP (avec AES192  pour chiffrer) comme c'est montré sur la capture Wireshark de la question six .**

---


**Question 9: Expliquez si c’est un mode tunnel ou transport.**

---

**Réponse :**  
Le mode utilisé dans ce laboratoire est le mode tunnel ,Comme on a vue dans le cours ,Il est le mode par default pour IPsec.
.En plus la configuration est faite depuis un routeur vers un routeur (avec mode tunnel)ce que n'est pas le cas pour un  mode de transport PSec qui est  généralement utilisé pour les communications de bout en bout,par exemple, pour la communication entre un client et un serveur ou entre un poste de travail et une passerelle .Dans la  configuration des routeur aussi on a utilisé des proxy pour que le routeur  agit en tant que proxy pour les hôtes derrière lui. .

---


**Question 10: Expliquez quelles sont les parties du paquet qui sont chiffrées. Donnez l’algorithme cryptographique correspondant.**

---

**Réponse : 

En s'appuyant sur le principe de IPsec via mode tunnel vue au cours l'ensemble du paquet IP d'origine est protégé (chiffré) par IPSec, c'est à dire l'entete IP originale avec les données et le ESP Trailer(pading ,next header)  est chiffré grace à l'algorithme AES.
```
RX1#show crypto map
    ...
        Transform sets={
                STRONG:  { esp-192-aes esp-sha-hmac  } ,
        }
  ...    
  ```
En peut remarquer aussi que le payload (ce qui présente le paquet original)du paquet est chiffré dans la capture wireshark dans la question 6.

---


**Question 11: Expliquez quelles sont les parties du paquet qui sont authentifiées. Donnez l’algorithme cryptographique correspondant.**

---

**Réponse :

Comme on a vu en cours, le mode tunnel nous permet d'authenitifier la totalité du packet ce qui englobe les données , entête IP orig + des parties du nouvel entête IP. L'algorithme est HMAC avec SHA**  

---


**Question 12: Expliquez quelles sont les parties du paquet qui sont protégées en intégrité. Donnez l’algorithme cryptographique correspondant.**

---

**Réponse :

la protection de l'intègrité du paquet concerne la totalité du paquet authentifié précédament dans la question 11 telle que l'entete IP du nouveau paquet n'est y pas compris.L'ICV (Integrity Check Value) est le résultat du processus d'intégrité,Cela implique normalement l'algorithme HMAC (Hash Message Authentication Code) et la fonction du hashage  SHA-1.on peut confirmer avec la sortie d la commande   show crypto map :
```
Transform sets={
        STRONG:  { ... esp-sha-hmac  } ,
}

---
