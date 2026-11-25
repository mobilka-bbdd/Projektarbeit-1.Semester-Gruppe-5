# Datenformat zwischen Oberfläche und Rechenmodulen

Stand: 25.11.2026 · Version: 0.2 · Status: Überarbeiteter Vorschlag.
Gilt als Teamvereinbarung, sobald beide Teammitglieder zugestimmt haben.
Beschreibt geplante Schnittstellen, nicht den aktuellen Implementierungsstand.

## 1 Zweck und Grundsätze

Oberfläche (Flask) und Rechenmodule (`themen/`) werden von verschiedenen Personen geschrieben. Dieses Dokument legt Eingaben, Ergebnisse, Fehler und Zuständigkeiten fest.

- Themen: Auflagerreaktionen (Hauptthema), Schwerpunkt, Haftung und Reibung.
- Alle Module verwenden dieselben Funktionsnamen und gemeinsamen Rückgabeformate.
- Themenmodule enthalten weder Flask noch HTML noch Grafikbibliotheken.
- Die Oberfläche enthält keine fachliche Rechenlogik. Sie stellt Eingaben zusammen, ruft Module auf und zeigt deren Ausgaben an.
- Jede berechnete Größe wird durch eine Funktion mit Parametern und Rückgabewert ermittelt. Gemeinsame interne Funktionen werden von Ergebnis und Rechenweg verwendet; keine unabhängig doppelt implementierten Formeln.
- Kein `eval()` für Benutzereingaben.
- Erwartete Eingabefehler werden als `ok: False` zurückgegeben. Programmierfehler werden nicht pauschal als Eingabefehler verschluckt.
- Erste Zeile jedes Themenmoduls: `# Rechenkern M05-BD1-2627`.
- Gemeinsame Hilfsfunktionen stehen in `hilfen.py` im Hauptordner.
- Die nachfolgenden Feldnamen, Toleranzen, Einheitenoptionen und Modelleinschränkungen sind Teamentscheidungen. Nicht alle Details sind durch die Aufgabenstellung vorgegeben.

## 2 Funktionen jedes Themenmoduls

| Funktion | Aufgabe | Rückgabe |
|---|---|---|
| `pruefe_eingabe_bd1(daten)` | Struktur prüfen, Zahlen lesen, Einheiten normalisieren, fachlich prüfen | Prüf-Dictionary |
| `ermittle_ergebnis_bd1(daten)` | Prüfung und Berechnung | Ergebnis-Dictionary |
| `ermittle_rechenweg_bd1(daten)` | Prüfung und Schritte derselben Berechnung | Liste von Schritten |

Jedes Modul besitzt `EINGABEFELDER`.
Nur das Hauptthema benötigt `erstelle_uebung_bd1`, `pruefe_antwort_bd1` und `ERKLAERUNGEN`.

Alle drei Funktionen dürfen rohe oder bereits normalisierte Daten erhalten. Eine erneute Prüfung normalisierter Daten darf keine zweite Einheitenumrechnung auslösen.

## 3 Gemeinsame Rückgabeformate

### 3.1 Prüfung

```python
{"ok": True, "daten": {...}}
```

Bei Fehlern wird in allen Funktionen, die ein Fehler-Dictionary liefern, dasselbe Format verwendet:

```python
{
    "ok": False,
    "fehler": [
        {"feld": "einzellasten[0].kraft",
         "text": "Die Kraft der ersten Einzellast fehlt."}
    ]
}
```

- `fehler` ist eine nicht leere Liste von Dictionaries mit `feld` und `text`.
- `feld` bezeichnet den Pfad im Eingabe-Dictionary; `daten` bezeichnet einen allgemeinen Fehler.
- Meldungen sind verständliche ganze deutsche Sätze.
- Unabhängige Fehler werden gesammelt; Folgeprüfungen mit ungültigen Werten unterbleiben.
- Die Oberfläche zeigt mindestens alle Fehlermeldungen und ordnet sie, soweit möglich, den Feldern zu.

### 3.2 Berechnung

```python
{
    "ok": True,
    "daten": {...},
    "ergebnisse": [
        {"name": "B", "text": "Auflagerkraft B",
         "wert": 10 / 3, "einheit": "kN", "anzeige": "3,33 kN"}
    ],
    "extra": {},
    "hinweise": []
}
```

