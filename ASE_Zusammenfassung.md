## Advanced Software Engineering Zusammenfassung Simon

---

# Patterns 
## Definition
>Design Patterns sind bewährte Lösungsschablonen für wiederkehrende Entwurfsprobleme.
>> **Für Simon => Patterns sind low level also Proxy z.B. ist keine API weil das schon viel zu weit Abstrahiert ist**

## Eigenschaften
- ein oder mehrere Probleme lösen
- ein erprobtes Konzept bieten
- auf realen Designs basieren
- über das rein Offensichtliche hinausgehen
- Beziehungen aufzeigen, die tiefergehende Strukturen und Mechanismen eines Systems umfassen
## Implementierung
>Ein Pattern ist ein abstrakter Lösungsentwurf, welcher verschiedene Designs haben kann. Diese können unterschiedlich designt werden und diese Designs können jeweils wieder unterschiedlich implementiert werden.
<!--![](/Bilder/Pattern_Design_Implementation.png)-->
<img src="Bilder/Pattern_Design_Implementation.png" width="300">

## Einteilung von Patterns
>Patterns können in drei Kathegorien unterschieden werden 
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

# Creational Patterns 
Der Vorteil objektorientierter Sprachen sind Klassen und verwendung von Polymorphie (Bsp. abstrakte Schnittstellen)
**Problem:** Polymorphie funktioniert nicht bei der Erzeugung von konkreten Objekten. Hier muss jedes mal Explizit der Typ angegeben werden.

<img src="Bilder/Polymorphie_CP_Seite_2.png" width="400">

## Factory Pattern
> Eine einfache Variante ist die Bereitstellung von Erzeugungsmethoden, welche ein Objekt des gewünschten Typs zurückgeben
```java
public final class ListFactory
{
    private ListFactory( ) {}
    public static <T> List<T> newArrayList()
    {
        return new ArrayList<T>();
    }
    public static <T> List<T> newLinkedList()
    {
        return new LinkedList<T>();
    }
}
```

## Abstract Factory Pattern
> Über spezielle Objekte (sogenannte abstract Factories) soll der Erzeugungskontext automatisch ermittelt werden

Dabei wird per Interface eine Methode welche das Objekt bereitstellt erzeugt und jedes Objekt welches von diesem Interface erbt stellt die jeweilig benötigte Implementierung bereit.
<img src="Bilder/Abstract_Factory.png" width="400">

### Beispiel zwei überlappende Factories

<img src="Bilder/Zwei_überlappende_abstract_Factories.png" width="400">

## Builder Pattern

Das Builder Pattern soll die Konstruktion eines Objektes von seiner Repräsentation trennen. Dadurch kann das gleiche Erstellungsverfahren unterschiedliche Arten und Darstellungen von Objekten erzeugen.

> **Problem**: Wenn mehrere ähnliche Konstruktorparameter gesetzt werden sollen müssen diese im Konstruktor klar definiert sein! Gerade in Java kann der Konstruktor die unterschiedlichen Parameter nicht anhand des Namens sondern nur anhand des Datentyps unterscheiden.
```java
class A{
    A(int a, b){}
    A(int b, c){} //Fehler
    A(int a, c){} //Fehler
}
```
>Per Setter Methoden könnte jeder Parameter einzeln gesetzt werden, was jedoch eine große Fehlerquelle darstellt, da die Initialisierung eines Objektes über mehrere Zeilen geschehen müsste.

Das Builder Pattern löst dieses Problem indem es indem innerhalb einer Klasse eine interne Builderklasse erzeugt wird. Diese erhält für jeden Parameter der Klasse ein äquivalent und wird an den Konstruktor der zu bauenden Klasse übergeben. 
>Dadurch ist es möglich die Hauptklasse immutable zu halten und jedem Parameter unabhängig voneinander konfigurieren zu können.

```java
Class A{
    private int a;
    private int b;
    private int c;
    A(ABuilder builder){
        this.a = builder.a;
        this.b = builder.b;
        this.c = builder.c;
    }
    class ABuilder{
        int a = 0;
        int b = 0;
        int c = 0;
        public ABuilder setA(int a){
            this.a = a;
            return this;
        }
        public ABuilder setB(int b){
            this.b = b;
            return this;
        }
        public ABuilder setC(int c){
            this.c = c;
            return this;
        }
        Public A build(){
            return new A(this);
        }
    }
}
```
Daraus ergibt sich der Aufruf
```java
A a = new A.ABuilder().setA(1).setB(2).setC(3).build();
```

