## Advanced Software Engineering Zusammenfassung Simon

---

# Patterns 
## Definition
Design Patterns sind bewährte Lösungsschablonen für wiederkehrende Entwurfsprobleme.
## Eigenschaften
- ein oder mehrere Probleme lösen
- ein erprobtes Konzept bieten
- auf realen Designs basieren
- über das rein Offensichtliche hinausgehen
- Beziehungen aufzeigen, die tiefergehende Strukturen und Mechanismen eines Systems umfassen
## Implementierung
Ein Pattern ist ein abstrakter Lösungsentwurf, welcher verschiedene Designs haben kann. Diese können unterschiedlich designt werden und diese Designs können jeweils wieder unterschiedlich implementiert werden.
<!--![](/Bilder/Pattern_Design_Implementation.png)-->
<img src="Bilder/Pattern_Design_Implementation.png" width="300">

## Einteilung von Patterns
Patterns können in drei Kathegorien unterschieden werden 
- Creational
- Structural
- Behavioral

### Creational Patterns
Dienen der Erzeugung von Objekten => Entkoppeln die Konstruktion eines Objekts von seiner Repräsentation

### Structural Patterns
Bieten vorgefertigte Schablonen für Beziehungen zwischen Klassen
### Behavioral Patterns
Modellieren komplexes Verhalten der Software und erhöhen damit die Flexibilität der Software hinsichtlich ihres Verhaltens

Patterns manifestieren sich auf Klassenebene (Benutzung von Vererbung) und auf Objektebene (Benutzung von Interfaces)
Patterns auf Objekt-Ebene sind viel gebräuchlicher, da sie zu pflegbarerem Code führen
- Replacement Principle = Interna der Implementierung können jederzeit geändert werden

Die Verwendung von Patterns erhöht meist die Komplexität des Entwurfs bzw. der Implementierung
- Durch Hilfsklassen/Interfaces => Führt (oft) zu schwer wartbarem Code

**Der Entwurf sollte immer so einfach wie möglich sein, aber nicht einfacher und nur so komplex wie unbeding nötig!**

---

# Template Methode
Die Template Methode ist ein Basisbeispiel der Polymorhie [^1]. 
Hier implementiert eine abstrakte Klasse Figur eine Methode um diese Figur zu zeichnen. Da jedoch alle Klassen die von Figur erben eigene Formen darstellen müssen diese unterschiedlich gezeichnet werden. Dafür wird die in Figur deklarierte draw-Methode in jeder erbenden Klasse konkret implementiert.

<img src="Bilder/Template_Pattern.png" width=500>

## Problem 1: Die konkrere Implementierung von draw() benötigt einen Zugriff auf den Farbwert

### Lösung 1: Implementierung eines Farbattributes
<img src="Bilder/Template_Pattern_Lösung_01.png" width=400>

Hier ergibt sich der Nachteil, dass in jeder Implementierung von draw der prepare-Aufruf vergessen werden kann => **Komplexität wird erhöht**

### Lösung 2: Implementierung einer getColor() Methode

<img src="Bilder/Template_Pattern_Lösung_02.png" width=400>

Die Oberklasse gesaltet hier den allgemeinen Ablauf des Zeichnens, beschreibt aber nicht das spezifische Zeichnen der einzelnen Figuren.
- Durch die prepare() Methode, welche in der Oberklasse implementiert wird werden alle Parameter(Farbe) gesetzt, diese bleibt den Unterklassen verborgen.
- doDraw() ist eine abstrakte Methode die von den Unterklassen überschrieben werden muss, hier wird die Logik für die spezifischen Figuren definiert.

















































































[^1]: Fähigkeit, dass Funktionen oder Methoden in verschiedenen Objekten unterschiedlich ausgeführt werden können