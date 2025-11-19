# Rapport hebdomadaire – Semaine du 17/11/2025

## 1. Objectif de la semaine
L’objectif de cette semaine était de poursuivre la prise en main du projet *Hello Zynq* et de comprendre la chaîne complète permettant d’exécuter une application ASTERIOS sur la carte ZUBOARD 1CG. Je voulais notamment :

- vérifier la configuration matérielle et logicielle ;
- exécuter le programme fourni ;
- commencer à modifier et recompiler l’application pour réaliser des premiers tests simples.

---

## 2. Travail effectué

### 2.1 Lecture et compréhension de la documentation
J’ai étudié la documentation fournie, en particulier les prérequis matériels et logiciels mentionnés dans le README :

- alimentation et câblage (USB-C, JTAG/UART) ;
- configuration du mode JTAG via les DIP switches ;
- installation des outils sous Windows (ASTERIOS Developer, RTK et toolchain ARM) ;
- commandes pour charger les binaires (`xsdb.bat`) et observer l’output via UART.

### 2.2 Mise en route de la carte
J’ai réussi à :

- configurer et alimenter correctement la carte ;
- établir la connexion JTAG/UART ;
- utiliser `xsdb.bat` pour charger le binaire déjà compilé (`load_binaries.tcl`) ;
- observer le fonctionnement du programme *Hello World* via le port série (115200 bauds).

Cela confirme que la chaîne matérielle et le script de chargement fonctionnent correctement.

### 2.3 Tentatives de modification et recompilation
J’ai ensuite essayé de modifier le code du projet (par exemple dans `HelloWorld.psy`) afin de créer un premier test personnalisé, puis de relancer : `./build.sh`


pour recompiler l’application.

Cependant, le processus de recompilation a échoué :

- le script `build.sh` signalait que le RTK (`k2.elf`) n’était pas trouvé ;
- des problèmes liés aux chemins (PATH) entre Windows et Git Bash empêchaient `psyko` de fonctionner correctement ;
- malgré plusieurs tentatives (définition de `RTK_PATH`, modifications du script, export de variables d’environnement), la compilation ne s’est pas terminée.

En conséquence, je n’ai pas pu générer un nouveau `app.elf`.

---

## 3. Difficultés rencontrées

### 3.1 Chemins Windows vs Git Bash
Les outils ASTERIOS sont installés dans `C:\Program Files...` alors que `build.sh` utilise des chemins POSIX (`/c/...`).  
Cela a entraîné des erreurs de détection de `psyko.exe` et du RTK.

### 3.2 Localisation du RTK
Le projet `hello-zynq` est conçu pour un package ASTERIOS complet (psyko + RTK).  
Dans mon installation, ils sont séparés, ce qui nécessite d’indiquer explicitement le chemin RTK via `-K` ou `RTK_PATH`.

### 3.3 Recompilation bloquée
Le script ne retrouve pas le RTK et la recompilation échoue avant de produire un nouveau binaire.

---

## 4. Prochaines étapes

### 4.1 Finaliser la configuration du build
- Définir correctement `RTK_PATH` et `PSYKO` dans `build.sh`.
- Vérifier que Git Bash pointe bien vers les installations ASTERIOS correctes.
- S’assurer que les modifications dans VS Code sont bien prises en compte par le build.

### 4.2 Évaluer l’utilisation d’ASTERIOS Developer
Je me demande si, pour réaliser les premiers tests simples, il serait utile de créer un petit projet directement dans ASTERIOS Developer pour générer le code automatiquement, puis l’intégrer au pipeline existant.

### 4.3 Une fois la recompilation opérationnelle
- Modifier progressivement l’application (période, messages, agents supplémentaires…).
- Tester sur la carte via `xsdb.bat`.
- Préparer ensuite un cas d’usage plus complexe pour le démonstrateur.

---

## 5. Conclusion
Cette semaine m’a permis de :

- comprendre la chaîne de développement ASTERIOS → ZUBOARD ;
- valider l’exécution du programme fourni ;
- identifier précisément les blocages au niveau de la recompilation.

La priorité pour la suite est d’obtenir un build fonctionnel afin de pouvoir commencer à développer et tester mes propres scénarios ASTERIOS sur la carte.


