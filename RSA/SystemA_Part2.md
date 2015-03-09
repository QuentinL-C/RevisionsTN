#Système Avancé Partie 2 : Gestion de la mémoire

##Introduction et concepts

* Pour être exécutées, les instructions d'un programme doivent être chargées en mémoire centrale
	* Plusieurs programmes sont chargés en même temps
	* Problème d'allocation des ressources
	* La partie du SE qui s'occupe de l'allocation de la mémoire s'appelle le **gestionnaire de mémoire**
* Objectifs
	* optimisation
	* Augmentation du rendement global du système

* Le gestionnaire de mémoire :
	* Garder une trace de l'état de chaque portion de la mémoire (libre ou allouée)
	* Une politique d'allocation
	* Une politique de libération

* Rappels sur la hiérarchie mémoire

* L'UC a accès à ses registres et à la mémoire :
	* Un cycle d'horloge (au moins) pour les registres
	* Plusieurs pour la mémoire principale
* Le cache se situe entre les registres et la mémoire principale
* Accès au disque dur plus lent par rapport à la mémoire centrale
* Le matériel et l'OS sont responsables du déplacement des objets à travers cette hiérarchie

* Adresses physiques vs logiques
	* Adresse physique = vision de la mémoire centrale (espace d'adressage physique / réel)
	* Adresse logique = adresse générée par l'unité centrale (espace d'adressage logique / virtuel)

	CPU <- adresse logique -> MMU <- adresse physique -> Mémoire physique

###Traduction d'adresses

* Liaison statique
	* Traduction lors de la compilation ou l'édition des liens
	* Programme **absolu**
	* Si l'adresse de chargement change, il est nécessaire de recompiler
	* .COM / MSDOS

* Liaisonau chargement
	* Programme translatable
	* L'adresse de chargement est laissée à l'initiative de l'allocateur
	* Gestion plus efficace
	* Limitation : un programme ayant commencé son exécution ne peut plus être déplacé. (S'il est swappé sur le disque, il faut qu'il retrouve son emplacement initial)

* Liason au moment de l'exécution
	* Traduction dynamique d'adresse
	* Permet la réimplémentation dynamique
	* Utilisation d'un registre de base / pagination ...

### MMU

La MMU est un dispositif matériel qui permet la conversion :

**@ logique -> @physique**

##Mémoire uniforme vs Mémoire centrale

###Mémoire uniforme

* La mémoire centrale = seule mémoire disponible, mémoire linéaire et gérée comme un vecteur d'emplacement
* À chaque processus est affectée une zone mémoire dont la taille est le max estimé de ses besoins
* Tout le processus est en mémoire
* Limitations :
	* taille du programme > taille MC
	* Ocuption de la mémoire par des processus inactifs

###Mémoire hiérarchisée

* Mémoire constituée d'un ensemble structuré de mémoire de taille et de temps d'accès différents, de telle sorte que la référence se fasse toujours à la mémoire la plus rapide


##Mémoire uniforme

###Partition unique

* Un seul processus à la fois
* Fragmentation interne
* Registre de base et registre limite
* Cas du MSDOS

####Avantages
* Facile à implanter
* Tout le processus est en mémoire

####Inconvénients
* Fragmentation interne
* Limite le degré de multiprogrammation
* Mauvaise exploitation de l'UC et de la MC

###Plusieurs partitions de tailles différentes
* Allocation **contigüe** + mécanisme de protection
* Partition peut contenir un seul processus
* Partitions fixes ou dynamiques

####Patitions fixes

* Facile à implanter

**,MAIS**

* degré de multiprogrammation limité par le nombre des partitions
* Processus dont la taille > taille d'une partition

**Solution : Partition dynamique**

####Partitions dynamiques

* Augmente le degré de multiprogrammation
* Elimine le problème de la fragmentation interne
* Problème : Fragmentation externe

####Fragmentation externe

* Compactage possible si réimplantation dynamique

	* Compacter tous les processus à chaque terminaison d'un processus
	* Compacter en cas ce non-possibilité d'allocation pour un processus
* problème : Overhead
* Solution : **Pagination**

####Partition multiples : Algos d'allocation

* First-fit -> allouer la première partition
* Best-fit -> allouer la plus petite
* Worst-fit -> allouer la plus grande
* Algo des frères siamois

	* liste(i): liste des partitions de taille 2^i
	* 2^MAX taille du bloc dispo

```pseudo_code
allocation(T)
	calcul de i tel que 2^i-1 < T <= 2^i
	retour(trouver_buddy(i))

trouver_buddy(i)
	si i>MAX alors
		retour (-1)

	si liste(i) non vide alors
		retour (adresse_bloc_dispo(liste(i)))

	sinon si trouver_buddy(i+1) != -1 alors
		diviser_bloc_dispo(liste(i+1))
		placer_bloc(liste(i)) //on place les blocs dans liste de i
		retour (adresse_bloc_dispo(liste(i)))

	sinon
		retour (-1)
```
###Pagination

