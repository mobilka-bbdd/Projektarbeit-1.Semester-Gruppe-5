***Beschreibung un Ziele:***\
Der Rechner soll die Auflagerreaktionen eines statisch bestimmten
Einfeldträgers mit und ohne Kragarm bestimmen.

Berücksichtigt werden Einzellasten und Gleichstreckenlasten in
beliebiger Lage auf dem Träger. Neben den Ergebnissen wird der
Rechenweg schrittweise dargestellt, damit die Berechnung
nachvollzogen und zum Lernen genutzt werden kann.

***Geplannte Eingaben:***\
- Gesamtlänge des Trägers
- Positionen der beiden Auflager
- Beträge und Positionen der Einzellasten
- Intensität sowie Anfang und Ende jeder Gleichstreckenlast
- Zugehörige Einheiten

Die Lage der Auflager ermöglicht die Berechnung von Trägern
mit und ohne Kragarm.

Dezimalzahlen mit Punkt und Komma werden akzeptiert.

***Eigabeprüfung:***\
Die Anwendung prüft:

- leere Eingabefelder
- nicht numerische Eingaben
- unzulässige Längen und Positionen
- übereinstimmende Auflagerpositionen
- ungültige Bereiche von Gleichstreckenlasten

Fehler werden verständlich erklärt, ohne dass die Anwendung abstürzt.

***Ergebnisse und Rechenweg:***\
Ausgegeben werden:

- die Auflagerreaktionen mit Einheit und sinnvoller Rundung
- die Ersatzkräfte der Gleichstreckenlasten und ihre Angriffspunkte
- die verwendeten Gleichgewichtsbedingungen
- eingesetzte Zahlen und Zwischenergebnisse
- die schrittweise Berechnung der Auflagerreaktionen

***Validierung:***\
Mindestens zwei Testfälle werden dokumentiert:

1. Eine vollständige Handrechnung.
2. Ein Beispiel aus der TM-Vorlesung oder einem Lehrbuch mit Quelle.

Sollwert, berechneter Istwert und Abweichung werden tabellarisch
verglichen. Die Testfälle werden zusätzlich in test_themen.py
ohne Benutzeroberfläche ausgeführt.

***Quellcode:***\
[Auflagerreaktionen am Einfeldträger](../Code/Auflagerreaktionen%20am%20Einfeldträger.py)
