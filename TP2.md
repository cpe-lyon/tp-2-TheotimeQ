**Quere Theotime 3ICS**

# Table des matières

[Exercice 1.Variables d'environnement](#Anch1)

[Exercice 2.Contrôle de mot de passe](#Anch2)

[Exercice 3.Expression rationelles](#Anch3)

[Exercice 4.Controle d'utilisateur](#Anch4)

[Exercice 5.Factorielle](#Anch5)

[Exercice 6.Le juste prix](#Anch6)

[Exercice 7.Statistique](#Anch7)

[Exercice 8.Bonus](#Anch8)

# Exercice 1. Variables d'environnement <a id='Anch1'></a>

## 1 : Dans quels dossiers bash trouve-t-il les commandes tapées par l’utilisateur ?

Toutes les commandes ce trouvent dans les fichiers specifiés dans la variable d'environnement PATH .
```console 
User@localhost:~$ printenv PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

## 2 : Quelle variable d’environnement permet à la commande cd tapée sans argument de vous ramener dans votre répertoire personnel ?

La variable d'environnement `$HOME` permet au cd de trouver le repertoire personnel quand il n'y a pas d'argument.
```console 
User@localhost:~$ printenv HOME
/home/User
```
Dans notre cas , HOME contient `/home/User`

## 3 : Explicitez le rôle des variables LANG, PWD, OLDPWD, SHELL

`LANG` : contient la langue des messages à afficher

`PWD` : contient le chemin absolu vers le répertoire courant

`OLDPWD` : contient le chemin absolu vers le répertoire courant précédent 

`SHELL` : l'interpreteur shell utilisé par défaut. La valeur habituelle sous linux est /bin/bash

## 4 : Créez une variable locale MY_VAR (le contenu n’a pas d’importance). Vérifiez que la variable existe

Pour créer une variable , on tape : 
```console 
User@localhost:~$MY_VAR="coucou maman"
```
Pour verifier son existance : 
```console 
User@localhost:~$ echo $MY_VAR
coucou maman
```

## 5 : Tapez ensuite la commande bash. Que fait-elle ? La variable MY_VAR existe-t-elle ? Expliquez. A la fin de cette question, tapez la commande exit pour revenir dans votre session initiale.

Apres avoir lancé bash , la variable MY_VAR n'existe plus . En effet MY_VAR à été crée seulement dans le shell car c'est une variable local. Elle n'existe donc pas dans bash.

Une fois exit , on peut retrouver notre variable dans le shell.

## 6 : Transformez MY_VAR en une variable d’environnement et recommencez la question précédente. Expliquez.

On transforme MY_VAR en variable d'environnement 
```console 
User@localhost:~$ export MY_VAR="coucou maman"
```
Cette fois ci apres avoir lancé bash on peut retrouver notre variable : 
```console 
User@localhost:~$ printenv $MY_VAR
coucou maman
```

## 7 : Créer la variable d’environnement NOM ayant pour contenu vos prénom et nom séparés par un espace.Afficher la valeur de NOM pour vérifier que l’affectation est correcte.

On créer la variable d'envirennement NOM : 
```console 
User@localhost:~$ export NOM="Theotime Quere"
```
On affiche sa valeur : 
```console 
User@localhost:~$ printenv NOM
Theotime Quere
```

## 8 : Ecrivez une commande qui affiche ”Bonjour à vous, prenom nom !” en utilisant la variable NOM

```console 
User@localhost:~$ echo "Bonjour à vous, $NOM !"
Bonjour à vous, Theotime Quere !
```

## 9 Quelle différence y a-t-il entre donner une valeur vide à une variable d’environnement et l’utilisation de la commande unset ?

Quand on donne une valeur vide , la variable existe mais n'a pas de valeur . 
Avec Unset , la variable n'existe plus , elle est effacée.

## 10 : Utilisez la commande echo pour écrire exactement la phrase : $HOME = chemin (où chemin est votre dossier personnel d’après bash)

```console 
User@localhost:~$ echo "$\HOME = $HOME"
$HOME = /home/User
```

# -- Programmation Bash --

# Exercice 2. Contrôle de mot de passe <a id='Anch2'></a>

## Écrivez un script testpwd.sh qui demande de saisir un mot de passe et vérifie s’il correspond ou non au contenu d’une variable PASSWORD dont le contenu est codé en dur dans le script. Le mot de passe saisi par l’utilisateur ne doit pas s’afficher.

```bash
#!/bin/bash

PASSWORD='test'

read -p 'Mot de passe :' -s mdp 

if [[ $mdp == $password ]]
then 
  echo 'Bon mot de passe'
else 
  echo 'Mauvais mot de passe'
fi
```

 ![](/TP2_IMG/TP2_1.png)

# Exercice 3. Expression rationelles <a id='Anch3'></a>

## Ecrivez un script qui prend un paramètre et utilise la fonction suivante pour vérifier que ce paramètre est un nombre réel :

```bash 
function is_number()
{
  re='^[+-]?[0-9]+([.][0-9]+)?$'
  if ! [[ $1 =~ $re ]] ; then
    return 1
  else
    return 0
  fi
}
```
* Il affichera un message d’erreur dans le cas contraire. *

```bash
#!/bin/bash
function is_number()
{
  re='^[+-]?[0-9]+([.][0-9]+)?$'
  if ! [[ $1 =~ $re ]] ; then
    return 1
  else
    return 0
  fi
}

is_number $1

if [[ $? -eq 0 ]] ; 
then
  echo '$1 est un nombre reel'
else
  echo '$1 n'est pas un nombre reel'
fi
```

![](/TP2_IMG/TP2_2.png)

# Exercice 4. Controle d'utilisateur <a id='Anch4'></a>

## Écrivez un script qui vérifie l’existence d’un utilisateur dont le nom est donné en paramètre du script. Si le script est appelé sans nom d’utilisateur, il affiche le message : ”Utilisation : nom_du_script nom_utilisateur”, où nom_du_script est le nom de votre script récupéré automatiquement (si vous changez le nom de votre script, le message doit changer automatiquement)

```bash
#!/bin/bash

NB=$(cat /etc/passwd | wc -l)

if ! [[ $1 ]]
then 
  echo "Utilisation : $0 nom_utilisateur"
  exit
else
  for i in $(seq 1 $NB) 
  do
    USER=$(cat /etc/passwd | cut -d : -f1 | sort | head -n $i | tail -n 1)  
    if [[ $1 == USER ]]
    then
      echo "User $1 trouvé"
      exit
  done
  echo "User $1 not found"
fi
exit
```

![](/TP2_IMG/TP2_3.png)
![](/TP2_IMG/TP2_4.png)

# Exercice 5. Factorielle <a id='Anch5'></a>

## Écrivez un programme qui calcule la factorielle d’un entier naturel passé en paramètre (on supposera que l’utilisateur saisit toujours un entier naturel).

```bash
#!/bin/bash

NB=1

for i in $(seq 1 $1)
do
  NB=$(($NB * $i))
done
echo "Facotirelle $1 = $NB"
exit
```

![](/TP2_IMG/TP2_6.png)
![](/TP2_IMG/TP2_5.png)

# Exercice 6. Le juste prix <a id='Anch6'></a>

## Écrivez un script qui génère un nombre aléatoire entre 1 et 1000 et demande à l’utilisateur de le deviner. Le programme écrira ”C’est plus !”, ”C’est moins !” ou ”Gagné !” selon les cas (vous utiliserez $RANDOM).

```bash
#!/bin/bash

RD_NB=$(( $RANDOM % 1000 + 1 ))
NB_TRY = 0
read -p 'Entrez un nombre :' USER_NB
while [[ $RD_NB != $USER_NB ]]
do
    if [[ $RD_NB < $USER_NB ]]
    then 
        echo "C'est moins !"
    fi
    if [[ $RD_NB > $USER_NB ]]
    then
        echo "C'est plus !"
    fi
    read -p 'Entrez un nommbre : ' USER_NB
    NB_TRY=$(($NBTRY+1))
done
echo 'Gagné en $NB_TRY essais !"
exit
```

![](/TP2_IMG/TP2_7.jpg)
![](/TP2_IMG/TP2_8.jpg)

# Exercice 7. Statistiques <a id='Anch7'></a>

## 1. Écrivez un script qui prend en paramètres trois entiers (entre -100 et +100) et affiche le min, le max et la moyenne. Vous pouvez réutiliser la fonction de l’exercice 3 pour vous assurer que les paramètres sont bien des entiers.

```bash
#!/bin/bash
function is_number()
{
  re='^[+-]?[0-9]+([.][0-9]+)?$'
  if ! [[ $1 =~ $re ]]
  then
      return 1
  fi
  if (( $1 >= -100 && $1 <= 100 ))
  then
      return 0
  fi
  return 1
}

if (( $# > 0 ))
then
    MAX=$1
    MIN=$1
fi
if (( $# != 3 ))
then 
  echo "Veuillez entrer 3 chiffres reels entre -100 et 100"
  exit
fi
  
for i in $1 $2 $3
do
  is_number $i
  if [[ $? -ne 0 ]]
  then
      echo 'Veuillez entrer des nombres reels entre -100 et 100'
      exit
  fi 
  if (( $i > $MAX ))
  then
      MAX=$i
  fi
  if (( $i < $MIN ))
  then
      MIN=$i
  fi
done
MOY=$(( $1 + $2 + $3 ))
MOY=$(( $MOY / $# ))

echo "Max : $MAX"
echo "Min : $Min"
echo "Moy : $Moy"

exit
```

![](/TP2_IMG/TP2_9.jpg)

## 2. Généralisez le programme à un nombre quelconque de paramètres (pensez à SHIFT)

```bash
#!/bin/bash
function is_number()
{
  re='^[+-]?[0-9]+([.][0-9]+)?$'
  if ! [[ $1 =~ $re ]]
  then
      return 1
  fi
  if (( $1 >= -100 && $1 <= 100 ))
  then
      return 0
  fi
  return 1
}

if [[ $# > 0 ]]
then
    MAX=$1
    MIN=$1
else
  echo "Veuillez entrer 3 chiffres reels entre -100 et 100"
  exit
fi
  
for VAR in "$@"
do
  is_number $VAR
  if [[ $? -ne 0 ]]
  then
      echo 'Veuillez entrer des nombres reels entre -100 et 100'
      exit
  fi 
  if (( $VAR > $MAX ))
  then
      MAX=$VAR
  fi
  if (( $VAR < $MIN ))
  then
      MIN=$VAR
  fi
  MOY=$(( $MOY + $VAR ))
done

MOY=$(( $MOY / $# ))

echo "Max : $MAX"
echo "Min : $Min"
echo "Moy : $Moy"

exit
```

![](/TP2_IMG/TP2_10.jpg)

## 3. Modifiez votre programme pour que les notes ne soient plus données en paramètres, mais saisies et stockées au fur et à mesure dans un tableau.
```bash
#!/bin/bash

function is_number()
{
        re='^[+-]?[0-9]+([.][0-9]+)?$'
        if ! [[ $1 =~ $re ]] ;
        then
                return 1
        fi
        if (( $1 >= -100 && $1 <= 100))
        then
                return 0
        fi
        return 1
}

function print_stat()
{
        for VAR in "$@"
        do
                is_number $VAR

                if [[ $? -ne 0 ]]
                then
                        echo 'Veuillez entrer des nombres reels entre -100 et 100'
                        exit
                fi
                if (( $VAR > $MAX ))
                then
                        MAX=$VAR
                fi
                if (( $VAR < $MIN ))
                then
                        MIN=$VAR
                fi
                MOY=$(( $MOY + $VAR ))
        done

        MOY=$(( $MOY / $# ))

        echo "Max : $MAX"
        echo "Min : $MIN"
        echo "Moy : $MOY"
}

read -p 'Combien de notes voulez vous entrer ? ' NB_NOTE
for i in $(seq 1 $NB_NOTE)
{
        read -p 'Veuillez entrer une note : ' tab[$i]
}

echo ${tab[@]}

if (( $NB_NOTE > 0 ))
then
        MAX=-200
        MIN=200
else
        echo "Veuiller entrer au moins une note"
        exit
fi

print_stat ${tab[@]}
```

![](/TP2_IMG/TP2_13.png)

# Exercice 8. Bonus <a id='Anch8'></a>

## Écrivez un script qui affiche les combinaisons possibles de couleurs (cf. TP 1) :

```bash
#!/bin/bash

for i in '39' '30' '31' '32' '33' '34' '35' '36' '37' '90' '91' '92' '93' '94' '95' '96' '97'
do
        for j in  '49' '40' '41' '42' '43' '44' '45' '46' '47' '100' '101' '102' '103' '104' '105' '106' '107'
        do
                echo -e "\e[${j};${i}m Hello World! \e[0;0m"
        done
done
exit
```

![](/TP2_IMG/TP2_11.jpg)

![](/TP2_IMG/TP2_12.jpg)

