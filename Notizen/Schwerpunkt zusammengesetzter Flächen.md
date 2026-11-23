***Beschreibung und Ziel:***\
Der Rechner soll den Flächenschwerpunkt zusammengesetzter ebener
Flächen bestimmen.

Die Gesamtfläche wird aus Rechtecken, Dreiecken und Kreisen
zusammengesetzt. Aussparungen werden ebenfalls berücksichtigt.

Die Anwendung zeigt den gesamten Flächeninhalt, die Koordinaten
des Gesamtschwerpunktes und einen nachvollziehbaren Rechenweg.

***Geplante Eingaben:*** \
Als Bedienkonzept ist vorgesehen:

- Auswahl des Teilflächentyps: Rechteck, Dreieck oder Kreis
- Eingabe der erforderlichen geometrischen Abmessungen
- Angabe der Lage im gemeinsamen Koordinatensystem
- bei Bedarf Angabe der Orientierun
- Kennzeichnung einer Teilfläche als Aussparung
- Angabe der Einheiten

Die genaue Eingabemaske wird im Oberflächenentwurf festgelegt.

Dezimalzahlen mit Punkt und Komma werden akzeptiert.

***Eingabeprüfung:***\
Die Anwendung prüft:
  
- leere Eingabefelder
- nicht numerische Eingaben
- unzulässige Abmessungen
- einen nicht positiven Gesamtflächeninhalt

Negative Koordinaten sind zulässig, sofern sie zur Lage der
Teilfläche im gewählten Koordinatensystem passen.
  
Fehler werden verständlich erklärt, ohne dass die Anwendung abstürzt.

***Ergebnisse und Rechenweg:***\
Ausgegeben werden:

- eine Tabelle der Teilflächen mit Flächeninhalt und
  Schwerpunktkoordinaten
- die Kennzeichnung der Aussparungen durch negative Flächenanteile
- der gesamte Flächeninhalt
- die Koordinaten des Gesamtschwerpunktes
- Formeln, eingesetzte Zahlen und Zwischenergebnisse

Die Ergebnisse werden mit Einheiten und sinnvoller Rundung angezeigt.

***Validierung:***\
Mindestens zwei Testfälle werden dokumentiert:

1. Eine vollständige Handrechnung.
2. Ein Beispiel aus der TM-Vorlesung oder einem Lehrbuch mit Quelle.

Sollwert, berechneter Istwert und Abweichung werden tabellarisch
verglichen. Die Testfälle werden zusätzlich in test_themen.py
ohne Benutzeroberfläche ausgeführt.

***Quellcode:***\
[schwerpunkt](../themen/schwerpunkt.py)