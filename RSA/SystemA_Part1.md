#Système avancé Partie 1


##Généralités : Systèmes informatiques et systèmes d'exploitation

###Structure générale d'un système informatique

####Système informatique moderne :

* CPU
* Structure de Stockage
* Périphérique d'E/S
* Communication via un **bus**

####CPU :

* ALU : Opération arithmétique
* Registres : de données d'adresse, SP, PC
* Unité de contrôle : Alimentation des données à chaque coup d'horloge


####Le bus :

* bus de données (données...)
* bus d'adresse (adresse + taille  mémoire adressable...)
* bus de commande (Signaux horloges/R/W...)

#####Ex Pentium :

* 64 lignes de données
* 32 lignes d'adresses
* quelques lignes de contrôle

####Hiérarchie de Stockage :

1. Registres (gérer par le compilateur)
2. Cache (gérer par le matériel)
3. Mémoire principale (gérer par l'OS)
4. Disque dur (gérer par l'OS)

###Périphériques d'E/S

Device controller (3 registres) :

* Command registers (read-only) : Réservé pour les commandes
* Status register (read-only) : Donne des infos sur son état
* Data-register (read/write)

Remarques :
Les instructions d'E/S peuvent être communes avec des instructions usuelles, exemple : MOV. Il peut également accéder au périphérique via un mapping

###Les registres

E : Extend
Taille d'un registre :  32 bits

* EAX : Accumulateur
* EBX : adressage indirect
* ECX : Compteur
* EDX : overflow
* ESI et EDI : Source Index et Destination Index
* EBP : Permet de parcourir le cadre d'une fonction appelée (params et variables locales)
* ESP : gestion de la pile
* Compteur programme
* Le registre EFLAG : Informe le processeur et le programmeur de l'état d'éxec d'un programme

####Registres spécialisés :
* Gestion mémoire virtuelle
* Coprocesseur arithmétique (FPU)
* Coprocesseur multimédia MMX
* Extension SIMD (fonctionnement avec plusieurs processeurs ou mémoire indépendante)

#####Les registres de segments

Registres spécialisés

* cs (code segment) pointe sur le segment contenant les instructions du programme
* ss (stack segment) pointe sur le segment contenant la pile du programme (interruption ss-prog)
* ds (data segment) pointe sur le segment contenant les données globales de statiques

3 registres plus généraux :


* ES : où on va stocker la pile
* FS : où on va stocker les données
* GS : où on va stocker le code

Rappel sur les modes d'adressage en assembleur (3) :

* Adressage registre

		addl %eax, %edx #ajoute le contenu de EAX et celui dans EDX. Res dans EDX

* Adressage immédiat

		addl $10, %eax #Ajoute 10 à la valeur contenue dans EAX. Res dans EAX

* Adressage indirect

		addl %edx, (%eax) #EAX contient l'adresse de la valeur à ajouter au contenu de EDX. Rés dans la case pointée par EAX

##Rappels sur l'OS (cf cours de Quinson)

Def d'un OS : est l'ensemble des programmes qui permettent l'exploitation du matériel. Il alloue les ressources (ressources + conflits).

Evolution :

1. Système monoprogrammé (un seul prog + un seul utilisateur)
2. Système multiprogrammé
3. Système multi-utilisateur

##Composants des systèmes d'exploitation

###Système interpréteur de commandes

* Interface entre l'utilisateur et le SE
* Inclu au noyau ou un programme spécial (UNIX, MS-DOS)

###Gestion des processus

* Création: exec, fork / terminaison : exit kill
* Suspension : wait, waitpid, sleep, pause
* Synchronisation et communication interprocessus
* Traitement des interblocages

###Gestion de la mémoire principale

Motivations :

* Seul dispositif de stockage auquel l'UC peut accèder directement
* Un programme doit être obligatoirement chargé en mémoire
* Multiprogrammation nécessite une gestion plus avancée de la mémoire

L'OS  doit :

* Savoir quelle partie de la mémoire est utilisée et par qui
* Décider des processus à charger en mémoire
* Affecter et désaffecter de l'espace mémoire

###Gestion des E/S

* Contrôle tous les périphériques d'E/S de l'ordinateur
* Fournir une interface entre les périphériques et le reste du système

**3 mécanismes :**

####Synchrone (PIO) :

* E/S bloquant
* Le processus émet une commande au périphérique et attend de manière active le changement du registre d'état
* Le périphérique traite la requête et informe le processus quand il a terminé

Remarque :

* Faible utilisation du processus

####Asynchrone :

* E/S : non bloquant
* Le processus émet une requête et continue son exécution
* Le périphérique traite la requête, une fois terminée, il émet une interruption
* Le processus s'interrompt et interroge le périphérique pour savoir si la requête a été traitée avec succès

####DMA (Direct Memory Access) :

Un moteur DMA gère le transfert des données entre la mémoire principale et un périphérique

Son fonctionnement :

1. Le processeur envoie une requête de transfert à un moteur DMA

2. Le moteur DMA interrompt le processeur lorsque le transfert est terminé

Remarques

* Moteur DMA placé sur le bus E/S ou sur les périphériques
* Nécéssaire pour multiplexage
* Peu utilisé
* Améliroation de l'utilisation du CPU

####Quelle stratégie choisir ?

* Si traitement long -> **Asynchrone**
* Si grande quantité de données -> **DMA**
* Si requête simple et rapide -> **Synchrone**

###Gestion des fichiers

* Vue logique est uniforme du contenu des différents dispositifs de stockage

* Des primitives de création / manipulation de fichiers (mkdir...)

###Système de protection

* Protection **matérielle** (Au niveau du processeur)
	* Intraprocessus -> Mode utilisateur / privilégié
	* Exemple : Protection des E/S, Protection du vecteur d'interruption, Protection Mémoire et UC (évite les boucles infinies)
* Protection **logicielle** -> protection des espaces d'adressage


##Conception des OS

###Le noyau
* Premier logiciel chargé en mémoire
* Partie critique et fondamentale
* Gère les ressources et permet la communication logiciel/matériel

Ses fonctions :

* Chargement et exécution des processus
* Gestion des E/S
* Interface de programmation
* Gestion de la mémoire

###Structure simple MS-DOS
* Maximoume de fonctions -> minimoume d'espace
* Non structuré

###Séparation en couche

* Division en couches :
	* La plus haute : interface utilisateur
	* La plus basse : matériel

###Systèmes monolithiques

* L'ensemble des fonctions du système sont regroupées dans un seul bloc

Avantages :

* Facile à développer et concevoir
* Rapide

Inconvénients :

* Code volumineux
* Sécurité difficile à intégrer
* Utilisation non optimisée de la mémoire

###Systèmes à micronoyau

* Le noyau ne contient que les fonctions essentielles (ordonnancement)
* Les autres services sont fournis par des programmes systèmes

#####Avantages :

* Modularité
* Portabilité
* Sécurité
* Configuration

#####Inconvénients

* Performances réduites
* Grand nombre d'appels systèmes

###Les noyaux modulaires

* Les parties principales du système sont regroupées dans un seul bloc
* Les autres sont réparties dans des blocs de fonctions auxiliaires

#####Avantages :
* Avantages du micronoyau + performances
* Chargement/déchargement dynamique
* Execution en mode noyau du code ajouté
* Architecture présente dans la majorité des OS GNU/Linux, BSD, Solaris

##Organisation du noyau UNIX

###Architecture du noyau UNIX

**3 grandes parties : **
* L'interface des appels système
* Le sous-système de gestion des processus (ordonnancement, mémoire)
* Le sous-système de gestion des fichiers

Le noyau UNIX est découpé en 2 grandes parties qui coopèrent :

* **Partie supérieure**
	* Appels système (services fournis aux processus utilisateurs) => **synchrone**
* **Partie inférieure**
	* Interruptions matérielles => **asynchrones**

###Invocation des services système

####Interruptions matérielles et exceptions

* Interruption est provoquée par un signal venant du monde extérieur (top d'horloge ou fin d'une E/S)
* Une exception est provoquée par un dysfonctionnement du programme en cours d'exécution (division par 0, faute de page...)
* Chaque interruption ou exception dispose d'un handler qui prend en charge l'évènement correspondant =>  **Table des vecteurs d'interruptions ou IDT**

Exception sous Linux :

1. Arrivée de l'exception / interruption
2. Sauvegarde du contexte actuel
3. Accès à la table des vecteurs d'interruptions
4. Exécution du sous-programme en mode noyau
5. Rétablissement du contexte et reprise de l'ancien programme en mode utilisateur ret_from_exception()

Remarques :

* La plupart des exceptions issues du CPU sont interprétées par Linux
	* Division par zéro
* Elles peuvent réentrainer la rééxecution du programme en cause (def de page)



Interruption sous Linux

1. Sauvegarde de la valeur de l'IRQ et le contenu de la pile
2. Envoi d'un ACK au PIC (lui permet de traiter d'autres interruptions)
3. Exection des routines de traitement d'interruption
4. invocation de ret_from_intr()

####Les appels système

* Interface entre SE et programme utilisateur
* Peut être réalisé à l'aide d'une interruption logicielle (déclenchée par un programme à l'aide d'une instruction trap, syscall)
* On rend la main au processus = préemption
* Passage d'informations nécessaires par registres, pile ou mémoire

##QCM

Un système mono-utilisateur monoprogrammé : MS-DOS

Les fonctions suivantes sont assurées par l'OS :
* LA gestion des E/S V
* La navigation sur internet F
* Le partage du matériel en user V
* Fournir une interface utilisateur F
* Fournir des éditeurs de texte F
* Éviter les interférences entre user F

La gestion de fichiers dans un OS inclut
* La création et supressuin des fichiers V
* Une protection matérielle F (protection logicielle, droit rwx)
* La possibilité de sauvegarde d'un fichier sur un support permanent V
* la correspondance entre la vue logique (user) et la vue physique (support de stockage) V

La gestion des processus inclut :
* la création et terminaison de processus V
* l'ordonnancement des processus V
* la protection intra et interprocessus F

* Linux permet le multithreading, mais ne permet pas la création d'un seul thread par processus. V
* Une JFRE est un exemple d'un système où un processus a plusieurs threads. F

* Le noyau est la partie fondamentale d'un OS, elle implémente les appels système (entre autres) V
* L'interface utilisateur (textuel ou graphique) est fournie par le noyau F
inclure trop de fonctionnalités dans un noyau peut se traduire par une moindre flexibilité V


* un noyau qui contient la totalité de l'os est un noyau : monolithique

* Un noyau qui se voit attribuer que très peu de fonctions gestion de l'espace d'adressage l'IPC et un ordonnancement basique est un noyau : micro noyau

Un système monolithique
* est facile à concevoir et à développer V
* utilise inefficacement la mémoire V
* s'exécute rapidement V
* permet plus de sécurité F

* SIMD stands for : Single Instruction Stream Multiple Data Stream


* Une architecture MIMD est plus facile à réaliser qu'une architecture SIMD : Faux

* Une E/S asynch est non bloquante V
* Une ES synchrone engendre une faible utilisation de l'UC V
* Une interruption est déclenchée à la fin d'une E/S sycnhrone F
* Une interruption est déclenchée à la fin d'une E/S asynchrone V
* Une interruption est déclenchée à la fin d'une E/S DMA V

* Un temps de traitement long par le périphérique : E/S asynchrone
* Une grande quantité de données à transférer: DMA
* une requête simple : ES synchrone

* une invocation d'un système appel système
* une réaction à un évènement externe : interruption
* prise en charge d'une erreur lors de l'exécution d'une instruction une exception

Dans une architecture x86 pour la réalisation dans appels système linux
* utilise une interruption logicielle
* Les paramètres sont passés le registre
* Le nombre d'appels système n'est pas limité à 6

* Un appel système est : asynchrone

* pour un processus un appel système est : volontaire

* une interruption matérielle est : asynchrone

* pour un processus, une interrupt matérielle est : involontaire