## Singleton
> Das Singleton-Pattern besagt, dass höchstens ein Objekt der Klasse existieren darf.
>> In der Regel soll von überall auf das Objekt zugegriffen werden können(Global Accessible) => **Das Singleton ist keine globale Variable!**

**Lazy Implementierung**
>**nicht** thread-safe!
```java
public class Singleton {
    private static Singleton instance;
    private Singleton() { }
    public static Singleton getInstance()
    {
        if( instance == null )
        {
            instance = new Singleton();
        }
        return instance;
    }
}
```
**Eager Implementierung**
>thread-safe
>> Instanziierung an das Laden der Klasse gebunden
```java
public class Singleton
{
    private static Singleton instance = new Singleton();
    private Singleton() { }
    public static Singleton getInstance()
    {
        return instance;
    }
}
```
**Implementierung mit Holder-Klasse**
>**Java only**

Erzeugung des Singletons an das Laden der internen Klasse gebunden
```java
public class HolderSingleton
    {
    public static class Holder
    {
    private static HolderSingleton instance = new HolderSingleton();
    }
    public static HolderSingleton getInstance()
    {
        return Holder.instance;
    }
    private HolderSingleton() { }
}
```
## Prototype Pattern
>Copy & Paste & Modify

Es existiert ein vorgefertigtes Objekt, welches entsprechend seiner angezielten Nutzung konfiguriert wird.

# Structural Patterns

Structural Patterns kommen zur Compilezeit und beschreiben die Beziehungen zwischen Objekten

## Adapter Pattern
Den Adapter gibt es in zwei Versionen(Objekt-Adapter / Klassen-Adapter)

>Lollypop Schnittstelle -o )-

Adapter werden verwendet um zwei Objekte/Schnittstellen miteinander kompatibel zu machen 
> []-o )-[]-o )-[]

> **Kompatibilität zwischen Schnittstellen => Keine neue Funktionalität => Nur eine Delegation**

## Decorator

>*"Ich habe ein Objekt welches ich funktional erweitere => Ich füge Funktionalität hinzu behalte aber den Typ bei"*

Unterschied zu Wrapper = Wrapper fügt in Java keine Funktionalität hinzu
Wrapper = Typkonvertierung 

Decorator erweitert Objekt und bestimmte Funktionalitäten => Pizza/Visierbeispiel da das Objekt beliebig "Dekoriert" werden kann

Problem bei Decorator => **Großer Vererbungszweig/Vererbungsbäume entstehen**

Dekoratoren werden oft in Ketten Organisiert

(Dekorator 1) -> (Dekorator 2) -> (Dekorator 3) -> (Konkretes Objekt)

> Wichtig ist, dass jeder Operator so behandelt werden kann wie das Objekt **Austauschbarkeit**

Kein Referenzvergleich == mehr möglich 
Gleichheit und Hashvalue müssen angepasst werden.

> Das hat den Hintergrund da hier die Fragestellung aufkommt was wird verglichen (Das Originalobjekt oder die Decoration)

Erzeugung von Decoratoren wird meist durch Factories implementiert

Dekoratoren verdecken die Objekt-ID des ursprünglichen Objekts

## Decorator vs Adapter

Dekorator erweiter Funktionalität der Schnittstelle
Adapter verbindet zwei nicht kompatible Schnittstellen (interface mix-in)

## Proxy

Stellvertreter Objekt um zu verhindern, dass ein direkter Zugriff auf ein Objekt entsteht

Remote Proxy: Bsp. Remote Zugriff auf ein Objekt 

P1  Netzwerk P2
[] - () - []

Um von P1 in Daten von P2 Zugriff zu bekommen baue ich einen Stellvertreter in P1 welcher auf die Objekte in P2 per Netzwerkrequest zugreifen kann

>**Nicht Read-Only** Per Set Methode können die Daten in P2 auch bearbeitet werden
>>Es geht also nur um den stellvertretenden Zugriff auf ein Fremdes Objekt ohne direkten Zugriff auf das Objekt zu haben.

>(alt)RMI / (neu)REST


