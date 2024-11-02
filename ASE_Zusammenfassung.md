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

Adapter werden verwendet um zwei nicht miteinander kompatible Objekte/Schnittstellen miteinander kompatibel zu machen.
> []-o )-[]-o )-[]

> **Kompatibilität zwischen Schnittstellen => Keine neue Funktionalität => Nur eine Delegation**
### Objekt-Adapter
<img src="Bilder/Adapter_Pattern_Verwendungsbeispiel.png" width=400>

### Klassen-Adapter
<img src="Bilder/Klassen_Adapter.png" width=400>

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

<img src="Bilder/Decorator_Pattern.png" width=400>

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

<img src="Bilder/Proxy_Pattern.png" width=400>

## Adapter vs. Decorator vs. Proxy

Adapter = Überbrückung zweier Schnittstellen
Decorator = Erweitert Verhalten des ursprünglichen Objekts
Proxy = Besitzt selbe Schnittstelle und zeigt selbes Verhalten wie das "abgeschirmte" Objekt


## Bridge Pattern
>*"Wenn sie Bridge Pattern hören denken sie immer an die golden gate Brigde, sie verbinden zwei Landzungen" => "Adapter für Klassenhierarchien"*

Das Bridge Pattern wird dafür verwendet größere Schnittstellen miteinander zu verbinden.

>*"Sind nichts anderes als Treiber wie zum Beispiel JPA oder JDBC also ein Großer Adapter zwischen Abstraktion und bestimmten Implementierungen"*
>>*"Schnittstelle auf der einen Seite mit einer großen Hierarchie wird adaptiert durch Implementierungen"*

<div style="display: flex; justify-content: space-around;">
    <img src="Bilder/Bridge_Pattern_UML.png" width="400">
    <img src="Bilder/Bridge_Pattern_Beispiel.png" width="400">
</div>

## Facade / Fassade

>*"Man baut was schönes und verbirgt dahinter was anderes. Also man macht nach außen was schönen hin weil man nach innen was verbergen möchte"*

Konkret Komplizierte Klassenkonstrukte die nach außen hin schön erreichbar gemacht werden soll => Schnittstelle

<img src="Bilder/Facade_Pattern.png" width=600>

### Vorteile

Vereinfacht:
- komplexes Domain Model

- Austausch von Implementierungen

### Nachteile
- Mutieren oft zu Monsterklassen

Facade = Keine Isolierung des Sub-Systems "Concevience Einstellung" => Soll idR. Zugriffe auf dahinterliegende Objekte zulassen und erleichtern
> Einfache Zugriffsschnittstelle für komplexere Aufrufe

## Composit 

Composite-Pattern = hierarschicher Aufbau einer Datenstruktur (Repräsentation einer Teil-Ganzes-Hierarchie)

**Einsatzzweck:**
- Aufbau einer flexiblen hierarchischen Struktur, deren Gestalt sich ändern kann
- Bestandteile können einheitlich behandelt werden, da sie einen algemeinen Typ repräsentieren

> **Knoten kann wieder Knoten enthalten Bsp. Baum-Datenstruktur, Dateisystem**


# Behavioral Patterns

Helfen das komplexe Verhalten von Software besser zu modellieren => erhöht Flexibilität der Software

## Command

Implementierung von Arbeitsanweisungen als Objekt anstatt als Methode. Wenn es also eine abstrakte Klasse Command gibt die eine Methode execute() vorgibt können alle erstellten Commands übergeben werden. Meistens gibt es einen Invoker, welcher den Befehl ausführt und einen Receiver welcher die Arbeit "verrichtet".

>**IntUnaryOperator** = Damit können Lambdaausdrücke genutzt werden 
```Java
public static void main(String[] args)
{
    System.out.println("Summe " + sum(0,10, i -> i) );
    System.out.println("Summe Quadratzahlen " + sum(0,10, i -> i*i) );
}
private static int sum(int start, int end, IntUnaryOperator function)
{
    int sum = 0;
    for(int i=start; i < end; i++)
    {
        sum += function.applyAsInt(i);
    }
    return sum;
}
```

### Erweiterungen
#### Command Stack

Bsp. Realisierung einer Undo Funktionalität
> Im Kontext von Tastatureingaben würde der CommandStack per undo dann per Strg+Z zurückabgearbeitet

<img src="Bilder/Command_Stack.png" width=400>

#### Command-Broker

Es gibt einen Command Handler dieser sucht sich einen passenden 
Commandhandler aus und übergibt diesem die Aufgabe

In manchen Implementierungen übernimmt Commandbroker Aufgabe selbst
In manchen übergibt er diese Aufgabe an einen Commandhandler

