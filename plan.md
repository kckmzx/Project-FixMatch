## Le but du projet

L'objectif est de **comprendre FixMatch en profondeur**, reproduire ses résultats, puis mener vos propres expériences pour aller au-delà du papier. La présentation dure 30 min donc vous devez avoir des choses solides à montrer.

---

## Plan en 4 phases

### Phase 1 — Comprendre le papier (1-2 jours)
Lire le papier [FixMatch](https://arxiv.org/abs/2001.07685) et comprendre :
- Pourquoi l'apprentissage semi-supervisé est utile ?
- Comment fonctionne la loss (supervisée + non supervisée) ?
- Le rôle du seuil de confiance (0.95)
- Ce que fait RandAugment

### Phase 2 — Reproduire les résultats (3-5 jours)
Lancer les vrais entraînements **sur Colab avec GPU** (pas sur ton Mac) et vérifier que vous obtenez des résultats proches du papier :

| Config | Résultat papier |
|---|---|
| CIFAR-10, 40 labels | ~86% |
| CIFAR-10, 250 labels | ~95% |
| CIFAR-10, 4000 labels | ~96% |

### Phase 3 — Expériences originales (1 semaine)
C'est **la partie la plus importante** pour la note. Le repo suggère deux pistes :

**Piste A — Autres méthodes semi-supervisées** : comparer FixMatch avec Mean Teacher ou MixMatch. Est-ce que FixMatch est vraiment meilleur ?

**Piste B — Sélection intelligente des labels** : au lieu de choisir les 250 exemples étiquetés au hasard, peut-on choisir les plus "informatifs" (style active learning) pour améliorer les performances ?

D'autres idées plus simples à implémenter :
- Faire varier le seuil de confiance (0.5, 0.7, 0.95, 0.99) et observer l'impact
- Tester sur CIFAR-100 au lieu de CIFAR-10
- Remplacer RandAugment par CutMix

### Phase 4 — Présentation (2-3 jours)
Structurer les résultats, faire les slides, préparer les 10 min de Q&A.

---

## Recommandation concrète

Pour un groupe de 2-3 personnes, je suggère de **se concentrer sur une seule expérience originale bien menée** plutôt que plusieurs superficielles. La variation du seuil de confiance est la plus accessible, la comparaison avec Mean Teacher est la plus impressionnante.

Tu es combien dans le groupe ?