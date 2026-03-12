## La data augmentation en clair

L'idée de base : **une image légèrement modifiée reste la même image**. Un chien photographié de face ou légèrement pivoté, c'est toujours un chien. On peut donc créer artificiellement plus de données d'entraînement en transformant les images existantes.

---

## Les transformations utilisées dans FixMatch

**Augmentation faible** (pour générer le pseudo-label) — transformations subtiles :
- Flip horizontal aléatoire (50% de chances)
- Translation légère (±12.5% de la taille de l'image)

L'image reste clairement reconnaissable.

**Augmentation forte** (pour entraîner le modèle) — transformations agressives, par exemple :
- Rotation forte
- Changement de contraste, luminosité, couleurs
- Solarisation (inversion des pixels au-dessus d'un seuil)
- **Cutout** : on cache une zone carrée aléatoire de l'image avec du gris

Visuellement, ça donne ça :

```
Image originale      → Augmentation faible   → Augmentation forte
[photo nette]        → [légèrement flippée]  → [tordue, décolorée, zone cachée]
```

---

## Pourquoi c'est important dans FixMatch

Sans augmentation forte, les résultats s'effondrent. Le papier le montre clairement : remplacer l'augmentation forte par une faible → le modèle plafonne à 45% puis s'effondre à 12%.

L'intuition : si les deux versions de l'image se ressemblent trop, le modèle n'apprend rien de nouveau. La version fortement déformée **force** le modèle à apprendre des caractéristiques vraiment robustes et générales.

---

On passe à la suite du projet ?