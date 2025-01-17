 
# Recette d'installation d'intelFPGA SDK for OpenCL sur Linux

Les guides d'installation officiels sont nommés Getting Started et fournis par Intel, en l'occurrence on veut les guides pour SDK, attention à la version des guides proposés.

https://www.intel.com/content/www/us/en/docs/programmable/683188/22-2/pro-edition-getting-started-guide.html

Attention à choisir sa version.

## Etape 1 : Télécharger l'installateur

On le trouve sur le **site d'Intel**. On peut utiliser au choix l'installateur de **Quartus** ou celui du **SDK**. OpenCL s'appuie sur Quartus mais dans les deux cas l'installation commencera par Quartus en fonction des options qui seront cochées, sachant que Quartus contient non seulement OpenCL mais aussi Modelsim, que ne contient pas SDK. <<<incomprehensible pour moi>>>

### Choix de version

Les versions testées sont 18.1 standard pour compilation sur une carte de1soc ainsi que 18.1 pro et 17.1 pro pour l'émulation sur arria 10. Le BSP (Board Support Package) de la carte de1soc est disponible pour les versions 14.0, 16.0 et 18.0 d'Altera OpenCL (renommé IntelFPGA OpenCL depuis le rachat et au moins depuis la version 17.1), il est possible qu'il fonctionne pour d'autres versions mais on recommandera la 18.1 qui fonctionne bien avec au moins en standard, à vérifier pour la version pro. Arria 10 est compatible avec les versions pro, au moins à partir de la 17.1.

### Téléchargement

S'assurer de la version de Quartus ou SDK choisie : version pro ou standard et surtout distribution, certaines pages offrent windows dans un premier tab même après avoir cliqué sur une page pour Linux. <<<les mots a partir de "certaines pages" incomprehensibles pour moi>>>

## Etape 2 : Installation

### Suivre les instructions

Sur la page de téléchargement se trouvent des instruction détaillées. Il s'agit de détarer le fichier puis d'exécuter le .sh qui sera chargé de l'installation.

### Root ou utilisateur

