#Système avancé


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
* Unité de contrôle : Alimentation des données à chaque coup d'horloges


####Le bus :

* bus de données (données...)
* bus d'adresse (adresse + taille  mémoire adressable...)
* bus de commande (Signaux horloges/R/W...)

#####Ex Pentium :

* 64 lignes de données
* 32 lignes d'adresses
* quelques lignes de contrôle

####Hiérarchie de Stockage :

1. registres (gérer par le compilateur)
2. cache (gérer par le matériel)
3. Mémoire principale (gérer par l'OS)
4. disque dur (gérer par l'OS)

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
* EBP : Permet de parcourir le cadre d'une fonction appelée (parms et variables locales)
* ESP : gestion de la pile
* Compteur programme
* Le registre EFLAG : Informe le processeur et le programmeur de l'état d'éxec d'un programme

####Registres spécialisés :
* gestion mem virtuelle
* coprocesseur arithmétique (FPU)
* Coprocésseure multimédia MMX
* Extension SIMD (fonctionnement avec plusieurs processeurs ou mémoire indépendante)

#####Les registres de segments

Registres spécialisés

*cs (code segment) pointe sur le segment contenant les instructions du programme
*ss (stack segment) pointe sur le segment contenant la pile du programme (interruption ss-prog)
*ds (data segment) pointe sur le segment contenant les données globales de statiques

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

* création: exec, fork / terminaison : exit kill
* suspension : wait, waitpid, sleep, pause
* synchronisation et communication interprocessus
* traitement des interblocages

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

####Synchrone :

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


Remarques
* Nécéssaire pour multiplexage
* Peu utilisé
