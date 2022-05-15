# 2022_CAPTEUR_Pereur_Cuculière
--------------------------------
## Sommaire
* 1 [Etapes de conception](#etapes-de-conception)
* 2 [Design et simulations du circuit analogique](#design-et-simulations-du-circuit-analogique)
* 3 [Conception du SHIELD sous KiCAD](#conception-du-shield-sous-kicad)
* 4 [Développement du programme Arduino](#développement-du-programme-arduino)
* 5 [Banc de tests](#banc-de-tests)
* 6 [Datasheet](#datasheet)

--------------------------------
## Etapes de conception

- Design et simulations du circuit d'amplification sous LtSpice
- Implémentation du circuit sous KiCAD et design du PCB
- Fabrication du PCB et soudure des composants
- Programmation du code arduino
- Création de l'application android
- Conception d'un banc de test
- Test et caractérisation du capteur

--------------------------------
## Design et simulations du circuit analogique

Le capteur que nous souhaitons utiliser pour ce projet est alimenté en 5V pour une résistance variant d’environ 20M à 100M ohm très grande devant l’impédance de l’arduino. Une mesure directe d’un courant de l’ordre de 100 nA n’est pas possible avec l’arduino. Il nous faut donc amplifier le signal. Pour cela nous avons utilisé le montage électrique ci-dessous. C’est un amplificateur transimpédance à 2 étages.

Ce montage gère à la fois l’amplification et le filtrage du signal pour limiter l’amplification du bruit. Le montage possède 3 filtres en tension et un filtre en courant (voir schéma ci-dessous).
![Schéma_Ampli](https://user-images.githubusercontent.com/98756768/163346556-cbdbaac7-0b5a-469e-9e8a-d9c0a090c3a1.jpg)

On peut calculer la résistance du capteur selon la formule suivante : 

<p align="center"><img width="438" alt="image" src="https://user-images.githubusercontent.com/98756768/166805082-91a18edf-60b1-47d5-a5ce-461050bdc2cc.png"></p>
  
|Filtre|Fréquence de coupure|
|------|------|
|  1   | 16 Hz|
|  2   | 2,1 Hz|
|  3   | 1,6 kHz|

Ci-dessous, nous pouvons voir la réponse de Vadc en fonction du temps pour une tension de capteur variant de 0 à 1V. A gauche le signal sans l'influence du 50hz et à droite avec l'influence du 50hz
<p align="center">
  <img src="https://user-images.githubusercontent.com/98756768/163351592-08c6bdaa-d5c6-4ba1-9c82-309b9e376945.jpg" width="500" />
  <img src="https://user-images.githubusercontent.com/98756768/163351538-64b146e8-af4c-4385-9f51-5eae3a89d5b3.jpg" width="500" /> 
</p>

Le gain du circuit est : A0 = 1+R3/R2 

On peut donc faire varier le gain du circuit en changeant la valeur de R2. Pour cela nous avons remplacé R2 de notre circuit par un potentiomètre digital pilotable grâce à l’Arduino. Nous avons donc étudié l'influence de R2 sur le signal. Pour cela, nous avons fait varier la résistance R2 de 100 ohm à 2 kohm avec un pas de 100 ohm. 
Sur le graphique ci-dessous, nous pouvons voir les différentes courbes de Vadc en fonction du temps. La courbe supérieure étant pour R2 = 100 ohm et inférieure pour R2 = 2 kohm. 

![Simulation_V(R2)_R0_100k](https://user-images.githubusercontent.com/98756768/163351654-184a1fdd-b3a8-4426-b8d4-4ebdba35b11a.jpg)

On observe une variation non-linaire de la réponse en fonction de R2. Notre plage d'utilisation se situe vers 1kohm. Autour de cette valeur, il y peu de calibres possible ce qui réduit la finesse de la calibration et rend les mesures plus difficiles. 


--------------------------------
## Conception du SHIELD sous KiCAD

>- Création des symboles de l'encodeur rotatoire, du module bluetooth, de l'OLED et du potentiomètre digital.
>  Tous les symboles sont répertoriés dans le dossier KiCad. 
>
>- Réalisation du schéma électrique comportant l'amplificateur transimpédance, l'OLED, le module bluetooth, le potentiomètre digital et l'encodeur rotatoire. 

![image](https://user-images.githubusercontent.com/98756768/161789242-9f2b5f16-10a0-4c07-a2e3-fd237e8268ff.png)

Nous avons ensuite procédé au routage du PCB et l'implémentation des modèles 3D des différents modules: 

<p float="left">
  <img src="https://user-images.githubusercontent.com/98756768/161790127-bbae3fb0-e9b2-458b-a9d0-22b62c6be26e.jpg" width="500" />
  <img src="https://user-images.githubusercontent.com/98756768/163399923-f231b26b-25a9-4389-b133-757dd3b60362.jpg" width="500" /> 
</p>



--------------------------------
## Développement du programme Arduino

### Description du programme 

Le programme de l'arduino uno, écrit en C++, permets de lire la tension à la sortie du capteur et calcul la valeur de la résistance. Il a été réalisé en utilisant le environnement de développement Arduino (Arduino IDE). 

Ce programme contient plusieurs parties :
* OLED : Création d'un menu contenant l'affichage de la résistance, l'autocalibration, les crédit du projet et une calibration pour obtenir le minium et maximum de la résistance
* Encodeur rotatoire : gestion du menu grâce à des interruptions
* SPI (potentiomètre digital) : gestion de la résistance variable R2 et autocalibration
* Bluetooth : traitement et envoi des données à l'application

Le code est disponible [ici.](https://github.com/MOSH-Insa-Toulouse/2021_2022_PEREUR_CUCULIERE/tree/main/PGM_Arduino/SHIELD/PGM_Capteur_1_5/PGM_Capteur_1.5)

### Bibliothèques utilisés
>- SoftwareSerial.h pour la définition des ports utilisés pour le module bluetooth et le potentiomètre digital
>- Adafruit_GFX.h et Adafruit_SSD1306.h pour l'OLED I2C
>- Wire.h qui s'occupe de la communication en I2C avec l'OLED
>- SPI.h pour la communication avec les module SPI, ici le potentiomètre digital

### Amélioration possibles 
>Une optimisation du code pour permettre une exécution plus rapide. Pour cela, il serait possible de diminiuer le nombre de variables utilisées pour la gestions des >menus ainsi qu'un changement sur la gestion du changement de menus. 
>
>Le code du potentiomètre digital pourrait également être améliorer pour une meilleure réactivité. Actuellement il nécessite un certain délai pour calibrer les >valeurs. 
>
>L'application doit être optimisé car elle ne fonctionne actuellement pas sur tous les téléphones.
--------------------------------
## Application android

L'application android a été crée à l'aide de l'outil MIT APP INVENTOR. Il est possible de la télécharger via le .apk du dossier [application](https://github.com/MOSH-Insa-Toulouse/2021_2022_PEREUR_CUCULIERE/tree/main/APPLICATION_ANDROID).

Le but de cette application est de récupérer les données du capteur via le module bluetooth présent sur le SHIELD et de les afficher sur un téléphone. Cette application fonctionne grâce au programme arduino qui va venir envoyer à interval régulier la valeur de Vadc. Vous pouvez voir ci-dessous une capture d'écran de l'application. 

<p align="center">
  <img src="https://user-images.githubusercontent.com/98756768/168440169-d3f04dfb-fd62-40b5-9823-554bceb8bd8f.jpg" width="300" />
</p>

--------------------------------
## Banc de tests

Les différents capteurs ont été testé en tension et en compression pour des rayons de courbures variant de 5,5 cm à 1,5 cm. Le banc de test est constitué d'un morceau de carton dans lequel des fentes ont été découpé et d'un support en plastique souple pour rigidifier le capteur. 

Le protocole de test est disponible en suivant [ce lien.](https://github.com/MOSH-Insa-Toulouse/2021_2022_PEREUR_CUCULIERE/tree/main/Banc_de_Tests)

--------------------------------
## Datasheet

La datasheet contient les caractéristiques principales du capteur ainsi que le circuit d'amplification transimpédance utilisé pour les mesures. On la retrouve dans le dossier [Datasheets](https://github.com/MOSH-Insa-Toulouse/2021_2022_PEREUR_CUCULIERE/tree/main/Datasheets) du github. 
