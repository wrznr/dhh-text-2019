layout: true
  
<div class="my-header"></div>

<div class="my-footer">
  <table>
    <tr>
      <td style="text-align:right">Sächsische Landesbibliothek – Staats- und Universitätsbibliothek</td>
      <td>Date</td>
      <td style="text-align:right"><a href="https://www.slub-dresden.de/">www.slub-dresden.de</a></td>
    </tr>
    <tr>
      <td style="text-align:right">Referate 2.5 und 4.3</td>
      <td />
    </tr>
  </table>
</div>

<div class="my-title-footer">
  <table>
    <tr>
      <td style="text-align:left"><b>Robert Sachunsky, Kay-Michael Würzner</b></td>
    </tr>
    <tr>
      <td style="text-align:left">Referate 2.5 und 4.3</td>
    </tr>
    <tr>
      <td style="font-size:8pt"><b>07.09.2021</b></td>
    </tr>
    <tr>
      <td style="font-size:8pt">Digital Herrnhut – Zweiter Sommerworkshop</td>
    </tr>
  </table>
</div>

---

class: title-slide
count: false

# Vom (Handschriften-)Digitalisat zum maschinenlesbaren Text
## Erste Ergebnisse

---

# Überblick

- erste im Rahmen des LDP digitalisierten Materialien aus dem Unitätsarchiv Herrnhut online: https://digital.slub-dresden.de/werkansicht/dlf/435266/1/
    + wunderschöne Digitalisate
    + noch ohne Volltext
- Ziele für heute
    + Handschriftenerkennung: Wie funktioniert's?
    + Training und *Ground Truth*
    + erste Ergebnisse

---

class: part-slide
count: false

# Handschriftenerkennung: Wie funktioniert's?

---

# Wie funktioniert's?

- Ziel: Transformation von Bilddaten in maschinenlesbaren Volltext
    + schrittweise Verarbeitung

<center>
<img src="img/nbg_region.png" width="400px" />
</center>
<center>
<p>↓</p>
</center>
<center>
<p style="display: inline-block; text-align: left; font-size: 16pt.; font-style: italic;">
oberwähntem Tage mancher sorgliche Gedanke auf,<br/>
&amp; wir seufzten öfters zum Heiland, daß Er uns<br/>
vor allem Schaden, der uns etwa in der folgen-<br/>
den Nacht begegnen könnte, in Gnaden bewahren
</p>
</center>

---

# Wie funktioniert's?

- Schritt 1: Zeilenerkennung
    + **regelbasierte** (Bildmorphologie) oder
    + **datengetriebene** Verfahren (e.g. Pixelklassifikation)

<center>
<img src="img/nbg_region.png" width="400px" />
</center>
<center>
<p>↓</p>
</center>
<center>
<img src="img/nbg_lines.png" width="400px" />
</center>

---

# Wie funktioniert's?

- Schritt 2: Vektorisierung
    + **Skalierung** auf einheitliche Höhe
    + **Unterteilung** in 1pixel-breite Streifen

<center>
<img src="img/nbg_lines.png" width="400px" />
</center>
<center>
<p>↓</p>
</center>
<center>
<img src="img/nbg_grid.png" width="400px" />
</center>

---

# Wie funktioniert's?

- Schritt 3: Textermittlung
    + **Übergangswahrscheinlichkeiten** zwischen Vektoren
    + Rückgriff auf (offline) trainiertes **Modell**

<center>
<img src="img/nbg_grid.png" width="400px" />
</center>
<center>
<p>↓</p>
</center>
<center>
<p style="display: inline-block; text-align: left; font-size: 16pt.; font-style: italic;">
oberwähntem Tage mancher sorgliche Gedanke auf,<br/>
&amp; wir seufzten öfters zum Heiland, daß Er uns<br/>
vor allem Schaden, der uns etwa in der folgen-<br/>
den Nacht begegnen könnte, in Gnaden bewahren
</p>
</center>

---

# Wie funktioniert's?

