#Système Avancé Partie 3 : Gestion des processus et ordonnancement

## Implémentation des processus

**Entité dynamique qui exécute un programme**

###Mémoire virtuelle d'un processus

* En mode utilisateur, un processus manipule :
	* la pile
	* les données
	* le code

* En mode noyau, un processus manipule :
	* les zones de données 
	* les zones de code
	* une **pile noyau**

* La pile utilisateur : contient les frames des fonctions appelées en mode utilisateur
* La pile noyau : Contient les frames des fonctions appelées en mode noyau

* Espace mémoire isolé
* Le programme d'un processus peut être chargé avec d'autres processus
* Les données ne peuvent pas être partagées
* Le multithreading permet de partager des données entre plusieurs threads

###Etats d'un processus

1. Created (Nouveau)
2. Ready to run (Prêt)
3. Kernel Running
4. User Running
5. Aslepp (Bloqué)
6. Preempted (Réquisitionné)
7. Zombie (Terminé)

###Process Control Block (PCB)

**Ensemble des informations associées à un processus : son contexte**

* État du processus (running, ready...)
* Compteur du programme (PC)
* Registres du processeur
* Informations sur l'ordonnancement
* Information pour la gestion de la mémoire
* statistiques
* infos sur l'état des E/S

####Implémentation du PCB

* table des processus
	* en permanence en mémoire
	* contient des infos sur tous les processus (même ceux en dehors de la mémoire)
* Strucutre utilisateur 
	* Fait partie de la mémoire virtuelle
	* Contient des infos qui ne sont que nécessaires lors de l'exécution du processus
	(cf schéma page 131)

##Contexte d'un processus
	* Contexte utilisateur : Zones texte + données + pile utilisateur
	* Contexte matériel : Registres + pointeurs au niveau du processus
	* Contexte système : défini globalement par la structure utilisateur
		* Structure du noyau
		* Pile noyau

###Changement de contexte
* Conssite en la sauvegarde du contexte d'un processus + restaurer celui d'un autre
* Nécessaire en cas de blocage sur une E/S, interruption, exception...
* Assure le partage et l'équité dans un système multiprogrammé

####Synchrone :
* involontaire allocation à un processus (interruption d'horloge)
* volontaire : un processus se bloque

####Asynchrone :
* Cas d'une interruption matérielle
* Communication prise en charge par le matériel

##Création d'un processus

* Différence entre ```fork()``` et ```exec()```

*```fork()```
* duplique le contexte complet du père
* retourne le pid du fils au père
* retourne 0 au fils

Problème à résoudre 

* Allocation des ressources pour le processus fils:
	* système : table des processus + pid
	* mémoire : texte, pile utilisateur, pile noyau + structure utilisateur
* Création d'un contexte d'exécution pour le processus fils à partir du contexte père
* Lancement du nouveau processus (double retour)


##Implentation des processus Linux 2.6

###Descripteur du processus

Le descripteur ```task_struct``` contient toutes les informations relatives à un processus

* Champ state
	* TASK_RUNNING (prêt à être exécuté ou en cours d'exécution)
	* TASK_INTERRUPTIBLE (processus suspendu en attendant qu'une condition soit réalisée)
	* TASK_STOPPED (processus arrêté à cause d'un signal SIGSTOP)
	* TASK_ZOMBIE (l'exécution du processus est terminée, mais son père n'a pas encore fait appel à wait()) 

Lightweight processes = processus légers 
	* = 1 thread
	* groupe de threads = ensemble de processus légers qui implante  un même application multithreadée
		* partagent l'espace d'adressage
		* Agissent comme un tout vis-à-vis du système
		* chacun à son pid mais un seul pid de groupe