***Rolle im Projekt:***\
Dieses Thema wird als weiteres Thema teilweise ausgearbeitet.

***Beschreibung und Ziel:***\
Der Rechner soll Aufgaben zur Haftung und Reibung behandeln.
Er umfasst einen Körper auf einer schiefen Ebene sowie die
Seilreibung nach Euler-Eytelwein.

Die Ergebnisse werden mit einem schrittweisen Rechenweg
dargestellt.

***Berechnungsumfang:***
1. *Körper auf der schiefen Ebene:*
	- Prüfen, ob der Körper haftet oder gleitet
	- Berechnung des Grenzwinkels
2. *Seilreibung:*
	- Berechnung nach Euler-Eytelwein

***Geplantes Eingabekonzept:***\
Für die schiefe Ebene sind die Eingabe der Masse, des
Neigungswinkels und des Haftreibungskoeffizienten vorgesehen.

Für die Seilreibung sind die Eingabe des Reibungskoeffizienten,
des Umschlingungswinkels und einer bekannten Seilkraft vorgesehen.
Die Zuordnung der Seilkräfte und der betrachtete Grenzfall
werden in der Eingabemaske eindeutig angegeben.

Die genaue Auswahl der Eingaben und berechenbaren Größen wird
bei der fachlichen Ausarbeitung festgelegt.

***Allgemeine Anforderungen:***
- Eingabe der Größen mit Einheiten
- Unterstützung von Dezimalzahlen mit Punkt und Komma
- Prüfung auf leere Felder, nicht numerische Eingaben und
  physikalisch unzulässige Werte
- Verständliche Fehlermeldungen ohne Programmabsturz
- Ergebnisse mit Einheiten und sinnvoller Rundung
- Rechenweg mit Formeln, eingesetzten Zahlen und Zwischenergebnissen
- Einheitliche Bedienung und Rückkehr zur Themenübersicht

***Validierung:***\
Mindestens zwei Testfälle werden dokumentiert:
1. Eine vollständige Handrechnung.
2. Ein Beispiel aus der TM-Vorlesung oder einem Lehrbuch mit Quelle.

Sollwert, Istwert und Abweichung werden tabellarisch verglichen.
Die Testfälle werden zusätzlich in test_themen.py ohne
Benutzeroberfläche ausgeführt.

Die Testauswahl soll beide Teilbereiche des Rechners abdecken.

***Quellcode:***\
[reibung](../themen/reibung.py)