*"Transportiert die Schnittstelle wo anders hin. Schreiber/Leser merkt nicht, dass er mit dem Proxy kommuniziert"*
## Adapter vs. Decorator vs. Proxy

Adapter = Überbrückung zweier Schnittstellen
Decorator = Erweitert Verhalten des ursprünglichen Objekts
Proxy = Besitzt selbe Schnittstelle und zeigt selbes Verhalten wie das "abgeschirmte" Objekt


## Bridge Pattern
>*"Wenn sie Bridge Pattern hören denken sie immer an die golden gate Brigde, sie verbinden zwei Landzungen" => "Adapter für Klassenhierarchien"*

>*"Sind nichts anderes als Treiber wie zum Beispiel JPA oder JDBC also ein Großer Adapter zwischen Abstraktion und bestimmten Implementierungen"*
>>*"Schnittstelle auf der einen Seite mit einer großen Hierarchie wird adaptiert durch Implementierungen"*

Beispiel dafür = JDBC Adapter

## Facade / Fassade

>*"Man baut was schönes und verbirgt dahinter was anderes. Also man macht nach außen was schönen hin weil man nach innen was verbergen möchte"*

Konkret Komplizierte Klassenkonstrukte die nach außen hin schön erreichbar gemacht werden soll => Schnittstelle

### Vorteile

Vereinfacht:
- komplexes Domain Model

- Austausch von Implementierungen

### Nachteile
- Mutieren oft zu Monsterklassen

Facade = Keine Isolierung des Sub-Systems "Concevience Einstellung" => Soll idR. Zugriffe auf dahinterliegende Objekte zulassen

## Composit 

Verschachtelung => Bsp. Directory 

Knoten kann wieder Knoten enthalten (Baum-Datenstruktur, Dateisystem)


# Behavioral Patterns

Helfen das komplexe Verhalten von Software besser zu modellieren => erhöht Flexibilität der Software

## Command

Implementierung von Arbeitsanweisungen als Objekt anstatt als Methode

>**IntUnaryOperator** = Damit können Lambdaausdrücke genutzt werden 
```Java
//Beispiel einfügen
```

### Erweiterungen
#### Command Stack

Bsp. Realisierung einer Undo Funktionalität
> CommandStack wird wird dann per Strg+Z zurückabgearbeitet

#### Command-Broker

Es gibt einen Command Handler dieser sucht sich einen passenden 
Commandhandler aus und übergibt diesem die Aufgabe

In manchen Implementierungen übernimmt Commandbroker Aufgabe selbst
In manchen übergibt er diese Aufgabe an einen Commandhandler

Executoren entsprechen Execution-Brokers 

=> Executor Service = Command Pattern
## State Pattern

Endlicher Automat (Einfach nur eine endliche Menge von Zuständen die halt bei Input wechseln)
Beispiele für State Machines:
- TCP = Wird als State Machine erzeugt
- Digitaluhr

>Prozedurale implementierungsvariante 
>> Immer Aktueller Zustand => Ereignis => Bedingung (Folgezustand)
>> Vorteil = Ablauforientiert "Das Bild (Zustandsautomat) verschwindet"

Jedoch aufwendig wenn neuer Zustand rein kommt weil jede Verzweigung angepasst werden muss

>Implementierungsart mit Zustandsklassen
>> "Das Bild (Zustandsklassen) bleibt" => Man ist näher am Bild dran was es leichter/verständlicher macht

> Implementierungsvariante als Graph
>> Knoten und Kanten => Zustand hat mehrere Knoten für jedes Event
>>> - Knoten = Zustandsübergänge
>>> - Kanten = Zustände
>> - Hier baut man dann die Struktur nach Also State a hat Übergang zu B, C, D
>> - Bsp Web oder Webframework => Wenn ich auf Taste drücke geh ich da hin

### Prodzedural oder Graph Implementierung

Wenn man von außen was konfigurieren will nimmt man Graph Implementierung
> Hier müssen erstmal die Grundlagen gemacht werden, Transitionsmodelle, etc.

Wenn mein Code sich vermutlich nicht ändern soll nimmt man State Framework
> Viel einfacher zu implementieren <br>
> Wahrscheinlich viel Performanter


## Template Method

Die Template Methode ist ein Basisbeispiel der Polymorhie [^1]. 
Hier implementiert eine abstrakte Klasse Figur eine Methode um diese Figur zu zeichnen. Da jedoch alle Klassen die von Figur erben eigene Formen darstellen müssen diese unterschiedlich gezeichnet werden. Dafür wird die in Figur deklarierte draw-Methode in jeder erbenden Klasse konkret implementiert.

