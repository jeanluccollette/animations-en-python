# Animations en Python
 
## Introduction

Un environnement de programmation en Python est proposé (archive [animabase.zip](animabase.zip)) afin de réaliser facilement des animations sur des systèmes mécaniques.

Les exemples proposés sont les suivants :
- Pendule simple
- Pendule double
- Pendule sur chariot avec commande d’asservissement (voir "Cas particulier du pendule sur un chariot mobile")
 
## Représentation d’un système

### Principe

Dans le fichier **objets.py**, on définit trois classes « objet » (**PenSim**, **PenDbl**, **PenCha**) associées chacune à la description d’un système mécanique, comme ceux mentionnés en introduction.

Elles héritent des classes **MethNumInt** (méthodes d’intégration numérique d’équation différentielle) et **AnimObj** (méthodes pour animer le graphique) qui définissent des méthodes communes aux trois classes mentionnées.

### Les classes PenSim, PenDbl et PenCha

Chaque classe « objet » (**PenSim**, **PenDbl**, **PenCha**) associée à la description d’un système est organisée de la même façon. Pour décrire cette organisation, l’exemple du pendule simple sera utilisé.

#### Création d’une instance

Le nom de la classe est par exemple **PenSim** pour le pendule simple. La création d’une instance (un pendule avec ses propres paramètres) se fera ainsi :
```python
import objets
pensim = objets.PenSim(l,g)
```
Ici, **l** est la longueur du pendule et **g** l’accélération de la pesanteur.

#### Les attributs

Dans l’instance créée, on pourra ainsi utiliser les paramètres propres au système (ici, longueur **self.l** du pendule et accélération de la pesanteur **self.g**). Les attributs suivants sont ceux associés à l’animation du graphique. 
- **self.T** : durée exacte de l’animation en s
- **self.Tdeb** : délai approximatif en s après lequel l’animation commence (doit être différent de 0)
- **self.interval** : intervalle de temps approximatif en ms entre deux mises à jour du graphique (mais non constant en réalité et plus long)
- **self.dt** : pas de calcul constant en s pour la résolution numérique de l’équation différentielle régissant le mouvement.
- **self.y0** : état initial du système

#### Les méthodes d’instance

##### méthode \_\_init\_\_()

On initialise notamment les attributs **self.l** et **self.g** quand l’instance est créée. Les valeurs par défaut de tous les attributs aboutissent à un fonctionnement correct.

#####  méthode **F()**

L’équation dynamique du système est mise sous la forme :

$$\dfrac{dY}{dt}=F(t,Y)$$

La méthode **F()** prend en paramètre l’instant **t** et le vecteur d’état **Y** (pour le pendule simple, l’angle avec la verticale et la vitesse angulaire) et retourne **Yp** (ce que vaut la dérivée du vecteur d’état à l’instant considéré). Cette méthode sera utilisée pour la résolution numérique de l’équation différentielle régissant le mouvement.

#####  méthode init_graph()

La méthode configure la structure générale du graphique, via l’initialisation de l’attribut **self.fig**. L’attribut **self.listegraph** doit rassembler dans une liste les éléments particuliers qui seront modifiés durant l’animation.

#####  méthode graph()

La méthode prend en paramètre l’instant **t** et le vecteur d’état **Y**, et sera lancée autant de fois que nécessaire pour la mise à jour du graphique, via la modification des éléments rassemblés dans la liste **self.listegraph**.

### La classe MethNumInt

Cette classe rassemble les méthodes numériques d’intégration pour les équations différentielles (**rk2()** ou **rk4()**). L’attribut **self.methode_int** désignera la méthode choisie dans chaque classe (**PenSim**, **PenDbl**, **PenCha**) qui en hérite. On y trouve notamment la méthode de Runge-Kutta d’ordre 4, qui sera celle choisie par défaut (**self.methode_int = getattr(self, meth)** avec **meth="rk4"**).

### La classe AnimObj

Cette classe gère la mise en œuvre de l’animation.

La méthode **animate()** est appelée à chaque itération, le paramètre **n** désignant le numéro de l’itération.
Cette méthode **animate()** est lancée via une instance de la classe **FuncAnimation** et met ainsi à jour le graphique, le paramètre **n** étant incrémenté d’une unité à chaque mise à jour. Pour remédier au fait que l’intervalle de temps **self.interval** ne correspond pas au temps qui s’est réellement écoulé entre deux mises à jour consécutives du graphique, on va mesurer ce temps réellement écoulé (fonction **time()**) pour faire avancer la simulation numérique du nombre variable de pas correspondant.
Ainsi, le temps affiché dans le graphique sera bien le temps réel qui s’écoule, et l’état final de la simulation sera toujours le même, quelle que soit la cadence aléatoire de rafraîchissement du graphique.

