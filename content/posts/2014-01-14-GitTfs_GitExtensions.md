---
title: "Git-tfs : Utilisation de Git avec un serveur TFS"
date: 2014-01-14T12:00:00+01:00
description: " Git-Tfs, GitExtension et la gestion des branches (Utilisé pour un Cours du soir Valtech)"
categories: [.Net]
tags: [Git,TFS]
---

*<span
class="hostile! environnement en Git utiliser comment ou"></span>*

***Mise à jour*** le 23/03/2013 : Suite à la version 0.17 de git-tfs et
amélioration des commandes (grace à un alias de auto-stash!)  
***Mise à jour*** le 14/01/2014 : Suite à la version 0.19.2 de git-tfs
et amélioration des commandes (grace à l’option -I!)

Le controleur de code source TFS (Team Foundation Server) est bien
souvent utilisé en entreprise.

Cependant, parmis les contrôleurs de code source, [TFS est celui le plus
détesté par les développeurs :( et Git celui le plus
apprécié](http://www.infoq.com/news/2010/03/Review-Version-Control-Systems)
.

Le contrôleur de code source Git apporte pourtant une plus grande
souplesse d’utilisation et de nombreux avantages par rapport à le
gestion de code source de TFS.

Nous allons donc voir comment nous pouvons utiliser Git en local tout en
utilisant TFS côté serveur et ainsi en tirer de nombreux avantages.

1.Installation des outils nécessaires
-------------------------------------

-   Installation de
    [GitExtension](http://code.google.com/p/gitextensions/) (et Git)  
    -\> Utilisation d’OpenSsh (facultatif)  
    -\> Décocher l’option GitGUI (facultatif)  
    -\> Options par defaut de Git sauf : “Checkout as-is, commit as-is”

<!-- -->

-   Installation de
    [p4merge](http://www.perforce.com/product/components/perforce_visual_merge_and_diff_tools)
    , [Telechargement](http://www.perforce.com/downloads/complete_list)
    (besoin d’un outil de 3way-merge!)  
    -\> Lors de la sélection des composants à installer, TOUT décocher
    sauf “Visual Merge Tool (P4Merge)”.

<!-- -->

-   Installation de [notepad<span
    class="underline"></span>](http://download.tuxfamily.org/notepadplus/6.2/npp.6.2.Installer.exe)
    (besoin d’un éditeur de texte multi-instances!!)

<!-- -->

-   Copie des fichiers de
    [Git-tfs](http://github.com/git-tfs/git-tfs/downloads) dans le
    répertoire de Git.  
    -Pour le moment, la gestion des branches par git-tfs que j’ai
    développée n’étant pas encore dans la version officielle 0.15 de
    git-tfs (pull request en attente de validation sur GitHub), on
    utilisera plutôt une [version personnelle de
    git-tfs](https://github.com/downloads/pmiossec/git-tfs/git-tfs-with-branches.zip-)
    La version officielle 0.16 gère maintenant les branches!

<!-- -->

-   Verifier que le répertoire ‘bin’ de Git est dans le path ainsi que
    celui des outils p4merge et notepad<span class="underline"></span>.

*Avant-propos : Pour consulter l’aide d’une commande git :*

    git commit -h //aide de la ligne de commande
    git help commit //aide html complete de la commande (page man)

2.Configuration de Git & GitExtension
-------------------------------------

3 moyens…

-   Edition directement du fichier .gitconfig (situé dans le répertoire
    utilisateur)  
    *Bootstrap* : Télécharger
    [l’archive](../../../../../files/article_git/git_config.zip) et
    copiez le contenu dans votre répertoire utilisateur puis…  
    -\> Edition du fichier .gitconfig (mettre vos informations
    personnelles!)  
    -\> Utilisation d’un .gitignore global (si vous ne voulez pas
    archiver un fichier .gitignore dans TFS) =\> Copie du
    .gitignore\_global( ou celui de
    [GitHub](https://github.com/github/gitignore) )

<!-- -->

-   Par ligne de commande  
    Les configurations nécessaires pour faire fonctionner Git:

<!-- -->

    git config --global user.name "Ton Nom"
    git config --global user.email ton_mail@valtech.fr

Les configurations nécessaires pour faire fonctionner Git sous Windows!:

    git config --global core.ignorecase true
    git config --global core.autocrlf false

Consultation de la liste des configurations :

    git config -l
    git config --global -l
    git config --local -l

-   Depuis GitExtension  
    Au lancement de GitExtension, verifier que le checkup est bon!  
    Ou configurer en conséquence…  
    Menu “Settings”~~<span
    style="text-align:right;">“Settings…”</span>~~\>Onglet “Global
    Settings”  
    Editor :

<!-- -->

    notepad++ -multiInst -notabbar

MergeTool : p4merge  
MergeTool Path :

    C:/Program Files (x86)/p4merge/p4merge.exe

MergeTool command :

    "C:/Program Files (x86)/p4merge/p4merge.exe" "$BASE" "$LOCAL" "$REMOTE" "$MERGED"

Difftool : p4merge  
Difftool Path :

    C:/Program Files (x86)/p4merge/p4merge.exe

Difftool command :

    "C:/Program Files (x86)/p4merge/p4merge.exe" "$LOCAL" "$REMOTE"

3.Rappels sur Git!
------------------

Git est :

-   un gestionnaire de contenu (et non de différence!)
-   stocké sous la forme d’un graphe acyclique d’objets ou
    DAG (Directed Acyclic Graph)
-   accessible avec des références


{{< figure src="http://git-scm.com/figures/18333fig0315-tn.png" title="Exemple de graphe acyclique représentant l’historique du dépot" >}}

Toute manipulation de Git ne fait que manipuler ce graphe… (Ne jamais
l’oublier et essayer de visualiser cela!!)  
…en ne faisant qu’ajouter des commits!

4.Présentation de GitExtension
------------------------------

{{< figure src="http://gitextensions.googlecode.com/files/commitlog205.png" title="Interface de GitExtension montrant le DAG d’historique du dépot" >}}

Démo…

-   Consultation du graphe de commits (diff)
-   Zone de staging , ajout de fichiers, de lignes
-   Message de commits, templates, workitems, commit local, amend
-   Stash, Merge, Rebase
-   Fetch, Fetch et Rebase, Commit
-   Historique (blame)

5.Utilisation de Git-TFS:
-------------------------

*Note :*

-   On pourra tester toutes les commandes git-tfs indiquées en utilisant
    le projet “valtechgittfs” disponible sur
    [Codeplex](http://valtechgittfs.codeplex.com/)  
    Pour cela, on pourra utiliser le compte utilisateur ‘vtccds’ (mot de
    passe:vtccds)\_
-   Attention : Lors de l’intéraction avec le serveur TFS de Codeplex,
    le login à fournir est de la forme (sans les crochets!):
    \[LoginCodePlex\]\_cp@snd  
    Le mot de passe reste celui du compte Codeplex.

\<!--Destruction comptes : https://www.codeplex.com/site/contact  vtcgitcds/cdsgitvtc & vtc\_cds/vtc\_cds --\>

### 5.1. Clonage du dépôt TFS

Commande de clone (dans le répertoire courant) du dépot TFS avec
historique (conseillé! mais long…):

    git tfs clone http://monServeurTfs:8080/tfs/DepotTfs $/MonProjet/NomDuTronc .

exemple:

    git tfs clone https://tfs.codeplex.com:443/tfs/TFS16 $/valtechgittfs/trunk . --username=vtccds_cp@snd --password=vtccds

*Note : Attention à ne cloner que le répertoire correspondant à la
branche principale du projet (le tronc) et non pas tout le dépot
entier!*

Commande de clone (dans le répertoire courant) du dépot TFS sans
historique (dernier changeset):

    git tfs quick-clone http://monServeurTfs:8080/tfs/DepotTfs $/MonProjet/NomDuTronc .

exemple:

    git tfs quick-clone https://tfs.codeplex.com:443/tfs/TFS16 $/valtechgittfs/trunk . --username=vtccds_cp@snd --password=vtccds

Convient pour un développement ponctuel ou lorsqu’on n’a pas besoin de
l’historique…

*Note :*

-   le clone ne doit pas être fait dans un répertoire mappé avec TFS
    (workspace).
-   on peut partager le dépot cloné juste par copie (ou par clonage
    git!) à un autre développeur.

### 5.2. Récupération des commits de TFS (sans merge ni rebase dans branche master)

    git tfs fetch

### 5.3. Création des commits locaux

Lors des commits locaux dans Git, l’association de Workitems se fait par
l’ajout des meta-données suivantes dans la description du commit

    #2198 //Pour associer un workitem

### 5.4. Synchroniser ses modifications avec TFS

Cette commande (comme certaines commande de git) nécessite de n’avoir
aucune modification non commité dans son espace de travail.  
Pour cela, git fourni une commande ‘stash’ pour mettre de côté les
modifications si on ne veut pas les commiter. Elle s’utilise comme cela
:

    git stash save //pour mettre les modifications de côté
    //Execution d'une commande git...
    git stash pop //pour récupérer les modifications mises de côté

Pour nous faciliter grandement la tâche, nous allons donc rajouter un
alias ‘s’ qui stash avant la commande et stash pop après la commande, si
besoin.

Ajouter donc cette ligne d’alias dans votre fichier .gitconfig (après la
ligne \[alias\])

    s = !sh -c \"git stash save | grep 'No local changes to save' && git $* || (git $* && git stash pop) \"

Cet alias s’utilise, avec n’importe quelle commande git, en intercalant
la lettre ‘s’ (comme stash) juste après le nom de la commande ‘git’ de
la façon suivante :

    git s rebase maBranche

La commande pour synchroniser ses modifications avec la dernière version
de TFS est donc la suivante (en utilisant l’alias ‘s’ défini ci-dessus)
:

    git s tfs pull --rebase

### 5.5. Report des commits locaux sur le serveur TFS

Méthode facile mais déconseillée :

    git tfs ct

Cette commande ouvre la fenêtre de commit de TFS (message + association
de workitems) mais crée un commit de merge (moche et peut y avoir des
conflits de merge au moment du commit :( )

Méthode conseillée :

    git tfs rcheckin

Cette commande réapplique les commits sur le serveur TFS. Cependant,
nécessite d’être à jour par rapport aux commits sur TFS et d’avoir
“rebasé” ses commits locaux. Cependant, une option <code>—quick</code>
et <code>—autorebase</code> permet de faire tout cela.

Cette commande nécessite également de ne pas avoir de modifications dans
l’espace de travail donc nous allons également utiliser notre alias ‘s’.

Pour commiter sur TFS, la commande est donc :

    git s tfs rcheckin --quick --autorebase

ou, en version courte :

    git s tfs rcheckin --quick -a

### 5.6.Gestion d’une branche de TFS:

Deux options se dessinent:

\- créer une branche avec git-tfs  
- récupérer une branche déjà exitante sur TFS

La création d’une branche à partir de n’importe lequel des commits
existant (il suffit de faire un checkout sur le bon commit avant de
créer la branche) se fait en utilisant la commande:

    git tfs branch $/MonProjet/BrancheACréer <nomOptionnelLocalDeMaBranche> --comment="Commentaire de création de la branche"

La récupération d’une branche TFS exitante grâce à la commande
“init-branch” :

    git tfs branch --init $/MyProject/MyTFSBranch //Pour récupérer une branche spécifique
    git tfs branch --init --all //Pour récupérer toutes les branches existantes

    ex :
    git tfs branch --init $/valtechgittfs/branche1

A l’identique des commandes utilisées pour travailler sur la branche
principale, on utilisera les même commandes que définies précédement en
l’option -I qui sélectionne automatiquement la bonne branche tfs. Cette
option a l’avantage de fonctionner aussi bien avec la branche prinipale
qu’avec les autre branches. On peut donc l’utiliser en permanence.

Récupération des commits d’une branche:

    git tfs fetch -I

Synchroniser ses modifications avec la dernière version de la branche
sur TFS:

    git s tfs pull --rebase -I

Commiter sur la branche sur TFS:

    git s tfs rcheckin --quick -a -I

### 5.7.Scripts GitExtension:

Il peut être pratique de créer dans GitExtension les scripts décrit
ci-dessous basée sur le commandes décrites précédement; elles sont
disponibles par un clic droit dans le Browser de GitExtension.  
Pour cela, allez dans le menu “Settings”~~<span
style="text-align:right;">“Settings…”</span>~~\>Onglet “Scripts” et
ajoutez une ligne où on remplira les 3 champs requis par les valeurs de
chaque ligne :

| Name       | Cmd.  | Arguments                                |
|------------|-------|------------------------------------------|
| Tfs.Fetch  | `git` | `tfs fetch -I`                           |
| Tfs.Rebase | `git` | `s tfs pull --rebase -I`                 |
| Tfs.Commit | `git` | `s tfs rcheckin --quick --autorebase -I` |

### 5.8.Nouvelle commandes de gestion des branches

Depuis la version 0.17 de git-tfs, on peut maintenant gérer un peu mieux
les branches.  
Outre la nouvelle commande de création des branches présentée
précédement, nous pouvonc maintenant…

\- afficher les branches gérées localement

    git tfs branch

\- afficher toutes les branches TFS

    git tfs branch -r

\- renomer un branche locale

    git tfs branch --move oldName newName

\- effacer une branche locale

    git tfs branch --delete myBranch

Note : Pour plus d’informations, vous pouvez toujours consulter l’aide
avec la commande :

    git tfs branch --help

### 5.9.Travailler avec les shelvesets

Liste de ses commits réservés

    git tfs shelve-list

Ceux d’un autre utilisateur:

    git tfs shelve-list -u LOGIN_UTILISATEUR

Créer une branche à partir d’un shelveset:

    git tfs unshelve MonShelveset MaBranche

Créer un commit réservé de la branche courante:

    git tfs shelve MonShelveset

Avantage de la branche Git sur le shelveset TFS :

-   on peut faire plusieurs commits
-   on connait le commit parent et voit exactement les modifications
    faites (là où TFS présente seulement un état de fichier et on ne
    sait pas comment il serait mergé!)
-   mergeable plus facilement (moins sensible au modifications)

6.Utilisation de Git
--------------------

-   **Règle de Base : Ne JAMAIS modifier des commits déjà
    partagés!!!!!!**

<!-- -->

-   Mise de coté des fichiers modifiés de l’espace de travail
    (fonctionne comme une pile)

<!-- -->

    git stash save //pour mettre de côté
    git stash pop //pour récupérer le dernier stash mis de côté

-   Reset d’une branche à un certain commit =\> Mixed : Reset de la
    branche mais pas du répertoire de travail / Hard : Reset de la
    branche ET du répertoire (faire un stash ou un commit de sauvegarde
    si vous voulez garder du contenu)

<!-- -->

-   Merge / Rebase

**Le merge :**

1.  crée un commit supplémentaire pour réunir les 2 branches.
2.  Il permet de matérialiser l’introduction d’une fonctionnalité dans
    une branche.
3.  Cette opération peut être réalisée avec des fichiers modifiés dans
    l’espace de travail.

|                                                               |
|:-------------------------------------------------------------:|
|      ![](http://git-scm.com/figures/18333fig0317-tn.png)      |
| *le DAG après un merge : le commit C6 est un commit de merge* |

**Le rebase :**

1.  réapplique les commit d’une branche sur un commit.
2.  Il permet d’avoir un historique linéaire.
3.  Cette opération ne peut être réalisée avec des fichiers modifiés
    dans l’espace de travail (nécessité de faire un stash auparavant).

|                                                                               |
|:-----------------------------------------------------------------------------:|
|              ![](http://git-scm.com/figures/18333fig0329-tn.png)              |
| *le DAG après un rebase : le commit C3 est réappliqué sur la branche master…* |

-   J’ai commité dans le tronc alors que je voulais faire une branche

<!-- -->

-   un bug à résoudre rapidement alors que je suis en plein
    développement

<!-- -->

-   Modification des commits existants (pour par ex. rajouter
    l’association de workitems!)

<!-- -->

    git rebase -i HEAD~n (avec 'n' à remplacer par le nombre de commit qu'on veut réécrire)

Attention : Ne pas enfreindre la règle de base!!!

-   Historique des opérations (et réparation des erreurs!) :

<!-- -->

    git reflog => voire l'historique des modification
    git branch MaBranche d995afd => creation d'une branche sur un commit (en indiquant son sha1)

-   Astuce: Git ne fait qu’ajouter des commits et n’en détruie AUCUN!
    (sauf lors du passage du Garbage Collector tous les 30 jours pour
    effacer ceux non utilisés).  
    Donc avant de faire une opération dont vous n’avez pas l’habitude,
    vous avez juste à créer une branche là où vous êtes qui vous servira
    de sauvegarde!  
    Et à l’effacer quand vous avez réussi…

<!-- -->

-   Trouver un commit fautif : Bisect !

7. Aperçu de Git-TF
-------------------

-   Par défaut, création d’un seul commit de merge (utiliser l’option
    ––deep pour réappliquer les commits locaux sur le serveur TFS) et
    perte des messages de commit (sauf le dernier)
-   Association des workitems dans la ligne de commande (donc pas de
    possibilité d’utiliser depuis une GUI :( )  
    et même association de workitems pour TOUS les commits (si
    utilisation de l’option ––deep. Et apparement il y aurait un bug :(
    )
-   Pas de gestion des branches (un clone, donc un dépot par branche)
-   C’est du Java (portabilité mais dépendance java à installer)

8. Retour d’expérience
----------------------

### Avantages:

-   Connaissance à tout instant de quelle version on utilise localement
-   Branches et commits locaux (expérimentation, retour en arrières,
    meilleur développement, baby steps,…)
-   Meilleur connaissance des modifications des autres utilisateurs
    (visualisation de l’historique + diffs)
-   BEAUCOUP plus grande souplesse de développement et de commit (commit
    de parties de fichiers!)
-   Remplacement du shelving par les branches ( et on peut faire
    plusieurs commits)
-   Possibilité d’annuler des commits (revert)
-   Trouver une régression facilement en testant les commits
    successivement par dichotomie (bisect)
-   Cherry Picking (prendre un commit intérressant d’une branche et
    l’appliquer sur la branche actuelle)
-   Meilleur Historique (plus de facilité à retrouver l’introduction
    d’un bug)
-   Plus de mode connecté/déconnecté de TFS :D
-   Backport de commit entre branches plus facile
-   Visual Studio est plus rapide car il n’a pas à contacter le serveur
    TFS pour extraire les fichiers en modifications (ainsi que beaucoup
    d’autres actions qui sont faites maintenant localement : blame,
    diff, …)

### Inconvénients:

-   Ticket d’entrée légèrement plus important (il faut
    comprendre/visualiser les manipulations du DAG, la gestion des
    branches) **mais qui ouvre de nombreuses possibilités!**
-   ~~Rechargement des fichiers et projets plus souvent que sous TFS (à
    cause de la glue Git-tfs qui créé les commits sur TFS, les récupère
    et fait un rebase dessus. Non présent sur un dépot distant Git qui
    ne fait qu’envoyer les commits sur le dépot distant…)~~. *Ce
    problème n’est plus présent avec VisualStudio 2012!*
-   ~~(?) Visual Studio exclue régulièrement des fichiers App.config &
    Dev.config de la Solution…~~. *Ce problème n’est plus présent avec
    VisualStudio 2012!*

9. Conclusion
-------------

Beaucoup d’avantages avec cependant quelques petits désagréments (si on
ne possède pas la version 2012 de VisualStudio)  
Le meilleur résumé serait celui ci : “TFS est une solution d’archivage
de code source, là où Git est un outil facilitant le développement”

PS :

-   Vous avez toutes les infos pour utiliser Git-tfs mais il vous reste
    à apprivoiser Git…
-   [Documentation](http://sixrevisions.com/resources/git-tutorials-beginners/)
-   Un [petit tutoriel](http://try.github.com/) assez marrant pour
    apprendre les commandes de base de git (et voir comment elles sont
    mieux pensées que celles de TFS ;) )

\<!--Introduction à Git par David Gageot : Git, la gestion de configuration qui vous veut du bien --\>  
\<!--Code freeze, merges douloureux, installation et administration d'un serveur, sauvegardes fastidieuses, perte de données, branches coûteuses, obstacle au refactoring, cycles de build trop longs… Ces mots sont-ils pour vous synonymes de Gestion de Configuration ? Vous avez appris à vivre en couple avec un outil en ignorant volontairement ses défauts, mais ils se rappellent régulièrement à vous ? Découvrez à travers des cas d'utilisation quotidiens comment Git sait se faire oublier et vous faire gagner beaucoup de temps. Vous allez aimer cet outil avec une administration proche de zéro, ne nécessitant pas de backup, utilisable en moins de cinq minutes sur une clef usb, un simple disque partagé, en serveur ou en mode SAAS. Les équipes agiles, seront « accros » de l'outil sachant se faire oublier lors de refactoring de code massifs et permettant l'intégration continue sans serveur. Les équipes distribuées apprécieront les branches locales, le cherry-picking et les performances de git. Vous êtes build 
manager en charge des branches et des code freezes ? Désolé, vous venez de perdre votre job.--\>
