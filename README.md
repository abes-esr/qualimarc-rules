# Qualimarc-rules 

![qualimarc](https://user-images.githubusercontent.com/328244/203315079-4cabb49a-58a8-4778-80b5-d789e48fb94d.PNG)

## Documentation d'utilisation du dépôt

Ce document explique étape par étape comment ajouter ou modifier des règles dans le projet QualiMarc.

## Sommaire
- [Utilisation de GitHub](#1)
- [Déclenchement de la mise à jour](#2)
- [Langage YAML](#3)
- [Syntaxe des règles simples](#4)
- [Syntaxe des règles complexes](#5)
- [Règles de dépendance](#6)
- [Jeux de règles personnalisés](#7)

## Utilisation de GitHub-  <a id="1"></a>
Github est un outil en ligne permettant de versionner des fichiers. Il dispose d'un éditeur en ligne permettant de modifier des fichiers puis de les sauvegarder tout en conservant un historique des modifications réalisées..

### Notion de branche
Une branche est une version du code disponible dans un projet. Elle dispose de son propre historique, et toutes les modifications effectuées sur cette branche auront leur propre cycle de vie. Pour sélectionner une branche, cliquez sur la liste déroulante : ![branches](https://user-images.githubusercontent.com/57490853/190959280-452d5f2b-a04e-4061-ac49-3bea09ddf00b.PNG) 
et sélectionnez la branche de travail. Le contenu de la branche est affiché.
> Pour modifier les règles et qu'elles soient prises en compte sur l'environnement de test, il est nécessaire de se 
> positionner sur la branche ``main``

### Editer un fichier
Pour modifier le contenu d'un fichier, cliquez sur le nom du fichier à modifier, puis sur la page suivante, cliquez sur le bouton ![edit](https://user-images.githubusercontent.com/57490853/190960013-f2216993-faee-468d-aee5-daf8dd0b41e3.PNG). Un éditeur en ligne s'affichera permettant de modifier le contenu du fichier.

### Sauvegarder un fichier
La sauvegarde d'un fichier est réalisée via l'action commit dans github. Lorsqu'on fichier est en édition, en bas de la page, renseignez un titre dans le premier champ texte, puis une description des modifications effectuées dans le second, cochez la case ``Commit directly to the <nom_de_la_branche> branch`` puis cliquez sur le bouton Commit changes :  
![commit](https://user-images.githubusercontent.com/57490853/190960543-e91a3708-9308-4f43-ad39-111ecb62a1ce.PNG) 
> Un commit est une sauvegarde à laquelle est ajoutée un commentaire.
> Chaque commit donne lieu à une nouvelle entrée dans l'historique des modifications du fichier et de la branche active.

## Déclenchement de la mise à jour <a id="2"></a>
A intervalles de temps régulier, la plateforme Ansible de l'Abes va scanner le dépot Github et détecter les changements sur la branche. Lorsqu'une modification est détectée, Ansible récupère les fichiers disponibles sur la branche, et appelle un programme qui va stocker le contenu des règles décrites dans les différents fichiers dans la base de données. Une fois terminé une notification est envoyée par mail, ainsi que sur le canal Slack #notif-qualimarc.

## Langage YAML <a id="3"></a>
Les fichiers de règles sont décrits dans un langage appelé YAML. Proche du JSON, ce langage permet de décrire des données selon un syntaxe simple et structurée. Cette partie décrit les éléments de base du langage.
- La première ligne du fichier doit être ``rules: `` (ce qui annonce que la suite du fichier contient des règles)
- La syntaxe de base repose sur des couples clé: valeur (l'espace après les : est absolument nécessaire)
- l'ordre des champs n'a pas d'importance (ceci dit, pour un type de règle donnée, il est recommandé de garder le même ordre dans les attributs de la règle pour une question de lisibilité)
- Pour ajouter un commentaire faire précéder la ligne du signe ``#``
- Chaque niveau d'imbrication doit respecter un alignement vertical. Par exemple, tous les attributs d'une règle doivent être placés au même niveau d'indentation. On utilise 4 espaces pour créer une indentation.
- les chaines de caractères doivent être placée sur une même ligne, sans retour à la ligne.
- Si un attribut dispose de plusieurs valeurs, celles-ci sont disposées sur des lignes différents et précédées d'un tiret ``- ``.

Par exemple :  

![typedoc](https://user-images.githubusercontent.com/57490853/190974752-3b5b1118-8c4e-42ce-8963-734559227c80.PNG) 


## Description des règles de Qualimarc
Toutes les règles de Qualimarc ont une structure commune, puis des champs propres à chaque type de règle. Dans le chapitre décrivant les règles, les éléments obligatoires seront précisés. Voici un exemple de fichier YAML décrivant une règle : 
``` YAML
rules:
  - id:             2
    id-excel:       2
    type:           presencezone
    message:        message test test 2
    zone:           330
    priorite:       P1
    presence:       false
    type-doc:
        - A 
        - B
        - O
    type-these:
        - REPRO
        - SOUTENANCE
```
Notez que tous les attributs de la règle sont alignés verticalement, et tous les attributs du type de document aussi. Les lettres A, B et O se rapportant au type de document, un niveau d'indentation supplémentaire a été rajouté.

## Syntaxe des règles simples <a id="4"></a>
A l'heure actuelle il existe 13 types de règles dans Qualimarc : 
- ``structure`` : Présence ou absence de zone
- ``structure`` : Présence ou absence de sous-zone
- ``structure`` : Nombre de zones dans une notice
- ``structure`` : Nombre de sous-zones dans une notice
- ``structure`` : Position de sous-zones dans une zone 
- ``structure`` : Présence de sous-zones dans une même occurrence d'une zone
- ``contenu`` : Valeur d'un indicateur
- ``contenu`` : Nombre de caractères dans une sous-zone
- ``contenu`` : Présence d'une ou plusieurs chaine(s) de caractères dans une sous-zone
- ``contenu`` : Comparaison des contenus de deux sous-zone
- ``contenu`` : Type de caractères dans une sous-zone
- ``contenu`` : Comparaison de dates entre deux sous-zones
- ``contenu`` : recherche d'une valeur dans une position de la zone 008

De façon à pouvoir aérer les fichiers contenant un nombre conséquent de règles, chaque type de règle sera disposé dans un fichier différent :
- présence / absence de zone : rulesStructurePresenceZone.yaml
- présence / absence de sous-zone : rulesStructurePresenceSousZone.yaml
- nombre de zone : rulesStructureNombreZone.yaml
- nombre de sous-zones : rulesStructureNombreSousZone.yaml
- position de sous-zone : rulesStructurePositionSousZone.yaml
- présence de sous-zones dans une meme occurrence de zone : rulesStructurePresenceSousZoneMemeZone.yaml
- valeur d'un indicateur : rulesContenuIndicateur.yaml
- nombre de caractères dans une sous-zone : rulesContenuNombreCaracteres.yaml
- présence de chaine(s) de caractères dans une sous-zone : presenceChaineCaractères.yaml
- comparaison contenu sous-zone : comparaisoncontenusouszone.yaml
- type de caractères dans une sous-zone : rulesContenuTypeCaracteres.yaml
- comparaison de dates entre deux sous-zones : rulesContenuComparaisonDate.yaml
- recherche dans 008 : rulesContenuTypeDocument.yaml

NB : Toutes les règles complexes seront stockées dans le même fichier.

Cette partie va décrire la structure des différentes règles en YAML.
### Champs communs aux règles simples
Voici les champs à renseigner pour décrire une règle simple toutes les règles héritent de ces champs. Ils doivent être renseignés pour chaque règle décrite dans un fichier YAML (à l'exception des champs optionnels) : 
- id : ``obligatoire`` / de type entier : identifiant unique de la règle dans la base, ce numéro est arbritraire, mais ne doit pas être retrouvé plusieurs fois dans les fichiers décrivant les règles. Deux règles simples ne peuvent pas avoir le même identifiant (cela inclut les règles simples qui composent une règle complexe), et deux règles complexes ne peuvent pas avoir le même identifiant.
- id-excel : ``optionnel`` / de type entier : Cet identifiant ne sert qu'à référencer le numéro de la ligne dans le fichier Excel des règles maintenu par les responsables fonctionnels de l'application. Il n'est pas exploité par l'application et n'est présent qu'à titre indicatif.
- message : ``obligatoire`` / de type chaine de caractère : indique le message à envoyer à l'utilisateur si la condition décrite dans la règle est validée dans la notice
- zone : ``obligatoire`` sauf indication contraire / de type chaine de caractère : indique la zone du format Unimarc d'export sur laquelle porte la règle. Pour les règles simples il est possible de mentionner une zone générique (4XX, 5XX, 6XX, 7XX). Si une telle zone est renseignée, la règle sera dupliquée n fois, n correspondant au nombre de zones de la zone générique correspondante (voir [ce fichier](https://docs.google.com/spreadsheets/d/1E98M405rEaCcAV4UDkwY_ld6_B2yIeE9XQjCQqfv0Qk/edit?usp=sharing "Fichier des correspondances de zones génériques") )
- priorite: ``obligatoire`` / une des deux valeurs possible : P1 ou P2 : indique la priorité de la règle (P1 utilisé pour analyse Rapide, P2 pour analyse experte)
- jeux-de-regles : ``optionnel`` : de type liste d'entier : indique les identifiants des jeux de règles auquel la règle appartient. (voir [ce fichier](https://docs.google.com/spreadsheets/d/1ao46m7mI-NhqtCuCn4eq0EH1iS4hXCZ8cqKbSKpJYaw/edit?usp=sharing "Fichier des correspondeances Identifiant au jeux de règles") )
- type-doc : ``optionnel`` : de type liste de chaines de caractères : indique les types de documents sur lesquels seront appliqués la règle. Si le champ n'est pas renseigné, la règle portera sur tous les types de documents, sans restriction.
> les valeurs possibles pour les types de documents sont les suivantes :
> B : Audiovisuel, K : Carte, O : Doc Elec, N : Enregistrement, I : Image, F : Manuscrit, Z : Multimédia, V : Objet, G : Musique, M : Partition, BD : Ressource continue, A : Monographie, PC : Partie composante
- type-these : ``optionnel`` : de type liste de valeur : indique si la règle doit être appliquée sur les thèses.
> les valeurs possibles pour les types de thèse sont REPRO ou SOUTENANCE
- type : ``obligatoire`` : de type chaine de caractère : indique le type de règle qui est décrite. Les valeurs possibles sont : 
  - ``presencezone`` : pour les règles permettant de tester la présence ou l'absence d'une zone dans la notice
  - ``presencesouszone`` : pour les règles permettant de tester la présence ou l'absence d'une sous zone dans la notice
  - ``nombrezone`` pour les règles permettant de compter le nombre d'occurrences d'une zone dans la notice
  - ``nombresouszone`` : pour les règles permettant de comparer le nombre d'occurrences d'une sous zone par rapport à une autre sous zone de la notice
  - ``positionsouszone`` : pour les règles permettant de vérifier la position d'une sous zone dans toutes les occurrences d'une zone 
  - ``presencesouszonesmemezone`` : pour les règles permettant de vérifier la présence ou l'absence de n sous-zones dans la même occurrence d'une zone
  - ``indicateur`` : pour les règles permettant de vérifier la valeur d'un indicateur
  - ``nombrecaractere`` : pour les règles permettant de vérifier le nombre de caractères dans une sous-zone
  - ``presencechainecaracteres`` : pour les règles permettant de vérifier la présence et la position d'une ou plusieurs chaines de caractères dans une sous-zone
  - ``comparaisoncontenusouszone`` : pour les règles permettant de comparer le contenu d'une sous-zone avec le contenu d'une autre sous-zone 
  - ``typecaractere`` : pour les règles permettant de vérifier le type de caractères dans une sous-zone
  - ``comparaisondate`` : pour les règles permettant de comparer la date de deux sous-zones
  - ``typedocument`` : pour les règles permettant de chercher une valeur à une position de la 008


### Présence / absence de zone
Liste des champs propres au type de règle presence de zone : 
- presence : ``obligatoire`` / de type booléen. Si la valeur est true et que la zone est présente dans la notice, le message est envoyé à l'utilisateur. Si la valeur est false et que la zone est absente de la notice, le message est envoyé à l'utilisateur

Exemple de fichier YAML :  
``` YAML
rules:
  - id:             2
    id-excel:       2
    type:           presencezone
    message:        message test 
    zone:           330
    priorite:       P1
    presence:       false
    jeux-de-regles:
        - 1 # Données codées (1XX) (ce commentaire n'est pas obligatoire)
        - 2 # Indexation-matière (6XX) (ce commentaire n'est pas obligatoire)
    type-doc:
        - A 
        - B
        - O
```

Règle numéro 2 dans la base, sur la ligne 2 du fichier Excel, permettant de tester l'absence de la zone 330 dans la notice. Si la 330 est absente, le message ``message test`` est envoyé à l'utilisateur. La règle sera lancée si le traitement rapide ou expert est choisi ou si le jeu de règle "Données codées (1XX)" ou
"Indexation-matière (6XX)" est choisi, et s'appliquera uniquement sur les types de documents Monographie, Audiovisuel ou Doc Elec 

### Présence / absence de sous-zone
Liste des champs propres au type de règle presence de sous-zone : 
- souszone : ``obligatoire`` / de type caractère. La sous-zone à vérifier. ATTENTION : le $ du format unimarc NE doit PAS être renseigné. 
- presence : ``obligatoire`` / de type booléen. Si la valeur est true et que la sous-zone est présente dans la notice, le message est envoyé à l'utilisateur. Si la valeur est false et que la sous-zone est absente de la notice, le message est envoyé à l'utilisateur

Exemple de fichier YAML :  
``` YAML
rules:
  - id:             3
    id-excel:       3
    type:           presencesouszone
    message:        message test presence sous zone
    zone:           330
    souszone:       a
    priorite:       P2
    type-doc:
        - A 
        - F
        - TS
```

Règle numéro 3 dans la base, sur la ligne 3 du fichier Excel, permettant de tester la présence de la sous zone $a de la zone 330 de la notice. Si la 330 $a est présente, le message ``message test presence sousZone`` sera envoyé à l'utilisateur. La règle sera lancée si le traitement expert est sélectionné et s'appliquera uniquement sur les types de documents Monographie, Manuscrit ou Thèse de soutenance.


### Nombre de zones
Liste des champs propres au type de règle nombre de zones : 
- operateur : ``obligatoire`` / peut prendre les valeurs : SUPERIEUR, INFERIEUR, EGAL
- occurrences : ``obligatoire`` / de type entier : le nombre d'occurrences de la zone à compter dans la notice.

Si le nombre de zone respecte la condition (SUPERIEUR / INFERIEUR / EGAL) au nombre d'occurrence choisi, le message est envoyé à l'utilisateur.

Exemple de fichier YAML :  

``` YAML
rules:
  - id:             4
    id-excel:       10
    type:           nombrezone
    message:        message test nombre zone
    zone:           330
    operateur:      INFERIEUR
    occurrences:    3
    priorite:       P1
```

Règle numéro 4 dans la base, sur la ligne 10 du fichier Excel, permettant de tester le nombre de zones dans la zone 330 de la notice. Si le nombre de 330 dans la notice est strictement inférieur à 3, le message ``message test nombre zone`` est envoyé à l'utilisateur. La règle sera lancée si le traitement rapide ou expert est sélectionné. L'absence du champ type-doc indique que la règle s'applique à tous les types de documents.

### Nombre de sous-zones
Liste des champs propres au type de règle nombre de sous-zones : 
- souszone : ``obligatoire`` / de type caractère. La sous-zone à vérifier. ATTENTION : le $ du format unimarc de catalogage NE doit PAS être renseigné.
- zonecible : ``obligatoire`` / de type chaine de caractères. La zone sur laquelle va s'effectuer la comparaison
- souszonecible : ``obligatoire`` / de type caractère. La sous zone sur laquelle va s'effectuer la comparaison. ATTENTION : le $ du format Unimarc NE doit PAS être renseigné.

Si le nombre de sous-zones dans la zone est différent du nombre de sous-zones dans la zone cible, le message est envoyé à l'utilisateur. En cas de zone répétée, toutes les occurrences de toutes les zones sont cumulées.

Exemple de fichier YAML :  
``` YAML
rules:
  - id:             5
    id-excel:       110
    type:           nombresouszone
    message:        message test nombre sous zone
    zone:           200,
    souszone:       a
    zonecible:      600
    souszonecible:  b
    priorite:       P2
```


Règle numéro 5 dans la base, sur la ligne 110 du fichier Excel, permettant de comparer le nombre de 200 $a avec le nombre de 600 $b. Si le nombre de 200 $a est différent du nombre de 600 $b, le message ``message test nombre sous zone`` est envoyé à l'utilisateur. La règle sera lancée uniquement si le traitement expert est sélectionné. L'absence du champ type-doc indique que la règle s'applique à tous les types de documents.

### Position de sous-zone
Liste des champs propres au type de règle position de sous-zone : 
- souszone : ``obligatoire`` / de type caractère. La sous-zone à vérifier. ATTENTION : le $ du format unimarc de catalogage ne doit pas être renseigné
- position : ``obligatoire`` / de type entier. La position de la sous-zone dans la zone à vérifier.

Exemple de fichier YAML :  
``` YAML
rules:
  - id:         9
    id-excel:   9
    type:       positionsouszone
    message:    Faire le lien à l'autorité en sous-zone $3
    zone:       608
    souszone:   3
    priorite:   P1
    position:   1
```

Si la souszone $3 n'est pas placée en première position dans l'une des occurrences de la zone 608, le message Faire le lien à l'autorité en sous-zone $3 est envoyé à l'utilisateur.

### Presence / absence sous-zones dans une même occurrence de zone
Liste des champs propres au type de règle présence / absence de sous-zones dans une même occurrence de zone :
- souszones : ``obligatoire`` / de type liste d'objets. La liste des sous-zones à vérifier. Les champs de la liste sont les suivants : 
- * souszone : ``obligatoire`` de type caractère la sous-zone à vérifier. ATTENTION : le $ du format Unimarc de catalogage ne doit pas être renseigné
- * presence : ``obligatoire`` de type booléen. true si on souhaite que la sous-zone soit présente, false sinon
- * operateur-booleen : une des deux valeurs possible : ET / OU : indique l'opérateur qui sera utilisé pour calculer la validité des différentes recherches de sous-zones dans la zone. Ce critère **ne** doit **pas** être renseigné pour la première sous-zone, et doit être **obligatoire** pour les suivantes. 

Exemple de fichier YAML :  
``` YAML
rules:
  - id:         4
    id-excel:   10
    type:       presencesouszonesmemezone
    message:    message test
    zone:       606
    priorite:   P1
    souszones:
        - souszone:          a
          presence:          true
        - souszone:          b
          presence:          false
          operateur-booleen: ET
```

Si une sous-zone $a est présente ET une sous-zone $b est absente dans une même occurrence de la zone 606 alors le message "message test" est envoyé à l'utilisateur.

### Valeur d'un indicateur
Liste des champs propres au type de règle valeur d'un indicateur :
- indicateur : ``obligatoire`` / de type entier. Indique sur quel indicateur de la zone porte la règle. Ce champ ne peut prendre que les valeurs 1 ou 2
- valeur : ``obligatoire`` / de type chaine de caractères. Indique la valeur de l'indicateur que la règle doit vérifier. Cette valeur ne peut être qu'un entier entre 0 et 9 ou un '#' (# entre guillemets car en Yaml le # est utilisé pour écrire un commentaire)
- type-de-verification : ``obligatoire`` / de type chaine de caractères. Indique le type de vérification à appliquer. Peut prendre les valeurs STRICTEMENT ou STRICTEMENTDIFFERENT 

Exemple de fichier YAML : <br />

``` YAML
rules:
  - id:                   1
    id-excel:             1
    type:                 indicateur
    message:              message test
    zone:                 200
    priorite:             P1
    indicateur:           2
    valeur:               '#'
    type-de-verification: STRICTEMENT
```
Si le second indicateur de la zone 200 a STRICTEMENT la valeur # alors le message "message test" est envoyé à l'utilisateur.

### Nombre de caractères
Liste des champs propres au type de règle nombre de caractères : 
- operateur : ``obligatoire`` / peut prendre les valeurs : INFERIEUR, SUPERIEUR, EGAL, SUPERIEUR_EGAL, INFERIEUR_EGAL
- souszone : ``obligatoire`` / de type caractère la sous-zone à vérifier. ATTENTION : le $ du format Unimarc de catalogage ne doit pas être renseigné
- occurrences : ``obligatoire`` / de type entier : le nombre d'occurrences de caractères à comparer
 
Exemple de fichier YAML : 

``` YAML
rules:
  - id:         1
    id-excel:   1
    type:       nombrecaractere
    message:    message test
    zone:       200
    priorite:   P1
    souszone:   a
    operateur:  INFERIEUR_EGAL
    occurrences: 20
```
Si le nombre de caractères dans la 200$a est inférieur ou égal à 20, alors le message "message test" sera envoyé à l'utilisateur.

### Présence d'une chaine de caractères

Liste des champs propres au type de règle présence chaine caractères:
* souszone : **obligatoire** - de type caractère. La sous-zone à vérifier. ATTENTION : le $ du format Unimarc de catalogage ne doit pas être renseigné
* type-de-verification : **obligatoire** - ne peut être que `STRICTEMENT` ou `COMMENCE` ou `TERMINE` ou `CONTIENT` ou `NECONTIENTPAS`
* chaines-caracteres : **obligatoire** - de type liste d'objets. La liste des chaine-caracteres à vérifier. Les champs d'un objet de la liste sont les suivants : 
    * operateur : de type opérateur logique. ne peut être que `ET` ou `OU`. 
    * chaine-caracteres :  la chaine de caractères à vérifier

Liste des valeurs possibles en fonction du type de vérification :
* La vérification `STRICTEMENT` peut comporter :
    * soit une `chaine-caracteres` sans `operateur`,
    * soit une `chaine-caracteres` sans `operateur` et plusieurs `chaine-caracteres` avec `operateur` `OU`.
* La vérification `COMMENCE` peut comporter :
    * soit une `chaine-caracteres` sans `operateur`,
    * soit une `chaine-caracteres` sans `operateur` et plusieurs `chaine-caracteres` avec `operateur` `OU`,
* La vérification `TERMINE` peut comporter :
    * soit une `chaine-caracteres` sans `operateur`,
    * soit une `chaine-caracteres` sans `operateur` et plusieurs `chaine-caracteres` avec `opérateur` `OU`,
* La vérification `CONTIENT` peut comporter :
    * soit une `chaine-caracteres` sans `operateur`,
    * soit une `chaine-caracteres` sans `operateur` et plusieurs `chaine-caracteres` avec `opérateur` `ET` ou `OU`,
* La vérification `NECONTIENTPAS` peut comporter :
    * soit une `chaine-caracteres` sans `operateur`,
    * soit une `chaine-caracteres` sans `operateur` et plusieurs `chaine-caracteres` avec `opérateur` `ET` ou `OU`,

``` YAML
---
rules:
    - id:                       1
      id-excel:                 1
      type:                     presencechainecaracteres
      message:                  message de retour
      zone:                     200
      priorite:                 P1
      type-these:
          - REPRO
      souszone:                 a
      type-de-verification:     STRICTEMENT
      chaines-caracteres:
        - chaine-caracteres:    chaine de caractères à chercher
    - id:                       2
      id-excel:                 2
      type:                     presencechainecaracteres
      message:                  message de retour
      zone:                     200
      priorite:                 P1
      type-these:
          - SOUTENANCE
      souszone:                 a
      type-de-verification:     STRICTEMENT
      chaines-caracteres:
        - chaine-caracteres:    texte à chercher
        - operateur:            OU
          chaine-caracteres:    deuxième chaine de caractères à chercher
    - id:                       3
      id-excel:                 3
      type:                     presencechainecaracteres
      message:                  message de retour
      zone:                     200
      priorite:                 P1
      type-these:
          - REPRO
      souszone:                 a
      type-de-verification:     CONTIENT
      chaines-caracteres:
        - chaine-caracteres:    premier chaine de caractères à chercher
        - operateur:            ET
          chaine-caracteres:    deuxième chaine de caractères à chercher
        - operateur:            OU
          chaine-caracteres:    deuxième chaine de caractères à chercher
```

### Comparaison contenu sous-zone

Liste des champs propres au type de règle comparaison contenu sous-zone:
* souszone : **obligatoire** - de type caractère. La sous-zone sur laquelle va porter la comparaison. ATTENTION : le $ du format Unimarc de catalogage ne doit pas être renseigné
* type-de-verification : **obligatoire** - ne peut être que `STRICTEMENT` ou `COMMENCE` ou `TERMINE` ou `CONTIENT` ou `NECONTIENTPAS` ou `STRICTEMENTDIFFERENT`
* nombreCaracteres : *optionnel* - de type chiffre. Le nombre de caractères de la souszonecible à comparer à la souszone. Ne peux contenir que deux chiffres maximum. Ce paramètre est pris en compte uniquement pour les type-de-verification COMMENCE et TERMINE.
* zonecible : **obligatoire** - de type caractère. La zone dans laquelle aller chercher la souszonecible qui permettra d'effectuer la comparaison.
* souszonecible : **obligatoire** - de type caractère. La souszonecible qui sera comparée à la souszone. ATTENTION : le $ du format Unimarc de catalogage ne doit pas être renseigné

``` YAML
---
rules:
    - id:                       1
      id-excel:                 1
      type:                     comparaisoncontenusouszone
      message:                  message de retour
      zone:                     307
      priorite:                 P1
      type-these:
          - REPRO
      souszone:                 a
      type-de-verification:     COMMENCE
      nombreCaracteres:         6
      zonecible:                215
      souszonecible:            a
```

### Type de caractères
Liste des champs propres au type de règle type de caractères :
- souszone : ``obligatoire`` / de type caractère la sous-zone à vérifier. ATTENTION : le $ du format Unimarc de catalogage ne doit pas être renseigné
- type-caracteres : ``obligatoire`` / peut prendre les valeurs : ALPHABETIQUE, ALPHABETIQUE_MAJ, ALPHABETIQUE_MIN, NUMERIQUE, SPECIAL

Exemple de fichier YAML :

``` YAML
 rules:
    - id:              1
      type:            typecaractere
      message:         message test
      zone:            603
      priorite:        P1
      souszone:        a
      type-caracteres:
      - "ALPHABETIQUE"
      - "NUMERIQUE"
      - "SPECIAL"
```
Si le type de caractères dans la 603$a est Alphabetique OU Numerique OU Special, alors le message "message test" sera envoyé à l'utilisateur.


### Comparaison de dates
Liste des champs propres au type de règle comparaison de dates :
- souszone : ``obligatoire`` / de type caractère la sous-zone à vérifier. ATTENTION : le $ du format Unimarc de catalogage ne doit pas être renseigné
- positionstart : ``optionel`` / de type entier, la position de début de la date à vérifier dans le champs de la sous zone. Par défaut, la position de début est 1.
- positionend : ``optionel`` / de type entier, la position de fin de la date à vérifier dans le champs de la sous zone. Si non renseigné, la date sera comprise jusqu'à la fin du champs
- zonecible : ``obligatoire`` / de type caractère, la zone cible à comparer avec la zone de la règle
- souszonecible : ``obligatoire`` / de type caractère, la sous-zone cible à comparer avec la sous-zone de la règle. ATTENTION : le $ du format Unimarc de catalogage ne doit pas être renseigné
- positionstartcible : ``optionel`` / de type entier, la position de début de la date à vérifier dans le champs de la sous zone cible. Par défaut, la position de début est 1.
- positionendcible : ``optionel`` / de type entier, la position de fin de la date à vérifier dans le champs la sous zone cible. Si non renseigné, la date sera comprise jusqu'à la fin du champs
- comparateur: ``obligatoire`` / de type caractère, le comparateur à utiliser pour la comparaison. Les valeurs possibles sont : `SUPERIEUR`, `INFERIEUR`, `EGAL`, `SUPERIEUR_EGAL`, `INFERIEUR_EGAL`, `DIFFERENT`

Exemple de fichier YAML :
``` YAML
---
rules:
   - id: 1
     id-excel: 1
     type: comparaisondate
     message: "La date 100$a doit etre egal a la date 214$d"
     zone: "100"
     souszone: "a"
     positionstart: 9
     positionend: 12
     comparateur: "DIFFERENT"
     zonecible: "214"
     souszonecible: "d"
     priorite: "P1"
```

### Contenu de la zone 008
Liste des champs propres à l'analyse de la zone 008 : 
- type-de-verification : ``obligatoire`` peut prendre les valeurs STRICTEMENT ou STRICTEMENTDIFFERENT : permet de déterminer si la valeur recherchée dans la 008 doit être égale ou différente.
- position : ``obligatoire`` de type entier, la position où rechercher dans le contenu de la zone 008. Cette position ne peut être comprise qu'entre 1 et 4.
- valeur : ``obligatoire`` de type caractère : la valeur à chercher dans la zone 008.

Attention ! pour cette règle, il n'est pas utile de préciser la zone, étant donné qu'elle porte sur la 008 quoiqu'il en soit.

Exemple de fichier YAML :
``` YAML
---
rules:
   - id: 1
     id-excel: 1
     type: typedocument
     message: "Le caractère à la position 1 de la 008 doit être a"
     position: 1
     type-de-verification: STRICTEMENT
     valeur : a
     priorite: "P1"
```
Si le caractère à la position 1 de la zone 008 est égal à "a", alors le message "Le caractère à la position 1 de la 008 doit être a" est envoyé à l'utilisateur.

## Syntaxe des règles complexes <a id="5"></a>
Une règle complexe est un assemblage de plusieurs règles simples qui seront testées les unes après les autres avec un opérateur booléen. Par exemple, une règle complexe composée de 3 règles simples avec un opérateur ET entre les deux premières et un opérateur OU entre les deux suivantes donnera l'expression suivante : règle 1 ET règle 2 OU règle 3. 

Les conditions seront évaluées dans l'ordre d'apparition et ne prennent pas en compte la priorité des opérateurs booléen (dans cet exemple, règle 1 ET règle 2 est évalué d'abord, puis le résultat OU règle 3 est évalué ensuite, ce qui équivalent à (règle 1 ET règle 2) ou règle 3).

Il est donc important de prendre en compte l'ordre de déclaration des différentes règles simples qui composent la règle composée pour que le résultat final soit conforme aux exigences.

Par ailleurs, une règle complexe doit être composée d'au moins deux règles simples (s'il n'y a qu'une règle simple, c'est une règle simple, voir section ci-dessus). La première règle simple ne doit pas avoir d'opérateur, et les suivantes doivent absolument en avoir un (une erreur sera renvoyée si ces conditions ne sont pas respectées).

Les règles simples qui composent une règle complexe ne peuvent pas avoir de zone générique. Il est donc interdit de créer une règle simple dans une règle complexe avec comme valeur du champ zone 4XX, 5XX, 6XX ou 7XX. Un message d'erreur sera envoyé si c'est le cas.

Les règles complexes seront toutes déclarées dans le fichier complexRules.yaml situé à la racine de la branche. 

### Champs communs aux règles complexes
Les règles complexes disposent de champs communs qui décrivent la règle complexe et seront disposés au premier niveau du bloc YAML : 
- id ``obligatoire`` / de type entier : identifiant de la règle dans la base de données. L'identifiant d'une règle complexe est unique et aucune autre règle complexe ne peut avoir le même identifiant. Il est par contre possible d'avoir une règle complexe disposant du même identifiant qu'une règle simple.
- id-excel ``optionnel`` / de type entier : Cet identifiant ne sert qu'à référencer le numéro de la ligne dans le fichier Excel des règles maintenu par les responsables fonctionnels de l'application. Il n'est pas exploité par l'application et n'est présent qu'à titre indicatif.
- message ``obligatoire`` / de type chaine de caractère : indique le message à envoyer à l'utilisateur si la condition décrite dans la règle est validée dans la notice
- priorite ``obligatoire`` / une des deux valeurs possible : P1 ou P2 : indique la priorité de la règle (P1 utilisé pour analyse Rapide, P2 pour analyse experte)
- type-doc : ``optionnel`` : de type liste de chaines de caractères : indique les types de documents sur lesquels seront appliqués la règle. Si le champ n'est pas renseigné, la règle portera sur tous les types de documents, sans restriction.
> les valeurs possibles pour les types de documents sont les suivantes :
> B : Audiovisuel, K : Carte, O : Doc Elec, N : Enregistrement, I : Image, F : Manuscrit, Z : Multimédia, V : Objet, G : Musique, M : Partition, BD : Ressource continue, A : Monographie, TS : Thèse de soutenance, TR : Thèse de reproduction, PC : Partie composante 

Ainsi, un exemple de fichier YAML permettant de définir les critères communs est présenté ci-dessous : 
``` YAML
---
rules:
  - id:             2
    id-excel:       2
    message:        test
    priorite:       P1
    type-doc:
        - A 
        - O
``` 

### Définition des règles simples composant la règle complexe
Suite à la déclaration des champs communs, il est nécessaire de décrire au moins 2 règles simples pour que la règle complexe soit valide. La déclaration des règles simples se fait dans un champ liste nommé **regles**. Les champs à déclarer pour chacune des règles simples sont les suivants :
- id : ``obligatoire`` / de type entier : identifiant unique de la règle dans la base, ce numéro est arbritraire, mais ne doit pas être retrouvé plusieurs fois dans les fichiers décrivant les règles simples. Deux règles simples ne peuvent pas avoir le même identifiant (cela inclut les règles simples qui composent une règle complexe)
- type : ``obligatoire`` : de type chaine de caractère : indique le type de règle qui est décrite. Les valeurs possibles sont les mêmes que lors de la déclaration d'une règle simple.
- zone : ``obligatoire`` : de type chaine de caractère : indique la zone du format Unimarc d'export sur laquelle porte la règle
- operateur-booleen : une des deux valeurs possible : ET / OU : indique l'opérateur qui sera utilisé pour calculer la validité de la règle simple par rapport à la précédente. Ce critère **ne** doit **pas** être renseigné pour la **première** règle simple qui compose une règle complexe, et est **obligatoire** pour toutes les règles composant la règle complexe à partir de la **seconde**

Les critères spécifiques au type de règle simple sélectionné doivent ensuite être rajouté (en suivant la même terminologie que dans le paragraphe sur les [règles simples](#4) ). 

Exemple de fichier YAML de 2 règles complexes composées de règles simples de différents types :
``` YAML
---
rules:
  - id:             2
    id-excel:       2
    message:        "message test"
    priorite:       P1
    type-doc:
        - A 
        - O
    regles:
        - id:                20
          type:              presencezone
          zone:              330
          presence:          false
        - id:                21
          type:              presencezone
          zone:              200
          presence:          true
          operateur-booleen: ET
  - id:             3
    id-excel:       3
    message:        "Message test 2"
    priorite:       P2
    regles:
        - id:               30
          type:             nombrezone
          zone:             '330'
          operateur:        EGAL
          occurrences:      1
        - id:               31
          type:             nombrezone
          zone:             '200'
          operateur:        SUPERIEUR
          occurrences:      1
          operateur-booleen:OU
        - id:               32
          type:             nombrezone
          zone:             '400'
          operateur:        INFERIEUR
          occurrences:      1
          operateur-booleen:OU

```

Le YAML précédent permet de déclarer 2 règles complexes. La première règle a un id 2, le message renvoyé sera **message test** si la règle est valide. Elle a une priorité de 1, et concerne les types de documents monographie et doc élec. Elle est composée de 2 règles simples : 
- La première règle simple a un id 20, et vérifie si la zone 330 est absente.
- La seconde règle simple a un id 21, et vérifie si la zone 200 est présente. Les deux règles simples doivent être valides pour que la règle complexe soit valide (operateur-booleen = ET)

La seconde règle a un id 3, le message renvoyé est **message test 2** si la règle est valide. Elle a une priorité de 2, et concerne tous les types de documents (absence du champ type-doc). Elle est composée de 3 règles simples : 
- La première règle simple a un id 30, et vérifie qu'il y a exactement 1 zone 330 dans la notice.
- La deuxième règle simple a un id 31, et vérifie qu'il y a plus d'1 zone 200 dans la notice.
- La troisième règle simple a un id 32, et vérifie qu'il y a moins d'une zone 400 dans la notice.

La règle complexe est valide si la première règle simple est valide, OU la deuxième règle simple est valide, OU que la 3è règle simple est valide. (donc, si la règle 3 est valide, mais que les règles 1 et 2 ne sont pas valides, la règle complexe est valide)

## Règles complexes sur une même instance de zone
Il est possible de déclarer des règles complexes sur une même instance de zone. Pour cela il faut déclarer une règle complexe avec une zone (n'accepte pas les zones génériques) et déclarer des règles simples sans aucune zone (la zone est déclarée dans la règle complexe) ni opérateur booléen (les règles s'enchainent selon un schéma logique ET).

les règle simple sur une même instance de zone sont les suivants :
- ``structure`` : Présence ou absence de zone
- ``structure`` : Présence ou absence de sous-zone
- ``structure`` : Position de sous-zones dans une zone
- ``contenu`` : Valeur d'un indicateur
- ``contenu`` : Présence d'une ou plusieurs chaine(s) de caractères dans une sous-zone

Exemple de fichier YAML d'une règle complexe composée de règles simples de différents types :

``` YAML
---
rules:
  - id: 1
    id-excel: XXX
    message: "214 doit truc truc"
    zone: 214
    priorite: P2
    jeux-de-regles:
        - 1
        - 2
    regles: 
      - id: 2
        type: presencezone
        presence: true
      - id: 3
        type: indicateur
        indicateur: 1
        valeur: "#"
        type-de-verification: STRICTEMENT
      - id: 4
        type: indicateur
        indicateur: 2
        valeur: "1"
        type-de-verification: STRICTEMENT
      - id: 5
        type: presencesouszone
        souszone: "d"
        presence: false
```


## Règles de dépendance <a id="6"></a>

### Articulation entre deux notices
Il est possible de créer des règles complexes permettant d'effectuer des vérifications dans une notice liée de la notice. Pour cela, une règle simple particulière doit être créée dans la règle complexe. Cette règle aura la valeur dependance dans le champ type. Voici un exemple de règle simple de type dépendance en YAML : 
``` YAML
---
rules:
  - id:             2
    type:           dependance
    zone:           '606'
    souszone:       '3'
    type-notice-liee:  AUTORITE
```

Lorsque Qualimarc consultera la liste des règles simples d'une règle complexe, s'il rencontre une règle simple de type dependance dans la liste, il ira récupérer la ou les notices liées spécifiées dans une zone / sous zone (si plusieurs occurrences de la zone sont trouvées dans la notice, la vérification sera faite sur tous les PPN de la première occurrence de la sous zone de chaque zone), et effectuera toutes les vérifications des règles simples situées après la règle de dépendance dans la notice liée. Dans l'exemple ci-dessus, Qualimarc récupèrera la notice liée présente derrière le ppn autorité contenu en 606$3 et vérifiera toutes les règles suivantes dans la notice liée.

Ainsi, un certain nombre de règles de gestion doivent être respectées :
- Une règle complexe ne peut pas commencer par une règle de dépendance. Il est nécessaire de toujours déclarer une règle simple d'abord qui effectuera une vérification dans la notice initiale.
- Une règle de dépendance doit être obligatoire suivie d'une règle simple dans le code YAML
- La première règle simple qui suit la règle de dépendance NE doit PAS avoir d'opérateur (elle sera la première règle qui passera sur la notice liée)
- Une règle de dépendance ne peut avoir que les attributs id, type, zone et souszone, aucun autre attribut d'une règle simple n'est possible et ces 4 attributs sont obligatoires
- Une règle complexe ne peut avoir qu'une seule règle de dépendance.

le champ ``type-notice-liee`` correspond au type de notice lié contenu dans la zone spécifié, à savoir notice bibliographique ou notice d'autorité. Ce champ ne peut prendre que les valeurs : ``AUTORITE | BIBLIO``

Exemple de fichier YAML d'une règle complexe avec une règle de dépendance : 
``` YAML
---
rules:
  - id:             1
    id-excel:       1
    message:        "message test"
    priorite:       P1
    type-doc:
        - A 
        - O
    regles:
        - id:                20
          type:              presencesouszone
          zone:              660
          souszone:          3
          presence:          true
        - id:                21
          type:              dependance
          zone:              606
          souszone:          3
          type-notice-liee:  BIBLIO
        - id:                22
          type:              presencesouszone
          zone:              250
          souszone:          a
          presence:          true
        - id:                23
          type:              presencezone
          zone:              200
          presence:          true
          operateur-booleen: ET
```

Le YAML précédent permet de créer une règle complexe qui renvoie le message **message test** si la règle est valide. Elle a une priorité de 1 et concerne les types de documents monographie et doc élec.  

Elle est composée de 4 règles simples qui seront validées dans l'ordre. La première règle vérifie la présence d'une 660$3. La seconde informe le programme que les règles suivantes seront appliquées sur la ou les notices bibliographiques liées dont le ppn est situé dans la première occurrence de la $3 de chaque 606 présente dans la notice. Si au moins une des notice liée contient une 250$a ET une 200, le message est envoyé à l'utilisateur.

### Règle de réciprocité
Ce type de règle ne peut être créé que dans une règle complexe, et doit être placée après une règle de dépendance. En effet, elle permet d'aller vérifier que la valeur contenu dans une zone / sous zone spécifique d'une notice liée correspond au PPN de la notice en cours d'analyse.

Liste des champs propres au type de règle reciprocite : 
- id : ``obligatoire`` / de type entier : identifiant de la règle dans la base de données
- type : ``obligatoire`` : doit avoir la valeur **reciprocite**
- zone : ``obligatoire`` / de type chaîne de caractères. La zone à vérifier dans la notice liée
- souszone : ``obligatoire`` / de type caractère. La sous-zone à vérifier dans la notice liée. ATTENTION : le $ du format unimarc NE doit PAS être renseigné. 

Exemple de fichier YAML :  
``` YAML
rules:
  - id:             1
    id-excel:       1
    message:        "message test"
    priorite:       P1
    type-doc:
        - A 
        - O
    regles:
        - id:                20
          type:              presencesouszone
          zone:              660
          souszone:          3
          presence:          true
        - id:                21
          type:              dependance
          zone:              606
          souszone:          3
          type-notice-liee:  BIBLIO
        - id:                22
          type:              reciprocite
          zone:              250
          souszone:          a
```

Le YAML précédent permet de créer une règle complexe qui renvoie le message **message test** si la règle est valide. Elle a une priorité de 1 et concerne les types de documents monographie et doc élec.  

Elle vérifie d'abord la présence de la zone 660$3, puis récupère le PPN en 606$3, analyse la notice bibliographique liée et si la valeur contenue dans la zone 250$a **ne** contient **pas** le PPN de la notice en cours d'analyse, elle envoie le message à l'utilisateur.

## Jeux de règles personnalisés <a id="7"></a>

### Principe
Sur le même principe que les règles, il est possible de créer des jeux de règles personnalisés via un fichier contenant des objets en langage Yaml. A chaque modification du fichier, l'application supprimera tous les jeux de règles existants et les remplacera par ceux du fichier.

### Création des jeux de règles personnalisés
Les jeux de règles personnalisés sont créés dans le fichier jeux_de_regles.yaml. Le premier élément du fichier doit être nommé **jeux-de-regles**. Une liste d'objet doit ensuite être déclarée dans le fichier. Les attributs qui composent un jeu de règle sont : 
- id : ``obligatoire`` / de type entier : identifiant du jeu de règles dans la base de données
- libelle : ``obligatoire`` / de type chaîne de caractères : libellé du jeu de règles tel qu'il sera affiché sur la page d'accueil de Qualimarc
- description : ``optionnel`` / de type chaîne de caractères. Une description du jeu de règle qui sera affichée dans l'info bulle.
- position : ``obligatoire`` / de type entier. Indique la position du jeu de règles dans la liste de la page d'accueil. Le premier jeu de règle aura la position 0.

Exemple de fichier YAML : 
```YAML
---
jeux-de-regles:
  - id:          1
    libelle:     Données codées (1XX)
    description: Données codées (1XX)
    position:    0
  - id:          2
    libelle:     Indexation-matière (6XX)
    description: Indexation-matière (6XX)
    position:    1
  - id:          3
    libelle:     Liens ($0/$3)
    description: Liens ($0/$3)
    position:    2
```