- `daten`: ausschließlich geprüfte Werte in internen Einheiten, einschließlich eines passenden `einheiten`-Dictionaries.
- `ergebnisse`: Liste; `name` ist innerhalb einer Berechnung eindeutig und stabil.
- `wert`: endliche Zahl ohne absichtliche Rundung; Grundlage für Tests und Weiterberechnungen. Gleitkommazahlen sind nicht mathematisch exakt.
- `einheit`: Ausgabeeinheit; dimensionslos wird als `""` dargestellt.
- `anzeige`: zentral in `hilfen.py` erzeugte Zeichenkette für die Darstellung.
- `extra`: definierte themenspezifische Zusatzdaten, keine HTML-Fragmente.
- `hinweise`: Liste deutscher Zeichenketten; nicht blockierende Modellhinweise oder Warnungen. Fehlen Hinweise, ist sie leer.
- Bei fehlerhaften Eingaben wird ausschließlich das Fehlerformat aus 3.1 geliefert; keine Teilergebnisse.

## 4 Rechenweg

```python
[
    {
        "titel": "Momentengleichgewicht um A",
        "text": "Die Summe aller Momente um A ist null.",
        "formel": "B = (F · a) / l",
        "einsetzen": "B = (10 kN · 2 m) / 6 m",
        "ergebnis": "B = 3,33 kN"
    }
]
```

- Pflicht: `titel`, `formel`, `einsetzen`, `ergebnis`; `text` ist optional.
- Formeln sind zunächst Klartext, kein HTML und kein ausführbarer Code.
- Eingesetzte Zahlen und Ergebnisse enthalten nachvollziehbare Einheiten.
- Alle fachlich erforderlichen Zwischenschritte werden gezeigt, nicht nur das Endergebnis.
- Bei falscher Eingabe wird `[]` geliefert. Die Oberfläche zeigt die Fehler aus `ermittle_ergebnis_bd1` und stellt eine leere Liste nicht als erfolgreiche Lösung dar.
- Ergebnis und Rechenweg verwenden dieselben internen Berechnungsfunktionen. Die Oberfläche rundet keine Werte für spätere Berechnungen vor.

## 5 Zahlen, Struktur, Einheiten und Rundung

### 5.1 Zahlen und Struktur

Akzeptiert: endliche Python-Zahlen (`int`, `float`, jedoch kein `bool`) oder numerische Zeichenketten wie `"0,71"`, `"0.71"`, `" 5 "`, `"-2"`, `"1e-3"`.

- Äußere Leerzeichen werden entfernt; Komma wird als Dezimaltrennzeichen akzeptiert.
- Keine Tausendertrennzeichen; `1,234` bedeutet 1.234, nicht 1234.
- Gemischte Trennzeichen (`1,234.56`), innere Leerzeichen, leere Werte, `None`, `NaN` und Unendlichkeit sind ungültig.
- Einheiten stehen separat, nicht im Zahlenfeld: `"5 kN"` ist keine Zahl.
- Das oberste Objekt ist ein Dictionary; Listen und Unterobjekte müssen den jeweiligen Schemas entsprechen.
- Pflichtfelder dürfen nicht fehlen. Unbekannte Schlüssel werden als Fehler gemeldet; bedingt unsichtbare Felder werden deshalb von der Oberfläche nicht übertragen.
- Unbekannte Aufgabentypen, Figurtypen, Auswahleinträge und Einheiten ergeben Fehler.
- Fehlende optionale Schlüssel erhalten nur die ausdrücklich dokumentierten Standardwerte.
- Geometrische Prüfungen erfolgen nach der Einheitenumrechnung.
- Nicht endliche Rechenergebnisse, etwa durch Überlauf, werden als verständliche Bereichsfehler gemeldet.

### 5.2 Interne Einheiten

| Größe | Intern / Ausgabe |
|---|---|
| Längen, Koordinaten, Radien | m |
| Fläche | m² |
| Winkel in Ein- und Rückgabedaten | grad / Anzeige ° |
| Masse | kg |
| Kräfte Auflager | kN |
| Streckenlast | kN/m |
| Kräfte Reibung | N |
| Reibungskoeffizienten | dimensionslos |

Der Begriff „intern“ bedeutet hier einheitliche Recheneinheiten pro Modul, nicht ausschließlich SI-Basiseinheiten.

Optionaler Eingabeschlüssel:

```python
"einheiten": {
    "laenge": "cm",
    "kraft": "N",
    "streckenlast": "N/m",
    "winkel": "grad",
    "masse": "kg"
}
```

