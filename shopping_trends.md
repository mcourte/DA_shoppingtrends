{
 "cells": [
  {
   "cell_type": "markdown",
   "id": "7cac215a-df25-4bf3-9103-af8e14745b2e",
   "metadata": {},
   "source": [
    "# ANALYSE DES VENTES D'UN SITE DE E-COMMERCE"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "2e4b46ab-ffef-48e7-a04d-c2126dc70e07",
   "metadata": {},
   "source": [
    "## Import et installation "
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "97571666-def9-4b49-bd47-92835b23bbda",
   "metadata": {},
   "outputs": [],
   "source": [
    "import pandas as pd\n",
    "import numpy as np\n",
    "import re\n",
    "import matplotlib.pyplot as plt\n",
    "import seaborn as sns\n",
    "import skimpy \n",
    "from skimpy import skim\n",
    "import plotly.express as px"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "8f26b889-138f-4115-be94-8aee2cb694b3",
   "metadata": {},
   "source": [
    "## Création du DataFrame de base"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "088e7b9a-f7ab-4049-8b52-5f84dea8239e",
   "metadata": {},
   "outputs": [],
   "source": [
    "data = pd.read_csv(\"shopping_trends.csv\", sep=\",\", low_memory=False)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "3563ad3e-f02f-4712-96c4-4d16e7c31602",
   "metadata": {},
   "outputs": [],
   "source": [
    "df = data.copy()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "cfe11cca-b179-4bda-a152-37dc3de864c5",
   "metadata": {},
   "outputs": [],
   "source": [
    "df.shape"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e1b7a58b-e151-4de0-bffa-89de061d8448",
   "metadata": {},
   "outputs": [],
   "source": [
    "df.info()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "c1603c24-9581-4819-88db-5cb72c9e9d01",
   "metadata": {},
   "outputs": [],
   "source": [
    "df.describe().T"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "fda27c20-01fe-49ad-877d-b9667dc67d6a",
   "metadata": {},
   "outputs": [],
   "source": [
    "df.describe(include=\"object\").T"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "b8a875db-8b2f-4124-b8c6-6e0a1ff77b2b",
   "metadata": {},
   "outputs": [],
   "source": [
    "skim(df)"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "753afa02-3008-4b22-8f42-36f33974d780",
   "metadata": {},
   "source": [
    "### Taux de remplissage des colonnes"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "fb322241-f4a9-45c3-b355-46e3b0863572",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Calculer le taux de remplissage pour chaque colonne\n",
    "column_fill_rates = df.notna().mean() * 100\n",
    "\n",
    "# Afficher le taux de remplissage pour chaque colonne\n",
    "column_fill_rates"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "5ae3ccbf-969c-4965-86f1-fdb8e57cf9bd",
   "metadata": {},
   "outputs": [],
   "source": [
    "df.isnull().sum()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "db18576c-5f80-4488-a968-3ffb94f425eb",
   "metadata": {},
   "source": [
    "L'ensemble des colonnes étant remplies et ne comportant aucune valeur nulle, nous pourrons faire une analyse complète des données."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "55ae9c13-7ad7-4ba5-8bd0-43edbe237c50",
   "metadata": {},
   "source": [
    "## Produits les plus vendus"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "fb17afc5-55df-4974-9cac-f0a92cb8b560",
   "metadata": {},
   "outputs": [],
   "source": [
    "item_purchased=df['Item Purchased'].value_counts().reset_index()\n",
    "item_purchased"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9a5c76cd-fac7-44e9-a639-0dce5b85e462",
   "metadata": {},
   "outputs": [],
   "source": [
    "plt.figure(figsize=(20, 8))\n",
    "df['Item Purchased'].value_counts().plot(kind='pie',autopct='%1.1f%%')\n",
    "plt.title('Distribution de Item purchased')\n",
    "plt.ylabel(\"\")\n",
    "plt.tight_layout()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "44da7f8c-15ac-46b1-81ea-59414a62f88e",
   "metadata": {},
   "source": [
    "On remarque une répartition plutôt égale de la vente des produits. Le produit le plus vendu a été 171 fois, & le moins vendu 124 fois."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "25e29fac-c038-401a-844a-996f9c3f7f4d",
   "metadata": {},
   "outputs": [],
   "source": [
    "item_revenu = df.groupby('Item Purchased')['Purchase Amount (USD)'].sum()\n",
    "item_revenu.sort_values(ascending=False)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "08dc7b36-9197-4d5f-bdf3-2aa2494b77f7",
   "metadata": {},
   "outputs": [],
   "source": [
    "columns_to_display = ['Item Purchased','Purchase Amount (USD)']\n",
    "df[columns_to_display]"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "fe1f60c7-199f-4a77-a2c5-4362429af633",
   "metadata": {},
   "source": [
    "On constate que le produit qui rapport le plus d'argent est la **blouse** ce qui est cohérent avec le fait que ça soit l'item le plus vendu et le plus cher."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "9cb92f7a-0321-4a8f-862f-27615db827d2",
   "metadata": {},
   "source": [
    "## Les Catégories"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "4358478a-6c2f-439b-a07f-672d464e82f1",
   "metadata": {},
   "outputs": [],
   "source": [
    "df['Category'].value_counts()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "cf1d1255-f730-455a-8667-667f24731632",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Calculer la distribution des catégories\n",
    "category_counts = df['Category'].value_counts()\n",
    "\n",
    "# Tracer un pie chart\n",
    "category_counts.plot(kind='pie', autopct='%1.1f%%', figsize=(8, 8), colors=sns.color_palette('Set3', len(category_counts)))\n",
    "\n",
    "# Ajouter un titre\n",
    "plt.title('Répartition des catégories de produits')\n",
    "\n",
    "# Afficher le graphique\n",
    "plt.tight_layout()\n",
    "plt.show()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "c61a3ef4-7ed6-4161-a9a2-e58b9bb73f9d",
   "metadata": {},
   "source": [
    "La catégories présente en plus grande proprotion est **Clothing** ( Vêtements) suivi d'**Acessories** ( Acessoires)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "b718ace1-7c47-4b82-89a3-a79e3e23e65d",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Regrouper les données par 'Category' et calculer la somme des 'Purchase Amount (USD)'\n",
    "category_revenue = df.groupby('Category')['Purchase Amount (USD)'].sum().reset_index()\n",
    "\n",
    "# Trier les résultats par montant décroissant\n",
    "category_revenue = category_revenue.sort_values(by='Purchase Amount (USD)', ascending=False)\n",
    "\n",
    "# Afficher le résultat\n",
    "display(category_revenue)"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "1b6fa650-f119-4324-a777-698438321ee5",
   "metadata": {},
   "source": [
    "La catégorie qui rapporte le plus à l'entreprise est **Clothing**, ce qui est cohérent avec le fait que ça la catégorie la plus vendue."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "927ce8b7-76f0-424d-86d3-f01c15f66996",
   "metadata": {},
   "source": [
    "## Ventes par genre"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "61679946-3480-4168-85e7-564a7ab5fe18",
   "metadata": {},
   "outputs": [],
   "source": [
    "df['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')\n",
    "plt.title('Distribution par genre')\n",
    "plt.tight_layout()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "3350fd02-4f34-4b91-bb86-5f07de0b151d",
   "metadata": {},
   "source": [
    "Ce sont majoritairement des **hommes** qui achètent sur ce site."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e311d30a-2ce8-42b1-a0d8-21874e2ee07b",
   "metadata": {},
   "outputs": [],
   "source": [
    "clothing = df[df['Category'] == 'Clothing']\n",
    "clothing['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')\n",
    "plt.title('Distribution par genre')\n",
    "plt.tight_layout()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e8307d16-8029-4635-95a0-c31ce7a1f3b2",
   "metadata": {},
   "outputs": [],
   "source": [
    "accessories = df[df['Category'] == 'Accessories']\n",
    "accessories['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')\n",
    "plt.title('Distribution par genre')\n",
    "plt.tight_layout()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9bebb02d-0194-4f0d-a73a-9a4503ffc635",
   "metadata": {},
   "outputs": [],
   "source": [
    "footwear = df[df['Category'] == 'Footwear']\n",
    "footwear['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')\n",
    "plt.title('Distribution par genre')\n",
    "plt.tight_layout()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "776a0dc8-3506-49b8-b437-1d5f5fb6f800",
   "metadata": {},
   "source": [
    "On remarque que la grande majorité des acheteurs sont des **hommes**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "87da96a2-0d50-4c9b-a3fc-8efeb934781e",
   "metadata": {},
   "outputs": [],
   "source": [
    "outerwear = df[df['Category'] == 'Outerwear']\n",
    "outerwear['Gender'].value_counts().plot(kind='pie',autopct='%1.1f%%')\n",
    "plt.title('Distribution par genre')\n",
    "plt.tight_layout()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "c55eb207-ee2a-4d61-ad42-2f8b8af3d095",
   "metadata": {},
   "source": [
    "La répartition des genres par catégories restent approximativement la même pour tous les types d'item"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "7dd5cd5b-e62a-4a6a-addc-3a1dd14392e1",
   "metadata": {},
   "source": [
    "## Age des acheteurs"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "444654da-6ce9-4083-a5c9-38bf89ad8d15",
   "metadata": {},
   "outputs": [],
   "source": [
    "df[\"Age\"].describe()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e1e84d6e-eaf2-4aa8-bcab-b21e1ee0092f",
   "metadata": {},
   "outputs": [],
   "source": [
    "fig, axes = plt.subplots(1, 2, figsize=(12, 4))\n",
    "\n",
    "sns.histplot(data=df, x=\"Age\", kde=True, ax=axes[0])\n",
    "sns.boxplot(data=df, x=\"Age\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "fd6d5d59-7f08-40a5-af36-92563bee9b67",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Calculer les statistiques de base pour la colonne 'Age'\n",
    "age_stats = df['Age'].describe()\n",
    "\n",
    "# Afficher les valeurs du minimum, du maximum et de la médiane\n",
    "print(f\"Min: {age_stats['min']}\")\n",
    "print(f\"Max: {age_stats['max']}\")\n",
    "print(f\"Median: {age_stats['50%']}\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "b7df280c-8b39-4f56-bf44-8837728582fd",
   "metadata": {},
   "outputs": [],
   "source": [
    "df_sorted = df.sort_values(by=[\"Age\", \"Gender\"])\n",
    "df['Age Group'] = pd.cut(df['Age'], bins=range(18, 80, 10), labels=[f\"{i}-{i+9}\" for i in range(18, 70, 10)])\n",
    "\n",
    "# Compter le nombre de personnes par tranche d'âge et genre\n",
    "# Groupby avec observed=False pour conserver le comportement actuel\n",
    "age_gender_count = df.groupby([\"Age Group\", \"Gender\"], observed=False).size().reset_index(name=\"Count\")\n",
    "\n",
    "# Graphique en barres\n",
    "plt.figure(figsize=(10, 6))\n",
    "sns.barplot(data=age_gender_count, x=\"Age Group\", y=\"Count\", hue=\"Gender\")\n",
    "palette = sns.color_palette(\"bright\")[::-1]\n",
    "plt.title(\"Nombre d'individus par tranche d'âge et par genre\")\n",
    "plt.xlabel(\"Tranche d'âge\")\n",
    "plt.ylabel(\"Nombre d'individus\")\n",
    "plt.legend(title=\"Genre\")\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "d01e48a4-cd67-49ad-b8e6-01238971de13",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Purchase Amount Distribution\n",
    "plt.figure(figsize=(12, 5))\n",
    "sns.set(style=\"whitegrid\")\n",
    "sns.boxplot(x=df['Purchase Amount (USD)'], color='lightcoral')\n",
    "plt.title('Boxplot of Purchase Amount', fontsize=16, fontweight='bold')\n",
    "\n",
    "plt.xlabel('Purchase Amount', fontsize=14)"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "5bf13033-5edc-44d9-b3e8-3e6eb8fa9886",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Calculer les statistiques de base pour la colonne 'Age'\n",
    "purchase_stats = df['Purchase Amount (USD)'].describe()\n",
    "\n",
    "# Afficher les valeurs du minimum, du maximum et de la médiane\n",
    "print(f\"Min: {purchase_stats['min']}\")\n",
    "print(f\"Max: {purchase_stats['max']}\")\n",
    "print(f\"Median: {purchase_stats['50%']}\")"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a7502797-8989-4def-9c7a-92fd2e5de3cf",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Créer des catégories d'âge\n",
    "bins = [18, 20, 30, 40, 50, 60, 70, 100]  # Définir les intervalles d'âge\n",
    "labels = ['18-20', '20-30', '31-40', '41-50', '51-60', '61-70', '71+']  # Noms des catégories\n",
    "\n",
    "# Ajouter une colonne avec les catégories d'âge\n",
    "df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels, right=False)\n",
    "\n",
    "# Tracer un boxplot des dépenses en fonction des groupes d'âge\n",
    "plt.figure(figsize=(12, 5))\n",
    "sns.set(style=\"whitegrid\")\n",
    "sns.boxplot(x=df['Age Group'], y=df['Purchase Amount (USD)'], color='lightcoral')\n",
    "\n",
    "# Ajouter les titres et labels\n",
    "plt.title('Boxplot of Purchase Amount by Age Group', fontsize=16, fontweight='bold')\n",
    "plt.xlabel('Age Group', fontsize=14)\n",
    "plt.ylabel('Purchase Amount (USD)', fontsize=14)\n",
    "\n",
    "# Affichage final\n",
    "plt.tight_layout()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "6a544dcb-9a7a-4bfd-a929-0145124a060b",
   "metadata": {},
   "source": [
    "On remarque l'âge a peu d'influence sur le montant des achats."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9b845fb2-fdd6-4034-bdf1-8bd41884211b",
   "metadata": {},
   "outputs": [],
   "source": [
    "size_counts = df['Size'].value_counts().reset_index()\n",
    "size_counts.columns = ['Size', 'Count']\n",
    "# Graphique en barres\n",
    "plt.figure(figsize=(10, 6))\n",
    "sns.barplot(data=size_counts, x=\"Size\", y=\"Count\", hue=\"Size\")\n",
    "palette = sns.color_palette(\"bright\")[::-1]\n",
    "plt.title(\"Taille les plus vendues\")\n",
    "plt.xlabel(\"Taille\")\n",
    "plt.ylabel(\"Nombre de produits vendus\")\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "0ecf4fa0-550f-4de0-85d9-2e7be77ca2a8",
   "metadata": {},
   "source": [
    "La taille **M** est de loin la plus vendue."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "47b9c0b6-45f8-4157-9178-c071043fa4b5",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "be2df743-ab16-405f-9d60-6b4aeea40b6c",
   "metadata": {},
   "outputs": [],
   "source": []
  },
  {
   "cell_type": "markdown",
   "id": "73450d51-df3d-4106-82da-10fb9e2a78d7",
   "metadata": {},
   "source": [
    "## Couleurs les plus vendues"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9f8cb839-4a66-4eae-9a5b-027767ad22a3",
   "metadata": {},
   "outputs": [],
   "source": [
    "df[\"Color\"].value_counts(normalize=True).reset_index()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "3cec8d21-8540-407d-aedc-35489fb64593",
   "metadata": {},
   "outputs": [],
   "source": [
    "fig, axes = plt.subplots(1, 2, figsize=(12, 4))\n",
    "\n",
    "# Graphique de barres\n",
    "sns.countplot(data=df, x=\"Color\", ax=axes[0])\n",
    "axes[0].set_xticks(range(len(df[\"Color\"].unique())))  # Définir les ticks\n",
    "axes[0].set_xticklabels(axes[0].get_xticklabels(), rotation=90)\n",
    "\n",
    "# Diagramme circulaire\n",
    "color_counts = df[\"Color\"].value_counts()  # Assurez-vous que color_counts est défini\n",
    "axes[1].pie(color_counts, labels=color_counts.index, startangle=90)\n",
    "\n",
    "# Affichage des graphiques\n",
    "plt.tight_layout()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "48cb322d-d462-4a53-964d-4c0f53c0aaab",
   "metadata": {},
   "source": [
    "On constate qu'il y a une répartition plutôt égale des couleurs des items vendus"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "e2f10d79-2751-4592-8986-b0d1e5b0db24",
   "metadata": {},
   "source": [
    "## Ventes par saison"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "101f0372-fcad-45c2-bb28-5aceab7fe54b",
   "metadata": {},
   "outputs": [],
   "source": [
    "season_counts = df[\"Season\"].value_counts()\n",
    "season_counts"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "467d310a-311c-409a-9a1d-7ef621c9edda",
   "metadata": {},
   "outputs": [],
   "source": [
    "season = df[\"Season\"].value_counts(normalize=1).reset_index()\n",
    "# Count popular payment methods\n",
    "season.columns = ['Season', 'Count']\n",
    "\n",
    "# Créer un diagramme circulaire\n",
    "fig = px.pie(\n",
    "    season, \n",
    "    names='Season', \n",
    "    values='Count', \n",
    "    title='Répartition des achats en en fontion des sainsons'\n",
    ")\n",
    "# Ajuster la taille et le titre\n",
    "fig.update_layout(\n",
    "    title={'text': 'Répartition des achats en en fontion des sainsons', 'x': 0.5, 'font': {'size': 18}},  # Taille du titre\n",
    "    width=800,  # Largeur du graphique\n",
    "    height=600  # Hauteur du graphique\n",
    ")\n",
    "# Afficher le graphique\n",
    "fig.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "17e98954-a076-4599-bfb9-325656cee16e",
   "metadata": {},
   "source": [
    "On remarque la saison n'influe pas sur les ventes du site."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "628f025e-4e18-4371-97a9-aa02707a4b6b",
   "metadata": {},
   "source": [
    "## Notations"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "95af717e-f504-4112-8488-b92f59046532",
   "metadata": {},
   "outputs": [],
   "source": [
    "df[\"Review Rating\"].describe()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9ce5d8fa-34ea-4375-a90e-965b25eb4e79",
   "metadata": {},
   "outputs": [],
   "source": [
    "fig, axes = plt.subplots(1, 2, figsize=(12, 4))\n",
    "\n",
    "sns.histplot(data=df, x=\"Review Rating\", ax=axes[0], kde=True)\n",
    "sns.boxplot(data=df, x=\"Review Rating\", ax=axes[1])"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "e814fe9c-fc2d-42c8-8fcd-9bfbf5c86652",
   "metadata": {},
   "source": [
    "La moyenne des avis est 3,75, avec un écart type de 0,72, indiquant une faible dispersion. Cela signifie que la plupart des avis sont concentrés entre 3,03 et 4,47. Les quartiles montrent que 50% des avis sont inférieurs à 3,7, mais que 75% sont inférieurs ou égaux à 4,4, ce qui reflète une appréciation généralement positive, avec peu de variations extrêmes."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "b6c562c5-673a-479f-8b1d-627957e1dd3b",
   "metadata": {},
   "source": [
    "## Acheteurs ayant un compte"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "f4ef4619-f10c-4827-a3f0-2670960dec9f",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Compter les utilisateurs selon leur statut d'abonnement\n",
    "subscription = df[\"Subscription Status\"].value_counts().reset_index()\n",
    "subscription.columns = ['Subscription Status', 'Count']\n",
    "\n",
    "# Créer un diagramme circulaire\n",
    "fig = px.pie(\n",
    "    subscription, \n",
    "    names='Subscription Status', \n",
    "    values='Count', \n",
    "    title='Répartition des acheteurs ayant un compte',\n",
    "    hole=0.4  # Optionnel : pour un graphique en anneau\n",
    ")\n",
    "\n",
    "# Ajuster la mise en page et ajouter des pourcentages\n",
    "fig.update_traces(\n",
    "    textinfo='percent+label',  # Affiche le pourcentage et l'étiquette\n",
    "    hoverinfo='label+value+percent'  # Infos au survol : étiquette, valeur, pourcentage\n",
    ")\n",
    "\n",
    "# Ajuster le titre et la taille\n",
    "fig.update_layout(\n",
    "    title={'text': 'Répartition des acheteurs ayant un compte', 'x': 0.5, 'font': {'size': 18}},\n",
    "    width=800,\n",
    "    height=600\n",
    ")\n",
    "\n",
    "# Afficher le graphique\n",
    "fig.show()\n"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "1e0f9f3c-cecd-4cbf-8ea4-956e55a054ce",
   "metadata": {},
   "source": [
    "La grande majorité des acheteurs n'ont pas de compte sur le site"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "fd978d82-56ed-4c7d-bb19-27ac1aa08d95",
   "metadata": {},
   "source": [
    "## Acheteurs qui profitent des réductions proposées"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "6c5db9e7-8a1c-42fb-9b11-a16f149ba76c",
   "metadata": {},
   "outputs": [],
   "source": [
    "df.columns"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "cb192dd1-8fd8-4b86-a922-8e2f06b22501",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Compter les utilisateurs selon leur statut d'abonnement\n",
    "promo_code = df[\"Promo Code Used\"].value_counts().reset_index()\n",
    "promo_code.columns = ['Promo Code Used', 'Count']\n",
    "\n",
    "# Créer un diagramme circulaire\n",
    "fig = px.pie(\n",
    "    promo_code, \n",
    "    names='Promo Code Used', \n",
    "    values='Count', \n",
    "    title='Répartition des acheteurs utilisant un code promo',\n",
    "    hole=0.4  # Optionnel : pour un graphique en anneau\n",
    ")\n",
    "\n",
    "# Ajuster la mise en page et ajouter des pourcentages\n",
    "fig.update_traces(\n",
    "    textinfo='percent+label',  # Affiche le pourcentage et l'étiquette\n",
    "    hoverinfo='label+value+percent'  # Infos au survol : étiquette, valeur, pourcentage\n",
    ")\n",
    "\n",
    "# Ajuster le titre et la taille\n",
    "fig.update_layout(\n",
    "    title={'text': 'Répartition des acheteurs utilisant un code promo', 'x': 0.5, 'font': {'size': 18}},\n",
    "    width=800,\n",
    "    height=600\n",
    ")\n",
    "\n",
    "# Afficher le graphique\n",
    "fig.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "757e6f8e-9822-4225-9ec0-e692bd0e1660",
   "metadata": {},
   "source": [
    "Une petite majorité des utilisateurs utilisent les Codes Promos proposés par le site"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "92a9285e-3680-4e38-8959-55cae716f675",
   "metadata": {},
   "source": [
    "## Réductions appliquées"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "6d253b6f-32a8-46e7-8e7f-203142aa8aa6",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Compter les utilisateurs selon leur statut d'abonnement\n",
    "discount = df[\"Discount Applied\"].value_counts().reset_index()\n",
    "discount.columns = ['Discount Applied', 'Count']\n",
    "\n",
    "# Créer un diagramme circulaire\n",
    "fig = px.pie(\n",
    "    discount, \n",
    "    names='Discount Applied', \n",
    "    values='Count', \n",
    "    title='Répartition des acheteurs ayant une remise',\n",
    ")\n",
    "\n",
    "# Ajuster la mise en page et ajouter des pourcentages\n",
    "fig.update_traces(\n",
    "    textinfo='percent+label',  # Affiche le pourcentage et l'étiquette\n",
    "    hoverinfo='label+value+percent'  # Infos au survol : étiquette, valeur, pourcentage\n",
    ")\n",
    "\n",
    "# Ajuster le titre et la taille\n",
    "fig.update_layout(\n",
    "    title={'text': 'Répartition des acheteurs ayant une remise', 'x': 0.5, 'font': {'size': 18}},\n",
    "    width=800,\n",
    "    height=600\n",
    ")\n",
    "\n",
    "# Afficher le graphique\n",
    "fig.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "ea3da583-48a7-463c-9c8c-3f12daac2cc7",
   "metadata": {},
   "source": [
    "La majorité des acheteurs n'ont pas de remise"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "dfbdb1f1-9b1f-4bb7-9550-944f6d176d0c",
   "metadata": {},
   "source": [
    "## Visualisation du nombre d'achats déjà réalisés"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "c7a2d470-0936-4fa4-922b-e23a0f67c25a",
   "metadata": {},
   "outputs": [],
   "source": [
    "df[\"Previous Purchases\"].describe()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "6676d64b-3348-4917-8098-1974756793ac",
   "metadata": {},
   "outputs": [],
   "source": [
    "fig, axes = plt.subplots(1, 2, figsize=(12, 4))\n",
    "\n",
    "sns.histplot(data=df, x=\"Previous Purchases\", ax=axes[0], kde=True)\n",
    "sns.boxplot(data=df, x=\"Previous Purchases\", ax=axes[1])"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "616c90d2-31a2-490f-9a8c-321e2e388a10",
   "metadata": {},
   "source": [
    "L'écart type des achats précédents est 14,45, ce qui indique une variabilité relativement importante autour de la moyenne 25,35. La plupart des données (environ 68%, si la distribution est normale) se situent dans l'intervalle approximatif [10,9 ; 39,8], tandis que les quartiles montrent que :\n",
    "\n",
    "- 25% des achats sont inférieurs à 13.\n",
    "- 50% sont inférieurs à 25.\n",
    "- 75% sont inférieurs à 38.\n",
    "Cela signifie que les achats précédents sont concentrés entre 13 et 38, mais avec une dispersion notable autour de la moyenne."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "9de0cd51-9c66-4427-95b5-a70cc197eccb",
   "metadata": {},
   "outputs": [],
   "source": [
    "df[\"Frequency of Purchases\"].describe()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "a766ac3a-8ab3-43e6-bb30-735bd335f4ea",
   "metadata": {},
   "outputs": [],
   "source": [
    "fig, axes = plt.subplots(1, 2, figsize=(15, 5))\n",
    "\n",
    "sns.histplot(data=df, x=\"Frequency of Purchases\", ax=axes[0], kde=True)\n",
    "sns.boxplot(data=df, x=\"Frequency of Purchases\", ax=axes[1])"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "782cf16b-2ec0-4ea8-a5e9-1ce98bfad950",
   "metadata": {},
   "source": [
    "## Méthodes de paiement"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "e3b050c1-c8f1-46fb-8a48-13294c800889",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Count popular payment methods\n",
    "payment_counts = df['Payment Method'].value_counts().reset_index()\n",
    "payment_counts.columns = ['Payment Method', 'Count']\n",
    "\n",
    "# Créer un diagramme circulaire\n",
    "fig = px.pie(\n",
    "    payment_counts, \n",
    "    names='Payment Method', \n",
    "    values='Count', \n",
    "    title='Répartition des méthodes de paiement populaires'\n",
    ")\n",
    "# Ajuster la taille et le titre\n",
    "fig.update_layout(\n",
    "    title={'text': \"Répartition des méthodes de paiement\", 'x': 0.5, 'font': {'size': 18}},  # Taille du titre\n",
    "    width=800,  # Largeur du graphique\n",
    "    height=600  # Hauteur du graphique\n",
    ")\n",
    "# Afficher le graphique\n",
    "fig.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "0ec8b72e-4b5b-40ec-8e61-f9e6df088e99",
   "metadata": {},
   "source": [
    "On peut remarquer qu'il n'y a pas de différence notable au niveau de la proportion des **méthodes de paiement**"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "0b7b50da-19ba-4990-9cef-a2e34f257517",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Créer des catégories d'âge\n",
    "bins = [18, 20, 30, 40, 50, 60, 70, 100]  # Définir les intervalles d'âge\n",
    "labels = ['18-20', '20-30', '31-40', '41-50', '51-60', '61-70', '71+']  # Noms des catégories\n",
    "\n",
    "# Ajouter une colonne avec les catégories d'âge\n",
    "df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels, right=False)\n",
    "\n",
    "# Tracer un boxplot des dépenses en fonction des groupes d'âge\n",
    "plt.figure(figsize=(12, 5))\n",
    "sns.set(style=\"whitegrid\")\n",
    "sns.boxplot(x=df['Age Group'], y=df['Payment Method'], color='lightcoral')\n",
    "\n",
    "# Ajouter les titres et labels\n",
    "plt.title('Boxplot of Payment Method by Age Group', fontsize=16, fontweight='bold')\n",
    "plt.xlabel('Age Group', fontsize=14)\n",
    "plt.ylabel('Purchase Amount (USD)', fontsize=14)\n",
    "\n",
    "# Affichage final\n",
    "plt.tight_layout()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "ea34ce8b-285e-44d0-88ee-2f992ebc0d3b",
   "metadata": {},
   "source": [
    "On constate que chez les 20-30 ans et les 41-50 ans le mode de paiement le plus utilisé est **Paypal**, et que c'est le **Cash** pour le reste des transhces d'âge."
   ]
  },
  {
   "cell_type": "markdown",
   "id": "1c3a7e18-902c-4b26-95c3-8ac20cb4f2a1",
   "metadata": {},
   "source": [
    "## Mode de livraison"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "0d836cd3-66c8-4769-a087-2483bedf73e2",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Count popular payment methods\n",
    "shipping = df['Shipping Type'].value_counts().reset_index()\n",
    "shipping.columns = ['Shipping Type', 'Count']\n",
    "\n",
    "# Créer un diagramme circulaire\n",
    "fig = px.pie(\n",
    "    shipping, \n",
    "    names='Shipping Type', \n",
    "    values='Count', \n",
    "    title='Répartition des modes d`envoie populaires'\n",
    ")\n",
    "# Ajuster la taille et le titre\n",
    "fig.update_layout(\n",
    "    title={'text': \"Répartition des modes d`envoie populaires\", 'x': 0.5, 'font': {'size': 18}},  # Taille du titre\n",
    "    width=800,  # Largeur du graphique\n",
    "    height=600  # Hauteur du graphique\n",
    ")\n",
    "# Afficher le graphique\n",
    "fig.show()"
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "id": "5a398c77-3339-4407-98c4-4e82c8490ba6",
   "metadata": {},
   "outputs": [],
   "source": [
    "# Créer des catégories d'âge\n",
    "bins = [18, 20, 30, 40, 50, 60, 70, 100]  # Définir les intervalles d'âge\n",
    "labels = ['18-20', '20-30', '31-40', '41-50', '51-60', '61-70', '71+']  # Noms des catégories\n",
    "\n",
    "# Ajouter une colonne avec les catégories d'âge\n",
    "df['Age Group'] = pd.cut(df['Age'], bins=bins, labels=labels, right=False)\n",
    "\n",
    "# Tracer un boxplot des dépenses en fonction des groupes d'âge\n",
    "plt.figure(figsize=(12, 5))\n",
    "sns.set(style=\"whitegrid\")\n",
    "sns.boxplot(x=df['Age Group'], y=df['Shipping Type'], color='lightcoral')\n",
    "\n",
    "# Ajouter les titres et labels\n",
    "plt.title(\"Boxplot des méthodes d`envoi par groupe d'age\", fontsize=16, fontweight='bold')\n",
    "plt.xlabel('Age Group', fontsize=14)\n",
    "plt.ylabel('Purchase Amount (USD)', fontsize=14)\n",
    "\n",
    "# Affichage final\n",
    "plt.tight_layout()\n",
    "plt.show()"
   ]
  },
  {
   "cell_type": "markdown",
   "id": "b99dbb5c-f02e-4320-9f93-556b8618a37d",
   "metadata": {},
   "source": [
    "On remarque que les **20-30 ans** et les personnes de plus de **71 ans** ont une préférence pour la **livraison standard**, tandis que les autres groupes d'âge préfère la **livraion à J+1**."
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3 (ipykernel)",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.12.0"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 5
}
