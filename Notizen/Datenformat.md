## Datenformat zwischen Oberfläche und Rechenmodulen

***Stand:*** 25.11.2026 · ***Status:*** Vorschlag. Gilt als Vereinbarung, sobald beide Teammitglieder zugestimmt haben.

***Zweck:***\
Oberfläche (Flask) und Rechenmodule (`themen/`) werden von verschiedenen Personen geschrieben. Dieses Dokument legt fest, was die Oberfläche an ein Modul übergibt und was das Modul zurückgibt. Wenn sich beide Seiten daran halten, passen die Teile zusammen.

## 1 Grundsätze

- Alle drei Themenmodule sind gleich aufgebaut: gleiche Funktionsnamen, gleiche Formate.
- Die Module enthalten keinen Oberflächencode (kein Flask, kein HTML, keine Grafikbibliothek).
- Die Oberfläche enthält keine Rechenlogik.
- Es wird kein `eval()` verwendet.
- Jede Größe wird in einer eigenen kleinen Funktion mit Parametern und Rückgabewert berechnet (Vorgabe der Aufgabenstellung). `ermittle_ergebnis_bd1` ruft diese Funktionen nur auf.
- Ein Modul gibt falsche Eingaben nicht als Absturz weiter, sondern als `ok: False` mit verständlichen Meldungen.
- Die erste Zeile jedes Themenmoduls lautet `# Rechenkern M05-BD1-2627`.

## 2 Funktionen jedes Themenmoduls

| Funktion | Aufgabe | Rückgabe |
|---|---|---|
| `pruefe_eingabe_bd1(daten)` | wandelt Text in Zahlen um (Punkt und Komma), prüft alle Werte | `{"ok": True, "daten": sauber}` oder `{"ok": False, "fehler": [...]}` |
| `ermittle_ergebnis_bd1(daten)` | ruft die Prüfung auf und berechnet alle Ergebnisse | Ergebnis-Dictionary (Abschnitt 3) |
| `ermittle_rechenweg_bd1(daten)` | gleicher Rechengang, aber als Schritte | Liste von Schritten (Abschnitt 4) |

Alle Module haben außerdem die Konstante `EINGABEFELDER` (Abschnitt 7).

Nur das Hauptthema (`auflager.py`) hat zusätzlich die Übungsfunktionen und die Konstante `ERKLAERUNGEN` (Abschnitt 6.1).

## 3 Ergebnis von `ermittle_ergebnis_bd1`

```python
# Eingaben in Ordnung
{
    "ok": True,
    "daten": {...},              # bereinigte Eingaben in Basiseinheiten (für Grafik und Rechenweg)
    "ergebnisse": [
        {
            "name": "B",                    # kurzer Name
            "text": "Auflagerkraft B",      # Beschreibung für die Anzeige
            "wert": 3.33333,                # ungerundet (für Tests)
            "einheit": "kN",
            "anzeige": "3,33 kN"            # gerundet, mit Komma (für die Oberfläche)
        }
    ],
    "extra": {...}               # themenspezifische Zusatzdaten (Abschnitt 6)
}

# Eingaben fehlerhaft
{
    "ok": False,
    "fehler": ["Die Trägerlänge muss größer als 0 sein."]
}
```

Regeln:

- `wert` ist immer ungerundet. Die Tests vergleichen mit `wert`.
- `anzeige` ist gerundet (Standard: 2 Nachkommastellen, Winkel: 1) und mit Komma geschrieben.
- Fehlermeldungen sind ganze deutsche Sätze und nennen das betroffene Feld.

## 4 Rechenweg von `ermittle_rechenweg_bd1`

Eine Liste von Schritten. Bei fehlerhaften Eingaben ist die Liste leer.

```python
[
    {
        "titel": "Momentengleichgewicht um A",
        "text": "Die Summe aller Momente um A ist null.",   # kurze Erklärung, darf fehlen
        "formel": "B = (F · a) / l",
        "einsetzen": "B = (10 · 2) / 6",
        "ergebnis": "B = 3,33 kN"
    }
]
```

Die Oberfläche zeigt alle Schritte bei allen Themen im gleichen Aussehen.

