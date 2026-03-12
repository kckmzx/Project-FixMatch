OK, on repart de zéro avec des mots simples. Oublie les formules pour l'instant.

---

## Le problème de base

Tu veux apprendre à un modèle à reconnaître des images (chien, chat, voiture...). Normalement tu as besoin de **beaucoup d'images étiquetées** — c'est-à-dire des images où quelqu'un a écrit "c'est un chien" à côté.

Le problème : étiqueter des images c'est long et cher. Par contre, trouver des images sans étiquette sur internet, c'est gratuit et illimité.

**La question de FixMatch : peut-on utiliser ces images non étiquetées pour améliorer le modèle ?**

---

## L'idée intuitive

Imagine que tu as un modèle déjà un peu entraîné. Tu lui montres une photo de chien (sans étiquette). Il dit "je pense que c'est un chien à 97%". 

→ 97% c'est très confiant. On peut faire confiance à cette prédiction et l'utiliser comme si c'était une vraie étiquette. C'est ce qu'on appelle un **pseudo-label**.

Maintenant, tu prends la même photo, tu la déforme fortement (tu la fais pivoter, tu changes les couleurs, tu caches une partie), et tu demandes au modèle : "c'est quoi ça ?". La bonne réponse doit toujours être "chien". Si le modèle se trompe, tu le corriges.

**C'est tout FixMatch.** Le reste c'est des détails.

---

## Les deux ingrédients

**Ingrédient 1 — Pseudo-labeling avec seuil de confiance**

Sur une image non étiquetée, le modèle prédit une probabilité pour chaque classe. Par exemple :
```
chien : 97%
chat  :  2%
lapin :  1%
```
Si la probabilité max dépasse le seuil τ = 0.95 (95%), on accepte "chien" comme pseudo-label. Sinon, on ignore cette image pour l'instant.

Pourquoi ce seuil ? Parce que si le modèle dit "chien à 60%", il n'est pas sûr, et utiliser ce pseudo-label risque d'apprendre des bêtises au modèle (c'est ce qu'on appelle le *confirmation bias* : le modèle se trompe, se renforce dans son erreur, et ça empire).

**Ingrédient 2 — Augmentation faible vs forte**

Pour générer le pseudo-label, on utilise une version **légèrement modifiée** de l'image (juste un petit flip horizontal, une légère translation). L'image est encore clairement reconnaissable → la prédiction est fiable.

Ensuite, on prend la **même image mais très fortement déformée** (couleurs changées, rotation, une zone cachée, etc.) et on entraîne le modèle à retrouver le même pseudo-label.

Pourquoi ? Parce que ça force le modèle à apprendre des représentations **robustes**. Si tu peux reconnaître un chien même quand la photo est tordue dans tous les sens, tu as vraiment compris ce qu'est un chien.

---

## Ce qui se passe pendant l'entraînement

Au début, le modèle est mauvais → il est rarement confiant à 95% → presque aucun pseudo-label n'est utilisé → la partie non supervisée contribue peu.

Au fur et à mesure que le modèle s'améliore grâce aux données étiquetées → il devient plus confiant → plus de pseudo-labels passent le seuil → la partie non supervisée contribue de plus en plus.

C'est ce que les auteurs appellent un **curriculum naturel** : la difficulté augmente progressivement toute seule, sans qu'on ait besoin de le programmer explicitement.

---

## La loss en français simple

La loss c'est juste la mesure de l'erreur du modèle. FixMatch minimise deux erreurs :

**Erreur supervisée** : sur les images étiquetées, est-ce que le modèle prédit la bonne classe ? (classique)

**Erreur non supervisée** : sur les images non étiquetées où le modèle est confiant, est-ce que sa prédiction sur la version fortement déformée correspond au pseudo-label ? (nouveau)

On additionne les deux et on minimise ça pendant l'entraînement.

---

## Un exemple concret de bout en bout

1. Tu as 250 images étiquetées de CIFAR-10 (25 par classe) et 50 000 images sans étiquette.
2. Le modèle voit une image non étiquetée d'un chien légèrement flippée → prédit "chien à 98%" → pseudo-label = chien ✓
3. Le modèle voit la même image mais avec couleurs modifiées + zone cachée + rotation → prédit "chat à 60%" → erreur ! → on le corrige vers "chien".
4. Cette correction améliore le modèle.
5. Répète des millions de fois.

Résultat : avec seulement 250 images étiquetées, FixMatch atteint 95% de précision sur CIFAR-10. Un modèle entraîné uniquement sur ces 250 images étiquetées atteindrait seulement ~60%.

---

Tu veux qu'on approfondisse un point en particulier ?