# Stim QEC Decoding Lab

Ce dépôt contient un travail de prise en main de **Stim** appliqué à la correction d'erreurs quantiques.
Il suit une progression allant des circuits stabilisateurs aux détecteurs, aux observables logiques, au `Detector Error Model`, puis à la construction des objets nécessaires au décodage :

$$
H,\quad A,\quad p,\quad s,\quad o.
$$

L'objectif final est de préparer une base expérimentale propre pour le développement de décodeurs de correction d'erreurs quantiques.

---

## Objectifs du projet

Ce dépôt vise à comprendre et manipuler les briques fondamentales nécessaires au décodage d'erreurs quantiques :

* générer des circuits stabilisateurs avec **Stim** ;
* simuler des circuits bruités ;
* comprendre les détecteurs Stim ;
* extraire des syndromes et des observables logiques ;
* lire et interpréter un `Detector Error Model` ;
* construire les matrices $H$ et $A$ ;
* préparer une base de travail pour implémenter des décodeurs QEC.

---

## Structure du dépôt

```text
stim-qec-decoding-lab/
├── notebooks/
│   └── 01_stim_getting_started_personnel.ipynb
├── src/
├── tests/
├── data/
├── requirements.txt
└── README.md
```

---

## Prérequis

Le projet utilise Python et les bibliothèques suivantes :

* `stim`
* `numpy`
* `matplotlib`
* `scipy`
* `pymatching`
* `jupyter`
* `ipykernel`

Installation recommandée dans un environnement virtuel :

```bash
python3 -m venv .venv
source .venv/bin/activate

pip install --upgrade pip
pip install stim numpy matplotlib scipy pymatching jupyter ipykernel
```

Ajout du kernel Jupyter :

```bash
python -m ipykernel install --user --name qec-stim --display-name "Python (qec-stim)"
```

Dans Jupyter, sélectionner ensuite le kernel :

```text
Python (qec-stim)
```

---

## Lancer Jupyter

Depuis la racine du projet :

```bash
source .venv/bin/activate
jupyter notebook
```

Puis ouvrir :

```text
notebooks/01_stim_getting_started_personnel.ipynb
```

---

## Contenu du premier notebook

Le notebook `01_stim_getting_started_personnel.ipynb` suit une progression pédagogique :

1. premiers circuits Stim ;
2. mesures brutes ;
3. détecteurs ;
4. génération d'un surface code de distance $d=3$ ;
5. sampling des syndromes et des observables ;
6. extraction du `Detector Error Model` ;
7. interprétation des lignes `error(p) D... L...` ;
8. préparation de la construction de $H$, $A$ et $p$.

---

## Rappel : modèle algébrique du décodage

À partir du `Detector Error Model`, on construit une matrice détecteur-erreur :

$$
H \in \mathbb{F}_2^{M \times N}.
$$

Dans cette matrice :

* $M$ est le nombre de détecteurs ;
* $N$ est le nombre de sources d'erreurs ;
* chaque ligne correspond à un détecteur ;
* chaque colonne correspond à une source d'erreur ;
* $H_{ij}=1$ si la source d'erreur $j$ retourne le détecteur $i$.

On construit aussi une matrice observable-erreur :

$$
A \in \mathbb{F}_2^{K \times N}.
$$

Dans cette matrice :

* $K$ est le nombre d'observables logiques ;
* $A_{kj}=1$ si la source d'erreur $j$ retourne l'observable logique $k$.

Le vecteur de syndrome est donné par :

$$
s = He.
$$

Le but du décodeur est de trouver une correction $\hat e$ telle que :

$$
H\hat e = s.
$$

En simulation, on vérifie aussi la classe logique :

$$
A\hat e = o.
$$

---

## Exemple Stim minimal

```python
import stim

circuit = stim.Circuit.generated(
    "surface_code:rotated_memory_z",
    distance=3,
    rounds=3,
    after_clifford_depolarization=0.001,
    after_reset_flip_probability=0.001,
    before_measure_flip_probability=0.001,
    before_round_data_depolarization=0.001,
)

print("Number of qubits:", circuit.num_qubits)
print("Number of detectors:", circuit.num_detectors)
print("Number of observables:", circuit.num_observables)

sampler = circuit.compile_detector_sampler()
detections, observables = sampler.sample(
    shots=1000,
    separate_observables=True,
)

print("Detections shape:", detections.shape)
print("Observables shape:", observables.shape)

dem = circuit.detector_error_model()
print(str(dem)[:1000])
```

---

## Objectif technique à court terme

À la fin de cette première étape, le projet doit permettre de générer proprement :

```text
H : matrice détecteur-erreur
A : matrice observable-erreur
p : probabilités des sources d'erreurs
s : syndromes échantillonnés
o : observables logiques échantillonnés
```


## Statut

Projet en cours.

Première étape : prise en main de Stim et construction du pipeline

$$
\text{circuit Stim} \rightarrow \text{Detector Error Model} \rightarrow H,A,p.
$$

---

## Références

* Stim — Quantum stabilizer circuit simulator
  https://github.com/quantumlib/Stim

* Stim Getting Started Notebook
  https://github.com/quantumlib/Stim/blob/main/doc/getting_started.ipynb

* Craig Gidney, *Stim: a fast stabilizer circuit simulator*
  https://arxiv.org/abs/2103.02202