- Nur zum Thema passende Einheitenschlüssel sind zulässig. Fehlende Einträge erhalten den jeweiligen internen Standard.
- `laenge` gilt einheitlich für ALLE Längen, Koordinaten, Positionen, Radien und Dreieckspunkte einer Aufgabe.
- `kraft` gilt für alle eingegebenen Kräfte derselben Aufgabe.
- `streckenlast` ist unabhängig von `kraft` und `laenge`; ihre Einheit wird ausdrücklich angegeben oder bleibt kN/m. Sie wird nicht aus anderen Einheiten abgeleitet.
- Unterstützte Optionen: Länge `m`, `cm`, `mm`; Kraft `N`, `kN`; Streckenlast `N/m`, `kN/m`, `N/mm`, `kN/cm`; Winkel `grad`, `rad`; Masse `kg`.
- Länge nach m: Faktoren 1, 0.01, 0.001.
- Kraft nach kN: N → 0.001; nach N: kN → 1000.
- Streckenlast nach kN/m: N/m → 0.001; kN/m → 1; N/mm → 1; kN/cm → 100.
- Winkel aus rad werden für normalisierte Daten in Grad umgerechnet. Trigonometrie und Euler-Eytelwein verwenden intern an der Rechenstelle Radiant.
- Normalisierte Daten enthalten nur zum Thema passende Einträge mit internen Einheiten. Alte Eingabeeinheiten dürfen nicht an bereits umgerechneten Zahlen hängen bleiben.
- Die Oberfläche zeigt immer die zu den Eingaben tatsächlich geltenden Einheiten. Einheitenauswahl ist eine zusätzliche Teamfunktion; feste, korrekt beschriftete Einheiten genügen als einfachere erste Implementierung.

### 5.3 Rundung

- Standard: zwei Nachkommastellen; Winkel: eine Nachkommastelle; Dezimalkomma.
- Bei sehr kleinen, von null verschiedenen Werten darf wissenschaftliche Schreibweise verwendet werden, damit kein irreführendes `0,00` entsteht.
- Negatives gerundetes Null wird als `0,00` angezeigt.
- Fachliche Berechnungen und Tests verwenden immer `wert`, niemals `anzeige`.

## 6 Themen

### 6.1 Auflagerreaktionen (`themen/auflager.py`, Hauptthema)

Modell: gerader statisch bestimmter Träger, Festlager A links vom Loslager B, nur senkrechte Kräfte und Gleichstreckenlasten. Die x-Achse beginnt am linken Trägerende. Positive Lasten wirken nach unten; positive Auflagerreaktionen nach oben.

```python
daten = {
    "laenge": "6,0",
    "lager_a": 0,
    "lager_b": 6,
    "einzellasten": [{"kraft": 10, "position": 2}],
    "streckenlasten": [{"q": 5, "von": 0, "bis": 4}]
}
```

Pflicht: `laenge`, `lager_a`, `lager_b`. Fehlende Lastlisten werden zu `[]`. Beide Listen dürfen leer sein; dann ergeben sich Nullreaktionen.

Prüfungen:

- `laenge > 0`; `0 <= lager_a < lager_b <= laenge`.
- Einzellasten: Pflichtfelder `kraft`, `position`; `0 <= position <= laenge`.
- Streckenlasten: Pflichtfelder `q`, `von`, `bis`; `0 <= von < bis <= laenge`.
- Negative Lastwerte sind zulässig und bedeuten Lasten nach oben. Nullwerte sind zulässig.
- Lasten dürfen auf Lagern und Kragarmen liegen; mehrere Lasten dürfen sich überlagern.

Rechenmodell:

```text
Q_i = q_i · (bis_i - von_i)
x_i = (von_i + bis_i) / 2
B = [Σ F_i · (x_i - lager_a) + Σ Q_j · (x_j - lager_a)]
    / (lager_b - lager_a)
A = Σ F_i + Σ Q_j - B
```

Ergebnisse in stabiler Reihenfolge: `A`, `B` (kN). Horizontale Reaktion `A_x = 0` wird in der Erklärung erwähnt, aber nicht als eigenes Ergebnis geliefert.

Negative Reaktionen werden ausgegeben, nicht automatisch verworfen. Modellannahme: Die idealisierten Lager können die berechnete Reaktionsrichtung aufnehmen. Bei negativer Reaktion enthält `hinweise` eine Warnung: Bei einem nicht zugfesten Kontaktlager wäre Abheben möglich; das lineare Modell prüft diesen Kontaktwechsel nicht.

```python
extra = {
    "traeger": {"laenge": 6.0, "lager_a": 0.0, "lager_b": 6.0},
    "ersatzkraefte": [
        {"q": 5.0, "von": 0.0, "bis": 4.0,
         "kraft": 20.0, "position": 2.0}
    ]
}
```

Die Oberfläche zeichnet aus normalisierten `daten`, `ergebnisse` und `extra`. Pfeilrichtungen berücksichtigen Vorzeichen. Die Skizze zeigt Träger, Lager, Lasten und Reaktionen und passt sich an Eingaben an.

