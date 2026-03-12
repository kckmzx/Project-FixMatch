Ok, on clarifie rapidement la cross-entropie, c'est important car c'est au cœur de la loss de FixMatch.

---

## La cross-entropie en clair

C'est simplement une **mesure de l'erreur entre ce que le modèle prédit et la vraie réponse**.

### Exemple concret

Le modèle doit classer une image parmi 3 classes : chien, chat, lapin.

La **vraie étiquette** (one-hot) : `[1, 0, 0]` → c'est un chien

Le modèle **prédit** : `[0.7, 0.2, 0.1]` → 70% chien, 20% chat, 10% lapin

La cross-entropie calcule : **à quel point la prédiction est loin de la vérité ?**

```
H = - log(0.7) ≈ 0.35   ← petite erreur, le modèle est assez confiant et correct
```

Si le modèle prédit `[0.1, 0.8, 0.1]` (très mauvaise prédiction) :
```
H = - log(0.1) ≈ 2.3   ← grande erreur
```

**La règle simple : plus le modèle est confiant ET correct, plus H est proche de 0.**

---

## Dans FixMatch

- **Loss supervisée** : cross-entropie entre la vraie étiquette et la prédiction → classique
- **Loss non supervisée** : cross-entropie entre le **pseudo-label** (traité comme une vraie étiquette) et la prédiction sur l'image fortement augmentée

C'est exactement la même formule dans les deux cas. La seule différence c'est que dans le cas non supervisé, l'étiquette vient du modèle lui-même (pseudo-label) et non d'un humain.

---

Tu veux qu'on revoit aussi la data augmentation, ou on passe à la suite du projet ?