---
layout: post
group: blog post
title:  "Blog Post #1"
date:   2015-10-01 13:51:00
categories: jekyll update
mathjax: true
---

**Sliding Window-Ansatz:** <br />
Die Detektion der Objekte in dem RGB-D Eingangs-Bild basiert auf einem *"Sliding Window-Ansatz"*, der auf verschiedene Bildskalierungen angewandt wird. Die bekannten Objekte werden durch ein Menge von mehreren tausend RGB-D-Template-Bildern fester Größe beschrieben. Diese Template-Bilder erfassen alle möglichen Objekt-Ansichten, wobei die Distanz zur Kamera fest gewählt ist, so dass das Objekt das Template-Bild optimal füllt. <br />

**Vorfilterung:** <br />
Um die Anzahl der zu verarbeitenden Bildpositionen zu verringern, wird jeder Bildausschnitt zunächst anhand seiner Wahrscheinlichkeit, dass er eines der Objekte enthält, bewertet und ggf. verworfen. Dies entspricht einer *"binären Klassifizierung"*, die nur zwischen Objekt und Hintergrund unterscheidet. Die Wahrscheinlichkeitsberechnung basiert dabei auf der Anzahl der "Depth-discontinuity edges" (Tiefenbild-Kanten) innerhalb des Ausschnitts und wird mit Hilfe eines Integral-Bildes berechnet. "Depth-discontinuity edges" treten an Pixeln auf, in denen das Ergebnis des Sobel-Operators (eines einfachen Kantendetektions-Filters) oberhalb eines Schwellwerts liegt. Der Bildausschnitt wird als "Objekt-enthaltend" klassifiziert, wenn die Zahl seiner Tiefenbild-Kanten mindestens 30% der Zahl der Tiefenbild-Kanten des Templates entspricht, welches die wenigsten solcher Kanten enthält. 

**Details zu Punkt 2 (Hypothesen Generierung):** <br />
Für jeden verbleibenden Bildausschnitt, der durch die Vorfilterung gekommen ist, wird nun schnell eine kleine Menge von Kandidaten-Templates (Bilder, die das dort abgebildete Objekt aus vielen verschiedenen Blickwinkeln zeigen) identifiziert. Dieser Schritt kann als *"Multi-Class-Klassifizierung"* angesehen werden, bei der es eine Klasse für jedes Trainings-Template gibt, jedoch keine Hintergrundklasse. Die hier verwendete Methode wählt Kandidaten-Templates aus mehreren trainierten Hashtabellen. Die Hashtabellen sind durch eine Reihe von Messungen indiziert, welche auf ein Template oder Sliding-Window durchgeführt werden. Dazu wird ein reguläres 12x12 Gitter auf dem Trainingstemplate platziert und jedem Gitterpunkt eine Tiefe und eine Oberflächennormale zugeordnet. Von den Gitterpunkten werden nun Triplets abgetastet, die als entsprechende Messung einen Vektor, bestehend aus 2 relativen Tiefenwerten und 3 Normalen liefern.

**Details zu Punkt 3 (Verifikation):** <br />
Da in dem vorherigen Schritt bereits für jeden objekt-enthaltenden Bildausschnitt eine kleine Menge von Kandidaten-Templates identifiziert wurde, kann dieser Schritt nun als eine Menge von *mehreren separaten binären Klassifizierungen* angesehen werden, die jeweils entscheiden, ob der Bildausschnitt das Objekt des entsprechenden Templates enthält, oder nicht. Die Verifikation (auch *"Template-Matching"*) besteht aus einer Reihe von Tests die Folgendes überprüfen: 1.) Objekt-Größe in Relation zur Kameradistanz, 2.) abgetastete Oberflächennormalen, 3.) abgetastete Bildgradienten, 4.) abgetastete Tiefenkarte und 5.) abgetastete Farbe. Ein verifiziertes Template, das also alle Tests bestanden hat, erhält einen abschließenden Punktestand, der beschreibt, wie gut das Template die einzelnen Testkriterien erfüllt hat. Schließlich wird durch **"Non-maxima Suppression"** von allen verfizierten Templates dasjenige ausgewählt, das einen möglichst hohen Punktestand erzielt hat und gleichzeitig eine möglichst große Objektansicht enthält, die möglichst viel zum Szenenverständnis beiträgt.