#### Übungsmodus

```python
erstelle_uebung_bd1(zufall=None)
# Rückgabe:
{
    "daten": {...},
    "aufgabe": "Berechne die Auflagerkräfte A und B.",
    "gesucht": [{"name": "A", "einheit": "kN"},
                {"name": "B", "einheit": "kN"}],
    "loesung": {"A": 12.5, "B": 7.5}
}
```

- `zufall`: optionales `random.Random`-Objekt, zum Beispiel `random.Random(42)`. Ohne Objekt wird eine eigene Zufallsquelle verwendet.
- Derselbe Startzustand der Zufallsquelle erzeugt dieselbe Aufgabenfolge.
- Generator liefert ausschließlich gültige Aufgaben mit endlichen Lösungen.
- Erste Generatorversion: Träger ohne Kragarm, Länge 4–10 m, Lager an den Enden, eine positive Einzellast von 1–20 kN innerhalb des Trägers. Bereiche und Positionsraster werden im Code nachvollziehbar festgelegt; das beschränkt nicht den normalen Rechner.
- Die Referenzlösung wird über dieselbe Berechnungslogik ermittelt, nicht durch separat kopierte Formeln.
- `loesung` bleibt serverseitig und wird nicht als verstecktes Feld, JavaScript-Datum oder vollständiges Aufgabenobjekt vorab an den Browser gesendet.
- Kein ungeschütztes Speichern der Referenzlösung in der standardmäßigen clientseitigen Flask-Session. Serverseitige Zuordnung über Aufgaben-ID ist möglich.
- Der Lösungsweg kommt aus `ermittle_rechenweg_bd1(uebung["daten"])` und wird auf Wunsch angezeigt.

```python
pruefe_antwort_bd1(uebung, antwort, rel_tol=0.01, abs_tol=0.01)
# antwort: {"A": "12,5", "B": "7,4"}; Antworten immer in kN
# Bei gültigem Zahlenformat:
{
    "ok": True,
    "richtig": False,
    "einzeln": {
        "A": {"richtig": True, "soll": 12.5, "ist": 12.5},
        "B": {"richtig": False, "soll": 7.5, "ist": 7.4}
    },
    "rueckmeldung": "A stimmt. B weicht zu stark ab."
}
```

Prüfung je Antwort:

```text
abs(ist - soll) <= max(abs_tol, rel_tol * abs(soll))
```

`rel_tol=0.01` bedeutet 1 %; `abs_tol=0.01` bedeutet 0.01 kN. Beide sind endlich und nicht negativ. Die Oberfläche erklärt diese Regel. Der absolute Anteil deckt Nullwerte ab.

Fehlende, nicht numerische oder nicht endliche Antworten liefern das gemeinsame Fehlerformat, zum Beispiel `feld: "antwort.A"`. Eine falsche Zahl ist dagegen eine gültige Eingabe mit `ok: True` und `richtig: False`.

`einzeln.soll` ist eine serverinterne Prüfinformation; die Oberfläche zeigt Referenzwerte und Lösungsweg erst auf Wunsch und nicht automatisch bei jedem Fehlversuch.

```python
ERKLAERUNGEN = [
    {"begriff": "Auflagerreaktion", "text": "...", "quelle": "..."}
]
```

Erklärungen umfassen Begriffe, Vorzeichen, Gleichgewichtsbedingungen und verwendete Formeln. Quellen müssen vor Fertigstellung konkret ergänzt werden.

### 6.2 Schwerpunkt (`themen/schwerpunkt.py`)

```python
daten = {"teilflaechen": [
    {"typ": "rechteck", "x": 0, "y": 0,
     "breite": 4, "hoehe": 2, "aussparung": False},
    {"typ": "dreieck", "punkte": [[0, 2], [4, 2], [2, 5]],
     "aussparung": False},
    {"typ": "kreis", "x": 2, "y": 1,
     "radius": 0.5, "aussparung": True}
]}
```

- `teilflaechen`: Pflichtliste mit mindestens einem Element.
- `typ`: `rechteck`, `dreieck` oder `kreis`.
- `aussparung`: boolescher Wert; Standard `False`. Keine Zeichenketten wie `"false"` im Modulvertrag; die Oberfläche erzeugt echte boolesche Werte.
- Rechteck: `x`, `y` linke untere Ecke; `breite > 0`, `hoehe > 0`.
- Kreis: `x`, `y` Mittelpunkt; `radius > 0`.
- Dreieck: genau drei Punkte mit je zwei Koordinaten; Fläche größer null. Beide Umlaufrichtungen sind zulässig; allein `aussparung` bestimmt das Flächenvorzeichen.
- Negative Koordinaten sind zulässig.
- Ergebnisfläche `A > 0`; sonst Fehler, keine Division durch null.