## 5 Zahlen, Einheiten, Rundung

- Eingabewerte dürfen Zahlen oder Text sein (`"0,71"`, `"0.71"`, `" 5 "`). Das Modul wandelt um. Leere Felder und Text, der keine Zahl ist, ergeben eine Fehlermeldung.
- Basiseinheiten: Länge in m, Winkel in Grad, Masse in kg. Kraft in kN (`auflager`) bzw. N (`reibung`).
- Optionaler Schlüssel `"einheiten"` in `daten`, zum Beispiel `{"laenge": "cm", "kraft": "N"}`. Das Modul rechnet in die Basiseinheiten um. Fehlt der Schlüssel, gelten die Basiseinheiten.
- Gemeinsame Hilfsfunktionen (Text in Zahl, Einheiten umrechnen, Anzeige runden) stehen in `hilfen.py` im Hauptordner, nicht im Ordner `themen/`. So bleibt `themen/` nur für Themenmodule.

## 6 Eingaben und Zusatzdaten je Thema

### 6.1 Auflagerreaktionen (`themen/auflager.py`, Hauptthema)

Konventionen: Die x-Achse beginnt am linken Trägerende. Lasten nach unten sind positiv, Auflagerkräfte nach oben sind positiv. Lager A ist das Festlager, Lager B das Loslager, es wirken nur senkrechte Lasten. Lager A liegt links von Lager B.

```python
daten = {
    "laenge": "6,0",
    "lager_a": 0,
    "lager_b": 6,       # liegt ein Lager nicht am Trägerende, entsteht ein Kragarm
    "einzellasten": [{"kraft": 10, "position": 2}],
    "streckenlasten": [{"q": 5, "von": 0, "bis": 4}]       # q in kN/m
}
```

Prüfungen unter anderem: Länge größer 0, beide Lager auf dem Träger, Lager nicht an gleicher Stelle, Lasten auf dem Träger, bei Streckenlasten `von` kleiner `bis`.

Ergebnisse: `A` und `B` in kN.

`extra`:

```python
{
    "traeger": {"laenge": 6.0, "lager_a": 0.0, "lager_b": 6.0},
    "ersatzkraefte": [{"q": 5.0, "von": 0.0, "bis": 4.0, "kraft": 20.0, "position": 2.0}]
}
```

Die Oberfläche zeichnet die Systemskizze aus `daten`, `ergebnisse` und `extra`.

Nur Hauptthema:

```python
erstelle_uebung_bd1(zufall=None)
# gibt zurück:
{
    "daten": {...},                  # Eingaben wie oben, sinnvolle Zufallswerte
    "aufgabe": "Berechne die Auflagerkräfte A und B.",
    "gesucht": [{"name": "A", "einheit": "kN"}, {"name": "B", "einheit": "kN"}],
    "loesung": {"A": 12.5, "B": 7.5}
}

pruefe_antwort_bd1(uebung, antwort, toleranz=0.01)
# antwort: {"A": "12,5", "B": "7,4"}
# gibt zurück:
{
    "richtig": False,
    "einzeln": {"A": {"richtig": True, "soll": 12.5, "ist": 12.5},
                "B": {"richtig": False, "soll": 7.5, "ist": 7.4}},
    "rueckmeldung": "A stimmt. B weicht zu stark ab."
}
```

- `zufall` ist optional (zum Beispiel `random.Random(42)`), damit Tests wiederholbar sind.
- Der Lösungsweg zur Übung kommt aus `ermittle_rechenweg_bd1(uebung["daten"])`.

Erklärungen für das Hauptthema:

```python
ERKLAERUNGEN = [
    {"begriff": "Auflagerreaktion", "text": "...", "quelle": "..."}
]
```

### 6.2 Schwerpunkt zusammengesetzter Flächen (`themen/schwerpunkt.py`)

```python
daten = {"teilflaechen": [
    # Rechteck: x, y = linke untere Ecke
    {"typ": "rechteck", "x": 0, "y": 0, "breite": 4, "hoehe": 2, "aussparung": False},
    # Dreieck: drei Eckpunkte
    {"typ": "dreieck", "punkte": [[0, 2], [4, 2], [2, 5]], "aussparung": False},
    # Kreis: x, y = Mittelpunkt
    {"typ": "kreis", "x": 2, "y": 1, "radius": 0.5, "aussparung": True}
]}
```

