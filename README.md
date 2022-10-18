# Qualimarc-rules
## Documentation d'utilisation du dépot

Ce document explique étape par étape comment ajouter ou modifier des règles dans le projet qualimarc.

## Sommaire
- [Utilisation de GitHub](#1)
- [Déclenchement de la mise à jour](#2)
- [Langage YAML](#3)
- [Syntaxe des règles](#4)

## Utilisation de GitHub-  <a id="1"></a>
Github est un outil en ligne permettant de versionner des fichiers. Il dispose d'un éditeur en ligne permettant de modifier des fichiers puis de les sauvegarder tout en conservant un historique des modifications réalisées.

### Notion de branche
Une branche est une version du code disponible dans un projet. Elle dispose de son propre historique, et toutes les modifications effectuées sur cette branche auront leur propre cycle de vie. Pour sélectionner une branche, cliquez sur la liste déroulante : ![branches](https://user-images.githubusercontent.com/57490853/190959280-452d5f2b-a04e-4061-ac49-3bea09ddf00b.PNG) 
et sélectionnez la branche de travail. Le contenu de la branche est affiché.
> Pour modifier les règles et qu'elles soient prises en compte sur l'environnement de test, il est nécessaire de se 
> positionner sur la branche ``main``

### Editer un fichier
Pour modifier le contenu d'un fichier, cliquez sur le nom du fichier à modifier, puis sur la page suivante, cliquez sur le bouton ![edit](https://user-images.githubusercontent.com/57490853/190960013-f2216993-faee-468d-aee5-daf8dd0b41e3.PNG). Un éditeur en ligne s'affichera permettant de modifier le contenu du fichier.

### Sauvegarder un fichier
La sauvegarde d'un fichier est réalisée via l'action commit dans github. Lorsqu'on fichier est en édition, en bas de la page, renseignez un titre dans le premier champ texte, puis une description des modifications effectuées dans le second, cochez la case ``Commit directly to the <nom_de_la_branche> branch`` puis cliquez sur le bouton Commit changes : <br />
![commit](https://user-images.githubusercontent.com/57490853/190960543-e91a3708-9308-4f43-ad39-111ecb62a1ce.PNG)<br />
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

Par exemple :  <br />
![typedoc](https://user-images.githubusercontent.com/57490853/190974752-3b5b1118-8c4e-42ce-8963-734559227c80.PNG)<br />


## Description des règles de Qualimarc
Toutes les règles de Qualimarc ont une structure commune, puis des champs propres à chaque type de règle. Dans le chapitre décrivant les règles, les éléments obligatoires seront précisés. Voici un exemple de fichier YAML décrivant une règle : 
![yaml](https://user-images.githubusercontent.com/57490853/190967163-2325fc91-8d6c-4303-9593-26e8f5c27b36.PNG)  
Notez que tous les attributs de la règle sont alignés verticalement, et tous les attributs du type de document aussi. Les lettres A, B et O se rapportant au type de document, un niveau d'indentation supplémentaire a été rajouté.

## Syntaxe des règles- <a id="4"></a>
A l'heure actuelle il existe 5 types de règles dans Qualimarc : 
- Présence ou absence de zone
- Présence ou absence de sous-zone
- Nombre de zones dans une notice
- Nombre de sous-zones dans une notice
- Position de sous-zones dans une zone 

De façon à pouvoir aérer les fichiers contenant un nombre conséquent de règles, chaque type de règle sera disposé dans un fichier différent :
- présence / absence de zone : rulesStructurePresenceZone.yaml
- présence / absence de sous-zone : rulesStructurePresenceSousZone.yaml
- nombre de zone : rulesStructureNombreZone.yaml
- nombre de sous-zones : rulesStructureNombreSousZone.yaml
- position de sous-zone : rulesStructurePositionSousZone.yaml
- règles complexes : complexRules.yaml

NB : Toutes les règles complexes seront stockées dans le même fichier.

Cette partie va décrire la structure des différentes règles en YAML.
### Champs communs aux règles simples
Voici les champs à renseigner pour décrire une règle simple toutes les règles héritent de ces champs. Ils doivent être renseignés pour chaque règle décrite dans un fichier YAML (à l'exception des champs optionnels) : 
- id : ``obligatoire`` / de type entier : identifiant unique de la notice dans la base, ce numéro est arbritraire, mais ne doit pas être retrouvé plusieurs fois dans les fichiers décrivant les règles. Deux règles simples ne peuvent pas avoir le même identifiant (cela inclut les règles simples qui composent une règle complexe), et deux règles complexes ne peuvent pas avoir le même identifiant.
- id-excel : ``optionnel`` / de type entier : Cet identifiant ne sert qu'à référencer le numéro de la ligne dans le fichier Excel des règles maintenu par les responsables fonctionnels de l'application. Il n'est pas exploité par l'application et n'est présent qu'à titre indicatif.
- message : ``obligatoire`` / de type chaine de caractère : indique le message à envoyer à l'utilisateur si la condition décrite dans la règle est validée dans la notice
- zone : ``obligatoire`` / de type chaine de caractère : indique la zone du format Unimarc d'export sur laquelle porte la règle
- priorite: ``obligatoire`` / une des deux valeurs possible : P1 ou P2 : indique la priorité de la règle (P1 utilisé pour analyse Rapide, P2 pour analyse experte)
- type-doc : ``optionnel`` : de type liste de chaines de caractères : indique les types de documents sur lesquels seront appliqués la règle. Si le champ n'est pas renseigné, la règle portera sur tous les types de documents, sans restriction.
> les valeurs possibles pour les types de documents sont les suivantes :
> B : Audiovisuel, K : Carte, O : Doc Elec, N : Enregistrement, I : Image, F : Manuscrit, Z : Multimédia, V : Objet, G : Musique, M : Partition, BD : Ressource continue, A : Monographie, TS : Thèse de soutenance, TR : Thèse de reproduction, PC : Partie composante
- type : ``obligatoire`` : de type chaine de caractère : indique le type de règle qui est décrite. Les valeurs possibles sont : 
  - ``presencezone`` : pour les règles permettant de tester la présence ou l'absence d'une zone dans la notice
  - ``presencesouszone`` : pour les règles permettant de tester la présence ou l'absence d'une sous zone dans la notice
  - ``nombrezone`` pour les règles permettant de compter le nombre d'occurrences d'une zone dans la notice
  - ``nombresouszone`` : pour les règles permettant de comparer le nombre d'occurrences d'une sous zone par rapport à une autre sous zone de la notice
  - ``positionsouszone`` : pour les règles permettant de vérifier la position d'une sous zone dans toutes les occurrences d'une zone 

### Présence / absence de zone
Liste des champs propres au type de règle presence de zone : 
- presence : ``obligatoire`` / de type booléen. Si la valeur est true et que la zone est présente dans la notice, le message est envoyé à l'utilisateur. Si la valeur est false et que la zone est absente de la notice, le message est envoyé à l'utilisateur

Exemple de fichier YAML : <br />
![presencezone](https://user-images.githubusercontent.com/57490853/190971728-7ca9f837-f505-4f15-8f89-0db26632c2d9.PNG)<br />
Règle numéro 2 dans la base, sur la ligne 2 du fichier Excel, permettant de tester l'absence de la zone 330 dans la notice. Si la 330 est absente, le message ``message test`` est envoyé à l'utilisateur. La règle sera lancée si le traitement rapide ou expert est choisi, et s'appliquera uniquement sur les types de documents Monographie, Audiovisuel ou Doc Elec 

### Présence / absence de sous-zone
Liste des champs propres au type de règle presence de sous-zone : 
- souszone : ``obligatoire`` / de type caractère. La sous-zone à vérifier. ATTENTION : le $ du format unimarc NE doit PAS être renseigné. 
- presence : ``obligatoire`` / de type booléen. Si la valeur est true et que la sous-zone est présente dans la notice, le message est envoyé à l'utilisateur. Si la valeur est false et que la sous-zone est absente de la notice, le message est envoyé à l'utilisateur

Exemple de fichier YAML :<br />
![presencesouszone](https://user-images.githubusercontent.com/57490853/190973035-2ae52c7c-49b3-404e-9770-1a36bf4818cb.PNG)<br />
Règle numéro 3 dans la base, sur la ligne 3 du fichier Excel, permettant de tester la présence de la sous zone $a de la zone 330 de la notice. Si la 330 $a est présente, le message ``message test presence sousZone`` sera envoyé à l'utilisateur. La règle sera lancée si le traitement expert est sélectionné et s'appliquera uniquement sur les types de documents Monographie, Manuscrit ou Thèse de soutenance.


### Nombre de zones
Liste des champs propres au type de règle nombre de zones : 
- operateur : ``obligatoire`` / peut prendre les valeurs : SUPERIEUR, INFERIEUR, EGAL
- occurrences : ``obligatoire`` / de type entier : le nombre d'occurrences de la zone à compter dans la notice.

Si le nombre de zone respecte la condition (SUPERIEUR / INFERIEUR / EGAL) au nombre d'occurrence choisi, le message est envoyé à l'utilisateur.

Exemple de fichier YAML : <br />
![nombrezone](https://user-images.githubusercontent.com/57490853/190973627-583d49b1-1183-479e-9002-db3b909e40ba.PNG)<br />
Règle numéro 4 dans la base, sur la ligne 10 du fichier Excel, permettant de tester le nombre de zones dans la zone 330 de la notice. Si le nombre de 330 dans la notice est strictement inférieur à 3, le message ``message test nombre zone`` est envoyé à l'utilisateur. La règle sera lancée si le traitement rapide ou expert est sélectionné. L'absence du champ type-doc indique que la règle s'applique à tous les types de documents.

### Nombre de sous-zones
Liste des champs propres au type de règle nombre de sous-zones : 
- souszone : ``obligatoire`` / de type caractère. La sous-zone à vérifier. ATTENTION : le $ du format unimarc de catalogage NE doit PAS être renseigné.
- zonecible : ``obligatoire`` / de type chaine de caractères. La zone sur laquelle va s'effectuer la comparaison
- souszonecible : ``obligatoire`` / de type caractère. La sous zone sur laquelle va s'effectuer la comparaison. ATTENTION : le $ du format Unimarc NE doit PAS être renseigné.

Si le nombre de souszone dans la zone est différent du nombre de sous zones dans la zone cible, le message est envoyé à l'utilisateur. En cas de zone répétée, toutes les occurrences de toutes les zones sont cumulées.

Exemple de fichier YAML :  <br />
![nombresouszone](https://user-images.githubusercontent.com/57490853/190974273-d357700f-e26e-41e2-97dd-21b9235646ab.PNG)<br />

Règle numéro 5 dans la base, sur la ligne 110 du fichier Excel, permettant de comparer le nombre de 200 $a avec le nombre de 600 $b. Si le nombre de 200 $a est différent du nombre de 600 $b, le message ``message test nombre sous zone`` est envoyé à l'utilisateur. La règle sera lancée uniquement si le traitement expert est sélectionné. L'absence du champ type-doc indique que la règle s'applique à tous les types de documents.

### Position de sous-zone
Liste des champs propres au type de règle position de sous-zone : 
- souszone : ``obligatoire`` / de type caractère. La sous-zone à vérifier. ATTENTION : le $ du format unimarc de catalogage ne doit pas être renseigné
- position : ``obligatoire`` / de type entier. La position de la sous-zone dans la zone à vérifier.

Exemple de fichier YAML :  <br />
![image](https://user-images.githubusercontent.com/57490853/194000274-e18a0dad-ed8e-49ac-a0c0-81653b7a5490.png)<br />

Si la souszone $3 n'est pas placée en première position dans l'une des occurrences de la zone 608, le message Faire le lien à l'autorité en sous-zone $3 est envoyé à l'utilisateur.