* Mémoire physique divisée en blocs de taille fixée appelèes cadres de pages ou cases
* Mémoire logique : Page
* Taille des cases puissance de 2 (8192 octets)
* La taille d'un cadre est définie par le matériel
* A l'exécution d'un programme, on charge ses pages dans les cases disponibles (Mémoire auxiliare)
* Allocation non contigüe

####Support matériel (MMU)

* TP : table des Pages
* PTBR : registre de base qui pointe vers la table des pages
* PTLR : induque la taille de la table des pages
* Machine m bits avec T(page) = 2^n

**(p,d) -> TP[p] x T(page) + d**

Schéma : cf schéma page 70

####Translation look-aside buffers (TLBs)

* Problème : 2 accès mémoire sont nécessaires pour accéder à une donnée/instruction

* Solution : utilisation d'une mémoire cache assez rapide appelée mémoire associative ou TLBs

	* Protection interprocessus : utilisation d'un identificateur par processus
	* Translation (p,d)->(c,d) :
		* Si p est dans la TLB, alors récupérer c, sinon aller à la table des pages en mémoire
####Pagination multiniveaux
* Tables des pages doit être entièrement chargée en mémoire physique

TP =  2^20 * 4 (pour coder le déplacement) octets

* La pagination mutliniveaux permet de limiter la taille de la table des pages qui doit être résidente en mémoire

* 2 niveaux :

1. P1 = 10 bits
2. P2 = 10 bits
3. d = 12 bits

La mémoire nécessaire est :

(2^10 + 2^10 * 2^10) * 4 octets

pour un programme de 2^10 pages besoin de 2^11 pages

p1|p2|d -> p1 outer page -> p2 pt -> d memory

####Table des pages inversées

* Problème : Nombre important de pages dans une TP

* Solution TP inversée
	* une seule table pour tout le système
	* Une entrée par case
	* inconvénient : parcourir la table pour trouver une correspondance
	* Solution : TLB ou hashage

####Table des pages Hashée

* Principe de hashage
	* Fonction qui associe à chaque clé un ou plusieurs éléments
	* Une entrée par clé -> plus rapide
	* Réduction de la complexité dans les systèmes > 32 bits

####Protection
* Un bit de protection est associé à chaque page mémoire (lecture seule ou lecture/écriture)
* un bit (valid/invalid) est associé à chaque page pour indiquer si elle est dans l'espace d'adressage du processus

* Exemple :
	* Espace d'adressage de 14 bits -> 11 bits pour le déplacement et 3 bits pour le numéro de page
	* Espace d'adressage contient 8 pages
	* Si un programme n'a besoin que de 6 pages, les pages 6 et 7 seront invalidées

####Conclusion sur la pagination

* Pas de fragmentation interne
* Séparation utilisateur / mem physique
* Protection interprocessus
* Paratage des pages entre plusieurs utilisateurs
* Augmentation du temps de communication
* Fragmentation interne

###Segmentation

* Allocation **non contigüe**
* Partition de tailles différentes
* Chaque partition est spécialisée

* Adresse virtuelle de la forme :
<num_segment, déplacement>
* Table des segments

####Support matériel

* STBR : Adresse de début de la table des segments
* STLR : Taille de la table des segments

####Protection
* protection interprocessus
* lecture/écriture/exécution
* Partage entre plusieurs utilisateurs
* MAIS fragmentation externe

####Segmentation paginée
* Segement composé de plusieurs pages
	* Une TP par segment
	* Permet de réduire la fragmentation externe
cf schéma p 91

##Mémoire hiérarchisée

###Mémoire virtuelle

* Mémoire = grand tableau de stockage uniforme
* Séparation entre mémoire logique est mémoire physique

* Implémentations:
 	* Swapping
	* Swapping à la demande
		* Pagination à la demande
		* Segmentation à la demande
###Pagination à la demande

* numéro de cadre de page
* bit de présence (valid/invalid)
* 3 bits de protection
* bit modifié (dirty bit) si positionné à 1 la page doit être écrite sur le disque
* bit référencé : mis à 1 chaque fois que la page est accédée en lecture ou en écriture

* la page est transférée de la mémoire virtuelle à la mémoire centrale lorsqu'on a besoin de cette page

###Algorithmes de remplacement

####FIFO

* On remplace la page la plus anciennement chargée


####Algorithme optimal
* On connait les séquence de chargement suivantes
* On remplace celui dont on aura besoin le plus tardivement ou celui dont on n’aura plus du tout besoin
####Algorithme LRU

* La page victime est la moins récemment référencée
* A chaque accès à une page sa date de réfécement est mise à jour

####Algorithme de l'horloge (de la seconde chance)

* On parcourt les pages chargées, dès que l'on rencontre une page, on met le bit  R à 0
* Dès que l'on rencontre une page dont R est à 0, on la remplace

####Alogirthme NRU

