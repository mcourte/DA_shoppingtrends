# ANALYSE DES VENTES D'UN SITE DE E-COMMERCE

## Import et installation 


```python
import pandas as pd
import numpy as np
import re
import matplotlib.pyplot as plt
import seaborn as sns
import skimpy 
from skimpy import skim
import plotly.express as px
```

## Création du DataFrame de base


```python
data = pd.read_csv("shopping_trends.csv", sep=",", low_memory=False)
```


```python
df = data.copy()
```


```python
df.shape
```


```python
df.info()
```


```python
df.describe().T
```


```python
df.describe(include="object").T
```


```python
skim(df)
```

### Taux de remplissage des colonnes


```python
# Calculer le taux de remplissage pour chaque colonne
column_fill_rates = df.notna().mean() * 100

# Afficher le taux de remplissage pour chaque colonne
column_fill_rates
```


```python
df.isnull().sum()
```

L'ensemble des colonnes étant remplies et ne comportant aucune valeur nulle, nous pourrons faire une analyse complète des données.

## Produits les plus vendus


```python
item_purchased=df['Item Purchased'].value_counts().reset_index()
item_purchased
```


```python
plt.figure(figsize=(20, 8))
df['Item Purchased'].value_counts().plot(kind='pie',autopct='%1.1f%%')
plt.title('Distribution de Item purchased')
plt.ylabel("")
plt.tight_layout()
plt.show()
```

On remarque une répartition plutôt égale de la vente des produits. Le produit le plus vendu a été 171 fois, & le moins vendu 124 fois.


```python
item_revenu = df.groupby('Item Purchased')['Purchase Amount (USD)'].sum()
item_revenu.sort_values(ascending=False)
```


```python
columns_to_display = ['Item Purchased','Purchase Amount (USD)']
df[columns_to_display]
```

On constate que le produit qui rapport le plus d'argent est la **blouse** ce qui est cohérent avec le fait que ça soit l'item le plus vendu et le plus cher.

## Les Catégories


```python
df['Category'].value_counts()
```


```python
# Calculer la distribution des catégories
category_counts = df['Category'].value_counts()

# Tracer un pie chart
category_counts.plot(kind='pie', autopct='%1.1f%%', figsize=(8, 8), colors=sns.color_palette('Set3', len(category_counts)))

# Ajouter un titre
plt.title('Répartition des catégories de produits')

# Afficher le graphique
plt.tight_layout()
plt.show()

```

La catégories présente en plus grande proprotion est **Clothing** ( Vêtements) suivi d'**Acessories** ( Acessoires)


```python
# Regrouper les données par 'Category' et calculer la somme des 'Purchase Amount (USD)'
category_revenue = df.groupby('Category')['Purchase Amount (USD)'].sum().reset_index()

# Trier les résultats par montant décroissant
category_revenue = category_revenue.sort_values(by='Purchase Amount (USD)', ascending=False)

# Afficher le résultat
display(category_revenue)
```

La catégorie qui rapporte le plus à l'entreprise est **Clothing**, ce qui est cohérent avec le fait que ça la catégorie la plus vendue.

## Ventes par genre


```python
df['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')
plt.title('Distribution par genre')
plt.tight_layout()
```

Ce sont majoritairement des **hommes** qui achètent sur ce site.


```python
clothing = df[df['Category'] == 'Clothing']
clothing['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')
plt.title('Distribution par genre')
plt.tight_layout()
```


```python
accessories = df[df['Category'] == 'Accessories']
accessories['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')
plt.title('Distribution par genre')
plt.tight_layout()
```


```python
footwear = df[df['Category'] == 'Footwear']
footwear['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')
plt.title('Distribution par genre')
plt.tight_layout()
```

On remarque que la grande majorité des acheteurs sont des **hommes**


```python
outerwear = df[df['Category'] == 'Outerwear']
outerwear['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')
plt.title('Distribution par genre')
plt.tight_layout()
```

La répartition des genres par catégories restent approximativement la même pour tous les types d'item

## Age des acheteurs


```python
df["Age"].describe()
```


```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

sns.histplot(data=df, x="Age", kde=True, ax=axes[0])
sns.boxplot(data=df, x="Age")
```


```python
# Calculer les statistiques de base pour la colonne 'Age'
age_stats = df['Age'].describe()

# Afficher les valeurs du minimum, du maximum et de la médiane
print(f"Min: {age_stats['min']}")
print(f"Max: {age_stats['max']}")
print(f"Median: {age_stats['50%']}")
```


