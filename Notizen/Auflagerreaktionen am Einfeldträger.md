## Auflagerreationen am Einfeldträger

***Rolle im Projekt:***\
Dieses Thema wird als Hauptthema vollständig ausgearbeitet.

***Beschreibung un Ziele:***\
Der Rechner soll die Auflagerreaktionen eines statisch bestimmten
Einfeldträgers mit und ohne Kragarm bestimmen.

Berücksichtigt werden Einzellasten und Gleichstreckenlasten in
beliebiger Lage auf dem Träger. Neben den Ergebnissen wird der
Rechenweg schrittweise dargestellt, damit die Berechnung
nachvollzogen und zum Lernen genutzt werden kann.

***Geplannte Eingaben:***
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

***Zusätzlicher Funktionsumfang als Hauptthema:***
1. *Dynamische Systemskizze:*\
	Die Anwendung zeigt den Träger mit seinen Auflagern, Einzellasten,
	Gleichstreckenlasten und Auflagerreaktionen.

	Die Darstellung passt sich an die eingegebenen Werte an und
	berücksichtigt Träger mit und ohne Kragarm.
2. *Erklärungen:*\
	Die verwendeten Begriffe und Formeln werden kurz erläutert.
	Die fachlichen Quellen werden angegeben.
3. *Übungsmodus:*\
	Die Anwendung erzeugt Aufgaben mit zufälligen, sinnvollen Werten.
	Die Nutzer geben die gesuchten Auflagerreaktionen ein.

	Die Antworten werden mit einer festgelegten Toleranz geprüft.
	Anschließend erhalten die Nutzer eine Rückmeldung und können
	sich auf Wunsch den vollständigen Lösungsweg anzeigen lassen.

***Quellcode:***\
[auflager](../themen/auflager.py)
