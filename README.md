# TouchDesigner X Chataigne

**Ou comment amener des couleurs et des valeurs en temps réel depuis Touchdesigner dans Chataigne.**

### Avant tout :

- Le [tuto introduction à Touchdesigner](https://github.com/LucieMrc/IntroTD) (EN).
- La [documentation officielle de Chataigne](https://bkuperberg.gitbook.io/chataigne-docs/v/fr/)
- Le [tuto introduction à Chataigne](https://github.com/LucieMrc/Chataigne_2spi) (🚧 en travaux 🚧).

# De TouchDesigner à Chataigne

## Exporter une couleur en temps réel

Dans **Touchdesigner**, on crée un `noise` TOP avec beaucoup de période, qui bouge lentement, avec peu d'harmoniques. On passe dans un `resolution` TOP pour avoir une image de 1 pixel par 1 pixel, le pixel central de l'image.

C'est ma méthode pour avoir une couleur random qui évolue dans le temps, mais on peux trouver n'importe quelle autre manière de récuperer un pixel de la couleur principale d'un visuel en temps réel.

On crée un `TOP to` CHOP pour récuperer les valeurs R, G, B et A de l'image et on les envoie en OSC avec `OSC out`.

![screen1](./images/screen1.png)

Dans **Chataigne**, on crée un module OSC dans le menu des modules en haut à gauche, et on met le même local port dans la partie `OSC Input` que dans Touchdesigner (par défaut : 10000).

Si on a le bon port, on voit les valeurs r, g, b, a et le sample rate.

![screen1](./images/screen2.png)

Pour assigner la couleur à des channels DMX, on crée un mapping dans la State Machine, et on crée un input pour R, G et B.

On crée ensuite un output `DMX`>`Set Color` et on peux entrer le numéro channel R de la fixture (sur des lights, le channel de la valeur de rouge est toujours suivi par celui de la valeur de vert puis la valeur de bleu).

![screen1](./images/screen3.png)

Si on clique sur le petit symbole lien 🔗 à gauche de la couleur, on voit que la couleur est calculée avec les trois valeurs reçues, R, G et B.

![screen1](./images/screen4.png)

Interface de Touchdesigner + la couleur dans Chataigne :

![screen1](./images/screen5.png)

## Exporter une variable en temps réel

Dans **Touchdesigner**, on crée un noise qui change lentement avec absTime.seconds*20, et on l'envoie dans `Osc out`, en passant dans un `Select` CHOP pour l'associer aux données R, G, B et A que l'on envoie déjà.

![screen1](./images/screen6.png)

Dans **Chataigne**, on crée un nouveau mapping et dans l'input on récupère le nom de la variable que l'on vient de créer (ici chan1).

![screen1](./images/screen7.png)

Le noise par défaut dans Touchdesigner est entre -0.5 et 0.5, donc on doit remapper la variable si on veux l'utiliser en DMX par exemple, entre 0 et 255.

Pour ça, il faut créer un nouveau filtre dans le mapping et choisir Remap > Remap.

![screen1](./images/screen8.png)

Ensuite, on mets -0.5 et 0.5 en Input Min/Max, et 0 et 255 en Target Min/Max.

![screen1](./images/screen9.png)

On peux maintenant se servir de cette variable pour contrôler un paramètre DMX.

## Enregistrer une courbe

Dans **Touchdesigner**, on crée un `Pattern` CHOP, et on met absTime.seconds en phase dans les paramètres. À nouveau, on l'envoie dans `Osc out`, en passant dans un `Select` CHOP pour l'associer aux données chan1, R, G, B et A que l'on envoie déjà.

![screen1](./images/screen10.png)

Dans **Chataigne**, on crée une nouvelle séquence et un Mapping dans cette séquence

![screen1](./images/screen11.png)

Dans l'inspecteur de ce mapping, on ouvre le menu `Recorder` et on choisit notre channel du pattern dans l'Input Value (ici chan2).

![screen1](./images/screen12.png)

Dans le Mapping dans la timeline, on passe la range à celle du Pattern de Touchdesigner, -1 et 1, et on fait Range Remap Mode > Proportional, afin de pouvoir remapper la courbe après.

![screen1](./images/screen13.png)

On clique sur `Arm` dans le Recorder ou directement dans le Mapping dans la timeline, et on lance la lecture de la timeline pour enregistrer la courbe.

![screen1](./images/screen14.png)

La courbe est rouge pendant son enregistrement, et devient une courbe avec des points éditables lorsqu'on met pause à la lecture de la timeline.

![screen1](./images/screen15.png)

# De Chataigne à Touchdesigner

## Exporter une couleur en temps réel

Dans **Chataigne**, créer une séquence de couleurs (voir [Lights salle fond vert > Créer une courbe de couleur](https://github.com/LucieMrc/SalleFondVert_Controller#courbe-de-couleur)) :

![screen1](./images/screen16.png)

Dans la partie Outputs de l'inspecteur de la séquence de couleurs, cliquer sur le bouton ➕ vert à droite et choisir `OSC`>`Custom Message`.

![screen1](./images/screen17.png)

Ensuite, on peux choisir le nom de la variable que l'on va envoyer en OSC avec la ligne `Adress` (par défaut : /example), et dans la partie `Arguments` on clique sur le bouton ➕ vert à droite pour choisir le type de variable que l'on envoie, ici `Add Color Argument`.

![screen1](./images/screen18.png)

Avant d'aller dans Touchdesigner, on vérifie le port de sortie OSC, dans l'inspecteur du module OSC, à la ligne `Remote port` (9000 par défaut).

![screen1](./images/screen19.png)

Dans **Touchdesigner**, on crée un `OSC in` CHOP, puis un `rename` pour changer le nom de chaque argument en r, g, b et a. On ajoute un `math` pour diviser chaque argument de la couleur par 255, car les couleurs sont entre 0 et 255 dans Chataigne mais entre 0 et 1 dans Touchdesigner.

Finalement, on crée un `CHOP to` TOP pour créer une texture avec cette couleur.

![screen1](./images/screen20.png)

Interface de la séquence de Chataigne + la couleur dans Touchdesigner :

![screen1](./images/screen21.png)

# Pour aller + loin

- Le tuto pour [contrôler les lights de la salle fond vert](https://github.com/LucieMrc/SalleFondVert_Controller).














