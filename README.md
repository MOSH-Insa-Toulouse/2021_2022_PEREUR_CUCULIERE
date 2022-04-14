# 2022_CAPTEUR_Pereur_Cuculière
--------------------------------
## Sommaire

--------------------------------
## Etapes de conception (Générales)

- Design et simulations du circuit d'amplification sous LtSpice
- Implémentation du circuit sous KiCAD et design du PCB
- Fabrication du PCB et soudure des composants
- Programmation du code arduino
- Création de l'application android
- Conception d'un banc de test
- Test et caractérisation du capteur

--------------------------------
## Design et simulations du circuit analogique - SHIELD

Le capteur que nous souhaitons utiliser pour ce projet est alimenté en 5V pour une résistance variant d’environ 20M à 100M ohm très grande devant l’impédance de l’arduino. Une mesure directe d’un courant de l’ordre de 100 nA n’est pas possible avec l’arduino. Il nous faut donc amplifier le signal. Pour cela nous avons utilisé le montage électrique ci-dessous. C’est un amplificateur transimpédance à 2 étages.

Ce montage gère à la fois l’amplification et le filtrage du signal pour limiter l’amplification du bruit. Le montage possède 3 filtres en tension et un filtre en courant (voir schéma ci-dessous).
![Schéma_Ampli](https://user-images.githubusercontent.com/98756768/163346556-cbdbaac7-0b5a-469e-9e8a-d9c0a090c3a1.jpg)

On peut calculer la résistance du capteur selon la formule suivante : 

R_Capteur=(1+\frac{R3}{R2}).R1.(\frac{V_CC}{V_ADC})-R1-R5 avec un gain `1+\frac{R3}{R2}`

|Filtre|Fréquence de coupure|
|------|------|
|  1   | 16 Hz|
|  2   | 2,1 Hz|
|  3   | 1,6 kHz|

Ci-dessous, nous pouvons voir la réponse de Vadc en fonction du temps pour une tension de capteur variant de 0 à 1V. A gauche le signal sans l'influence du 50hz et à droite avec l'influence du 50hz
<p float="left">
  <img src="https://user-images.githubusercontent.com/98756768/163351592-08c6bdaa-d5c6-4ba1-9c82-309b9e376945.jpg" width="503" />
  <img src="https://user-images.githubusercontent.com/98756768/163351538-64b146e8-af4c-4385-9f51-5eae3a89d5b3.jpg" width="503" /> 
</p>

Le gain du circuit est : A0 = 1+R3/R2 

On peut donc faire varier le gain du circuit en changeant la valeur de R2. Pour cela nous avons remplacé R2 de notre circuit par un potentiomètre digital pilotable grâce à l’Arduino. Nous avons donc étudié l'influence de R2 sur le signal. Pour cela, nous avons fait varier la résistance R2 de 100 ohm à 2 kohm avec un pas de 100 ohm. 
Sur le graphique ci-dessous, nous pouvons voir les différentes courbes de Vadc en fonction du temps. La courbe supérieure étant pour R2 = 100 ohm et inférieure pour R2 = 2 kohm. 

![Simulation_V(R2)_R0_100k](https://user-images.githubusercontent.com/98756768/163351654-184a1fdd-b3a8-4426-b8d4-4ebdba35b11a.jpg)




--------------------------------
## Conception du SHIELD sous KiCAD

Nous avons commencé par le l'implémentation de l'amplificateur transimpédance dans le schéma électrique global du SHIELD visible ci-dessous : 
![image](https://user-images.githubusercontent.com/98756768/161789242-9f2b5f16-10a0-4c07-a2e3-fd237e8268ff.png)

Nous avons ensuite procédé au routage du PCB : 

<p float="center">
  <img src="https://user-images.githubusercontent.com/98756768/161790127-bbae3fb0-e9b2-458b-a9d0-22b62c6be26e.jpg" width="503" />
  <img src="https://user-images.githubusercontent.com/98756768/163399923-f231b26b-25a9-4389-b133-757dd3b60362.jpg" width="503" /> 
</p>



--------------------------------
## Développement du programme Arduino


--------------------------------
## Banc de tests


--------------------------------
## Création de la datasheet