Koordinaten dürfen negativ sein. Mindestens eine Teilfläche. Die Gesamtfläche muss größer 0 sein. Ein Dreieck mit Fläche 0 ist ungültig.

Ergebnisse: `A` (Gesamtfläche, m²), `x_s` und `y_s` (m).

`extra`:

```python
{"tabelle": [
    {"nr": 1, "typ": "rechteck", "aussparung": False, "flaeche": 8.0, "x_s": 2.0, "y_s": 1.0}
]}
```

Bei Aussparungen ist `flaeche` negativ.

### 6.3 Haftung und Reibung (`themen/reibung.py`)

Der Schlüssel `"aufgabe"` wählt die Teilaufgabe.

```python
# Körper auf der schiefen Ebene (Basiseinheiten: kg, N, Grad)
{"aufgabe": "schiefe_ebene", "masse": 50, "winkel": 25, "mu0": 0.4}

# Seilreibung nach Euler-Eytelwein
{"aufgabe": "seilreibung", "mu": 0.3, "umschlingung": 180, "bekannt": "S2", "kraft": 100}
```

- Bei der Seilreibung ist S1 die größere Seilkraft (Zugseite) und S2 die kleinere. `bekannt` sagt, welche der beiden gegeben ist. Berechnet wird die andere. `mu` ist der Haftreibungskoeffizient (Grenzfall).
- Schiefe Ebene: Ergebnisse unter anderem Grenzwinkel (°) und nötige und maximale Haftkraft (N). `extra["status"]` ist `"haftet"` oder `"gleitet"`.
- Seilreibung: Ergebnis ist die gesuchte Seilkraft, `extra` enthält den Faktor e^(μ·α).

## 7 Eingabefelder für die Oberfläche

Damit ein weiteres Thema ohne Änderung der Oberfläche ergänzt werden kann, beschreibt jedes Modul seine Eingabefelder selbst. Die Oberfläche baut das Formular daraus.

```python
EINGABEFELDER = [
    {"name": "laenge", "label": "Trägerlänge", "einheit": "m", "art": "zahl"},
    {"name": "einzellasten", "label": "Einzellasten", "art": "liste",
     "felder": [
         {"name": "kraft", "label": "Kraft", "einheit": "kN"},
         {"name": "position", "label": "Position", "einheit": "m"}
     ]}
]
```

Optional hat ein Feld den Schlüssel `"gilt_fuer"`, zum Beispiel `{"aufgabe": "seilreibung"}`, damit Felder nur bei einer Teilaufgabe erscheinen (`reibung`).

## 8 Registrierung der Themen

Die Themen stehen an einer Stelle in `app.py`. Themenübersicht und Navigation entstehen daraus.

```python
THEMEN = {
    "auflager": {"name": "Auflagerreaktionen am Einfeldträger", "modul": auflager, "haupt": True},
    "schwerpunkt": {"name": "Schwerpunkt zusammengesetzter Flächen", "modul": schwerpunkt, "haupt": False},
    "reibung": {"name": "Haftung und Reibung", "modul": reibung, "haupt": False},
}
```

Die Oberfläche zeigt den Übungsmodus nur an, wenn das Modul `erstelle_uebung_bd1` hat.

## 9 Tests

- `test_themen.py` importiert die Module direkt und ruft `ermittle_ergebnis_bd1` mit `daten` auf. Es wird keine Oberfläche gestartet.
- Verglichen wird `wert` mit dem Sollwert. Ausgegeben werden Soll, Ist und Abweichung.
- Je Thema mindestens zwei Testfälle: eine Handrechnung und ein Beispiel aus Vorlesung oder Lehrbuch mit Quelle.

## 10 Offene Punkte

- Toleranz im Übungsmodus (Vorschlag: 1 %).
- Genaues Format von `EINGABEFELDER` mit der Oberfläche abstimmen.
- Grafikbibliothek für Skizzen (zum Beispiel matplotlib oder SVG im Browser). Davon hängt `requirements.txt` ab.