Modellgrenzen: vorzeichenbehaftete Teilflächen, keine automatische Vereinigung oder Schnittflächenberechnung. Positive Teilflächen dürfen sich nicht überlappen. Aussparungen müssen vollständig in der Materialfläche liegen und dürfen nicht mehrfach abgezogen werden. Diese räumlichen Bedingungen werden in Version 1 nicht automatisch geprüft; die Oberfläche weist darauf hin. Numerische Größenprüfungen ersetzen keine geometrische Gültigkeitsprüfung.

Ergebnisse: `A` (m²), `x_s` (m), `y_s` (m).

```python
extra = {"tabelle": [
    {"nr": 1, "typ": "rechteck", "aussparung": False,
     "flaeche": 8.0, "x_s": 2.0, "y_s": 1.0,
     "moment_x": 16.0, "moment_y": 8.0}
]}
```

`moment_x` bezeichnet hier ausdrücklich `flaeche * x_s`, `moment_y` bezeichnet `flaeche * y_s` (je m³), NICHT Flächenträgheitsmomente oder eine Benennung nach der Bezugsachse. Diese Definition wird als Spaltenbeschriftung verwendet. Bei Aussparungen sind Fläche und Beiträge vorzeichenbehaftet, Schwerpunktkoordinaten bleiben geometrische Koordinaten.

```text
A = Σ A_i
x_s = Σ(A_i · x_i) / A
y_s = Σ(A_i · y_i) / A
```

Eine Grafik und ein Übungsmodus sind für diese Nebenthema-Version nicht vorgesehen.

### 6.3 Haftung und Reibung (`themen/reibung.py`)

`aufgabe` ist Pflicht und wählt genau eine Teilaufgabe. Gemeinsame Konstante: `g = 9.81 m/s²`.

#### Schiefe Ebene

```python
{"aufgabe": "schiefe_ebene", "masse": 50, "winkel": 25, "mu0": 0.4}
```

Alle gezeigten Felder sind Pflicht. Modell: Körper ohne zusätzliche äußere Kräfte auf einer Ebene; nur Gewicht, Normalkraft und Reibung.

- `masse > 0`, `0 <= winkel <= 90` Grad, `mu0 >= 0`.
- `mu0`: Haftreibungskoeffizient.
- Normalisierte Winkel in Grad; für sin, cos und atan entsprechend umrechnen.

```text
normalkraft = masse · g · cos(winkel)
hangabtriebskraft = masse · g · sin(winkel)
haftkraft_max = mu0 · normalkraft
grenzwinkel = atan(mu0), ausgegeben in Grad
```

Bei exakt 90 Grad wird die Normalkraft ausdrücklich als null behandelt, um Rundungsreste von cos(90°) zu vermeiden.

Ergebnisnamen in dieser Reihenfolge:

| name | Einheit | Bedeutung |
|---|---|---|
| `normalkraft` | N | Normalkraft |
| `hangabtriebskraft` | N | zum Haften erforderliche Gegenkraft |
| `haftkraft_max` | N | maximal verfügbare Haftkraft |
| `grenzwinkel` | ° | Grenzwinkel der Haftung |

`extra`:

```python
{"status": "haftet", "grenzfall": False, "g": 9.81}
```

Grenzfallvergleich: `math.isclose(hangabtriebskraft, haftkraft_max, rel_tol=1e-9, abs_tol=1e-9)` in N. Dieser numerische Vergleich ist NICHT der Antworttoleranz des Übungsmodus gleichzusetzen.

- Haftung, wenn die erforderliche Kraft kleiner ist oder beide Kräfte im obigen Sinn gleich sind.
- Bei Gleichheit: `status = "haftet"`, `grenzfall = True`; Hinweis „Grenzfall der Haftung“.
- Sonst: `status = "gleitet"`, `grenzfall = False`.
- Nach Beginn des Gleitens werden weder tatsächliche Gleitreibung noch Beschleunigung berechnet. `hangabtriebskraft` wird dann nicht als tatsächlich wirkende Haftreibung bezeichnet.

#### Seilreibung nach Euler-Eytelwein

```python
{"aufgabe": "seilreibung", "mu": 0.3,
 "umschlingung": 180, "bekannt": "S2", "kraft": 100}
```

Alle Felder sind Pflicht. `mu >= 0`, `umschlingung >= 0`, `kraft > 0`; `bekannt` ist `S1` oder `S2`.

