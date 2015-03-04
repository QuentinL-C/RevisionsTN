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

* Mémoire constituée d'un ensemble structuré de mémoires de taille et de temps d'accès différents, de telle sorte que la référence se fasse toujours à la mémoire la plus rapide


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

**MAIS**

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
	* Compacter en cas ce non possibilité d'allocation pour un processus
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
* A l'exécution d'un programme on charge ses pages dans les cases disponibles (Mémoire auxiliare)
* Allocation non contigüe

####Support matériel (MMU)
