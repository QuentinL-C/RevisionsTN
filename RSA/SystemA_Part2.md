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

@ logique -> @physique

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


