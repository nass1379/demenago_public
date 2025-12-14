# Demenago Vision

## Overview
**Demenago Vision** is a prototype developed by **Manip AI** that automatically estimates the **volume of a move** from **photos** and recommends the **most suitable truck**.

---

## Features
- Photo upload of rooms and objects
- Automatic total volume estimation (m³ / liters)
- Recommended truck selection
- Available fleet comparison
- Demo mode with sample images (no upload required)

---

## Project Architecture

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

## Demo Mode
The **Launch demo (sample images)** button preloads example images, allowing the product to be demonstrated without manual uploads.

---

## Volume Estimation (Principle)
Volume estimation relies on a **simplified geometric approach**, combining computer vision with reasonable physical assumptions.

### Main steps
1. **Object detection**  
   A detection model (YOLO) identifies objects in each photo (bed, sofa, table, refrigerator, etc.) and produces bounding boxes with confidence scores.

2. **Camera intrinsics**  
   Camera parameters are estimated from EXIF metadata (focal length, device model) when available. If EXIF data is missing, a **default field of view** is used.

3. **Floor and horizon assumption**  
   The system attempts to detect the horizon line. For objects assumed to be placed on the floor (bed, sofa, fridge, etc.), the camera-to-object distance is estimated using the camera height and the lower edge of the bounding box.

4. **Pixel-to-meter conversion**  
   Object width and height in pixels are converted into real-world dimensions (meters) using the camera model.

5. **Class-based priors**  
   To stabilize the results, **reference dimensions (priors)** are defined per object category. Some classes (e.g., wall-mounted TVs) rely entirely on these priors.

6. **Constraints and bounds**  
   Estimated dimensions are clamped within realistic, class-specific limits to avoid outliers.

7. **Volume computation**  
   The volume is computed for each object:  
   `volume = width × height × depth`  
   then converted to liters. Volumes are **aggregated per image** and **globally** across all photos.

### Output
The system produces:
- an estimated total volume (m³ / liters)
- a breakdown by object type
- a reliable basis for recommending the appropriate truck

⚠️ Results are **indicative** and intended for demonstration purposes.

---

## Deployment
- Frontend: Streamlit Cloud
- Backend: Railway

---

## Local Run

```bash
pip install -r requirements.txt
streamlit run frontend/manipai_demenago_ui/demenago_computer_vision/app.py
```

---

## Team
**Manip AI**

Contact: contact@manip.ai  
LinkedIn: https://www.linkedin.com/company/manip-ai

---

## Possible Improvements

The prototype can be enhanced along several dimensions:

- **Richer object classes**  
  Train or fine-tune the model on a moving/furniture-specific dataset to detect currently under-represented objects, such as *small wardrobes*, *dressers*, *bookshelves*, or *nightstands*.

- **Multi-view and 3D fusion**  
  Combine multiple views of the same object to reduce uncertainty compared to single-image estimation.

- **Dedicated depth estimation**  
  Integrate a monocular depth model (e.g., MiDaS) to improve camera–object distance estimation, complementing the current geometric assumptions.

- **Context-aware personalization**  
  Automatically adapt assumptions (camera height, dimensional constraints) based on housing type or geographic region.

- **Learning from real-world feedback**  
  Gradually refine priors and truck recommendations using actual volumes observed after real moves.

---

*Document intended for demonstration purposes.*