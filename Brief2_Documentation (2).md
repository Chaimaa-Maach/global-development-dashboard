
# 🌍 Power BI – Global Development Dashboard  
Analyse des indicateurs mondiaux : PIB, CO₂, Population (2015–2022)

---

## Presentation d'équipe 
1_Leila Mourid

2_Chaimaa Maach

3_Ayoub El harem

4_Hiba Azizi

## Repartition des taches:
<img width="1000" height="300" alt="image" src="https://github.com/user-attachments/assets/34552e4d-33ef-4720-b8f6-8b5ab07b4116" />


---




## 🧩 Sources de données

### **API 1 – REST Countries**
➡ Informations géographiques, linguistiques, politiques  
**URL :** https://restcountries.com/v3.1/all

### **API 2 – World Bank Indicators**
➡ PIB, émissions CO₂, population (2015–2022)  
Exemple :  
https://api.worldbank.org/v2/country/all/indicator/NY.GDP.MKTP.CD?date=2015:2022&format=json

---

## ✔ Étapes dans Power BI (Power Query)

### **1. Importer l’API REST Countries**
- Power BI → *Home* → **Get Data → Web**  
- Coller l’URL REST Countries  
- Sélectionner *JSON* → Convertir en table  
- Dérouler : `name`, `cca3` (ISO3), `region`, `subregion`, `languages`, `area`, `independent`  
- Vérifier les types

### **2. Importer les indicateurs World Bank**
Importer séparément :

| Indicateur | Code |
|------------|------|
| PIB | `NY.GDP.MKTP.CD` |
| Population | `SP.POP.TOTL` |
| CO₂ | `EN.ATM.CO2E.KT` |

Étapes :
- *Transform Data* → Get Data → Web  
- Convertir JSON → Table  
- Filtrer années **2015–2022**  
- Conserver : ISO3, Year, Value, Indicator ID

---

## ✔ Champs identifiés

| Table | Champ | Type | Description |
|-------|--------|------|-------------|
| REST Countries | ISO3 | Texte | Code pays ISO3 |
| REST Countries | Name | Texte | Nom du pays |
| REST Countries | Region | Texte | Continent |
| REST Countries | Subregion | Texte | Sous-région |
| REST Countries | Languages | Texte | Langue(s) |
| REST Countries | Area | Nombre | Superficie (km²) |
| REST Countries | Independence | Booléen | Statut d’indépendance |
| World Bank | ISO3Code | Texte | Identifiant pays |
| World Bank | Year | Nombre | Année |
| World Bank | Value | Nombre | Valeur indicateur |

### ✔ Observations initiales
- Données manquantes sur certaines années  
- Valeurs nulles fréquentes pour CO₂  
- Clé de fusion : **ISO3Code + Year**  
- Normalisation nécessaire : ISO3 (texte), Year (entier), Value (décimal)

---

## 🧼 Étape 2 – Transformation des données (Power Query)

### ✔ Tableau des transformations

| Étape / Colonne | Problème | Correction | Raison |
|------------------|----------|-----------|--------|
| Types de colonnes | Typage incohérent | ISO3→texte, Year→nombre, Value→décimal | Cohérence |
| PIB | Valeurs nulles | Remplacement par moyenne | Continuité |
| CO₂ | Nulls par région | Remplacement par médiane continentale | Comparabilité |
| Doublons | ISO3+Year en double | Suppression | Unicité |
| Pivot | Format long | Pivot Indicator → colonnes PIB/CO₂/Population | KPI simplifiés |
| Fusion | Info pays dispersée | Merge REST Countries | Enrichissement |


---

## 🧱 Étape 3 – Modélisation (Schéma en étoile)

### ✔ Tables

| Table | Type | Clé | Relations |
|-------|------|------|-----------|
| FactIndicateurs | Fait | ISO3 + Year | Vers DimPays & DimDate |
| DimPays | Dimension | ISO3 | Vers Fact / DimRégion |
| DimDate | Dimension | Year | Vers Fact |
| DimRégion | Dimension | Région | Vers DimPays |

Relations :  
- **DimPays (1) → FactIndicateurs (*)**  
- **DimDate (1) → FactIndicateurs (*)**  
- **DimRégion (1) → DimPays (*)**

---

## 📐 Étape 4 – Création des mesures DAX

### **Économie**
- PIB total  
- Croissance du PIB (%)  
- PIB par habitant  
- Part du PIB mondial / régional  
- Évolution du PIB depuis 2015 (%)  

### **Population**
- Population totale  
- Croissance démographique (%)  
- Densité de population  

### **Environnement**
- CO₂ total  
- CO₂ par habitant  
- Intensité carbone (CO₂/PIB)  
- Évolution CO₂ depuis 2015 (%)  

### **Développement durable**
- Ratio PIB/CO₂  
- Productivité (PIB / population / surface)  

### **Comparatifs**
- Classement PIB  
- Classement intensité carbone  

---

## 📊 Étape 5 – Construction du tableau de bord Power BI

### ✔ Pages du rapport

- **Monde**  : Vue globale

  
<img width="1367" height="735" alt="vue mondiale" src="https://github.com/user-attachments/assets/b031401e-e2e4-4b84-814b-0d05226eb979" />


- **Région**  : Comparaison continent / sous-région

  
<img width="1348" height="732" alt="vue regionale" src="https://github.com/user-attachments/assets/ea54a35a-9d2f-4c0e-b368-024d4e3085f1" />

- **Pays** : Fiche pays

  
<img width="1362" height="731" alt="vue pays" src="https://github.com/user-attachments/assets/0b793b88-ee39-4981-aa2b-a312b1514df9" />

- **Corrélation** : Analyse PIB–CO₂–Population

  
<img width="1335" height="731" alt="correlation" src="https://github.com/user-attachments/assets/4b2081da-75ed-4dc2-9ae0-67182515ee20" />