- `mu`: Haftreibungskoeffizient im Grenzfall beginnenden Gleitens.
- `S1` ist die größere oder bei Faktor 1 gleich große Seilkraft; `S2` die kleinere.
- Eingabewinkel standardmäßig Grad. Für die Exponentialfunktion muss der normalisierte Winkel in Radiant umgerechnet werden.

```text
alpha_rad = radians(umschlingung)
faktor = exp(mu · alpha_rad)
S1 = S2 · faktor
S2 = S1 / faktor
```

Ergebnisse immer `S1`, `S2` in N, auch die bekannte Kraft. `extra = {"faktor": ..., "alpha_rad": ...}`. Der Rechenweg kennzeichnet die bekannte und die gesuchte Kraft.

Bei `mu=0` oder `umschlingung=0` gilt Faktor 1. Überlauf und numerisch nicht mehr darstellbare positive Kräfte führen zu einem Bereichsfehler mit Hinweis auf zu große Eingabekombinationen. Die Gleichung beschreibt den Grenzfall, nicht zwingend die tatsächlichen Kräfte jedes beliebigen ruhenden Seils.

## 7 Beschreibung der Eingabefelder

Die Oberfläche rendert einen gemeinsamen Satz von Feldarten. Neue Themen, die diese Feldarten nutzen, benötigen keine Änderung des Formular-Renderers. Neue Feldarten können eine Renderer-Erweiterung erfordern; die vorhandenen Themenmodule bleiben unverändert.

### 7.1 Feldschema

Jedes Feld hat `name`, `label`, `art`.

| Schlüssel | Bedeutung |
|---|---|
| `pflicht` | Standard True; bei unsichtbaren Feldern nicht angewendet |
| `standard` | optionaler Anfangswert; ersetzt keine Modulprüfung |
| `einheit` | angezeigte interne Standardeinheit bei Zahlen |
| `einheit_typ` | Bezug zu `einheiten`, z. B. `laenge` |
| `optionen` | bei Auswahl: Liste aus `wert` und `text` |
| `felder` | Felder eines Listenelements |
| `min_anzahl`, `max_anzahl` | optionale Listengrenzen; Standard 0 / unbegrenzt |
| `gilt_fuer` | Bedingungen im aktuellen Objekt, mit UND verknüpft |

Feldarten:

- `zahl`: Zahleneingabe; Punkt und Komma müssen bis zum Modul gelangen können.
- `auswahl`: definierte Optionen, keine freien Texte.
- `bool`: Checkbox; Ergebnis immer echtes `True` oder `False`.
- `liste`: wiederholbare Gruppe aus `felder`.
- `punkte3`: genau drei Punkte mit je x und y; Serialisierung als `[[x1,y1],[x2,y2],[x3,y3]]`. Dies ist eine zusätzliche fest definierte Feldart für Dreiecke.

Bedingungen gelten auf Wurzelebene für das Wurzel-Dictionary, innerhalb einer Liste für das jeweilige Listenelement. Unsichtbare Felder werden nicht übertragen. Bei einem Wechsel des Typs dürfen alte unsichtbare Werte nicht im Request verbleiben.

HTML-Formulardaten werden von der Oberfläche in die hier definierten verschachtelten Dictionaries und Listen umgebaut; numerische Texte bleiben bis zur Modulprüfung Texte. HTML-spezifische Feldnamen gehören nicht zur Rechenlogik.

### 7.2 Vollständige Felder: Auflager

```python
EINGABEFELDER = [
    {"name": "laenge", "label": "Trägerlänge", "art": "zahl", "einheit": "m", "einheit_typ": "laenge"},
    {"name": "lager_a", "label": "Position Lager A", "art": "zahl", "einheit": "m", "einheit_typ": "laenge"},
    {"name": "lager_b", "label": "Position Lager B", "art": "zahl", "einheit": "m", "einheit_typ": "laenge"},
    {"name": "einzellasten", "label": "Einzellasten", "art": "liste", "pflicht": False, "felder": [
        {"name": "kraft", "label": "Kraft", "art": "zahl", "einheit": "kN", "einheit_typ": "kraft"},
        {"name": "position", "label": "Position", "art": "zahl", "einheit": "m", "einheit_typ": "laenge"}
    ]},
    {"name": "streckenlasten", "label": "Gleichstreckenlasten", "art": "liste", "pflicht": False, "felder": [
        {"name": "q", "label": "Lastintensität", "art": "zahl", "einheit": "kN/m", "einheit_typ": "streckenlast"},
        {"name": "von", "label": "Anfang", "art": "zahl", "einheit": "m", "einheit_typ": "laenge"},
        {"name": "bis", "label": "Ende", "art": "zahl", "einheit": "m", "einheit_typ": "laenge"}
    ]}
]
```