```python
df_sorted = df.sort_values(by=["Age", "Gender"])
df['Age Group'] = pd.cut(df['Age'], bins=range(18, 80, 10), labels=[f"{i}-{i+9}" for i in range(18, 70, 10)])

# Compter le nombre de personnes par tranche d'âge et genre
# Groupby avec observed=False pour conserver le comportement actuel
age_gender_count = df.groupby(["Age Group", "Gender"], observed=False).size().reset_index(name="Count")

# Graphique en barres
plt.figure(figsize=(10, 6))
sns.barplot(data=age_gender_count, x="Age Group", y="Count", hue="Gender")
palette = sns.color_palette("bright")[::-1]
plt.title("Nombre d'individus par tranche d'âge et par genre")
plt.xlabel("Tranche d'âge")
plt.ylabel("Nombre d'individus")
plt.legend(title="Genre")
plt.show()
```


```python
# Purchase Amount Distribution
plt.figure(figsize=(12, 5))
sns.set(style="whitegrid")
sns.boxplot(x=df['Purchase Amount (USD)'], color='lightcoral')
plt.title('Boxplot of Purchase Amount', fontsize=16, fontweight='bold')

plt.xlabel('Purchase Amount', fontsize=14)
```


```python
# Calculer les statistiques de base pour la colonne 'Age'
purchase_stats = df['Purchase Amount (USD)'].describe()

# Afficher les valeurs du minimum, du maximum et de la médiane
print(f"Min: {purchase_stats['min']}")
print(f"Max: {purchase_stats['max']}")
print(f"Median: {purchase_stats['50%']}")
```


```python
# Créer des catégories d'âge
bins = [18, 20, 30, 40, 50, 60, 70, 100]  # Définir les intervalles d'âge
labels = ['18-20', '20-30', '31-40', '41-50', '51-60', '61-70', '71+']  # Noms des catégories

# Ajouter une colonne avec les catégories d'âge
df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels, right=False)

# Tracer un boxplot des dépenses en fonction des groupes d'âge
plt.figure(figsize=(12, 5))
sns.set(style="whitegrid")
sns.boxplot(x=df['Age Group'], y=df['Purchase Amount (USD)'], color='lightcoral')

# Ajouter les titres et labels
plt.title('Boxplot of Purchase Amount by Age Group', fontsize=16, fontweight='bold')
plt.xlabel('Age Group', fontsize=14)
plt.ylabel('Purchase Amount (USD)', fontsize=14)

# Affichage final
plt.tight_layout()
plt.show()
```

On remarque l'âge a peu d'influence sur le montant des achats.


```python
size_counts = df['Size'].value_counts().reset_index()
size_counts.columns = ['Size', 'Count']
# Graphique en barres
plt.figure(figsize=(10, 6))
sns.barplot(data=size_counts, x="Size", y="Count", hue="Size")
palette = sns.color_palette("bright")[::-1]
plt.title("Taille les plus vendues")
plt.xlabel("Taille")
plt.ylabel("Nombre de produits vendus")
plt.show()
```

La taille **M** est de loin la plus vendue.


```python

```


```python

```

## Couleurs les plus vendues


```python
df["Color"].value_counts(normalize=True).reset_index()
```


```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

# Graphique de barres
sns.countplot(data=df, x="Color", ax=axes[0])
axes[0].set_xticks(range(len(df["Color"].unique())))  # Définir les ticks
axes[0].set_xticklabels(axes[0].get_xticklabels(), rotation=90)

# Diagramme circulaire
color_counts = df["Color"].value_counts()  # Assurez-vous que color_counts est défini
axes[1].pie(color_counts, labels=color_counts.index, startangle=90)

# Affichage des graphiques
plt.tight_layout()
plt.show()
```

On constate qu'il y a une répartition plutôt égale des couleurs des items vendus

## Ventes par saison


```python
season_counts = df["Season"].value_counts()
season_counts
```


```python
season = df["Season"].value_counts(normalize=1).reset_index()
# Count popular payment methods
season.columns = ['Season', 'Count']

# Créer un diagramme circulaire
fig = px.pie(
    season, 
    names='Season', 
    values='Count', 
    title='Répartition des achats en en fontion des sainsons'
)
# Ajuster la taille et le titre
fig.update_layout(
    title={'text': 'Répartition des achats en en fontion des sainsons', 'x': 0.5, 'font': {'size': 18}},  # Taille du titre
    width=800,  # Largeur du graphique
    height=600  # Hauteur du graphique
)
# Afficher le graphique
fig.show()
```

