Voici une explication complète du papier, page par page.

---

## Le problème de départ

Entraîner un réseau de neurones nécessite beaucoup de données **étiquetées** (labellisées), ce qui est coûteux. L'apprentissage semi-supervisé (SSL) cherche à exploiter aussi les données **non étiquetées**, qui sont beaucoup plus faciles à obtenir.

---

## L'idée centrale de FixMatch (pages 1-3)

FixMatch combine deux techniques classiques de SSL :

**1. Consistency regularization** : si tu montres deux versions légèrement différentes d'une même image au modèle, il devrait prédire la même classe. Le modèle est pénalisé s'il n'est pas cohérent.

**2. Pseudo-labeling** : on utilise le modèle lui-même pour générer des étiquettes artificielles sur les données non étiquetées, mais seulement quand il est **très confiant** (probabilité > seuil τ = 0.95).

La **nouveauté de FixMatch** est de combiner les deux avec une asymétrie forte :
- Augmentation **faible** → génère le pseudo-label (flip + translation légère)
- Augmentation **forte** → c'est sur cette version distordue que le modèle doit prédire le pseudo-label

---

## La formule mathématique (page 3)

La loss totale est simplement :

```
L = Ls + λu × Lu
```

**Ls** (loss supervisée) : cross-entropie classique sur les données étiquetées avec augmentation faible.

**Lu** (loss non supervisée) :
```
Pour chaque image non étiquetée ub :
  qb = prédiction du modèle sur α(ub)  [augmentation faible]
  Si max(qb) ≥ τ :
      pénalise si la prédiction sur A(ub) [augmentation forte] ≠ pseudo-label
```

Le seuil τ crée un **curriculum naturel** : au début de l'entraînement le modèle est peu confiant → peu de pseudo-labels → faible contribution de Lu. Au fur et à mesure que le modèle s'améliore, il devient plus confiant et Lu contribue de plus en plus.

---

## Les augmentations utilisées (page 4)

**Faible (α)** : flip horizontal (50%) + translation aléatoire ±12.5%

**Forte (A)** : deux variantes testées dans le papier :
- **RandAugment** : tire aléatoirement des transformations parmi une liste (rotation, contraste, netteté, solarisation, etc.) avec une magnitude aléatoire
- **CTAugment** : même principe mais les magnitudes sont **apprises en ligne** pendant l'entraînement selon les performances sur les données étiquetées

Dans les deux cas, on applique aussi **Cutout** (masque une zone carrée aléatoire de l'image).

---

## Les résultats expérimentaux (pages 5-7)

FixMatch est testé sur CIFAR-10, CIFAR-100, SVHN, STL-10 et ImageNet. Le tableau clé à retenir :

| Dataset | Labels | FixMatch | Meilleur concurrent |
|---|---|---|---|
| CIFAR-10 | 40 | 11.4% erreur | 19.1% (ReMixMatch) |
| CIFAR-10 | 250 | 5.1% erreur | 5.4% (ReMixMatch) |
| CIFAR-10 | 4000 | 4.3% erreur | 4.7% (ReMixMatch) |

FixMatch est plus simple ET plus performant que les méthodes précédentes dans presque tous les cas.

Un résultat frappant : avec **seulement 1 label par classe** (10 images pour 10 classes de CIFAR-10), FixMatch atteint jusqu'à **78% de précision** si les exemples sont prototypiques (représentatifs de leur classe). Avec des outliers, le modèle ne converge pas du tout → la **qualité des rares exemples étiquetés compte énormément**.

---

## L'étude d'ablation (pages 8-9 + annexes)

C'est la partie la plus riche du papier pour votre projet. Les auteurs testent chaque composant :

**Seuil de confiance τ** : 0.95 est optimal. Un seuil trop bas → beaucoup de pseudo-labels faux → confirmation bias → le modèle se trompe et se renforce dans ses erreurs. Un seuil trop haut → trop peu de pseudo-labels → la loss non supervisée n'apporte rien.

**Augmentation forte vs faible** : cruciale. Si on met une augmentation forte pour générer le pseudo-label aussi, le modèle diverge. Si on met une augmentation faible pour les deux, les performances s'effondrent à ~12%.

**Optimiseur** : SGD avec momentum β=0.9 est bien meilleur qu'Adam. Avec β=0.999, le modèle ne converge tout simplement pas (84% d'erreur !).

**Weight decay** : très sensible. Un ordre de grandeur d'erreur dans ce paramètre peut coûter 10 points de précision.

**Ratio données non étiquetées/étiquetées (µ)** : plus on utilise de données non étiquetées par batch, mieux c'est. µ=7 par défaut (7x plus de non étiquetés que d'étiquetés).

---

## Ce que ça signifie pour votre projet

Les ablations du papier vous donnent directement des **idées d'expériences reproductibles** :
- Faire varier τ (facile, juste changer un argument en ligne de commande)
- Comparer à une baseline supervised-only (le papier donne les chiffres dans la Table 9 : avec 250 labels sans SSL → 39-42% d'erreur vs 5% avec FixMatch, c'est un argument fort)
- Tester µ différents

Et les pistes suggérées par le repo (Mean Teacher, active learning) s'inscrivent naturellement dans les limites discutées dans la conclusion du papier.