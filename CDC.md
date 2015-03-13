# Introduction #

### Zingole.com ###
Toutes les informations relatives à zingole.com

Le site permet à quiconque de s'inscrire.
Chaque user inscrit (appelé zingole) peut dénoncer une zingolerie. Toute zingolerie dénoncée entraine le vote de l'ensemble des zingoles sauf le zingole concerné.
Une fois le vote terminé, une maj est effectué; le zingole est crédité du nombre de points.
Il est possible d'affiche le classement des zingoles, les dernieres zingoleries, les tops zingolerie...
Il est possible d'avoir le détail des votes sur une zingolerie
On peut observer le profil de chaque zingole


# Composition page web #
## header ##

contient

  * le logo du site (trop classe ^^)
  * le pseudo avec qui l'on est connecté
  * un bouton option/mon compte
  * un bouton deconnexion

## footer ##

contient

  * des liens vers les sites partennaire (mossieurestunmossieru notement...)
  * le nom/pseudo des dev
  * l'heure actuelle du serveur (permet de voir s'il y a pas de pb dans les votes)

## menu ##

dans le panneau latérale qui permet la navigation entre les pages...

## corps ##
tout le contenu détaillé dans la partie suivante


# Technique #

## Accueil // Historique zingolerie ##

on affiche les trois tableau les uns ou dessus des autres?
ou l'on décide quel tableau on affiche?


affiche les dernière zingoleries sous la forme de tableau
|date de fin vote|zignole victime|type|le motif|zingole dénonciateur|note attribuée|
|:---------------|:--------------|:---|:-------|:--------------------|:--------------|

affiche le top zingole (possibilité de trier par nbr de zg ou nbr de pts)
|rang|zignole victime|nombre zingolerie|note attribuée|
|:---|:--------------|:----------------|:--------------|


affiche le top zingolerie (si nbr pts== la plus récente en haut)
|rang|note attribuée|zignole victime|type|le motif|zingole dénonciateur|date de fin vote|
|:---|:--------------|:--------------|:---|:-------|:--------------------|:---------------|

## Inscription ##
un formulaire avec les champs
  * email `type_html:input`
  * pseudo `type_html:input`
  * password `type_html:password`
  * confirmer password `type_html:password`
  * description type\_html:text (500 caractères) (optionnel lors de l'inscription)
  * avatar type\_html:file (50ko max) (optionnel lors de l'inscription)
  * groupe `type_html:select` (valeur défaut au début, et en hidden)
et le bouton d'action
  * valider inscription

## Connexion ##
un formulaire avec les champs:
  * pseudo `type_html:input`
  * password `type_html:password`
et le bouton d'action
  * valider connexion

Un lien vers une page mot de passe perdu? (pas dans la version de base)

## mon compte ##
Permet de changer notre adresse email

> un formulaire avec le champ
    * email `type_html:input`
> et le bouton d'action
    * changer adresse email
Permet de changer notre mot de passe


> un formulaire avec les champs
    * ancien password `type_html:password`
    * nouveau password `type_html:password`
    * confirmer password `type_html:password`
> et le bouton d'action
    * modification mdp

Permet de changer notre description

> un formulaire avec le champ
    * description type\_html:text (500 caractères)
> et le bouton d'action
    * valider description

Permet de changer notre avatar

> un formulaire avec les champs
    * avatar type\_html:file (50ko max)
> et le bouton d'action
    * upload avatar


## Dénoncer zingolerie ##
Page contenant un formulaire avec le champs
  * zingole `type_html:select`
  * description `type_html:text` (100 caractères)
  * type `type_html:select` (valeur défaut au début, et en hidden)
bouton action:
  * valider

## Voter ##
il y a un tableau à plusieurs colonnes, les lignes sont affichées du vote le  plus récent en haut de tableau au plus ancien en bas de tableau. Cependant un vote en cours plus anciens sera forcement en tete de tableau

le format des lignes est:

pour un vote en cours:
|zignole victime|type|le motif|zingole dénonciateur|un formulaire contenant 6 ou 10 boutons radio et un bouton "voter!"|date de fin de vote|
|:--------------|:---|:-------|:--------------------|:------------------------------------------------------------------|:------------------|

pour un vote terminé et voté valide:
|zignole victime|type|le motif|zingole dénonciateur|note attribuée|date de vote|
|:--------------|:---|:-------|:--------------------|:--------------|:-----------|

pour un vote terminé et non voté :
|zignole victime|type|le motif|zingole dénonciateur|non voté|date de vote|
|:--------------|:---|:-------|:--------------------|:--------|:-----------|

pour un vote terminé et non voté et invalidé:
|zignole victime|type|le motif|zingole dénonciateur|non voté & non valide|date de vote|
|:--------------|:---|:-------|:--------------------|:---------------------|:-----------|

pour un vote terminé et voté et invalidé:
|zignole victime|type|le motif|zingole dénonciateur|note attribuée & non valide|date de vote|
|:--------------|:---|:-------|:--------------------|:---------------------------|:-----------|




## Profil zingole ##
affiche l'avatar

affiche son nombre de points

affiche le tableau de toutes ses zingolerie terminées
|date de fin vote|type|le motif|zingole dénonciateur|note attribuée|
|:---------------|:---|:-------|:--------------------|:--------------|

(affiche le tableau des zingoleries dénoncées terminées ?)
|date de fin vote|type|le motif|zingole victime|note attribuée|
|:---------------|:---|:-------|:--------------|:--------------|

## zingolerie en detail ##

affiche l'avatar et de pseudo du zingole concerné

affiche un tableau avec l'ensemble des couples pseudo/ notes de tous les votes pour cette zingolerie
|zingole votant|note attribuée|date de vote|
|:-------------|:--------------|:-----------|


## maj bdd ##

cette mise a jour se fera avec une page PHP, que l'on appelera toutes les minutes au moyen d'une tache cron
en x étapes:

étape 1:

dans la table des zingoleries, on s'occupe toute celle qui ont terminé à false et une date dans le passé.
on passe terminé à vrai. on cherche tous les votes associés, et l'on fait la moyennes de tous les bulletins ayant a\_voté a vrai.
on met alors a jour l'entrée "point" de la zingolerie

on met à jours tous les bulletins de votes relatifs à la zingolerie qui n'étaient pas utilisés (a\_voter a false): on mets points à -1 (pour différencier d'un bulletin valable) et a voter\_a vrai

étape 2:

dans la table des zingoleries, on s'occupe toute celle qui ont terminé à false et une date dans le futur. on récupère le nombre de votant sur cette zingolerie, et l'on compte le nombre de bulletins exprimés ayant une note de zero.

**Si** ce nombre est suppérieur ou égale à la moitier du nombre de de votant, le vote est annulé

**alors**
on met terminé à vrai et le nombre de point est fixé à zero

on met à jours tous les bulletins de votes relatifs à la zingolerie qui n'étaient pas utilisés (a\_voter a false): on mets points à -1 (pour différencier d'un bulletin valable) et a voter\_a vrai

**sinon**
on ne fait rien

étape 3:

dans la table des users, on fait la mise a jour de ses points en faisant simplement la somme de toutes les zingoleries qui lui sont attribuées


# Sécurité #
## Suivi de session ##
Activer le suivi de session et support des cookies
Systeme parallèle pour les connexions qui n'acceptent pas les cookies
## Autorisations des accès aux pages ##
Espace zone membre
## Cryptage des mots de passe ##
Utilisation du SHA256 + Salt
## Contrôle des données saisies ##
HTMLEntity pour eviter les XSS