On remarque la saison n'influe pas sur les ventes du site.

## Notations


```python
df["Review Rating"].describe()
```


```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

sns.histplot(data=df, x="Review Rating", ax=axes[0], kde=True)
sns.boxplot(data=df, x="Review Rating", ax=axes[1])
```

La moyenne des avis est 3,75, avec un écart type de 0,72, indiquant une faible dispersion. Cela signifie que la plupart des avis sont concentrés entre 3,03 et 4,47. Les quartiles montrent que 50% des avis sont inférieurs à 3,7, mais que 75% sont inférieurs ou égaux à 4,4, ce qui reflète une appréciation généralement positive, avec peu de variations extrêmes.

## Acheteurs ayant un compte


```python
# Compter les utilisateurs selon leur statut d'abonnement
subscription = df["Subscription Status"].value_counts().reset_index()
subscription.columns = ['Subscription Status', 'Count']

# Créer un diagramme circulaire
fig = px.pie(
    subscription, 
    names='Subscription Status', 
    values='Count', 
    title='Répartition des acheteurs ayant un compte',
    hole=0.4  # Optionnel : pour un graphique en anneau
)

# Ajuster la mise en page et ajouter des pourcentages
fig.update_traces(
    textinfo='percent+label',  # Affiche le pourcentage et l'étiquette
    hoverinfo='label+value+percent'  # Infos au survol : étiquette, valeur, pourcentage
)

# Ajuster le titre et la taille
fig.update_layout(
    title={'text': 'Répartition des acheteurs ayant un compte', 'x': 0.5, 'font': {'size': 18}},
    width=800,
    height=600
)

# Afficher le graphique
fig.show()

```

La grande majorité des acheteurs n'ont pas de compte sur le site

## Acheteurs qui profitent des réductions proposées


```python
df.columns
```


```python
# Compter les utilisateurs selon leur statut d'abonnement
promo_code = df["Promo Code Used"].value_counts().reset_index()
promo_code.columns = ['Promo Code Used', 'Count']

# Créer un diagramme circulaire
fig = px.pie(
    promo_code, 
    names='Promo Code Used', 
    values='Count', 
    title='Répartition des acheteurs utilisant un code promo',
    hole=0.4  # Optionnel : pour un graphique en anneau
)

# Ajuster la mise en page et ajouter des pourcentages
fig.update_traces(
    textinfo='percent+label',  # Affiche le pourcentage et l'étiquette
    hoverinfo='label+value+percent'  # Infos au survol : étiquette, valeur, pourcentage
)

# Ajuster le titre et la taille
fig.update_layout(
    title={'text': 'Répartition des acheteurs utilisant un code promo', 'x': 0.5, 'font': {'size': 18}},
    width=800,
    height=600
)

# Afficher le graphique
fig.show()
```

Une petite majorité des utilisateurs utilisent les Codes Promos proposés par le site

## Réductions appliquées


```python
# Compter les utilisateurs selon leur statut d'abonnement
discount = df["Discount Applied"].value_counts().reset_index()
discount.columns = ['Discount Applied', 'Count']

# Créer un diagramme circulaire
fig = px.pie(
    discount, 
    names='Discount Applied', 
    values='Count', 
    title='Répartition des acheteurs ayant une remise',
)

# Ajuster la mise en page et ajouter des pourcentages
fig.update_traces(
    textinfo='percent+label',  # Affiche le pourcentage et l'étiquette
    hoverinfo='label+value+percent'  # Infos au survol : étiquette, valeur, pourcentage
)

# Ajuster le titre et la taille
fig.update_layout(
    title={'text': 'Répartition des acheteurs ayant une remise', 'x': 0.5, 'font': {'size': 18}},
    width=800,
    height=600
)

# Afficher le graphique
fig.show()
```

La majorité des acheteurs n'ont pas de remise

## Visualisation du nombre d'achats déjà réalisés


```python
df["Previous Purchases"].describe()
```


```python
fig, axes = plt.subplots(1, 2, figsize=(12, 4))

sns.histplot(data=df, x="Previous Purchases", ax=axes[0], kde=True)
sns.boxplot(data=df, x="Previous Purchases", ax=axes[1])
```

