# Système Embarqué de Détection de Chute (FPGA / VHDL)

Ce dépôt documente la conception d'un système numérique embarqué sur FPGA (carte Nexys4 / Artix-7) destiné à la détection de chute pour les personnes âgées, réalisé dans le cadre de ma formation en L2 à Sorbonne Université.

*Note : Les fichiers sources VHDL étant archivés, ce dépôt présente le rapport de conception détaillé ainsi que le fichier de contraintes matérielles (mapping des broches).*

---

### 1. Objectif du Projet
L'objectif est de s'interfacer avec un accéléromètre embarqué pour acquérir en temps réel les données d'accélération sur 3 axes (X, Y, Z), de calculer la norme du vecteur d'accélération, et de déclencher une alerte mémorisée si une situation d'impesanteur (caractéristique d'une chute libre) est détectée.

---

### 2. Architecture Matérielle et Acquisition
* **Capteur** : Utilisation de l'accéléromètre ADXL362 intégré à la carte.
* **Protocole de Communication** : Interfaçage via un bus SPI (Signaux MOSI, MISO, SCLK, CS) pour récupérer les données brutes sur 12 bits.
* **Contraintes Matérielles** : Le fichier (Projet_Detection_Chute.pdf) détaille la configuration complète du fichier XDC, liant les ports logiques de l'entité VHDL aux broches physiques du FPGA (LEDs, interrupteurs, afficheurs 7 segments).

---

### 3. Traitement Numérique du Signal
La logique de détection repose sur plusieurs blocs VHDL instanciés structurellement :
* **Calcul de la Norme** : Implémentation du calcul $Vs = \sqrt{Ax^2 + Ay^2 + Az^2}$ pour évaluer l'accélération globale.
* **Codage en Virgule Fixe** : Les calculs et les comparaisons sont réalisés en utilisant un format de représentation en virgule fixe **Q4.8**.
* **Logique de Seuil** : Conception d'un comparateur N-bits paramétrable. La chute est détectée dès que la norme $Vs$ passe en dessous d'un seuil critique fixé à $0.25g$.

---

### 4. Gestion de l'Alerte
* **Mémorisation de l'état** : L'alerte de chute ne doit pas disparaître après l'impact. Une **bascule RS** a été intégrée à la logique séquentielle pour figer le signal d'alerte à "1" dès le franchissement du seuil, allumant une LED de détresse jusqu'à l'intervention et la réinitialisation manuelle (Reset) du système.