La méthode **anim()** lance l’animation complète.

## L’animation

Avec les classes ainsi définies, le lancement de l’animation est très simple pour chaque objet. Les fichiers mentionnés ci-dessous sont directement exécutables. On y créée une instance de la classe choisie, puis on lance l’animation associée à cette instance.

Fichier pendule_simple.py :
```python
import objets
pensim = objets.PenSim()
pensim.anim()
```

Fichier pendule_double.py :
```python
import objets
pendbl = objets.PenDbl()
pendbl.anim()
```

Fichier pendule_chariot.py :
```python
import objets
pencha = objets.PenCha()
pencha.anim()
```

## Cas particulier du pendule sur un chariot mobile
Pour la classe **PenCha** dans le fichier **objets.py**, on remarquera que d’autres méthodes d’instance ont été ajoutées à celles déjà décrites. Elles introduisent une commande qui est une force horizontale s’appliquant sur le chariot, permettant le maintien du pendule dans sa position d’équilibre instable. On peut interrompre l’application de cette commande à un instant donné (paramètre **tcut**) pour observer ensuite l’évolution du système libre. L’élaboration de cette commande n’est donnée qu’à titre d’exemple et ne fonctionne qu’avec les paramètres par défaut.

## Illustration

https://github.com/user-attachments/assets/3be57380-4d9c-4e44-8916-9406f658c1fd

## Bonus avec l'effet Djanibekov

L'effet se produit pour tout corps rigide en impesanteur — ou chute libre — qui présente trois axes principaux avec des moments d'inertie différents, selon la [description](https://fr.wikipedia.org/wiki/Effet_Djanibekov) disponible sur Wikipédia.

La méthode décrite dans le [document](https://www.f-legrand.fr/scidoc/srcdoc/sciphys/meca/solide/solide-pdf.pdf) rédigé par [Frédéric Legrand](https://www.f-legrand.fr/scidoc/) a été utilisée.

L'archive [toupies.zip](toupies.zip) contient les programmes permettant la simulation numériques, selon les mêmes principes déjà exposés.

Fichier toupie.py :
```python
import toupies
toupie = toupies.Toupie()
toupie.anim()
```

Pour la mise en équation, on définit le vecteur rotation $\vec{\Omega}_s$ dans le repère solidaire du corps rigide. Ce repère correspond aux axes principaux d'inertie, et les moments principaux d'inertie y sont notés $I_1$, $I_2$ et $I_3$.

\begin{equation}
\vec{\Omega}_s=\left(\begin{matrix}\Omega_1\\\Omega_2\\\Omega_3\end{matrix}\right)
\end{equation}

Par ailleurs, on définit les trois vecteurs orthonormaux orientant les axes principaux d'inertie du corps rigide, rassemblés dans les trois colonnes d'une matrice $Q$. Le mouvement s'effectue dans un référentiel inertiel et aucune force ne s'applique au solide.

L'équation dynamique est donnée ci-dessous. Elle comprend 12 variables, 3 pour les vitesses angulaires et 9 pour la matrice Q qui permettra de représenter le mouvement du solide dans le référentiel inertiel.

\begin{align}
\frac{d\Omega_1}{dt}&=\Omega_2\Omega_3\frac{(I_2 - I_3)}{I_1}\\
\frac{d\Omega_2}{dt}&=\Omega_3\Omega_1\frac{(I_3 - I_1)}{I_2}\\
\frac{d\Omega_3}{dt}&=\Omega_1\Omega_2\frac{(I_1 - I_2)}{I_3}\\
\frac{dQ}{dt}&=Q\left(\begin{matrix}
0&-\Omega_3&\Omega_2\\
\Omega_3&0&-\Omega_1\\
-\Omega_2&\Omega_1&0
\end{matrix}\right)\\
\end{align}

La résolution numérique de cette équation dynamique peut être validée en calculant le moment cinétique $\vec{\sigma}$ dans le référentiel inertiel, qui doit rester constant en théorie. Le moment cinétique $\vec{\sigma}_s$ défini sur les axes principaux d'inertie a une expression simple et donne accès à $\vec{\sigma}$ via le changement de base réalisé avec la matrice $Q$.

\begin{equation}
\vec{\sigma}=Q\vec{\sigma}_s=Q\left(\begin{matrix}I_1 \Omega_1\\I_2\Omega_2\\I_3\Omega_3\end{matrix}\right)
\end{equation}

