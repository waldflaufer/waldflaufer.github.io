---
title: "Jetclay (Clay 3D-Printing)"
description: "Two ceramic objects printed with JetClay: material, calibration, printing, firing and glazing."
date: 2026-02-15
categories:
  - 3D Printing
  - JetClay
tags:
  - Workshop
image: cover.jpg
license: CC BY 4.0
---

This page documents two self-printed ceramic objects produced using [JetClay](https://jetclayacademy.com/).

I mix the ceramic clay with water to get a more fluid material that can be pumped through the printer. After preparing and testing the print parameters, I start the printer and, once everything works, print the final model.

## 1. Prepare the ceramic material

The ceramic clay is mixed with water to achieve a more fluid consistency. The goal is to obtain a paste-like material that can be pumped through the printer.

The viscosity must be carefully adjusted:

- too thick → the material cannot be extruded
- too thin → the printed object will lose its shape

*To make the process reproducible, a flat plate of clay can be prepared and a calibrated sphere dropped from a fixed height. The penetration depth is measured and used as a reference for material consistency (see step 2).*

![Preparing the ceramic material](step-01-material-1.jpg)
![Preparing the ceramic material](step-01-material-2.jpg)
![Preparing the ceramic material](step-01-material-3.jpg)

{{< video src="preprocess-porcelain.mp4" >}}

## 2. Reproducibility check (for step 1)

To ensure consistent material properties, a simple drop test can be used.

**Method 1 – Ceramic sphere drop test**

A small sphere made from the same ceramic or porcelain material is dropped from a defined height onto a hard surface (e.g., stone or metal). The deformation of the sphere is evaluated:

- slight deformation without cracks → suitable consistency
- cracks → material too dry / too stiff
- strong flattening → material too soft

Because the drop height is fixed, the deformation serves as a reproducible reference.

**Method 2 – Glass sphere penetration test**

A calibrated glass sphere is dropped from a defined height onto a rolled-out plate of the ceramic or porcelain mass. The penetration depth is measured and recorded. With repeated tests, reference values can be established for each material to reproduce the required consistency (for 3D printing).

![Drop test](step-02-droptest-1.jpg)
![Drop test](step-02-droptest-2.jpg)

## 3. Prepare the printer and set parameters

Before generating the print file, the printer must first be made ready:

1. **Start and initialize the printer**

    - Establish the connection between the PC and the printer
    - Home all axes and ensure the printer is properly aligned
    - Adjust the Z-axis / print height according to the material and nozzle requirements
    - Run a test print to observe material flow and adjust print parameters during the process

2. **Set and test print parameters**
   Adjust settings such as extrusion rate, layer height, and speed specifically for the prepared ceramic paste. Use the test print results to fine-tune the parameters before printing the final model.

Once the printer is fully ready and the parameters are calibrated, the actual print file can be generated and printed.

![Printer setup](step-03-printer-1.png)
![Printer setup](step-03-printer-2.jpg)
![Printer setup](step-03-printer-3.jpg)

## 4. Prepare the print file

Before starting the printer, the 3D model must be prepared in the slicer software (Cura).

Relevant print parameters (Cura settings: [JetClay_MINI-Standard.curaprofile](https://unikat-ilmenau.de/wiki/books/projekte/page/jetclay-workshop)) should be carefully adjusted, including:

- Nozzle diameter (I used a 3 mm nozzle, similar to a pastry tip)

Once the print file is ready and parameters are calibrated, the actual model can be printed.

**My 3D models:**

- The [curved honeycomb vase](https://www.thingiverse.com/thing:2376777) was downloaded from Thingiverse. It was slightly modified (the base was reinforced).
- Another 3D model was captured with a handheld 3D scanner at the [Group for Quality Assurance and Industrial Image Processing](https://www.tu-ilmenau.de/en/university/departments/department-of-mechanical-engineering/profile/institutes-and-groups/group-for-quality-assurance-and-industrial-image-processing) at TU Ilmenau and edited using Meshmixer.

**Where to find existing 3D models:**

- [Thingiverse](https://www.thingiverse.com/)
- [Printables](https://www.printables.com/)
- [MyMiniFactory](https://www.myminifactory.com/)
- [Sketchfab](https://sketchfab.com/)
- [Thangs](https://thangs.com/)

![Print file in Cura](step-04-printfile.png)

## 5. Print the final object

After successful calibration, the final model can be printed. The printed object must then be dried and later fired in a kiln, depending on the type of ceramic used.

![Printing](step-05-print-1.jpg)
![Printing](step-05-print-2.jpg)
![Printing](step-05-print-3.jpg)
![Printing](step-05-print-4.jpg)
![Printing](step-05-print-5.jpg)
![Printing](step-05-print-6.jpg)

{{< video src="printing_v2.mp4" >}}

## 6. Drying and post-processing

After printing, the object is left to dry completely. Depending on the geometry, minor post-processing (such as smoothing or trimming) can be done at this stage.

![Drying](step-06-drying.jpg)

## 7. Bisque firing (Schrühbrand)

Once fully dry, the object is placed in a kiln for **bisque firing** (Schrühbrand). This first firing removes remaining moisture and hardens the ceramic, preparing it for glazing and final firing.

**Special thanks to Karl-Heinz Kerntopf** for handling the firing process.

![Bisque firing](step-07-bisque-1.jpg)
![Bisque firing](step-07-bisque-2.jpg)

## 8. Glazing

1. **Mix the glaze**
   Stir thoroughly before use, as pigments and heavier particles settle at the bottom, leaving excess water on top.
2. **Dipping and handling**
   Using glazing tongs or two fingers, fully immerse each object for approx. **3 seconds**. The vase is glazed inside and outside. Ensure no air bubbles are trapped inside during immersion. After removal, gently shake to allow excess glaze to drip off.
3. **Drying**
   Place the pieces aside to dry. The scanned figure dries quickly. The vase dries more slowly (low absorption due to thin wall thickness); drying can be accelerated with a hair dryer or heat gun if necessary.
4. **Re-glazing (if required)**
   If air bubbles occurred or the interior base is not fully coated, allow the piece to become touch-dry. Then pour a small amount of glaze into the vase, swirl to coat evenly, and pour it out again to ensure full interior coverage.
5. **Cleaning the foot ring**
   Once touch-dry, wipe glaze from the foot ring with a damp sponge to prevent the piece from fusing to the kiln shelf during firing.

![Glazing](step-08-glazing-1.jpg)
![Glazing](step-08-glazing-2.jpg)
![Glazing](step-08-glazing-3.jpg)
![Glazing](step-08-glazing-4.jpg)
![Glazing](step-08-glazing-5.jpg)
![Glazing](step-08-glazing-6.jpg)
![Glazing](step-08-glazing-7.jpg)
![Glazing](step-08-glazing-8.jpg)

## 9. Glaze firing (niedriger Glasurbrand)

**Special thanks to Karl-Heinz Kerntopf** for handling the firing process.

![Glaze firing](step-09-glazefiring-1.jpg)
![Glaze firing](step-09-glazefiring-2.jpg)
![Glaze firing](step-09-glazefiring-3.jpg)
![Glaze firing](step-09-glazefiring-4.jpg)

## Result

Both objects turned out well. One thing I would do differently next time: glaze the vase only on the inside. The printed layers are part of what makes the piece look the way it does, and the glaze on the outside hides them.

## Acknowledgements

This was part of a JetClay workshop. Many thanks to **Humberto Torres** and **Karl-Heinz Kerntopf ([AG Keramik](https://kuko-ev.de/ags-und-vereine/ag-keramik/) Ilmenau)** for sharing their expertise in ceramics and porcelain, and to Sparch and Matthias.

If you want to try ceramic 3D printing yourself, contact [AG Keramik](https://kuko-ev.de/ags-und-vereine/ag-keramik/) Ilmenau.

More information:

- [Jetclay Workshop](https://unikat-ilmenau.de/wiki/books/projekte/page/jetclay-workshop) (UNIKAT wiki)
- [Jetclay Academy](https://jetclayacademy.com/)
- [Original project documentation](https://fablab.tu-ilmenau.de/#!/projects/3d-printing-with-ceramic-jetclay) at FabLab@TU-Ilmenau
- Attached document on the original page: [EN_dOSIER_Iniciación-1.pdf](https://fablab.tu-ilmenau.de/uploads/project_cao/931/EN_dOSIER_Iniciaci%C3%B3n-1.pdf)