<img src="Bilder/Template_Pattern.png" width=500>

## Problem 1: Die konkrete Implementierung von draw() benötigt einen Zugriff auf den Farbwert

### Lösung 1: Implementierung eines Farbattributes
<img src="Bilder/Template_Pattern_Lösung_01.png" width=400>

Hier ergibt sich der Nachteil, dass in jeder Implementierung von draw der prepare-Aufruf vergessen werden kann => **Komplexität wird erhöht**

### Lösung 2: Implementierung einer getColor() Methode

<img src="Bilder/Template_Pattern_Lösung_02.png" width=400>

Die Oberklasse gesaltet hier den allgemeinen Ablauf des Zeichnens, beschreibt aber nicht das spezifische Zeichnen der einzelnen Figuren.
- Durch die prepare() Methode, welche in der Oberklasse implementiert wird werden alle Parameter(Farbe) gesetzt, diese bleibt den Unterklassen verborgen.
- doDraw() ist eine abstrakte Methode die von den Unterklassen überschrieben werden muss, hier wird die Logik für die spezifischen Figuren definiert.
=> Simpel gesagt es wird nur die draw()-Methode der Oberklasse aufgerufen welche "versteckt" die abstrakte Methode doDraw() aufruft. Da doDraw() abstract ist muss jede Klasse die von Figur erbt diese für sich implementieren. Dadurch ist es möglich draw() immer aufzurufen, da bei Aufruf die Farbwerte von der Oberklasse gesetzt und die doDraw von der erbenden Klasse bereitgestellt wird.

> Beispiel aus der Vorlesung: Temperatursensor der sich immer wieder von Version zu Version verändert
>> Lösung abstrakte Funktion getTemp() <br>
>> Jeder Sensor implementiert getTemp() so wie er funktioniert. Dadurch kann jeder Sensor verwendet werden ohne bei Versionswechsel immer wieder den Code ändern zu müssen

> Template Pattern basiert auf Polymorphie / Baut darauf auf

## Strategy

Erlaubt den Austausch/Konfiguration eines Algorithmus oder Verfahrens zu Laufzeit.
Machbar wenn mehrere Algorithmen-Verfahren zur verfügung stehen.


*"Wenn sie etwas sortieren wollen können sie den Sortieralgorithmus austauschen"*

> Bsp Comparator => Anhand des Vergleichs bzw der Implementierung des Comparators ist die Logig verändert
>> Man übergibt dem Sortieralgorithmus eine Strategie => Die Entscheidung wann etwas größer/kleiner ist wird verändert

> Bsp 2. Baum-Beispiel
>> Wie durch den Baum iteriert("Traversiert") wird kann anhand einer übergebenen Strategie festgelegt. => Man gibt einem Algorithmus eine Strategie mit und verändert dadurch die Logik/Ausführung


## Observer
"**Das wichtigste und komplizierteste Pattern**"

"Ihr Code den sie lesen wird nicht in der Reihenfolge ausgeführt wie sie ihn lesen. Durch Callback Methoden die Aufgerufen werden wenn ein Event ausgelöst wird"

Es gibt ein Observable 
- register() => Dort registrieren sich die Listener "adListener"
- deregister() => Deregisteirung
- notify() => Läuft durch alle Observer und ruft dort eine Methode (meistens action() o.ä.) auf

> ** Das Observer-Pattern dient der automatischen Weitergabe von Änderungen an einem Objekt an von diesem Objekt abhängige Strukturen **

Man unterscheidet in drei Arten das Observer-Pattern umzusetzen

- Push Notification
    - Observer bekommt Nachricht es hat sich was geändert und muss aktiv nachfragen was/wie sich etwas geändert hat
- Push Update
    - Push Update => Da werden die geänderten Informationen mitgeschickt
- Pull Notification
    - Beobachter fragt selbstständig nach dem Zustand des Objektes

> **"MCV Pattern ist Observer => View ist Observer von Model"**
>> Model kennt View nicht dadurch sind die Views austauschbar
































































[^1]: Fähigkeit, dass Funktionen oder Methoden in verschiedenen Objekten unterschiedlich ausgeführt werden können