L'écart type des achats précédents est 14,45, ce qui indique une variabilité relativement importante autour de la moyenne 25,35. La plupart des données (environ 68%, si la distribution est normale) se situent dans l'intervalle approximatif [10,9 ; 39,8], tandis que les quartiles montrent que :

- 25% des achats sont inférieurs à 13.
- 50% sont inférieurs à 25.
- 75% sont inférieurs à 38.
Cela signifie que les achats précédents sont concentrés entre 13 et 38, mais avec une dispersion notable autour de la moyenne.


```python
df["Frequency of Purchases"].describe()
```


```python
fig, axes = plt.subplots(1, 2, figsize=(15, 5))

sns.histplot(data=df, x="Frequency of Purchases", ax=axes[0], kde=True)
sns.boxplot(data=df, x="Frequency of Purchases", ax=axes[1])
```

## Méthodes de paiement


```python
# Count popular payment methods
payment_counts = df['Payment Method'].value_counts().reset_index()
payment_counts.columns = ['Payment Method', 'Count']

# Créer un diagramme circulaire
fig = px.pie(
    payment_counts, 
    names='Payment Method', 
    values='Count', 
    title='Répartition des méthodes de paiement populaires'
)
# Ajuster la taille et le titre
fig.update_layout(
    title={'text': "Répartition des méthodes de paiement", 'x': 0.5, 'font': {'size': 18}},  # Taille du titre
    width=800,  # Largeur du graphique
    height=600  # Hauteur du graphique
)
# Afficher le graphique
fig.show()
```

On peut remarquer qu'il n'y a pas de différence notable au niveau de la proportion des **méthodes de paiement**


```python
# Créer des catégories d'âge
bins = [18, 20, 30, 40, 50, 60, 70, 100]  # Définir les intervalles d'âge
labels = ['18-20', '20-30', '31-40', '41-50', '51-60', '61-70', '71+']  # Noms des catégories

# Ajouter une colonne avec les catégories d'âge
df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels, right=False)

# Tracer un boxplot des dépenses en fonction des groupes d'âge
plt.figure(figsize=(12, 5))
sns.set(style="whitegrid")
sns.boxplot(x=df['Age Group'], y=df['Payment Method'], color='lightcoral')

# Ajouter les titres et labels
plt.title('Boxplot of Payment Method by Age Group', fontsize=16, fontweight='bold')
plt.xlabel('Age Group', fontsize=14)
plt.ylabel('Purchase Amount (USD)', fontsize=14)

# Affichage final
plt.tight_layout()
plt.show()
```

On constate que chez les 20-30 ans et les 41-50 ans le mode de paiement le plus utilisé est **Paypal**, et que c'est le **Cash** pour le reste des transhces d'âge.

## Mode de livraison


```python
# Count popular payment methods
shipping = df['Shipping Type'].value_counts().reset_index()
shipping.columns = ['Shipping Type', 'Count']

# Créer un diagramme circulaire
fig = px.pie(
    shipping, 
    names='Shipping Type', 
    values='Count', 
    title='Répartition des modes d`envoie populaires'
)
# Ajuster la taille et le titre
fig.update_layout(
    title={'text': "Répartition des modes d`envoie populaires", 'x': 0.5, 'font': {'size': 18}},  # Taille du titre
    width=800,  # Largeur du graphique
    height=600  # Hauteur du graphique
)
# Afficher le graphique
fig.show()
```


```python
# Créer des catégories d'âge
bins = [18, 20, 30, 40, 50, 60, 70, 100]  # Définir les intervalles d'âge
labels = ['18-20', '20-30', '31-40', '41-50', '51-60', '61-70', '71+']  # Noms des catégories

# Ajouter une colonne avec les catégories d'âge
df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels, right=False)

# Tracer un boxplot des dépenses en fonction des groupes d'âge
plt.figure(figsize=(12, 5))
sns.set(style="whitegrid")
sns.boxplot(x=df['Age Group'], y=df['Shipping Type'], color='lightcoral')

# Ajouter les titres et labels
plt.title("Boxplot des méthodes d`envoi par groupe d'age", fontsize=16, fontweight='bold')
plt.xlabel('Age Group', fontsize=14)
plt.ylabel('Purchase Amount (USD)', fontsize=14)

# Affichage final
plt.tight_layout()
plt.show()
```

On remarque que les **20-30 ans** et les personnes de plus de **71 ans** ont une préférence pour la **livraison standard**, tandis que les autres groupes d'âge préfère la **livraion à J+1**.