Executoren entsprechen Execution-Brokers 
=> Executor Service = Command Pattern

<img src="Bilder/Command_Broker.png" width=400>

## State Pattern

Endlicher Automat (Einfach nur eine endliche Menge von Zuständen die halt bei Input wechseln)
Beispiele für State Machines:
- TCP = Wird als State Machine erzeugt
- Digitaluhr

### Prozedurale implementierungsvariante 
> Aktueller Zustand => Ereignis => Bedingung/Folgezustand
<img src="Bilder/State_Pattern_Prozendural_Beispielbild.png" width=400>

Vorteil = Ablauforientiert "Das Bild (der Zustandsautomat) verschwindet"
```Java
while (index < eventSequenz.length()) {
    char event = eventSequenz.charAt(index);
    switch (state) {
        case "Q1":
            switch (event) {
                case 'a': state = "Q2"; break;
                case 'b': state = "Q1"; break;
                default: System.err.println("Falsches Event");
            }
        break;
        case "Q2":
            …..
        case "Q3":
            …..
        default: System.err.println("Falscher Zustand");
    }
```
Dieser Ansatz ist jedoch Zeitaufwendig, da jede Verzweigung angepasst werden muss, sobald ein neuer Zustand dazu kommt.

### Implementierungsart mit Zustandsklassen
> "Das Bild (Zustandsklassen) bleibt" => Man ist näher am Bild dran was es leichter/verständlicher macht

> Implementierungsvariante als Graph
>> Knoten und Kanten => Zustand hat mehrere Knoten für jedes Event
>>> - Knoten = Zustandsübergänge
>>> - Kanten = Zustände
>> - Hier baut man dann die Struktur nach Also State a hat Übergang zu B, C, D
>> - Bsp Web oder Webframework => Wenn ich auf Taste drücke geh ich da hin

<img src="Bilder/State_Pattern_Zustandsklassen.png" width=500>

```Java
public class Q1 extends Zustand
{
    @Override
    public Zustand processEventA() { return new Q2(); }
    @Override
    public Zustand processEventB() { return this; }
}
public static void main(String[] args)
{
    Zustand state = new Q1();
    state = state.processEventA().processEventB().processEventA();
    System.out.println( state.getClass().getSimpleName() );
}
```

### Graph Implementierung


<img src="Bilder/State_Pattern_Graph_Implementierung.png" width=400>

Wenn man von außen Parameter konfigurieren will nimmt man die Graph Implementierung
> Hier müssen erstmal die Grundlagen gemacht werden, Transitionsmodelle, etc.

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

1. Bsp Comparator => Anhand des Vergleichs bzw der Implementierung des Comparators ist die Logig verändert
    - Man übergibt dem Sortieralgorithmus eine Strategie => Die Entscheidung wann etwas größer/kleiner ist wird verändert
2. Bsp. Baum-Beispiel
    - Wie durch den Baum iteriert("Traversiert") wird kann anhand einer übergebenen Strategie festgelegt. => Man gibt einem Algorithmus eine Strategie mit und verändert dadurch die Logik/Ausführung

<img src="Bilder/Strategy_Pattern.png" width=400>

## Observer
"**Das wichtigste und komplizierteste Pattern**"

"Ihr Code den sie lesen wird nicht in der Reihenfolge ausgeführt wie sie ihn lesen. Durch Callback Methoden die Aufgerufen werden wenn ein Event ausgelöst wird"

Es gibt ein Observable 
- register() => Dort registrieren sich die Listener "adListener"
- deregister() => Deregisteirung
- notify() => Läuft durch alle Observer und ruft dort eine Methode (meistens action() o.ä.) auf

> **Das Observer-Pattern dient der automatischen Weitergabe von Änderungen an einem Objekt an von diesem Objekt abhängige Strukturen**

Man unterscheidet in drei Arten das Observer-Pattern umzusetzen

- Push Notification
    - Observer bekommt Nachricht es hat sich was geändert und muss aktiv nachfragen was/wie sich etwas geändert hat
- Push Update
    - Push Update => Die geänderten Informationen werden mitgeschickt
- Pull Notification
    - Beobachter fragt selbstständig nach dem Zustand des Objektes

> **"MCV Pattern ist Observer => View ist Observer von Model"**
>> Model kennt View nicht dadurch sind die Views austauschbar
>> Dadurch lose kopplung

Controller = Zuständig für Eingaben