.cols[
.fourty[
- Schritt 3: Zeichenerkennung (Prinzip)
    + Erkennung auf Zeilenebene
    + Ausgabe der Zeichen mit höchster Wahrscheinlichkeit
]
.sixty[
<p style="margin-top:-20px">
<img src="https://files.gitter.im/5b97ae51d73408ce4fa7b1ee/LYSx/lstm-fraktur-arrows.png" width="650px" style="transform:rotate(90deg);"/>
</p>
]
]

---

# Wie funktioniert's?

- Software
    + [**Tesseract**](https://github.com/tesseract-ocr/tesseract): komplettes Open-Source-Paket
        * regelbasierte Bildvorverarbeitung und Layouterkennung
        * datengetriebene Texterkennung (unterstützt > 100 Sprachen)
        * Ease-of-Use-Training eigener Modelle
        * für OCR und **HTR** verwendbar
    + [**OCRopy**](https://github.com/ocropus/ocropy): umfangreiches Open-Source-Paket
        * regelbasierte Bildvorverarbeitung und Layouterkennung
        * datengetriebene Texterkennung (nur sehr wenige Modelle vorhanden)
        * für OCR und **HTR** verwendbar
        * prominente Ableger: [**kraken**](https://kraken.re) und [**Calamari**](https://github.com/Calamari-OCR/calamari)
    + [**OCR-D**](https://ocr-d.de/): Workflow-Engine
        * Orchestrierung verschiedener Open-Source-Pakete zu stabilen Workflows
        * gleichzeitig DFG-Förderprogramm zur Verbesserung von OCR für historische Drucke
        * SLUB als maßgebliche Entwicklungseinrichtung
    + [**LAREX**](https://github.com/OCR4all/LAREX): GT-Erstellung
        * Webapp zur Annotation von Struktur und Text (Transkription)

---

class: part-slide
count: false

# Training und *Ground Truth*

---

# Training und *Ground Truth*

- Basis der Erkennung: **trainierte** Modelle
    + anhand manuell erstellter, fehlerfreier Trainingsdaten
- für Text:
    + Paare von Zeilenbildern und deren Text
- für Struktur (Regionen und Zeilen):
    + Seitenbilder und Koordinaten der Struktureinheiten
- Algorithmus lernt Zuordnung
    + Wahrscheinlichkeitsverteilung über mgl. Zeichen und Zeichentrenner
- bei ausreichender Menge und **Repräsentativität** der Daten → verbesserte Erkennung

---

# Training und *Ground Truth*: Beispiel

.cols[
.fourty[
- Herrnhut Nachrichten 1806 (&gt;800 Seiten)
- GT-Erstellung mittels LAREX (z.Z. 15 Seiten)
   <img src="img/herrnhut-hetjens-larex-transkription1.png"/>
]
.sixty[
<img src="img/herrnhut-hetjens-larex-transkription.png"/>
<img src="img/herrnhut-hetjens-larex-transkription2.png"/>
]
]

---

# Training und *Ground Truth*: Beispiel

- keine existierenden HTR-Modelle für Tesseract
   → Transkription von Grund auf  
   → (Pre-)Training mit großem existierendem GT  
     z.B. [Konzilsprotokolle Universitätsarchiv Greifswald](https://zenodo.org/record/215383#.YJFuPHVfjDs) (8770 Zeilen)  
     <img src="https://camo.githubusercontent.com/06493331adfcac6c297a8cd048fcb77742088085c31cf7c5046c4c17c06d4bbc/68747470733a2f2f66696c65732e6769747465722e696d2f77727a6e722f744153492f4f43522d442d494d472d4445535045434b5f303030355f72315f72316c32362e706e67"/>

---

# Training und *Ground Truth*: Beispiel

- Training und Test mit Tesseract
   * Basis: HTR Konzilsprotokolle  
     <img src="https://camo.githubusercontent.com/06493331adfcac6c297a8cd048fcb77742088085c31cf7c5046c4c17c06d4bbc/68747470733a2f2f66696c65732e6769747465722e696d2f77727a6e722f744153492f4f43522d442d494d472d4445535045434b5f303030355f72315f72316c32362e706e67"/>
    ```
    $ make training MODEL_NAME=htr MAX_ITERATIONS=30000
    $ tesseract htrline1.png - -l htr --psm 13 --dpi 300
    sowohl, als auch eb bei der Schrift etwas zu er-
    ```
   * Finetuning: HTR Herrnhut  
     <img src="img/GT_0005_r1_l1.bin.png"/>
    ```
    $ make training MODEL_NAME=hetjens MAX_ITERATIONS=3000 \
                    START_MODEL=htr
    $ tesseract hetjens-no1.png - -l hetjens --psm 13 --dpi 300
    No 1. des eren Theiles der Gemein⸗
    ```

---

# Training und *Ground Truth*: Beispiel

- Evaluierung (extern):

| **Daten** | **Modell** | **CER (%)** |
| --- | --- | --- |
| Konzilsprotokolle (train) | htr | 5.4 |
| Konzilsprotokolle (val) | htr | 11.2 |
| Herrnhut | htr | 59.4 |
| Herrnhut (train) | htr→hetjens | 0.6 |
| Herrnhut (val) | htr→hetjens | 15.8 |

---

# Training und *Ground Truth*: Optimierungen

- mehr Trainingsdaten
    + Erfahrungen mit gedrucktem Material: für verlässliches buchspezifisches Modell
        * ~20-40 Seiten Transkriptionen (von Grund auf), oder
        * ~100-200 Zeilen Transkriptionen (bei Finetuning)
    + für Handschriften
        * kritische Menge an Zeilen nötig (Gefahr *Overfitting*)
        * höhere Binnenvarianz als bei Drucken
- bessere Bildvorverarbeitung (v.a. Binarisierung)
    + relativ viele Artefakte in den Zeilenbildern
    + Konzilsprotokolle „sauberer“
- variante und robuste Eingangsmodelle
    + schmutzigere Trainingsdaten
    + mehr Handschriften (e.g. [e-manuscripta.ch](https://www.e-manuscripta.ch/zuz/briefe/content/wpage/22344))
    + Pretraining mit synthetischen Trainingsdaten

---

# Training und *Ground Truth*: Optimierungen

- Optionen allgemein
    + **Transfer-Learning**
        * Anpassung eines existierenden, ähnlichen Modells mit wenigen Zeilen GT auf spezifische Vorlage
    + **generische Modelle**
        * auf Basis großer Mengen diversen GTs trainierte Modelle mit Allgemeinheitsanspruch 
        * schwierig im Bereich HTR
    + **synthetisches Training**
        * mit Hilfe großer Textmengen und verschiedener Computerschriftarten automatisch erzeugter GT
        * Erzeugung einer realistischeren Optik durch
    + **Augmentierung**
        * größere Modellrobustheit und Variantenstabilität durch gezieltes Verrauschen
        * Schrägstellung, Spiegelung, Störpixel, horizontale und vertikale Verzerrung etc.
- Fazit allgemein
    + Aufwand **werkspezifisches** Modell meist nicht leistbar
    + also **schrift/materialspezifische** Modelle und ggf. Werk-Finetuning

---

class: part-slide

# Diskussion und lose Enden

- verlässliche Handschriftenerkennung in Reichweite
- Digital Herrnhut
    + vglw. normierte und sehr gut leserliche Handschriften
    + viel Material „aus einer Hand“
- GT-Problematik
    + gemischtes Vorgehen
        * existierende Kurrentdatensätze als Grundlage
    + „echte“ Ground Truth für NBG
        * Larex zur Transkription
        * Podcasts zur indirekten Transkription
        * Einbindung von Bürgerwissenschaftlerinnen und -wissenschaftler sowie Studierende
    + synthetische Ground Truth
        * gute Kurrentschriften gesucht!


---

class: part-slide

# Vielen Dank für Ihre Aufmerksamkeit

<center>
<a href="https://wrznr.github.io/dhh-text-2021/">wrznr.github.io/dhh-text-2021/</a>
</center>
