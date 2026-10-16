# Etude-de-marché-identifier-les-pays-cibles-pour-l-export-de-volaille

Python · Jupyter · Statistiques · Formation

Analyse des performances de vente d'une librairie en ligne, du comportement de ses clients et de ses produits, avec des tests statistiques pour distinguer les liens réels des simples effets de taille d'échantillon.

## Contexte

Ce projet a été réalisé dans le cadre de ma formation de Data Analyst chez OpenClassrooms. La mission : analyser les ventes d'une librairie en ligne pour en comprendre l'évolution, le profil de la clientèle et les produits stratégiques, afin de proposer des pistes d'amélioration marketing.

Trois fichiers : 8 621 clients, 3 286 produits répartis en 3 catégories (0, 1 et 2), et 687 534 lignes de transaction entre 2021 et 2024.

## Démarche

1. **Préparation des données**
   - Contrôle de chaque fichier avant jointure (unicité des identifiants, distributions)
   - Conversion des dates en série temporelle, jointure clients / transactions / produits

2. **Évolution dans le temps**
   - Nombre de transactions par jour avec moyenne glissante sur 30 jours
   - Chiffre d'affaires par tranche de 10 jours et décomposition saisonnière
   - Évolution mensuelle du nombre de clients, de transactions et de produits vendus

3. **Profil des clients et concentration du CA**
   - Courbe de Lorenz du CA par client
   - Identification de 4 clients à plus de 100 000 € de dépenses, écartés ensuite de l'analyse (BtoB)

4. **Comportement des clients**
   - Genre et catégorie de livre (test du χ²)
   - Âge et montant des achats, fréquence d'achat, panier moyen (corrélation de Spearman)
   - Âge et catégorie de livre (test de Kruskal-Wallis et post-hoc de Dunn)

5. **Top / Flop des produits**
   - Classement par fréquence de vente et par chiffre d'affaires

## Résultats

- Les **4 plus gros clients** (0,05 % de la base) pèsent environ **7,4 % du CA** : des comptes de 114 000 € à 326 000 €, très probablement des clients professionnels. Ils sont traités à part pour ne pas fausser les analyses.
- Répartition des ventes : **60 % en catégorie 0, 34 % en catégorie 1 et 5 % en catégorie 2**
- Les femmes réalisent 52 % des achats. Le lien entre genre et catégorie est statistiquement significatif (χ² = 22,67 ; p = 1,2e-5), mais **l'effet est négligeable** (V de Cramér < 0,01) : les proportions varient de moins d'un point.
- **Âge et fréquence d'achat** : lien faible mais net (ρ = +0,21). Les clients plus âgés reviennent plus souvent.
- **Âge et panier moyen** : corrélation négative modérée (ρ = −0,33). Plus les clients sont jeunes, plus leur panier moyen est élevé.
- **Âge et catégorie** : lien significatif, avec une taille d'effet modérée (ε² ≈ 0,11)
- Les **best-sellers en volume** sont tous en catégorie 1 (le premier : 2 205 ventes), alors que les **produits les plus rentables** sont en catégorie 2 (le premier : 91 098 € pour 624 ventes)
- Les produits les moins vendus sont presque tous en catégorie 0, avec une seule vente sur la période

## Aller plus loin : analyses complémentaires

- **Détection et traitement des valeurs extrêmes** par la courbe de Lorenz : repérage du « biseau » artificiel causé par 4 clients atypiques, puis recalcul de la courbe sans eux
- **Taille d'effet systématique** : au-delà de la p-value, calcul du V de Cramér et de l'epsilon² pour ne pas confondre significativité et pertinence, ce qui compte avec 640 000 lignes
- **Choix des tests justifié par les données** : Spearman plutôt que Pearson, et Kruskal-Wallis plutôt qu'ANOVA, car la distribution des âges n'est pas normale. Complété par un test post-hoc de Dunn (correction de Bonferroni).
- **Lecture des graphiques en densité** (hexbin) à la place des nuages de points illisibles à cette échelle, qui ont montré qu'un « L » apparent sur le premier graphique était trompeur

## Réalisations

**Significativité et taille d'effet : genre et catégorie (Python)**

```python
table = pd.crosstab(df["sex"], df["categ"])
chi2, p, dof, expected = chi2_contingency(table)

# Taille d'effet : V de Cramér
n = table.values.sum()
cramers_v = np.sqrt(chi2 / (n * (min(table.shape) - 1)))
```

![Courbe de Lorenz](images/lorenz.png)
*Courbe de Lorenz du CA par client*

![Évolution du CA](images/evolution_ca.png)
*Évolution du chiffre d'affaires dans le temps*

![Distribution des âges par catégorie](images/boxplot_age_categ.png)
*Distribution des âges par catégorie de produit*

## Stack

`Python` · `pandas` · `seaborn` · `scipy` · `statsmodels` · `scikit-posthocs` · `Jupyter`

[Voir le projet complet sur GitHub →]()
[Retour à la liste de projets →]()
