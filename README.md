# Demenago Vision

## Présentation
**Demenago Vision** est un prototype développé par **Manip AI** permettant d’estimer automatiquement le **volume d’un déménagement** à partir de **photos** et de recommander le **camion le plus adapté**.

---

## Fonctionnalités
- Upload de photos des pièces et objets
- Estimation automatique du volume total (m³ / litres)
- Recommandation du camion le plus adapté
- Comparatif de la flotte disponible
- Mode démo avec images d’exemple (sans upload)

---

## Architecture du projet

```
demenago/
├── frontend/
│   └── manipai_demenago_ui/
│       └── demenago_computer_vision/
│           ├── app.py
│           ├── pages/
│           │   ├── 1_Profil_client.py
│           │   ├── 2_Demande_devis.py
│           │   ├── 3_Contact.py
│           │   └── 4_FAQ.py
│           └── assets/
│               └── logo_manip_ai.svg
├── backend/
├── requirements.txt
└── README.md
```

---

## Mode démo
Le bouton **Lancer une démo (images exemples)** permet de précharger des images d’exemple afin de présenter le produit sans upload manuel.

---

## Estimation du volume (principe)
L’estimation du volume repose sur une **approche géométrique simplifiée**, combinant vision par ordinateur et hypothèses physiques raisonnables.

### Étapes principales
1. **Détection des objets**  
   Un modèle de détection (YOLO) identifie les objets sur chaque photo (lit, canapé, table, réfrigérateur, etc.) et produit des boîtes englobantes (*bounding boxes*) avec un score de confiance.

2. **Intrinsics caméra**  
   Les paramètres de la caméra sont estimés à partir des métadonnées EXIF (focale, modèle) lorsque disponibles. En l’absence d’EXIF, un **champ de vision par défaut** est utilisé.

3. **Hypothèse de sol et horizon**  
   Le système tente de détecter la ligne d’horizon. Pour les objets supposés posés au sol (lit, canapé, frigo…), la distance caméra–objet est estimée à partir de la hauteur de la caméra et de la position basse de la boîte englobante.

4. **Conversion pixels → mètres**  
   La largeur et la hauteur en pixels de chaque objet sont converties en dimensions réelles (mètres) grâce au modèle caméra.

5. **Priors par classe**  
   Pour stabiliser les résultats, des **dimensions de référence** (priors) par type d’objet sont utilisées. Certaines classes (ex. téléviseur mural) reposent uniquement sur ces priors.

6. **Contraintes et bornes**  
   Les dimensions estimées sont limitées par des bornes réalistes propres à chaque classe afin d’éviter les valeurs aberrantes.

7. **Calcul du volume**  
   Le volume est calculé pour chaque objet :  
   `volume = largeur × hauteur × profondeur`  
   puis converti en litres. Les volumes sont ensuite **agrégés par image** et **globalement** sur l’ensemble des photos.

### Résultat
Le système produit :
- un volume total estimé (m³ / litres)
- une répartition par type d’objet
- une base fiable pour recommander le camion adapté

⚠️ Les résultats sont **indicatifs** et destinés à la démonstration.

---

## Déploiement
- Frontend : Streamlit Cloud
- Backend : Railway

---

## Lancement local

```bash
pip install -r requirements.txt
streamlit run frontend/manipai_demenago_ui/demenago_computer_vision/app.py
```

---

## Équipe
**Manip AI**

Contact : contact@manip.ai
LinkedIn : https://www.linkedin.com/company/manip-ai

---

## Améliorations possibles

Le prototype peut être amélioré sur plusieurs axes :

- **Enrichissement des classes détectées**  
  Entraîner ou affiner le modèle avec un dataset spécialisé (mobilier/déménagement) afin de détecter des objets actuellement sous‑représentés, par exemple : *petite armoire*, *commode*, *bibliothèque*, *table de chevet*.

- **Multi‑vues et fusion 3D**  
  Exploiter plusieurs photos d’un même objet pour fusionner les estimations et réduire l’incertitude liée à une vue unique.

- **Estimation de profondeur dédiée**  
  Intégrer un modèle de profondeur monoculaire (ex. MiDaS) pour améliorer l’estimation de la distance caméra–objet, en complément des hypothèses géométriques actuelles.

- **Personnalisation par contexte**  
  Adapter automatiquement certaines hypothèses (hauteur caméra, contraintes de dimensions) selon le type de logement ou le pays.

- **Apprentissage par retour terrain**  
  Corriger progressivement les priors et recommandations de camions à partir des volumes réellement observés après déménagement.

---

*Document destiné à la démonstration.*