### 7.3 Vollständige Felder: Schwerpunkt

```python
EINGABEFELDER = [
    {"name": "teilflaechen", "label": "Teilflächen", "art": "liste", "min_anzahl": 1, "felder": [
        {"name": "typ", "label": "Form", "art": "auswahl", "optionen": [
            {"wert": "rechteck", "text": "Rechteck"},
            {"wert": "dreieck", "text": "Dreieck"},
            {"wert": "kreis", "text": "Kreis"}
        ]},
        {"name": "aussparung", "label": "Aussparung", "art": "bool", "pflicht": False, "standard": False},
        {"name": "x", "label": "x", "art": "zahl", "einheit": "m", "einheit_typ": "laenge", "gilt_fuer": {"typ": ["rechteck", "kreis"]}},
        {"name": "y", "label": "y", "art": "zahl", "einheit": "m", "einheit_typ": "laenge", "gilt_fuer": {"typ": ["rechteck", "kreis"]}},
        {"name": "breite", "label": "Breite", "art": "zahl", "einheit": "m", "einheit_typ": "laenge", "gilt_fuer": {"typ": "rechteck"}},
        {"name": "hoehe", "label": "Höhe", "art": "zahl", "einheit": "m", "einheit_typ": "laenge", "gilt_fuer": {"typ": "rechteck"}},
        {"name": "radius", "label": "Radius", "art": "zahl", "einheit": "m", "einheit_typ": "laenge", "gilt_fuer": {"typ": "kreis"}},
        {"name": "punkte", "label": "Drei Eckpunkte", "art": "punkte3", "einheit": "m", "einheit_typ": "laenge", "gilt_fuer": {"typ": "dreieck"}}
    ]}
]
```

Bei `gilt_fuer` bedeutet ein einzelner Wert Gleichheit, eine Liste bedeutet Zugehörigkeit zu einer der angegebenen Optionen. Die Koordinatenbeschriftung erklärt abhängig vom Typ „linke untere Ecke“ oder „Mittelpunkt“.

### 7.4 Vollständige Felder: Reibung

```python
EINGABEFELDER = [
    {"name": "aufgabe", "label": "Aufgabentyp", "art": "auswahl", "optionen": [
        {"wert": "schiefe_ebene", "text": "Schiefe Ebene"},
        {"wert": "seilreibung", "text": "Seilreibung"}
    ]},
    {"name": "masse", "label": "Masse", "art": "zahl", "einheit": "kg", "einheit_typ": "masse", "gilt_fuer": {"aufgabe": "schiefe_ebene"}},
    {"name": "winkel", "label": "Neigungswinkel", "art": "zahl", "einheit": "grad", "einheit_typ": "winkel", "gilt_fuer": {"aufgabe": "schiefe_ebene"}},
    {"name": "mu0", "label": "Haftreibungskoeffizient", "art": "zahl", "gilt_fuer": {"aufgabe": "schiefe_ebene"}},
    {"name": "mu", "label": "Haftreibungskoeffizient", "art": "zahl", "gilt_fuer": {"aufgabe": "seilreibung"}},
    {"name": "umschlingung", "label": "Umschlingungswinkel", "art": "zahl", "einheit": "grad", "einheit_typ": "winkel", "gilt_fuer": {"aufgabe": "seilreibung"}},
    {"name": "bekannt", "label": "Bekannte Seilkraft", "art": "auswahl", "gilt_fuer": {"aufgabe": "seilreibung"}, "optionen": [
        {"wert": "S1", "text": "S1 (größere Kraft)"},
        {"wert": "S2", "text": "S2 (kleinere Kraft)"}
    ]},
    {"name": "kraft", "label": "Bekannte Kraft", "art": "zahl", "einheit": "N", "einheit_typ": "kraft", "gilt_fuer": {"aufgabe": "seilreibung"}}
]
```

Einheitenwähler werden pro Einheitenkategorie zentral angezeigt, nicht unabhängig je Listenelement. Wenn eine Version nur feste Einheiten unterstützt, sendet sie keine abweichenden Einheiteneinträge.

## 8 Themenregistrierung und Erweiterbarkeit

```python
THEMEN = {
    "auflager": {"name": "Auflagerreaktionen am Einfeldträger", "modul": auflager, "haupt": True},
    "schwerpunkt": {"name": "Schwerpunkt zusammengesetzter Flächen", "modul": schwerpunkt, "haupt": False},
    "reibung": {"name": "Haftung und Reibung", "modul": reibung, "haupt": False}
}
```

