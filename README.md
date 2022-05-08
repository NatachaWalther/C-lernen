# C++-lernen

```cpp
// Code here
```

# 1 Es geht los!
# 2 Programmstrukturierung
# 3 Objektorientierung 1
# 4 Zeiger
# 5 Objektorientierung 2
# 6 Vererbung
# 7 Fehlerbehandlung
# 8 �berladen von Operatoren

Den vordefinierten Operatorsymbolen in C++ kann man f�r Klassen neue Bedeutungen
zuordnen. Damit k�nnen Operationen mit Objekten auf sehr einfache Weise ausgedr�ckt
werden.

Operator Functions define how a certain operator will behave with a specific datatype.
4 + 5 -> Compiler knows how to behave with 2 integers
car1 + car2 -> what will happen wand what will the + operater do?


# 9 Dateien und Str�me

Die C++-Standardbibliothek stellt die notwendigen Mechanismen f�r die Ein- und Ausgabe von Grunddatentypen 
zur Verf�gung, erlaubt aber auch die Konstruktion eigener Ein- und Ausgabefunktionen f�r selbst definierte Klassen.

Auf der untersten Ebene wird ein *stream* als Strom oder Folge von Bytes aufgefasst.

![](https://i.stack.imgur.com/dXhXP.png)

```cpp
// Auszug aus <iostream>:
namespace std {
extern istream cin; // Standardeingabe
extern ostream cout; // Standardausgabe
extern ostream cerr; // Standardfehlerausgabe
extern ostream clog; // gepufferte Standardfehlerausgabe
}
```




## 9.1 Eingabe

Klasse *istream* enth�lt Operatoren f�r die Grunddatentypen die die eingelesenen Zeichen in den richtigen Datentyp umwandeln.

```cpp
istream& operator>>(char*); // C-Strings
istream& operator>>(char&);
istream& operator>>(float&);
istream& operator>>(int&);
//... usw.
```
Der Operator *>>* ist ein �berladener Operator. F�r einen Datentyp T (T steht f�r einen der Grunddatentypen) sei
hier das Schema der Definition gezeigt:


```cpp
istream& istream::operator>>(T& var) {
// �berspringe Zwischenraumzeichen
// lies die Variable var des Typs T aus dem istream ein
return *this;
}
```
Wie bei der Ausgabe bewirkt die R�ckgabe einer Referenz auf istream, dass mehrere
Eingaben verkettet werden k�nnen:

```cpp
cin >> a >> b; // ist gleichbedeutend mit
(cin >> a) >> b; // und wird interpretiert als
(cin.operator>>(a)).operator>>(b);
```
Falls Zeichen oder Bytes eingelesen und Zwischenraumzeichen nicht ignoriert werden
sollen, kann die Elementfunktion istream& get() genommen werden, die in mehreren
�berladenen Versionen bereitsteht, von denen eine ein einzelnes Zeichen einliest, wie
das Beispiel zeigt.

```cpp
// zeichenweises Kopieren der Standardeingabe
char c;
while (cin.get(c)) { // true, falls kein Fehler auftritt (vgl. 9.7)
cout << c;
}
// ebenfalls m�glich ist:
while (cin.get(c)) {
cout.put(c);
}
```
Zum sicheren Einlesen einer Zeichenkette in einen Pufferbereich wird einer anderen
�berladenen Version von get() der Zeiger auf den Pufferbereich und die Puffergr��e
mitgegeben, die wegen des abschlie�enden �\0�-Zeichens um eins gr��er als die Anzahl
der maximal einzulesenden Elemente sein muss. Die Deklaration im Header <iostream>
lautet istream& get(char*, streamsize, char t=�\n�);. streamsize ist ein implementationsabh�ngiger vorzeichenbehafteter Ganzzahltyp, zum Beispiel long int. Die Zeichenkette wird bis zu einem festzulegenden Zeichen t �bernommen (t steht f�r �Terminator�),
wobei das Terminatorzeichen im Eingabestrom verbleibt. Es ist mit der Zeilenendekennung vorbesetzt. Beispiel:


```cpp
// Zeile einlesen (max. n-1 Zeichen):
constexpr int n{100};
char buf[n];
cin >> buf; // unsicher und Abbruch beim ersten Zwischenraumzeichen
cin.get(buf, n); // sicher wegen L�ngenbegrenzung auf n-1 Zeichen
// ... hier ggf. Terminatorzeichen lesen
```


```istream& putback(char c);```

Diese Funktion gibt ein Zeichen c an den istream zur�ck. 

Die Funktion

```int get();```

holt das n�chste Zeichen und gibt es als int-Wert entsprechend seiner Position in der
ASCII-Tabelle zur�ck. Bei EOF wird �1 zur�ckgegeben. Man k�nnte die Standardeingabe
daher auf folgende Art kopieren:

```cpp
int i;
while (cin.good() && (i = cin.get()) != EOF) { // d.h. weder Lesefehler noch EOF
 downloaded from www.hanser-elibrary.com by Fernfachhochschule Schweiz (FFHS) on January 18, 2022
For personal use only.
9.2 Ausgabe 417
cout.put(static_cast<char>(i));
}
```
Eine andere M�glichkeit ist die Abfrage mit eof(), eine Funktion, die true zur�ckgibt,
wenn ein Leseversuch wegen Erreichen des Dateiendes erfolglos bleibt:
```cpp
char c;
while (!cin.eof()) { // besser: while (cin.good()) ...
	cin.get(c);
	if (!cin.fail()) { // EOF ist m�glich, deswegen nicht if (cin.good())
		cout.put(c);
	}
}
```
```cin.good()``` ist vorzuziehen, weil nicht nur EOF, sondern auch Lesefehler ber�cksichtigt
werden. Eine Vorschau auf das n�chste Zeichen im Eingabestrom wird durch die Funktion
```int peek();```
erlaubt. Die Wirkung von ```c = cin.peek()``` ist wie die Hintereinanderschaltung der Anweisungen (mit impliziter Typumwandlung):
```cpp
c = cin.get();
cin.putback(c);
```
## 9.2 Ausgabe

Klasse *ofstream* umfasst �berladene Operatroen f�r eingebaute Datentypen. Der Operator *<<* wandelt ein Objekt eines eingebauten Datentype in eine Zeichenfolge um.

```cpp
ostream& operator<<(const char*); // C-Strings
ostream& operator<<(char);
ostream& operator<<(int);
ostream& operator<<(float);
ostream& operator<<(double);
//... usw.
```
Der R�ckgabetyp des Operators ist eine Referenz auf ostream. Das Ergebnis des Operators
ist das ostream-Objekt selbst, sodass ein weiterer Operator darauf angewendet werden
kann. Damit ist die Hintereinanderschaltung von Ausgabeoperatoren m�glich:
```cerr << "x= " << x;```
wird interpretiert als
```(cerr.operator<<("x= ")).operator<<(x);```

## 9.3 Formatierung mit std::format

Der Funktion werden ein Format-String
(Typ string_view) sowie die auszugebenden Daten �bergeben. Der Format-String kann
Text enthalten, aber auch Platzhalter f�r die Daten in Form von geschweiften Klammern
{} (in der einfachsten Form). Im folgenden Beispiel werden die Platzhalter der Reihe nach
durch die Daten ersetzt:

```cpp
string msg = format("{} Kaffee bitte und {} {}\n", 2, 1, "Wasser");
```

Die Ausgabe des Strings msg ergibt 2 Kaffee bitte und 1 Wasser. Die automatische Indizierung kann durch eine manuell festgelegte Reihenfolge ersetzt werden, wobei die
Nummerierung mit 0 beginnt. So bewirkt
```cpp
cout << format("{1} Kaffee bitte und {0} {2}\n", 2, 1, "Wasser");
```
die Ausgabe *1 Kaffee bitte und 2 Wasser*. Platzhalter k�nnen wiederholt werden. 


```cpp

```


```cpp

```


```cpp

```

```cpp

```


```cpp

```


```cpp

```


```cpp

```


```cpp

```


```cpp

```


```cpp

```


```cpp

```


```cpp

```





# 10 Die Standard Template Library (STL)
# 11 
# 12 Lambda-Funktionen
# 13 
# 14
# 15 Threads und Coroutinen
# 16 
# 17
# 18
# 19
# 20
# 21
# 22
# 23
# 24 Datei- und Verzeichnisoperatoren
# 25 Aufbau und �bersicht
# 26 Hilfsfunktionen und Klassen
# 27 Container
# 28 Iteratoren
# 29 Algorithmen
# 30
# 31
# 32
# 33
