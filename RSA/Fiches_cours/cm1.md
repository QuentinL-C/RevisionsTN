RSA : Généralités
=================

<table>
    <tr>
        <td>Level</td>
        <td>Registers</td>
        <td>Cache</td>
        <td>Main Memory</td>
        <td>Disk storage</td>
    </tr>
    <tr>
        <td>Typical size</td>
        <td>< 1Ko</td>
        <td>few Mo</td>
        <td>few Go</td>
        <td>100s Go</td>
    </tr>
    <tr>
        <td>Acces time (ns)</td>
        <td>0.25 - 0.5</td>
        <td>0.5 - 25</td>
        <td>80 - 250</td>
        <td>5 000 000</td>
    </tr>
    <tr>
        <td>Bandwith (Mo/s)</td>
        <td>20K - 100K</td>
        <td>5K - 10K</td>
        <td>1K - 5K</td>
        <td>20 - 150</td>
    </tr>
    <tr>
        <td>Volatile ?</td>
        <td>Yes</td>
        <td>Yes</td>
        <td>Yes</td>
        <td>No</td>
    </tr>
    <tr>
        <td>Managed by</td>
        <td>Compiler</td>
        <td>Hardware</td>
        <td>OS</td>
        <td>OS</td>
    </tr>
    <tr>
        <td>Backed by</td>
        <td>Cahce</td>
        <td>Main mem</td>
        <td>Disk</td>
        <td>CD or tape</td>
    </tr>
</table>

## Mécanismes d'E/S

* E/S synchrones (polling ou PIO) -> le processeur envoie une commande à un périphérique et attend que le périphérique la traite.
* E/S asynchrones -> le processeur envoie une commande à un périphérique et celui-ci envoie une interruption au proc pour le notifier qu'il a fini.

Un moteur DMA (Direct Memory Access) gère le transfert de données entre la mémoire principale et un périphérique.

Si le traitement par le périphérique est long => interruption: pas d'attente active

Si la qté de données à transférer est grande => DMA : pas de gaspillage du tps proc

Si la requête est simple à traiter => PIO

## Type de noyau

* Monolitique: toutes les fonctions du sytème et pilotes sont dans un seul bloc de code et un seul bloc binaire est généré.

Exemples : certains BSD et ancienne version de Linux et Unix

-> Avantages :
    * facilité de conception
    * vitesse d'exécution

-> Inconvénients :
    * code volumineux difficile à maintenir et à faire évoluer
    * utilisation inefficace de la mémoire(tout est chargé)

#### Alternative: micro-noyau

Le noyau ne contient que l'essentiel (gestion processus, ordonnanceur simple, E/S, mécanisme de comm entre processus IPC) et les autres services sont chargés plus "haut" (programmes systèmes)

Ex:
    * Hurd
    * Mac OS X

-> Avantages :
    * modularité, portabilité, fiabilité de la maintenance
    * pernet de concevoir des systèmes très généraux
    * plus de securité en plaçant de nombreux services à risques en espace utilisateur
    * meilleure utilisateur de la mémoire
    * le système est très configurable

-> Inconvénients :
    * perf réduites à causes des mécanismes lourds de comm (IPC)
    * grand nombre d'appels syst (car les services sont externes)

#### Alternative utilisée en grande partie aujourd'hui: Noyau Modulaire

les parties principales du système sont regroupés dans un bloc de code unique. Les autres fonctions sont regroupésen différents modules.

Exemples : Linux, BSD (plupart), Solaris


## Architecture de Linux:

3 parties:

    * interface des appels systèmes: entre les prog utilisateur et le noyau ;
    * le sous système de gestion des processus: scheduler, memory management, inter-processus communication ;
    * le sous système de gestion des fichiers: périphérique de type caractère et bloc ;

## Exceptions:

La plupart des exceptions issues du CPU sont gérées par Linux en envoyant un signal au processus en question.

Certaines exceptions entrainent la réexécution de l'instructionen cause. (exemple : défaut de page)

Si matériel: pas de signal, interruption d'horloge, interruption IO

## Appels systèmes

Fonction qui s'exécute en mode noyau. Un appel est généralement réalisé à l'aide d'une interruption logiciel. L'interruption est déclenchée par un prog avec une instruction spéciale(trap, syscall). Il n'y a pas de préemption.
Limite du nombre de paramètres à 6.