- Module werden in `app.py` importiert und an dieser einen Stelle registriert.
- Übersicht und Navigation entstehen aus `THEMEN`.
- Übungsmodus nur anbieten, wenn beide Übungsfunktionen vorhanden sind.
- Ergebnisdarstellung und Rechenweg sind allgemein; für die Hauptthema-Skizze ist eine eigene Darstellungsfunktion außerhalb des Rechenkerns zulässig.
- Weiteres Thema: Modul nach diesem Vertrag hinzufügen, importieren und registrieren; vorhandene Themenmodule bleiben unverändert.

## 9 Tests und Abnahme der Schnittstelle

Pflichtvalidierung laut Aufgabenstellung: je Thema mindestens zwei dokumentierte Fälle mit Soll, Ist und Abweichung, davon eine vollständige Handrechnung und ein Beispiel aus Vorlesung oder Lehrbuch mit Quelle. Dieselben Fälle laufen als Code in `test_themen.py` ohne Oberfläche.

Zusätzliche Teamtests:

- Beide Teilaufgaben der Reibung abdecken.
- Balken mit/ohne Kragarm, beide Lastarten, Vorzeichen und Nullreaktionen.
- Schwerpunkt mit jeder Figurart und Aussparung; ungültiges Dreieck und Gesamtfläche null.
- Zahlen mit Punkt/Komma; leere, falsche, nicht endliche Werte; fehlerhafte Datenstrukturen.
- Gleichwertige Eingaben in verschiedenen Einheiten liefern dieselben normalisierten Ergebnisse.
- Normalisierte Daten erneut prüfen/berechnen, ohne doppelte Umrechnung.
- Grenzfälle der Reibung, Winkel 0 und 90 Grad, Seilfaktor 1 und Überlauf.
- Übungsantworten richtig/falsch/ungültig, Nullreferenz und Toleranzgrenzen.
- Generator mit fester Zufallsquelle wiederholbar; erzeugte Aufgaben stets gültig.
- Rückgabeformate, Ergebnisnamen und notwendige Felder der Rechenschritte prüfen.

Numerische Tests vergleichen ungerundete `wert`-Werte mit ausdrücklich festgelegten Testtoleranzen, nicht Zeichenketten. Testtoleranzen sind getrennt von der didaktischen Antworttoleranz zu dokumentieren. Quellen und Sollwerte dürfen nicht allein aus der zu prüfenden Implementierung stammen.

## 10 Noch zu bestätigen und nicht Bestandteil dieses Vertrags

Vor gemeinsamer Umsetzung bestätigen:

- Zustimmung zu Version 0.2 durch beide Teammitglieder.
- Feste Einheiten zuerst oder sofort Einheitenauswahl.
- Unterstützung aller Feldarten einschließlich `punkte3` im Renderer.
- Vorgeschlagene Antworttoleranzen und Generatorbereiche.
- Modellgrenzen, insbesondere Flächenüberlappungen und negative Lagerreaktionen.
- Konkrete Fachquellen und Validierungsbeispiele.
- Grafikwerkzeug (z. B. SVG oder matplotlib) und davon abhängige Bibliotheken.
- Konkrete serverseitige Speicherung der Übungsaufgabe und Aufgaben-ID.

Dieses Dokument ersetzt nicht Konzept, User Flow, Wireframes, Landingpage, README, Usability-Test, vollständige Dokumentation oder Abgabeplanung. Die verpflichtende Projektstruktur umfasst zusätzlich insbesondere `docs/`. Optionale Erweiterungen wie PDF-Export oder dauerhafter Lernstand sind hier nicht vereinbart.

## 11 Änderungen gegenüber Version 0.1

- Einheitliche strukturierte Fehler und zusätzliche Hinweise.
- Präzise Normalisierung, Einheitenoptionen und Regeln gegen doppelte Umrechnung.
- Verbindliche Ergebnisnamen und Modellgrenzen für alle drei Themen.
- Vollständige Eingabefeldbeschreibungen einschließlich Auswahl, Checkbox, Bedingungen und Dreieckspunkten.
- Getrennte relative/absolute Antworttoleranz und Behandlung ungültiger Antworten.
- Serverseitige Referenzlösung und gemeinsame Berechnungslogik für Übungen.
- Ergänzte Struktur-, Grenzfall- und Schnittstellentests.

Grundlage: ursprüngliches `Datenformat.md` vom 25.11.2026 und Abgleich mit der Projektaufgabenstellung, insbesondere Abschnitte 1–4. Neue Details sind Vorschläge zur Teamvereinbarung und noch keine Aussage über vorhandenen Code.
