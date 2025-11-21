# Rapport hebdomadaire – Semaine du 17/11/2025

## 1. Objectif de la semaine
L’objectif de cette semaine était de poursuivre la prise en main du projet *Hello Zynq* et de maîtriser toute la chaîne permettant d’exécuter une application ASTERIOS sur la carte ZUBOARD 1CG. Les buts principaux étaient :

- vérifier la configuration matérielle et logicielle ;
- comprendre le fonctionnement du script de chargement (`xsdb`) ;
- installer correctement les outils (psyko, RTK, toolchain ARM, drivers Xilinx) ;
- recompiler l’exemple fourni et préparer la phase de débogage.

---

## 2. Travail effectué

### 2.1 Installation et configuration de l’environnement
J’ai installé l’ensemble des outils nécessaires :

- ASTERIOS Developer + RTK ;
- Xilinx Hardware Server 2022.2 (avec drivers JTAG) ;
- toolchain ARM `aarch64-none-elf-gcc` (version 10.3).

Après plusieurs essais, j’ai constaté que Git Bash ne trouvait pas certains exécutables Windows lorsque les chemins contenaient des espaces ou des répertoires imbriqués.  
**La solution a été de définir explicitement les chemins avec `export` au début de chaque session Git Bash**, par exemple :

```bash
export PSYKO="/c/Program Files (x86)/Krono-Safe/psyko-9.5.0/bin/psyko.exe"
export XSDB="/c/Xilinx/HWSRVR/2022.2/bin/xsdb.bat"
export RTK_PATH="/c/Program Files (x86)/Krono-Safe/rtk-armv8-zynq-zcu10x-apu-gcc-1.8.0/"
export PATH=$PATH:/c/Toolchains/gcc-arm-10.3/gcc-arm-10.3-2021.07-mingw-w64-i686-aarch64-none-elf/bin
```

Cela m’a permis d’obtenir un environnement cohérent entre Windows et Git Bash et de lancer correctement le script de compilation.

---

### 2.2 Mise en route de la carte
J’ai pu :

- configurer la carte en mode JTAG (DIP switches) ;
- l’alimenter et établir la connexion JTAG/UART ;
- charger les binaires via `xsdb build/load_binaries.tcl` ;
- ouvrir le port série via PuTTY à **115200 bauds**.

Le programme *Hello World* s’affiche correctement, ce qui confirme que la chaîne matérielle et logicielle fonctionne comme prévu.

---

### 2.3 Recompilation de l’application
Une fois les problèmes de PATH résolus, j’ai pu exécuter :

```bash
./build.sh
```

La recompilation fonctionne désormais correctement.  
Le script génère notamment :

- `build/app.elf` ;
- `build/load_binaries.tcl` ;
- les binaires RTK et bootloader nécessaires.

J’ai donc maintenant une chaîne complète : **modifier → compiler → charger → exécuter**.

---

## 3. Difficultés rencontrées

### 3.1 Configuration PATH Windows / Git Bash
La double structure des dossiers de la toolchain et la présence d’espaces dans les chemins Windows ont posé problème.  
J’ai résolu cela en utilisant les chemins Unix (`/c/...`) et en définissant explicitement les variables nécessaires avec `export`.

### 3.2 Débogage GDB encore bloqué
Bien que la compilation fonctionne, je n’ai pas encore réussi à utiliser :

```bash
aarch64-none-elf-gdb build/app.elf
```

Le binaire `aarch64-none-elf-gdb.exe` ne semble pas fonctionner correctement ou n’est pas détecté via Git Bash.  
C’est actuellement la seule étape du pipeline qui reste à débloquer.

---

## 4. Prochaines étapes

### 4.1 Finaliser GDB + hw_server
- Vérifier la présence et le bon fonctionnement de `aarch64-none-elf-gdb.exe` ;
- corriger définitivement la structure de la toolchain si nécessaire ;
- tester la connexion entre GDB et la carte via :

```gdb
tar ext :3001
```

- placer les premiers breakpoints (`uart_write`, `error_handler`).

### 4.2 Premières modifications de l'application
Une fois la partie débogage fonctionnelle, je pourrai :

- ajouter une deuxième tâche ;
- modifier les périodes d’exécution ;
- tester des échanges simples entre agents ;
- observer le comportement via UART et GDB.

### 4.3 Préparation du démonstrateur
Commencer à réfléchir au scénario du POC (traitement périodique, charge temps réel simple, mesures de latence, etc.).

---

## 5. Conclusion
Cette semaine m’a permis de :

- installer et comprendre l’environnement ASTERIOS ;
- exécuter le programme de démonstration sur la ZUBOARD ;
- réussir la recompilation complète via `build.sh` ;
- identifier clairement le dernier point bloquant : l’utilisation du débogueur GDB.

La priorité pour la semaine prochaine est de finaliser la configuration du débogage afin de pouvoir ensuite développer et tester des scénarios ASTERIOS plus avancés.
