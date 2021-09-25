---
title: "Build a efficient continuous integration environment for .NET"
date: 2012-06-02T12:00:00+01:00
description: "Build a efficient continuous integration environment for .NET"
categories: [.Net]
tags: [Jenkins,Continuous Integration]
darft: true
---

*<span
class="tutorial!! *THE* ! environment .Net unfriendly an in jenkins use to how or"></span>*

What is a Continuous Integration environment ?
----------------------------------------------

A Continuous Integration environment is group of tools permitting to
automate some steps of software developpment.

A consequence, is to permit the set up of an optimised and automated
processus, to make it repeatable as wished and without monopolising a
member of the team (and without human errors)

Installation and set up
-----------------------

2 parts are absolutely necessary to realise a continuous integration
platform:

-   the source code control, or VCS (Version COntrol System) which
    contains the source code of the application
-   the continuous integration sofware which permit to realise the
    differents tasks (building, tests,…)

We will see how to install and configure these two parts. We will use,
for this, Git (for the source conde controller) and Jenkins (continuous
pour integration ).

### 1. Installation et configuration of the source code controller : Git

The source code controller, being an essential part, we will make a
presentation but short, dur too the fact that the subject is too big. We
will present the smallest part necessary and invite you too search the
web for more informations.

Under Windows, using Git need the use of windows port
[msysgit](http://code.google.com/p/msysgit/) . We recommand to install
[GitExtension](http://code.google.com/p/gitextensions/) which permit to
install in the same time a good graphical interface for Git and a
VisualStudio addon (but msysgit is sufficient for the contiious
integration).

Lanch install process (Next, next… et répondez au 2 questions (format de
fin de ligne des fichiers et intégration de git dans le système)
suivants vos besoins. Les choix par défaut conviennent la plupart du
temps.

Après, l’installation, Git necessite de configurer le nom et l’email de
l’utilisateur. Cela peut être fait, soit en lançant GitExtension pour la
première fois (ou après dans l’onglet “Global Settings” de la fenêtre de
configuration obtenu dans le menu “Settings=\>Settings…”), soit en ligne
de commande:

    git config --global user.name "John Doe"
    git config --global user.email johndoe@example.com

Le but n’est pas de détailler l’utilisation de Git (il y a suffisamment
de documentation sur le sujet) et l’utilisation de l’interface graphique
de GitExtension est en outre assez explicite. Voici toutefois quelques
commandes de base necessaire à creer notre dépot et ajouter du contenu
pour pouvoir configurer et tester notre usine logicielle.

    //creation d’un dépot dans le répertoire courant
    git init
    //ajout de tous les fichiers du répertoire courant
    git add .
    //commit
    git commit -m 'mon message'

Quelques astuces :

-   télécharger le fichier .gitignore pour éviter d’archiver les
    fichiers inutiles générés par VisualStudio :
    https://raw.github.com/github/gitignore/master/Global/VisualStudio.gitignore
-   Généralement, le fichier .gitignore consiste en le premier commit du
    projet mais il est possible (et peut être judicieux si tous les
    projets sont dans le même langage) de le définir plutôt dans ses
    préférences utilisateur pour ne pas être obligé de le rependre dans
    tous ses dépots.
-   Utilisation de l’outil de merge p4merge qui permet une comparaison
    de fichiers et surtout un merge de modifications de façon plus aisée
    (3 way merge).
    [Téléchargement](http://www.perforce.com/product/components/perforce_visual_merge_and_diff_tools)
    et
    [installation](http://pontusmunck.com/2010/01/30/using-a-cool-merge-tool-with-svn-or-git/)
    .

### 2. Installation et configuration de Jenkins

#### 2.1. Installation de Jenkins

L’installation de jenkins reste assez simple. Pour cela, téléchargez la
dernière version au format war [sur le site de
Jenkins](http://jenkins-ci.org) et copier le fichier dans le répertoire
où vous voulez installer Jenkins.

Pour lancer jenkins, vous devez avoir une version de la JVM (Java
Virtual Machine) installée (nous ne détaillerons pas le procédure
d’installation ici).

Lancez jenkins depuis la ligne de commande (depuis le répertoire de
jenkins) :

    java.exe -jar jenkins.war

Attendre le lancement et dans le navigateur chargez la page
<http://localhost:8080/>

![Ecran principal de Jenkins avec 2 projets configurés](../images/article_jenkins/Jenkins_01_ecran_principal.png "Ecran principal de Jenkins avec 2 projets configurés")

*Ecran principal de Jenkins avec 2 projets configurés*

Il est fortement conseillé d’installer jenkins en tant que service
windows sinon la procédure de lancement en ligne de commande décrite
ci-dessus devra être reproduite systématiquement.  
Pour cela, dans l’écran principal, cliquez sur [Administrer
Jenkins](http://localhost:8080/manage) puis sur “Installer en tant que
service Windows”.

#### 2.2. Configuration de la construction des sources du projet

**Note préalable :** La suite de cet article indique la marche à suivre
pour configurer entièrement votre usine logicielle jenkins et vaut la
lecture ;). Cependant, si vous souhaitez installez rapidement jenkins,
peut-être devriez vous directement lire le paragraphe [§Bonus](#bonus) .

##### 2.2.1. Installation des plugins

Nous allons maintenant installer les plugins nécessaires pour gérer
notre projet .Net. Pour cela, toujours dans [Administrer
Jenkins](http://localhost:8080/manage) , cliquez sur [Gestion des
plugins](http://localhost:8080/pluginManager/) .

Dans l’onglet “Disponibles”, nous choisirons :

-   Git
-   MSBuild
-   MStest
-   HTML Publisher plugin (pour pouvoir afficher le résultat de la
    couverture de code)

Puis cliquer sur “Installer sans redémarrer”

##### 2.2.2. Configuration des outils .Net

Toujours dans [Administrer Jenkins](http://localhost:8080/manage) ,
cliquez sur [Configurer le système](http://localhost:8080/configure) .  
Verifiez que l’executable de Git est bien détecté. Si ce n’est pas le
cas, rajoutez Git dans le PATH de votre système (solution conseillée) ou
renseignez dans le champ proposé, le chemin complet de l’exécutable.  
Dans le paragraphe “MSBuild”, ajoutez une configuration de MSBuild en
lui donnant un nom et le chemin vers l’exécutable  
(ex : “MSBuild .Net 4” et
“C:\\Windows\\Microsoft.NET\\Framework\\v4.0.30319\\MSBuild.exe”  
cf <https://wiki.jenkins-ci.org/display/JENKINS/MSBuild+Plugin> )  
Cliquez sur “Enregistrer” pour valider la configuration.

##### 2.2.3. Creation du job d’intégration

Nous allons maintenant configurer le job d’intégration continue de notre
projet. Pour cela, cliquez sur [Nouveau
Job](http://localhost:8080/view/Tous/newJob) .  
Nommez votre projet puis sélectionnez l’option “Construire un projet
free-style”.

Astuce : Par la suite, si vous voulez un job similaire à un job
existant, vous pourez choisir l’option “Copier un Job existant” en
saisissant son nom et ainsi récupérer le configuration du projet
similaire pour l’adapter à votre besoin.

##### 2.2.4. Configuration du dépot Git

Dans le paragraphe “Gestion de code source”, sélectionnez “Git” et
renseignez le champ “Repository URL” avec le chemin vers votre dépôt Git
et le champ “Branches to build” avec “master” pour ne compiler que la
branche principale (la valeur par défaut permet de scruter TOUTES les
branches).

##### 2.2.5. Déclenchement du build

Dans le paragraphe “Ce qui déclenche le build”, sélectionnez “Scruter
l’outil de gestion de version” et renseignez le planning à “\* \* \* \*
\*” pour scruter le dépôt Git chaque minute.

##### 2.2.6. Compilation des sources

Dans le paragraphe “Build”, cliquez sur le bouton “Ajouter une étape au
Build” et selectionnez “Build a VisualStudio projet or solution using
MSBuild”

Dans “MsBuild Version”, vous devez retrouver la ligne correspondant à la
configuration que vous avez effectuée à l’étape [Configuration des
outils .Net](#config_dotnet) . Sélectionnez là.

Dans “MsBuild Build File”, indiquez le chemin (en relatif) du fichier
.sln de votre projet.

Dans “Command Line Arguments”, indiquez les paramètres de msbuild.
Souvent, le seul paramètre nécessaire permet d’indiquer la configuration
à compiler par le paramètre “/p:Configuration=DEBUG”.

Il peut cependant être nécessaire de reconstruire entièrement le projet
(dans notre cas pour obtenir tous les warnings lors de la compilation
pour utiliser par la suite le plugin “warnings” mais qui à pour
contrepartie d’augmenter le temps de build), ce qui peut se faire en
ajoutant le paramètre “/t:Clean;Build”.

A ce stade, vous pouvez lancer un build et vérifier que votre projet est
bien rapatrié du dépôt Git et compile. Pour cela, n’oubliez pas de
sauver la configuration puis cliquez sur [Lancer un
build](http://localhost:8080/job/JenkinsTest/build?delay=0sec) .

Si le build ne marche pas ou que le résultat attendu n’est pas le bon,
vous pouvez maintenant et tout au long de la configuration des
différentes étapes voir les logs de déroulement de chaque build en
affichant la page “Sortie Console” du build souhaité.

![Ecran de résultat d'un build avec l'ensemble des métriques disponibles](../images/article_jenkins/Jenkins_07_resultat_compilation.png "Ecran de résultat d'un build avec l'ensemble des métriques disponibles")

*Ecran de résultat d’un build avec l’ensemble des métriques disponibles*

##### 2.2.7. Configuration d’une tache de tests unitaires

Nous allons maintenant configurer l’intégration continue pour executer
les tests unitaires MSTest.

Pour cela, nous allons “Ajouter une étape au build”, cette étape étant
de type “Exécuter une ligne de commande batch windows”.

Dans la zone de texte, insérer la ligne de commande pour l’execution des
tests mstest. Ne pas oublier de :

\- précéder la ligne de commande par la suppression du fichier de
résultat auquel cas la ligne de commande échoue si le fichier existe
déjà,  
- terminer le script par un “exit 0” car sinon un échec d’un des test
entraînerait l’échec du build.

Un exemple de script pour exécuter des tests mstest ressemblerait à
celui-ci (les parties en gras sont celles à adapter à votre besoin) :

    REM MSTest necessite l’absence du fichier résultat
    del MSTestResults.trx
    MSTest.exe /nologo /usestderr /testSettings:"%WORKSPACE%\Solution\ContinuousIntegration.testsettings" /resultsfile:MSTestResults.trx  /testcontainer:"%WORKSPACE%\Solution\MSTest.MonProjet.Assembly1\bin\Debug\MSTest.MonProjet.Assembly1.dll"  /testcontainer:"%WORKSPACE%\Solution\MSTest.MonProjet.Assembly2\bin\Debug\MSTest.MonProjet.Assembly2.dll"
    REM On ne veut pas que le build soit en echec si les tests ne sont pas tous un succès
    exit 0

Dans la partie “Actions à la suite du build”, recherchez et activer
l’option “Publish MSTest test result report” et indiquez le nom du
fichier trx de résultat des tests (dans notre cas “MSTestResults.trx”).

Vous pouvez également sauvegarder le fichier de résultat de test pour le
laisser à la disposition des développeur. Il peut alors être ouvert avec
VisualStudio pour avoir les résultats des tests unitaires. Pour cela,
cochez la case “Archiver des artefacts” et indiquez le nom du fichier de
résultat des test (dans notre cas “MSTestResults.trx”).

A ce stade, vous pouvez lancer un build et vérifier que les tests sont
bien executés. N’oubliez pas de sauver la configuration puis cliquez sur
[Lancer un
build](http://localhost:8080/job/JenkinsTest/build?delay=0sec) .

L’execution de 2 builds avec des résultats fait ainsi apparaitre un
graphe représentant l’évolution du nombre de tests en échec sur le
projet.

![Résultats des tests untaires des différents builds](../images/article_jenkins/Jenkins_08_resultat_tests.png "Résultats des tests untaires des différents builds")

*Résultats des tests untaires des différents builds*

##### 2.2.8. Mesure de la couverture de code

Nous allons maintenant configurer la couverture de code qui permet de
visualiser les parties du code de l’application couvertes ou non par les
tests unitaires.

Pour cela, nous allons utiliser l’outil
[opencover](https://github.com/sawilde/opencover/downloads) et l’outil
de génération de rapport
[ReportGenerator](http://reportgenerator.codeplex.com/) . Les
télécharger, les installer et mettre les exécutables dans le PATH.

Pour générer la couverture de code, nous allons modifier le script écrit
précédement de façon à appeler les tests unitaires depuis l’outil de
couverture de code.

Une ligne de commande utilisant opencover doit être de la forme :

    OpenCover.Console.exe -register:user -target:"Chemin absolu vers l’executable de test" -targetargs:"arguments de l’executable passés précédement" -filter:+[MonProjet.*]* -output:"coverage_report.xml"

Notes :

1.  Faire attention à bien passer le chemin absolu vers l’executable de
    tests (soit dans notre cas “C:\\Program Files (x86)\\Microsoft
    Visual Studio 10.0\\Common7\\IDE\\MSTest.exe”)
2.  Faire également attention à bien échapper par un ‘\\’ tous les
    guillemets ’ “ ‘ dans les arguments de l’executable.

Il reste à inclure dans le script l’appel au générateur de rapport html
avec les résultats du test :

    reportgenerator.exe fichierResultatCouverture.xml repertoireDeGeneration

Dans notre cas le script une fois modifié est donc le suivant :

    del MSTestResults.trx
    OpenCover.Console.exe -register:user -target:"C:\Program Files (x86)\Microsoft Visual Studio 10.0\Common7\IDE\MSTest.exe" -targetargs:"/nologo /usestderr /testSettings:\"%WORKSPACE%\Solution\ContinuousIntegration.testsettings\" /resultsfile:MSTestResults.trx  /testcontainer:\"%WORKSPACE%\Solution\MSTest.MonProjet.Assembly1\bin\Debug\MSTest.MonProjet.Assembly1.dll\"  /testcontainer:\"%WORKSPACE%\Solution\MSTest.MonProjet.Assembly2\bin\Debug\MSTest.MonProjet.Assembly2.dll\" " -filter:+[MonProjet.*]* -output:"coverage_report.xml"
    reportgenerator coverage_report.xml coverage_report
    exit 0

Il reste à indiquer au plugin “HTML Publisher” les informations de
couverture à publier. En se referant aux données ci-dessus, on indiquera
les données suivantes :  
HTML directory to archive = coverage\_report  
Index page\[s\] = index.htm  
Report title = Couverture de code

Sauvez et executez un build pour obtenir des résultats.

Un nouveau lien “Couverture de code” apparaît sur la page du projet nous
permettant de consulter le rapport généré.

![Résultats de la couverture de code](../images/article_jenkins/Jenkins_09_couverture.png "Résultats de la couverture de code")

*Résultats de la couverture de code*

Nous avons maintenant mis en place assez rapidement une usine logicielle
minimale nous permettant de compiler les sources archivées sur un dépôt
Git commun, exécuter les tests unitaires et obtenir une couverture de
code.

Arrivée ici, nous avons rempli une bonne partie de nos objectifs et
avons une usine logicielle fonctionnelle. Il convient toutefois de
permettre à l’équipe d’être au courant de l’état du build (cela est très
important de fournir de façon active les résultats à l’équipe de
développement). Pour cela, Jenkins fourni de nombreuses méthodes
nativement ou à travers l’installation de plugins (email, flux rss, lava
lamp,…). Nous conseillons toutefois d’installer le plugin [Hudson Tray
Application
Plugin](http://wiki.hudson-ci.org/display/HUDSON/Hudson+Tray+Application)
qui permet à toute personne d’installer depuis le portail Jenkins un
programme affichant le résultat des builds dans la barre de notification
du système.

##### 2.2.9. Archivage de fichiers de build

Jenkins permet, à la suite d’un build, de conserver certains fichiers de
votre choix. Pour cela, dans la configuration du projet, cocher la case
“Archiver des artefacts” et indiquez le chemin des fichiers à archiver
(par exemple : “.\\bin\\Debug\\\*”).

#### 2.3. Mises en place de métriques de notre code

Il est maintenant intéressant d’obtenir des métriques un peu plus
poussées sur le code écrit au court du projet pour donner un retour aux
développeurs sur la qualité du code écrit. Pour cela, nous pouvons
utiliser plusieurs plugin, décrit ci-dessous, au choix…

Nous allons ici adresser les thèmes suivants :

-   Complexité du code écrit (plugin “CCM”)
-   Duplication de code (plugin “Dry” / outil Simian)
-   Suivi des taches ouvertes (plugin “Open Task Scanner”)
-   Analyse statistiques de code et des assemblys (Plugin “Violation” /
    outil FxCop, Simian, Gendarme, StyleCop)
-   Suivi des Warnings de compilation (Plugin “Warnings”)

##### 2.3.1. La Complexité ciclomatique : CCM

C’est un indicateur permettant de connaître la complexité du code écrit.
Pour une méthode donnée, la complexité exprime le nombre de chemins
différents pour parcourir l’ensemble du code. Plus le nombre est élevé,
plus la méthode est difficilement testable et maintenable. Il est alors
utile de la refactoriser.

Pour cela, on peut utiliser CCM, un outil qu’on trouvera ici :
<http://www.blunck.se/ccm.html>

A installer, ainsi que le plugin “CCM plugin” proposé dans Jenkins.

Aller dans “Administrer Jenkins” puis [Configurer le
système](http://localhost:8080/configure) puis dans le paragraphe “CCM”,
indiquez le chemin de l’executable (par ex : C:\\Program Files
(x86)\\ccm\\bin\\CCM.exe)

“Ajoutez une étape au Build” du type “Invoke CCM” et spécifiez dans
“Project Source Folders” la liste des projet C\# à analyser (en allant à
la ligne après chaque chemin indiqué). Par exemple :  
.\\Solution\\MonProjet.Assembly1  
.\\Solution\\MonProjet.Assembly2

Sauvez, buildez et contemplez les résultats :)

![Suivi de la complexité du code pour les différents builds](../images/article_jenkins/metrique_01_CCM.png "Suivi de la complexité du code pour les différents builds")

*Suivi de la complexité du code pour les différents builds*

##### 2.3.2. Métriques de qualité du code source : [Violations](https://wiki.jenkins-ci.org/display/JENKINS/Violations)

Tout d’abord, nous allons installer les outils suivants :

-   [Gendarme](http://mono-project.com/Gendarme)
-   [Simian](http://www.harukizaemon.com/simian/index.html)
-   FxCop depuis “C:\\Program Files (x86)\\Microsoft
    SDKs\\Windows\\v7.0A\\FXCop\\FxCopSetup.exe” (FxCop est fourni dans
    le sdk 7.0 ou 7.1)
-   [StyleCop](http://stylecop.codeplex.com)

Ne pas oublier de mettre les outils dans le path du système…

Pour utiliser ce plugin, il faut ajouter une étape supplémentaire dans
le projet de build pour pouvoir générer les métriques souhaitées (au
format xml, seul pris en compte par le plugin).

Pour cela, cliquez sur le bouton “Ajouter une étape au build”, puis
choisissez “Executer une ligne de commande batch Windows”.

Nous allons ajouter une ligne de commande pour chaque outil utilisé.

###### 2.3.2.1. Gendarme

Gendarme est un outil d’analyse d’assembly. Pour cela, nous devons
spécifier le chemin des assemblys que nous souhaitons analyser. Nous lui
indiquons également de générer un fichier xml et lui indiquons le nom du
fichier résultat.

    gendarme --xml gendarme_report.xml .\Chemin\Vers\Mon\Assembly\Mon.Assembly.exe .\Chemin\Vers\Mon\Assembly\Mon.Assembly2.dll

Note: Gendarme nécessite la présence des fichiers pdb dans le répertoire
du fichier assembly analysé.

###### 2.3.2.2. Simian

Simian est un outil d’analyse de code source à la recherche de code
dupliqué. Pour cela, nous devons spécifier le pattern des fichiers à
analyser (et également si besoin celui des fichiers à exclure). Nous lui
indiquons également de générer un fichier xml et lui indiquons le nom du
fichier résultat.

    simian **/*.cs -formatter=xml:simian-output.xml -excludes=**/*.xaml.cs

Note: Le pattern `**/*.cs` indique qu’il faut analyser tous les fichier
.cs se trouvant dans tous les sous-répertoires

###### 2.3.2.3. FxCop

FxCop est un outil d’analyse des fichier assembly.

Comme gendarme, il faut lui indiquer les fichiers assembly à analyser
(paramètre “/file:”).

FxCop fonctionne grâce à des fichiers de règles que l’on peut utiliser
au choix (paramètres “/rule:”) et dont la liste est disponible dans le
sous-répertoire “rules” du dossier d’installation de FxCop. Dans
l’exemple ci-dessous, toutes les règles sont utilisées.

Il convient également de lui indiquer le fichier de sortie (paramètre
“/out:”)

    fxcopcmd /file:"%WORKSPACE%\Chemin\Vers\Mon\Assembly\MonProjet.*.*" /directory:"%WORKSPACE%\Chemin\Vers\Mon\Assembly"  /directory:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.0" /rule:UsageRules.dll /rule:DesignRules.dll /rule:GlobalizationRules.dll /rule:InteroperabilityRules.dll /rule:MobilityRules.dll /rule:NamingRules.dll /rule:PerformanceRules.dll /rule:PortabilityRules.dll /rule:SecurityRules.dll /out:fxcop-result.xml

Note: Pour des raisons inconnues, il faut également indiquer à FxCop le
répertoire (paramètre “/directory:”) du fichier “System.Core.dll” soit,
dans notre cas, et en fonction su système et du framework “C:\\Program
Files (x86)\\Reference
Assemblies\\Microsoft\\Framework\\.NETFramework\\v4.0”. Il est également
conseillé de lui indiquer le répertoire des assemblys à tester

###### 2.3.2.4. StyleCop

StyleCop est fourni à travers un ensemble de dlls et s’intègre au sein
de VisualStudio. Pour pouvoir l’utiliser en ligne de commande, on
utilisera l’outil “StyleCop CLI” (<http://stylecopcli.sourceforge.net/>
).

StyleCop fonctionne à partir d’un fichier de règles qu’il est préférable
d’adapter à son besoin. Il est disponible dans le répertoire
d’installation de StyleCop : “Settings.StyleCop”.

La configuration se fait avec l’outil “StyleCopSettingsEditor.exe” en
passant le fichier de configuration en paramètre de l’application. Une
fois configuré selon vos besoins, placez le fichier dans le répertoire
de travail de Jenkins ( qui correspond à
JENKINS\_HOME\\jobs\\MonJob\\Workspace avec JENKINS\_HOME au répertoire
de jenkins — cf valeur dans [Administrer
Jenkins](http://localhost:8080/manage) - \> [Informations sur le
système](http://localhost:8080/systemInfo) - \> ”JENKINS\_HOME” — et
MonJob au nom du job de build créé dans Jenkins). Si le fichier n’est
pas positionné dans ce répertoire, lui indiquer le chemin complet.

On peut maintenant ajouter la ligne de commande suivante au script où on
indique le chemin vers le fichier de la Solution, le nom du fichier de
résultat à générer et le fichier de paramètre à utiliser :

    StyleCopCLI -sln %WORKSPACE%\Solution\MaSolution.sln -out StyleCopResults.xml -set Settings.StyleCop

Enfin, le batch doit se terminer par “exit 0” pour que le build n’échoue
pas si un des outils renvoie un code non satisfaisant.

Notre batch ressemble donc à cela :

    REM Analyse statistique
    gendarme --xml gendarme_report.xml .\Chemin\Vers\Mon\Assembly\Mon.Assembly.exe .\Chemin\Vers\Mon\Assembly\Mon.Assembly2.dll
    simian **/*.cs -formatter=xml:simian-output.xml -excludes=**/*.xaml.cs
    fxcopcmd /file:"%WORKSPACE%\Chemin\Vers\Mon\Assembly\MonProjet.*.*" /directory:"%WORKSPACE%\Chemin\Vers\Mon\Assembly"  /directory:"C:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\v4.0" /rule:UsageRules.dll /rule:DesignRules.dll /rule:GlobalizationRules.dll /rule:InteroperabilityRules.dll /rule:MobilityRules.dll /rule:NamingRules.dll /rule:PerformanceRules.dll /rule:PortabilityRules.dll /rule:SecurityRules.dll /out:fxcop-result.xml
    StyleCopCLI -sln %WORKSPACE%\Solution\MaSolution.sln -out StyleCopResults.xml -set Settings.StyleCop
    exit 0
    REM Fin Analyse statistique

Il reste à configurer le plugin de façon à prendre en compte les
fichiers générés lors de cette étape du build. Pour cela, il faut cocher
l’option “Report Violations” et renseigner pour chaque ligne
correspondante à l’outil utilisé (Gendarme, Simian, FxCop, StyleCop), le
nom du fichier de résultat généré ainsi que ajuster (ou pas) selon vos
besoins les 3 chiffres correspondants aux valeurs limites indiquant un
build bon/de qualité médiocre/instable!

Le plugin est configuré et un graphe regroupant les diverses métriques
devrait être disponible dans le tableau de bord du projet lors du
prochain build. Un lien “Violations” est également disponible dans la
colonne de gauche.

![Graphe de suivi des métriques d'analyse de code](../images/article_jenkins/Jenkins_10_metriques_violation.png "Graphe de suivi des métriques d'analyse de code")

*Graphe de suivi des métriques d’analyse de code*

##### 2.3.3. Détection de tâches à faire : Task Scanner Plugin

Ce plugin permet de détecter des chaines de caractères types (FIXME,
TODO,… dans les commentaires et de suivre leur évolution.

Après l’avoir installé, allez dans la configuration du projet et activez
la case à cocher “Recherche des tâches ouvertes dans le workspace”.

Puis configurez les options suivantes :  
“Files to scan” = /\*.cs  
“Tasks tags” = configuer les 3 champs de priorités avec les chaines de
caractère que vous souhaitez.

Le plugin est configuré et un graphe “Open Tasks Trend” devrait être
disponible dans le tableau de bord du projet lors du prochain build. Un
lien “Tâches ouvertes” est également disponible dans la colonne de
gauche.

![Suivi des tâches laissées ouverte dans le code source](../images/article_jenkins/metrique_02_OpenTasks.png "Suivi des tâches laissées ouverte dans le code source")

*Suivi des tâches laissées ouverte dans le code source*

##### 2.3.4. Détection des similarités de code : Simian / Dry Plugin

Ce plugin permet une autre visualisation intéressante de l’analyse de la
duplication de lignes de codes (donné par l’outil simian).

Le plugin utilise le même fichier de résultat obtenu par la commande
définie plus tôt lors de l’utilisation du plugin “Violation”.

Dans la configuration du projet et activez la case à cocher “Publish
duplicate code analysis results”.

Puis configurez l’option “Duplicate code results” par le nom du fichier
xml de résultat (pour nous : simian-output.xml )

Le plugin est maintenant configuré et, comme le plugin “Task Scanner
Plugin”, un graphe “Duplicate Code Trend” devrait être disponible dans
le tableau de bord du projet lors du prochain build. Un lien “Duplicate
Code” est également disponible dans la colonne de gauche pour parcourir
les résultats…

![Affichage des similarités, ou code dupliqué, dans le dernier build](../images/article_jenkins/metrique_06_Similarités.png "Affichage des similarités, ou code dupliqué, dans le dernier build")

*Affichage des similarités, ou code dupliqué, dans le dernier build*

##### 2.3.5. Suivi des Warnings de compilation : [Warnings Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Warnings+Plugin)

Ce plugin permet l’affichage, la comptabilisation et le suivi des
warnings lors de la compilation du projet.  
Dans la configuration du projet et activez la case à cocher “Scan for
compiler warnings” et cliquez sur le bouton “ajouter” à côté du texte
“Scan console log” et sélectionnez le parser “MSBuild”.  
Le plugin est maintenant configuré et, comme le plugin “Task Scanner
Plugin”, un graphe “Compiler Warnings Trend” devrait être disponible
dans le tableau de bord du projet lors du prochain build. Un lien
“Compiler Warnings” est également disponible dans la colonne de gauche
pour parcourir les résultats..

![Suivi des Warnings de compilation](../images/article_jenkins/metrique_03_Warnings.png "Suivi des Warnings de compilation")

*Suivi des Warnings de compilation*

##### 2.3.6. Gamification

Il existe des plugins qui peuvent encourager votre équipe à venir
consulter et résoudre les problèmes apparus lors des différents build et
ceci en introduisant une certaine décontraction. Nous vous conseillons
donc de tester le plugin “ChuckNorris” qui ajoutera une touche d’humour
(informaticien) à chacun de vos builds ou le plugin “Continuous
Integration Game” qui attribue et retire des points aux commiteurs en
fonction de la qualité de leur commit et lors du cassage ou de la
réparation des builds. Ambiance garantie!

##### 2.3.7. ThinBackup

Ce plugin permet de faire des sauvegardes et restaurations des
configurations des différents jobs créés.

Installez le plugin, redemarrez Jenkins et allez dans [”Administrer
Jenkins”](http://localhost:8080/manage) ,
[”ThinBackup”](http://localhost:8080/thinBackup) et ”Settings”.
Configurez le “Backup directory” avec le répertoire de votre choix.

Vous pouvez maintenant sauvegarde et restaurer vis configurations.

#### 2.4. Installation facile et rapide : Bonus exceptionnel!!!!! ;)

Nous allons utiliser ThinBackup pour pouvoir installer facilement la
configuration complète de job utilisant tous les plugins comme décrit
tout au long de cet article. Nous allons vous fournir ci-dessous un
squelette de configuration à adapter de façon à mettre en place jenkins
TRES rapidement.  
Pour cela, il faut installer préalablement TOUS les plugins utilisés
dans la configuration (sinon la configuration sera perdue lors de
l’import de la configuration), soit :

-   Git Plugin
-   CCM Plugin
-   [Duplicate Code Scanner
    Plug-in](http://wiki.jenkins-ci.org/x/X4IuAg) ,
-   [HTML Publisher
    plugin](http://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin)
    ,
-   [Hudson Tray Application
    Plugin,](http://wiki.hudson-ci.org/display/HUDSON/Hudson+Tray+Application)
-   [MSBuild
    Plugin](http://wiki.jenkins-ci.org/display/JENKINS/MSBuild+Plugin) ,
-   [MSTest
    plugin](http://wiki.jenkins-ci.org/display/JENKINS/MSTest+Plugin) ,
-   [Task Scanner Plug-in](http://wiki.jenkins-ci.org/x/XoAs) ,
-   [Violations
    plugin](http://localhost:8080/pluginManager/wiki.jenkins-ci.org/display/JENKINS/Violations)
    ,
-   [Warnings Plug-in](http://wiki.jenkins-ci.org/x/G4CGAQ)

Configurer les plugins le necessitant (git, msbuild, ccm) dans
[”Administrer Jenkins”](http://localhost:8080/manage) , [”Configurer le
système”](http://localhost:8080/configure) . Cette configuration a été
décrite précédement.

Décompressez maintenant [cette
archive](../files/article_jenkins/FULL-2012-04-09_23-48.zip) dans le
répertoire utilisé par ThinBackup.

Allez dans [Administrer Jenkins](http://localhost:8080/manage) ,
[ThinBackup](http://localhost:8080/thinBackup),
[Restore](http://localhost:8080/thinBackup/restoreOptions) et restaurez
le backup correspondant.

Puis dans [Administrer Jenkins](http://localhost:8080/manage) ,
[Recharger la configuration à partir du
disque](http://localhost:8080/reload) .

Vous devez maintenant avoir un job fonctionnel pré-configuré avec toutes
les étapes du build. Il ne reste plus qu’à adapter les chemins et noms à
vos besoins :D

Vous pouvez également supprimer ce dont vous ne voulez pas…

Note: si vous avez oublié un plugin, vous pouvez le rajouter par la
suite et relancer la restauration si besoin.

Nous avons maintenant créé un job avec Jenkins pour gérer l’intégration
de notre projet de développement. Ce job étant relativement complet, il
peut prendre un certain temps et toutes les métriques ne sont pas
forcement nécessaires à chaque build. Il peut donc être utile de
construire un build plus léger (avec seulement les tests unitaires et la
couverture de code).

On peut donc toujours créer un ou plusieurs nouveaux jobs en copiant le
job précédement créé et en supprimant les étapes non souhaitées.

Conclusion
----------

Au cours de cet article, nous avons installé et configuré Jenkins pour
mettre en oeuvre facilement une usine logicielle pour pouvoir construire
et analyser un projet .Net. Nous avons également passé en revu les
différents outils à notre disposition qui peuvent être intégrés lors du
build pour obtenir des métriques permettant de donner un retour aux
développeur et aussi faire une première configuration de ces outils.
Nous avons vu que Jenkins est un solution satisfaisante et rapide à
mettre en oeuvre pour construire vos projets .Net.

Il ne vous reste plus maintenant qu’à mettre en oeuvre ces outils sur
vos projet pour le bonheur de vos équipes…