Lambdas = Kurzschreibweise um Interfaces mit nur einer Funktionalität aufzurufen
```Java
public interface TickerStateChangeListener {
    public void StateHasChanged();
}

//Dieser Aufruf ist dasselbe wie
this.ticker.registerStateChangeListener(new TickerStateChangeListener() {
            @Override
            public void StateHasChanged() {
                setDisplay(ticker.getValue());
            }
        });

// dieser Aufruf
this.ticker.registerStateChangeListener(() -> setDisplay(ticker.getValue()));
```

---

# Design Prinzipien

## SOLID

S = Single Responsibility Only

O = Open-Closed Prinzip

L = Liskovsches Substitutionsprinzip

I = Interface Segregation Prinzip

D = Dependency Inversion Prinzip

**Wenn jemand im Bewerbungsgesrpäch fragt muss man wissen, dass es ein Akronym ist und für Designprinzipien steht. Noch bessern wenn man diese Kennt**

### SRP: Single Responsibility Principle
- "Für was ist was zuständig"
- Eine Klasse/Funktion hat nur eine Funktionalität

"Es sollte nie mehr als einen Grund dafür geben, eine Klasse zu ändern"
=> Vermeidung von Seiteneffekten

Kann auf Klassen-, Methoden- und Komponentenebene angewendet werden.

> Dies ist wichtig, da wenn eine Klasse mehr als eine Zuständigkeit hat, dann werden auch diese welche bei der Nutzung dieser Klasse nicht benötigt werden implizit gekoppelt. Wenn nun eine dieser Zuständigkeiten verändert wird kann die Zuständigkeit der anderen beeinträchtigt oder behindert werden.

<div style="display: flex; justify-content: space-around;">
<img src="Bilder/SRP_Beispiel_Bevor.png" width=400>
<div width=40><p>=></div>
<img src="Bilder/SRP_Beispiel_Danach.png" width=400>
</div>

<br/>
Auf Komponentenebene wird SRP zum Common-Closure-Prinzip

Auf Architekturebene wird SRP zum Axis-of-Change-Modell

### OCP = Open Closed Principle

"Wie baue ich meine Software offen für Erweiterungen aber geschlossen für Veärnderungen"

**Open -> Erweiterung**
Das Verhalten eines Moduls bzw. einer Klasse soll erweitert werden können, wenn sich die Anforderungen der Anwendung ändern.
**Closed -> Veärnderungen**
Das Erweitern des Verhalten eines Moduls bzw. einer Klasse soll keine Änderungen am bestehenden Quellcode des Moduls beinhalten => Die ausführbare Version des Moduls/der Bibliothek bleibt unangetastet
> Die Erweiterungen sollen die Klasse nicht kaputt machen.

Änderungen sollten Lokal bleiben und keine Seiteneffekte haben.
Wenn ein starres Design vorliegt kann eine Änderung zu einer Kaskade von Änderungen führen.

<div style="display: flex; justify-content: space-around;"> 
<img src="Bilder/OCP_Beispiel_Davor.png" width=400>
<div width=10><p>=></p></div>
<img src="Bilder/OCP_Beispiel_Danach.png" width = 400>
</div>
> In Beispiel 2 Liegt das Interface Sortierbar im model.util, da es zu dem Sortierer gehört. Wenn die Klassen den Sortierer nutzen wollen müssen sie auch das Interface unter dessen Hoheit implementieren.

Zielsetzung ist es, das System so zu gestalten, dass es leicht erweiterbar ist, ohne einen zu hohen Einfluss durch Modifikatoren zuzulassen.
Wird erreicht durch:
- Unterteilung des Systems in Komponenten
- Anordnung der Komponenten in einer Abhängigkeitshierarchie
- Komponenten niedrigerer Ebenen durch Änderungen von Komponenten höherer Hierarchien schützen

### Liskovsches Substitutionsprinzip
- "Leitlinie für Vererbung"
- Unterklassen müssen immer Oberklassen substituieren können

Vererbung bedeutet Substituierbarkeit und sollte nicht als eine ist-ein Beziehung interpretiert werden.

> Untergeordnete Klassen einer Vererbungshierarchie können die überliegenden vollständig ersetzen
### ISP = Interface Segregation Principle
"Clients sollten nicht gezwungen sein von Interfaces abzuhängen, die sie nicht verwenden"

**Unnötige Interfaceverkettungen oder große/riesige Interfaces vermeiden!**

>Machen sie kleine Interfaces => Interfaces sollten immer eine Rolle haben, welches das Objekt spielen kann

Simpel heruntergebrochen soll vermieden werden, dass eine Klasse ein großes Interface mit zu vielen Eigenschaften implementieren muss obwohl nur eine Eigenschaft dieses Interface implementiert werden soll.
Lösung = Implementierung mehrerer kleiner Interfaces die jeweils nur eine Eigenschaft verkörpern so, dass eine Klasse nur das implementiert was sie auch braucht.