Attention, l'installateur peut vous suggérer d'exécuter (commande pour l'exemple) :

```bash
user@computer:~/Downloads/AOCL-pro-18.1.0.222-linux$ sudo ./setup_pro.sh 
```
Si vous installez avec sudo l'installation sera dans /root/intelFPGA(_pro). J'ignore s'il y a des inconvénients mais on ne perd rien à installer en tant que simple utilisateur et c'est ce que je recommanderais par principe, le dossier d'installation sera alors dans ~/intelFPGA. La commande est alors :

```bash
user@computer:~/Downloads/AOCL-pro-18.1.0.222-linux$ ./setup_pro.sh 
```

### En cas de freeze

C'était le cas pour toutes les installations de **version 17.1** sous Linux essayées et cela pourrait être le cas pour d'autres versions, notamment antérieures. Un sous-programme de l'installation ne parvient pas a quitter. Cela n'est pas une erreur fatale. J'ignore exactement où s'arrête l'installateur dans ce cas et ma recommandation est alors d'effacer l'installation complètement (ou de venir directement à cette étape) <<<je ne comprends pas entre les parentheses>>> pour la faire proprement :

```bash
exemple-selon-dossier-d-installation-attention-avec-cette-commande$ rm -rf ~/intelFPGA_pro 
```

Ensuite il faut faire l'**installation des composants** individuellement, ils se trouvent dans le dossier d'installation préalablement détaré sous "components" et sont sous la forme [nom-du-composant].sh ou .run. Comme précédemment, on les exécute par l'invite de commande depuis leur dossier, avec par exemple :

```bash
user@computer:~/Downloads/AOCL-pro-18.1.0.222-linux/components$ ./QuartusProSetup-18.1.0.222-linux.run 
```

L'ordre est (je crois) important : dans le doute **commencer par Quartus** puisque les autres composants s'appuient dessus. Pour un AOCL (alias d'Altera OpenCL, ancien nom) fonctionnel on installera Quartus, tout ce qui porte le nom AOCL, SDK ou d'autres variantes éventuelles d'OpenCL ainsi que le BSP, j'ignore si ce dernier est utile lorsque l'on utilise un BSP qui ne provient pas d'Intel (comme la de1soc) mais ce sera nécessaire dans le cas de l'arria 10, la cyclone V etc.

De la même façon que précédemment, à l'installation, **Quartus va freeze**. Ce n'est pas grave. Il va d'abord finir de tout installer puis simplement ne va pas s'arrêter. Il est très important de le laisser finir réellement, c'est difficile d'être certain qu'il a fini, il fait parfois apparaître une fenêtre supplémentaire qu'il refermera, vers la fin. Il peut ne pas progresser un certain temps sans avoir réellement terminé. Dans le doute, laissez-lui 20 min supplémentaires à partir du moment où il semble ne plus rien faire de nouveau.

Ensuite on n'annule rien, il me semble qu'alors il efface l'installation. On le tue de force, je suggère la commande suivante :
```bash
anywhere$ kill -9 [process id]
```
Pour repérer l'id, la commande top peut suffire à trouver le logiciel du même nom que le composant dont vous avez lancé l'installateur (a priori commençant par Quartus).
```bash
ps -aux | grep Quartus
```
devrait faire l'affaire également.

Si d'autres composants décident de freeze on procédera de même.

## Etape 3 : Variables d'environnement

Les variables d'environnement sont correctement décrites dans les guides d'installation fournis par Intel. Je joins deux fichiers fonctionnels pour mes installations de Quartus 17.1 pro et 18.1 pro. <<<ou sont ces fichiers?>>> Je suggère de définir les variables dans un fichier de configuration que l'on exécutera dans chaque nouveau shell avant toute utilisation de fonctions opencl, comme ceci :

```bash
user@computer:path-to-file$ source ./initCL
```
Deux points sont à noter. Premièrement, sous ```sh``` ou sous ```bash```, il faut bien utiliser la commande ```source``` pour exécuter le fichier de configuration, c'est la seule façon de l'exécuter qui permet aux définitions de variables d'environnement qu'il contient d'être integrées par le shell courant. Deuxièmement, si nous avons besoin tout le temps du même fichier de configuration (parce que nous utilisons tout le temps la même installation de Quartus), alors il est commode d'inclure la commande ```source ./initCL``` dans ```~/.bashrc``` (c'est le fichier d'initialisation que chaque instance de ```bash``` exécute automatiquement à son lancement).

_A ne pas faire_ : il n'est pas recommandé de simplement taper les commandes changeant les variables d'environnement dans un terminal. Les définitions de variables entrées de cette façon ne valent en effet que pour la seule instance en cours du shell (et pour ses descendants - ce qui a peu d'importance pratique), et ne survivent pas au reboot.

### Licence

Ne pas oublier de mettre la licence à l'endroit indiqué par LM_LICENSE_FILE (chemin complet incluant le fichier, contrairement à la plupart des variables d'environnement indiquant des dossiers) (attention à l'orthographe américaine: il y a un S à la place d'un C). Si on utilise une clef usb qui pourra changer d'adresse mac, on va changer cette adresse vers celle pour laquelle est faite la licence. Pour cela, avec ifconfig, on repère le nom de l'interface de la clef (pour l'exemple ce sera wlx200db024752e) ainsi que l'adresse mac de la license (ici 001583ED8A53) et on pourra écrire ces deux commandes après les installations indiquées éventuelles :
```bash
sudo ifconfig wlx200db024752e down
sudo macchanger -m 00:15:83:ED:8A:53 wlx200db024752e
```

## Etape 4 : Tester l'émulation sur carte Intel

On part du principe qu'il n'y a besoin ni de BSP spécifique ni de l'EDS qui permet la cross-compilation, déjà c'est le test de l'installation la plus basique.

D'abord il faut s'assurer que la commande :
```bash
anywhere$ aocl version
```
est bien définie et renvoie la version attendue. Si ça n'est pas le cas, revoir les étapes précédentes et s'assurer que l'exécutable aocl est bien dans le PATH ainsi que INTELFPGAOCLSDKROOT renvoie bien au bon dossier d'installation (ALTERAOCLSDKROOT pour des versions plus anciennes, au moins jusqu'à la 14).

### Préparer le test

Télécharger un exemple depuis le site Intel, par exemple Hello World compatible avec Quartus version 17.1 ou ultérieures, pour Linux puisque nous allons émuler sur une machine hôte Linux. Installations préalables : gcc, g++ et make, il peut être nécessaire de les installer/réinstaller manuellement. Il faut faire pointer la variable AOCL_BOARD_PACKAGE_ROOT sur le dossier du BSP de la carte intel utilisée, j'ai utilisé le BSP suivant correspondant à l'arria 10 : $INTELFPGAOCLSDKROOT/board/a10_ref.

Notons que des exemples existent pour hôte Linux, Windows (avec une configuration pour Microsoft Visual Studio) et pour une carte avec Arm, ce qui donne de très bonnes référeneces, notamment avec Linux et Arm qui ont des makefile lisibles.

Ce qu'on espère :
- on compile le host grâce au makefile fourni (commande make à l'endroit du makefile)
- on compile le device pour l'émulation (commande fournie plus loin)
- s'ils ne sont pas dans le même dossier <<<pas compris: quoi n'est pas dans le même dossier?>>>, on les déplace et on lance le host (commande indiquée plus loin) pour une compilation parfaite

Ca n'arrivera probablement pas, çc n'est pas grave.<<< ??? >>>

### Méthode pour compiler le projet

Pour compiler le host on fera normalement :
```bash
user@computer:~/Downloads/exm_opencl_hello_world_x64_linux/hello_world$ make
```
Notons qu'il est écrit et compilé en C++ avec quelques commandes spécifiques à OpenCL.


Le "device" en .cl est écrit en C avec ses spécificités et décrit les fonctions du FPGA. Pour le compiler en vue de l'émulation on fera typiquement la commande suivante :
```bash
user@computer:~/Downloads/exm_opencl_hello_world_x64_linux/hello_world$ aoc -march=emulator -v -report device/hello_world.cl -o bin/hello_world.aocx
```
ou -v et -report donnent de l'information et -o renomme : il est important de donner à l'exécutable le nom attendu par le host sans quoi il ne le trouvera pas

Enfin, comme suggéré par le compilateur du device, il faudra exécuter le host avec la commande suivante (depuis n'importe quel dossier où se trouvent le host et l'aocx):
```bash
user@computer:~/Downloads/exm_opencl_hello_world_x64_linux/hello_world/bin$ env CL_CONTEXT_EMULATOR_DEVICE_INTELFPGA=1 ./host
```

### Problèmes de l'éditeur de liens

La plupart des problèmes viendront de bibliothèques que le linker (éditeur de liens) ne parviendra pas à trouver. Le linker GNU se nomme ld et se trouve dans /usr/bin/ld ; il est utilisé sur pratiquement toutes les distributions Linux (si vous ne l'avez pas, vous êtes probablement assez calé pour adapter les solutions proposées). S'il produit une erreur ou s'il est mentionné dans une erreur, c'est très probablement qu'il y a une bibliothèque que ld ne parvient pas à trouver. Cela peut prendre plusieurs formes, y compris pendant l'exécution du host et émanant du host (c'est un piège, sauf si vous repérez l'erreur en question dans le host ou dans ses includes, elle proviendra probablement de ld aussi <<<pas compris le truc entre parentheses>>>).

Il faut repérer où se trouve la bibliothèque en cause et analyser la situation.

#### _Repérer une bibliothèque_

La commande locate est très efficace pour trouver des fichiers dans tout l'ordinateur. Après installation elle nécessite la commande **updatedb** qui prendra du temps la première fois mais répertorie toute l'arborescence que locate ira lire. On écrira donc :

```bash
anywhere$ locate [nom fourni par ld]
```

Si au lieu d'un bibliothèque .so l'erreur concerne qqch de la forme -lref il s'agira généralement d'un fichier de type libref.[?] et on pourra chercher locate libref (ou aller chercher son vrai nom sur google).

#### _Déterminer la bonne bibliothèque_

Si la bibliothèque **n'existe pas**, c'est problématique. Il faut aller rechercher de quelle façon on l'installe proprement, ces cas sont assez bien documentés. Il m'a fallu installer libpng12-0, libstdc++5 et libncurses5 (avec sudo apt-get install). Cela a toutes les chances de résoudre le problème directement pour la bibliothèque en question, sinon on peut faire un updatedb puis recommencer locate et poursuivre sur les cas suivants.

Si une bibliothèque existe pour une **autre architecture** (typiquement avec "arm" dans le son chemin) on la traitera comme hors sujet, d'ailleurs il se peut qu'un warning affiche "skipping incompatible" en la mentionnant.

Si la bibliothèque existe en **plusieurs versions**, on privilegiera celle avec le nom de version le plus court, il est possible qu'elle soit un lien symbolique vers la version précise du moment (qui porte un nom plus long).

Si la bibliothèque existe à **plusieurs endroits**, on privilégiera l'endroit qui semble le plus générique, en aucun cas un lié à un logiciel propriétaire qui n'a rien à voir, gcc est acceptable, intelFPGA/Altera en second lieu. Il peut être rassurant de comparer deux fichiers en cas de doute (diff [fichier 1] [fichier 2]), s'ils sont bien identiques on ne craint rien à choisir l'un ou l'autre, si ça n'est pas le cas il est important de repérer lequel est adéquat sachant que l'hôte est la machine linux et ses bibliothèques devraient être les bonnes.

#### _Indiquer au linker comment trouver la bibliothèque_
La variable d'environnement LD_LIBRARY_PATH permet d'ajouter pour ld des chemins où chercher ses bilbiothèques. On peut essayer, ceci dit de mon expérience cela n'a pas fonctionné une seule fois.

Une autre option probablement élégante que je n'ai pas vraiment utilisée mais que j'aurais envie de recommander à l'avenir est de configurer le path de ld  <<<ce n'est pas le path: le path, c'est où on cherche les exécutables>>> avec son fichier de configuration. D'abord on peut afficher le chemin que parcourt ld avec la commande suivante :
```bash
ld --verbose | grep SEARCH_DIR | tr -s ' ;' \\012
```
Pour lui ajouter des chemins on peut simplement ajouter un dossier par ligne dans le fichier dédié /etc/ld.so.conf (par exemple en l'ouvrant avec sudo gedit /etc/ld.so.conf). Ensuite on mettra à jour les chemins avec ldconfig.
**Attention** cependant, l'ennui de cette méthode est que si plusieurs fichiers portent le même nom il faudra en remplacer un avec un lien symbolique, il faut donc être prudent avec ce que l'on fait, ne surtout pas effacer de fichier uniques et faire les liens dans le bon sens. C'est probablement la méthode la plus propre mais il faut savoir ce que l'on fait.
 
<<<je n'ai pas compris ce qui precede: je pense qu'il faut ajouter des lignes dans /etc/ld.so.conf ou plutot ajouter des fichiers dans /etc/ld.so.conf.d/ et je n'ai pas bien compris pourquoi cela exige qu'on fasse d'abord ld --verbose | grep SEARCH_DIR | tr -s ' ;' \\012 >>>

Méthode fastidieuse mais facile : créer des liens symboliques entre la bibliothèque que l'on a identifiée comme adéquate et ce que cherche ld à un endroit où il cherche. Attention aux noms, je ne pense pas (à vérifier ceci dit) que ld accepterait bib.so.0.1 lorsqu'il cherche bib.so. Pour l'endroit où créer le lien symbolique, à votre convenance parmi les endroits ou ld cherche. On pourrait même créer un dossier exprès, le rajouter à son .conf et directement copier les bibliothèques. Ce que je préconise c'est de créer des liens symboliques vers /usr/lib <<< mauvaise preconisation: ils est bien crade d'ajouter des choses non standard dans les repertoires fondamentaux de la machine. En fait, on peut le faire, mais en ayant honte, et non en le preconisant>>>. Cela se fait avec la commande ln -s [bibliotheque] [lien à créer]. Voici certains liens que vous aurez probablement à créer, attention ils peuvent être légèrement ou tout à fait differents :

```bash
sudo ln -s /usr/lib/x86_64-linux-gnu/libOpenCL.so.1 /usr/lib/libOpenCL.so
sudo ln -s /usr/lib/gcc/x86_64-linux-gnu/11/libgcc.a /usr/lib/libgcc.a
sudo ln -s /usr/lib/gcc/x86_64-linux-gnu/11/libgcc_s.so /usr/lib/libgcc_s.so
sudo ln -s /usr/lib/gcc/x86_64-linux-gnu/11/crtbeginS.o /usr/lib/crtbeginS.o
sudo ln -s /usr/lib/gcc/x86_64-linux-gnu/11/crtendS.o /usr/lib/crtendS.o
sudo ln -s /home/eva/intelFPGA_pro/18.1/hld/board/a10_ref/linux64/lib/libaltera_a10_ref_mmd.so /usr/lib/libaltera_a10_ref_mmd.so
sudo ln -s $INTELFPGAOCLSDKROOT/linux64/lib/dspba/linux64/libdsdk.so /usr/lib/libdsdk.so 
```
(Et beaucoup d'autres provenant du dernier dossier, je recommande de rajouter ce dossier au path de ld par son fichier de configuration, je n'ai pas eu de conflits pour lui et même si je ne peux pas exclure qu'il y ait des incompatibilités d'une version à l'autre, je recommanderais d'ajouter une ligne qui altère et mette à jour le chemin de ld dans le bashrc ou le initCL écrit précédemment pour rester cohérent avec la version, bien qu'il soit tout à fait possible d'ajouter la quinzaine de bibliothèques concernée à la main si l'on reste sur une seule version ou que l'on ne recontre pas d'incompatibilités entre des versions coexistentes.) <<les remarques et l'exemple qui precedent sont crades; on peut travailler comme ça, mais à condition d'avoir honte.>>>

_A ne pas faire_ : éviter de se fatiguer avec des options pour les commandes aoc, elles ne permettent pas d'ajouter des dossiers au path de ld.

### Problèmes d'inclusion pour gcc/g++

Il se peut que certains fichiers ne soient pas trouvés par gcc lors de la compilation du host. Ce sont des erreurs de compilateur tout à fait classiques et reconnaissables. Il s'agit de s'assurer que le makefile inclue bien les bons dossiers dans lesquels chercher. On peut à nouveau utiliser locate pour repérer où sont les fichiers manquants et ajouter leur dossier à la ligne INC_DIRS du makefile. Dans mon cas j'ai dû ajouter /home/eva/intelFPGA/18.1/hld/host/include/CL où se trouvaient des fichiers .h utiles à opencl :
```makefile
INC_DIRS := ../common/inc /home/eva/intelFPGA/18.1/hld/host/include/CL
```

### Problèmes d'exécutables introuvables

Les exécutables ne sont pas recherchés par le linker ld, c'est un problème distinct. Si autre chose qu'une librairie ou qu'un fichier .h pose problème, il est probable que ce sera un exécutable (généralement sans aucune extension à son nom). Dans mon cas, aocl-clang était introuvable.

La solution est encore de repérer l'exécutable par son nom avec locate. Ensuite, on ajoute le dossier où il est à la fin de la variable d'environnement PATH, qui indique où chercher les exécutables. Il est recommandé de faire cela dans le fichier d'initialisation des variables d'environnement mentionné plus haut (chapitre _Etape 3 : Variables d'environnement).

### Corriger un message d'erreur après l'autre jusqu'à ce qu'il n'y en ait plus

Normalement les méthodes ci-dessus devraient venir à bout de tous les problèmes rencontrés et l'exécution du host donnera un beau message disant que tout s'est déroulé comme prévu.

## Etape 5 optionnelle : compiler sur une carte physique

Il s'agira alors d'installer l'EDS (Embedded Software and Tools) d'Intel. L'installation devrait se passer sans heurts sauf à la dernière étape, pour l'installation du ds5. Lorsque l'installateur préconise de reprendre avec sudo, j'ignore si c'est vraiment utile, mais il peut y avoir des problèmes intrinsèquement liés au nommage : j'inclus la petite note que j'avais écrite pour éviter les ennuis :

 install ds5 from []/intelFPGA/[version]/embedded/ds5-installer with prompt <<<comprends pas cette ligne>>>
 ```bash
    sudo ./install.sh [sth]/embedded/ds-5
```
 NB : very important to name it ds-5 otherwise it won't be found, other places than embedded might work but unsure
 
 link from []/intelFPGA/[version]/embedded/host_tools/altera/ds5_link : prompt
 ```bash
    ./ds5_link
```
should work directly if not the path can be specified by using
```bash
    sudo ./ds5_link [path-to-ds5-folder]
```
 
 finally cross-compilation environment in intelFPGA/[version]/embedded should work and find sd5 correctly after
 ```bash
    ./embedded_command_shell.sh
```
 
 why? because naming magic

### Tester l'installation

Tout comme avec l'émulation, on peut télécharger un exemple sur le site (cette fois un exemple pour arm) et le tester, cette fois-ci il faut omettre l'option -march=emulator dans la compilation du device, transférer le host et le .aocx sur la cible (toujours dans le même fichier) et exécuter le host directement après lui avoir donné des droits d'exécution.

### Etape 6 optionnelle : compiler ou émuler sur une carte non-Intel

Il faut récupérer le BSP ainsi que les instructions du fournisseur. Dans le cas de la de1soc on peut trouver le BSP et le manuel sur cette même page : https://www.terasic.com.tw/cgi-bin/page/archive.pl?Language=English&CategoryNo=165&No=836&PartNo=4#contents

Il faudra bien faire pointer AOCL_BOARD_PACKAGE_ROOT sur le BSP de la carte cible.