* 2 bits associés à chaque page :
	* M mis à 1 quand une page est modifiée
	* R mis à 1 quand une page est référencée
* Toutes les 4 instructions, le bit de référence est remis à 0
* Ordre de préférence pour décharger une page
	* M = R = 0
	* M = 1
	* R = 1
	* M = R = 1

##Etude de cas : Linux

3 zones :
* Zone DMA
* Zone Normal
* Rone Highmem
###Organisation physique de la mémoire

* Machine 32 bits (Pentium)
* Espace d'adressage d'un processus : 2^32 = 4 Go
* 3 go pour lae processus
* 1 réservé et utilisé pour le processus en mode noyau : table des pages et autres données du noyau

###Espace d'adressage d'un processus : Régions

* Une région contient un ensemble de pages consécutives possédant les mêmes propriétés
	* région de code
	* région de fichiers mappés
	* ...
* A un processus est associé une liste de descripteurs de régions (vm_area_struct)
	* vm_mm :  mm_struct à laquelle appartient cette région
	* vm_start, vm_end : adresse de début et de fin de la région
	* vm_next : région suivante appartenant au même processus
	* vm_page_prot : flags de protection
	* vm_flags

##QCM

###la pagination
* utilise des partitions fixes -> oui
* utilise des partitions varaibales -> non
* impose une allocation  contiguë en mémoire -> non
* limite la fragmentation externe ->  non (elle enlève totalement)
* limite la fragmentation interne -> oui
* permer la mise en place la protection inter processus, mais pas le partage -> Non

###la segmentation
* utilise des partions fixes -> non, variables
* permet d'avoir une vision utilisateur de la mémoire -> oui
* permet une réalisation efficace d'une "mémoire virtuelle" -> non
* peut produire de la fragmentation interne -> non


###Concernantr les  algos de remplacement de pages:
* FIFO est le plus simple -> oui
* LRU souffre de l'anomalie de Belady -> Non  (si on augmente le nombre de cadres de page, cela ne réduit pas forcément les def de pages)
* l'algo de la seconde chance est le plus optimal -> non ( algo optimal )
* l'algorithme du bit de référence est une approximation de LRU -> oui

###Vrai ou faux
* une adresse logique est générée par l'UC -> V
* une adresse physique est calculée par le processeur -> F
* la liaison dynamique consiste en la traduction d'adresses au moment de l'édition des liens ->  F
* la liaison dynamique permet l'obtention d'un programme traénslatable, mais nin relogeable -> Non ( les deux sont obtenus

###la taille d'une page dans le x86 peut-être
* 4 Ko ou 4 Mo

###Dans le X86  version 32 bits
* une adresse logique est codée sur 32 bits -> Non
* une adresse linéaire est codée sur 32 bits -> Oui
* une adresse physique est codée sur 32 bits -> Oui


###Linux par rapport à la segmentation x86
* n'utilise pas du tout les segments, car il adopte une pagination à 3 niveaux généralisée à toutes les architectures -> pagination à 3 niveaux, mais utilise la segmentation
* utilise la segmentation d'une façon limitée -> V
* utilise la segmentation pour différencier les segments noyau et utilisateur -> V


###Le nb de threads par proc Linux est
* donné par cat/proc/sys/kernel/threads-max
* limité par la taille de la mémoire physique disponible
* un seul

###Soit un extrait de la sortie commande  `cat/proc/self/maps`

```bash
08048000-0804c000 r-xp 00000000 03:07 293186 /bin/cat
08048000-0804d000 rw-p 00003000 03:07 293186 /bin/cat
.
.
.
bfffe000-c0000000 rwxp fffff000 00:00 0
```

* la première région consiste en le code du processus cat qui se situe à l'offset 0 de * l'éxécutable /bin/cat -> oui
* la région de données de tous les processus * Linux commence à l'adresse 0x0804c000 -> Vrai car adresse logique
* la pile utilisateur du processus fait 3000 octets -> faux 2000
* l'adresse 0xc0000000 correspond à la limite de l'espace utilisateur du processus -> Oui



###Trouver et corriger les erreurs . On suppose que la déclaration thread_union est correction

```c
THREAD_UNION
union thread_union {
struct thread_info thread_info;
unsigned long stack[1024];
}

CURRENT_THREAD_INFO
mov1  $0xffffe000, %ecx
and1 %esp, %ecx
mov1  %ecx,p

CURRENT

mov1  $0xffffe000, %ecx
and1 %esp, %ecx
mov1  (%ecx),
```

* la taille de thread_info est 1Ko -> F (58Ko)
* la pile fait 2Ko -> F ( 4ko 1024*4)
* la thread_union fait 2Ko -> F (4Ko max entre thread info et thread union)

####Pourquoi le thread_info et la pile sont regroupés dans la même structure ?

La thread_union peut faire seulement 4Ko au lieu de 8 Ko. Quel en est l'intérêt ?

...