### DIP = Dependency Inversion Principle
**Ganz wichtig!**
"Module hoher Ebenen sollten nicht von Modulen niedrigerer Ebenen abhängen. Beide sollten von Abstraktionen abhängen."
"Abstraktionen sollten nicht von Details abhängen => Details sollten von Abstraktionen abhängen!"

Beispiel für dieses Principle = Abstract Factory
=> Iterator weiß welche Datenstruktur vorliegt und kann passend zur Datenstruktur einen Iterator auswählen

DIP besagt, dass Systeme, in welchen sich Quellcode-Abhängigkeiten ausschließlich auf Abstraktionen beziehen anstatt auf konkrete Realisierungen, am flexibelsten sind.
=> "High-Level"(Layer) sollen nicht von "Low-Level"(konkrete Implementierungen) abhängig sein

> Dieses Prinzip uist nicht durchzuhalten! => Abhängigkeiten von stabilem konkreten Code sind erlaubt.

## API-Design
Benutzbarkeit = Leicht verstädnlich und erlernbar sein
Effizienz = geringe Übertragung von Datenvolumen
Zuverlässigkeit = Wie wird mit Fehlern umgegangen? => **Ein Fehler bei Verwendung sollte nicht in einem Absturz resultieren!**

**Wichtig:**
- Konsistenz = Durchgängige Namensgebung
- Verständlichkeit = Konventionen
    - Getter und Setter sollten auch so benannt sein (getId(), setId(int id))
- Eine gute API sollte schwer falsch zu benutzen sein!
    - Eine gute API sollte nicht nur einfach zu benutzen sein, sondern auch schwer sein sie falsch zu verwenden
- Leaking References vermeiden
    - Eine API sollte keine Referenzen zurückgeben (modifizierbare Interna)
    - Wenn eine beispielsweise eine Liste zurückgegeben wird sollte entweder eine Kopie dieser zurückgegeben werden oder eine unmodifizierbare Version.
- Keine null Rückgaben!
    - Lieber ein eigenen Typ/Fehler zurückgeben als stumpf null

## Weitere Konzepte

### DRY = Dont Repeat Yourself
Ziel Redundanz vermeiden und Funktionalität zentralisieren

<img src="Bilder/DRY_Beispiel.png" width=600>

**Vorsicht: Kann Abhängigkeiten einführen**

### YAGNI = You Aren´t Gonna Need It

Unnötiges weglassen => Erst implementieren wenn es benötigt wird 

### KISS = Keep It Simple And Stupid

Einfachheit ist eine erforderliche Eigenschaft von Softwaresysteme
- Gilt für Gestaltung und Implementierung

Wenn Software kompliziertet ist als sie sein muss sinkte ihre Qualität

=> höhere Komplexität verringert Wartbarkeit, behindert Wiederverwendbarkeit und kann zu Zunahme der Anzahl von Fehlern führen.

## Fazit von Design Prinzipien
Die Anwendung der Prinzipien führt nicht automatisch zu besserer Software. Meistens landet man bei "es kommt darauf an!" abhängig von dem jeweiligen Fall.

# Verteilte Systeme

Verteiltes System = Zusmmenschluss unabhängiger Computer, die sich für den Benutzer als ein einziges System präsentieren.
- Menge interagierender Prozesse (oder Prozessoren), welche über keinen gemeinsamen Speicher verfügen und daher mit Nachrichten miteinander kommunizieren.

## Client-Server-System
Viele Clients greifen auf einen oder mehrere Server zu.

## Verteilte Anwendung
Durch die Programmierung der Anwendung wir das verteilte System erstellt.

## Verteiltes Betriebssystem
Das Betriebssystem selbst ist verteilt, für Benutzer und Anwendungen ist dies nicht sichtbar.

## Schichtenarchitektur

- Graphische Präsentation (Benutzeroberfläche)
- Benutzungsinterface (Interaktionssteuerung)
- Datenverarbeitung (Business-Logic)
- DBMS = Datenbankmanagementsystem
    - Datenmanagement
    - Daten

<img src="Bilder/Softwareschichten_einer_Anwendung.png" width=400>

> Verstecken von Komplexität nach unten (Obere Schichten können nur auf Schicht untendrunter zugreifen (z.B. durch Observer) )

## Monolitische Systeme


### Standalone-Systeme = Alles auf einem Rechner

<img src="Bilder/Monolith_Standalone.png" width=300>

### Komplette Anwendung läuft zentral auf einem Server

Interaktive Benutzung nur über "dumme" Terminals

<img src="Bilder/Monolith_zentral_auf_Server.png" width=300>

## Client/Server-Systeme

Leistungsfähige PCs ermöglichen Ausführung auf Nutzerrechnern
- Bsp. Fileserver, Datenbank-Server, Workflow-Engines

<img src="Bilder/Client_Server_Architekturen.png" width=400>

1. Logik auf Client
2. Logik in der Datenbank (Stored Procedures)
3. Logik und Anwendungssteuerung innerhalb des DBMS

## Klassische Two-Tier Architektur

<img src="Bilder/Klassische_two_tier_Architektur.png" width=400>

Vorteile
- Einfaches Programmiermodell
- Applikationslogik liegt hauptsächlich auf dem Client

Nachteile
- Hohe Anforderungen an die Client-Rechner (hohe Kosten)
- Nicht unbegrenzt skalierbar
- Softwareverteilungsproblem: Client-Versionen müssen alle synchron gehalten werden

## Verteilte-Anwendungen / Three-Tier / n-Tier Architekturen

<img src="Bilder/Verteilte_Anwendungen.png" width=400>

Verteilung bedeutet einfach nur, dass das Programm auf diverse Rechner verteilt wird.
Verteilung kann sowohl Vertikal, Horizontal als auch in beiden Dimensionen erfolgen.

Vertikale Verteilung = Schichten liegen auf unterschiedlichen Rechnern
Horizontale Verteilung = Anzahl der Schichten
<img src="Bilder/Horizontal_Vertikal_verteiltes_System.png" width=400>

Aufteilung in DB, Logik(Backend) und Presentation(Frontend)
> So funktionieren die meisten Webseiten/Systeme
<img src="Bilder/Klassische_three_tier_Architektur.png" width=400> 


## Middelware

Die Middleware liegt zwischen den Betriebssystemen (OS) der Rechner (Rechner A, B, C) und der verteilten Anwendung. Sie bietet Middleware-Services, die die Kommunikation und Koordination zwischen verschiedenen Rechnern in einem verteilten System ermöglichen.

- Namensdienst
- Kommunikationsdienst
- Filedienst
- Zeitdienst
- Konkurrenzdienst
- Transaktionsdienst
- Sicherheitsdienst

Zusammengefasst bieten Middleware-Services die Infrastruktur, die die Entwicklung und den Betrieb verteilter Anwendungen durch Standardfunktionen vereinfacht.

>Die Middleware kapselt die Anwendung von den benutzten Protokollschichten
<img src="Bilder/Middleware_OSI.png" width=300>

## Kommunikationsarten
<table>
<th>Synchron</th>
<th>Asynchron</th>
<tr>
<td>http, REST</td>
<td>fetch(JavaScript), JMS(Messaging)</td>
</tr>
</table>


<table>
<th>One-to-One</th>
<th>One-to-Many</th>
<tr>
<td>http, Rest, fetch, JMS mit Queues</td>
<td>Eventauslieferung beim Observer, JMS mit Topics</td>
</tr>
</table>

### Synchrone Kommunikation

<img src="Bilder/Synchrone_Kommunikation.png" width=400>

### Asynchrone Kommunikation 

<img src="Bilder/Asynchrone_Kommunikation.png" width=400>


### Kommunikationsfehler

Mögliche Fehler:
- Request geht verloren oder wird verzögert
- Response geht verloren oder wird verzögert
- Client oder Server sind zwischenzeitlich nicht verfügbar

> Hier ist wichtig wie damit umgegangen wird. Wird ein Request erneut gesendet, wie lange wartet man auf die Antwort, etc.

## RMI (Remote Method Invocation)

Java-Mechanismus zur Realisierung der Interprozesskommunikation

Mit Hilfe von RMI können Methoden entfernter Objekte aufgerufen werden
> Ziel: Aufruf sollte dabei möglichst transparent sein

### Architektur von RMI

Server = Dienstanbieter
Client = Dienstnehmer

(Bild einfügen)

Server muss in Middleware-Schichte verankert werden.


Verankerung des Servers in der Mittleware (RMI-Mechanismus) 
erfolgt über Vererbungskonventionen
> Marker Interface => Makiert, dass Klasse öffentlich zugänglich gemacht werden kann

>Naming Service = RMI Registry
>> Name, Remotereferenz









# Wahrscheinlich nicht Prüfungsrelevant aber interessant
API-Gateway = Früher. Session Dispatcher => Verteilt API-Anfragen auf die verteilten Backend-Dienste.

Idempotent = Mehrfachauslieferung macht keine Probleme



[^1]: Fähigkeit, dass Funktionen oder Methoden in verschiedenen Objekten unterschiedlich ausgeführt werden können