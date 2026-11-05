---
title: "Testing in Production: Risiken, Vorteile und Best Practices"
date: 2025-02-13T10:39:00+02:00
tags: ["Testing"]
image: prod_testing_img.png
draft: false
---

Testing in der Produktion wurde lange Zeit als riskant angesehen und führte oft
zu Diskussionen unter Entwicklern, Testern und Stakeholdern. In der heutigen,
schnelllebigen Technologiewelt wird es jedoch immer häufiger. Testing in der
Produktion bedeutet nicht, Tests in niedrigeren Umgebungen zu überspringen und
sich ausschließlich auf Tests in der Produktion zu verlassen. Es bedeutet
einfach, dass wir nach gründlichen Tests in der Staging- und anderen
Vorproduktionsumgebungen in der Produktion weiter validieren, um die
Zuverlässigkeit und Leistung in der realen Welt sicherzustellen.

## Verständnis von Testing in der Produktion

Testing in der Produktion bedeutet, Tests in einer Live-Umgebung durchzuführen.
Während Staging- und Entwicklungsumgebungen versuchen, die Produktion zu
imitieren, gelingt es ihnen oft nicht vollständig. Testing in der Produktion
hilft, diese Lücke zu überbrücken, und stellt sicher, dass Anwendungen unter
realen Bedingungen wie erwartet funktionieren.

## Wann Testing in der Produktion entscheidend ist

Manchmal reicht das Testen in niedrigeren Umgebungen nicht aus. Zum Beispiel:

- **Drittanbieter-Abhängigkeiten**: Einige Dienste, wie externe APIs, verhalten
  sich in der Produktion anders als in der Staging-Umgebung.
- **Echter Benutzerverkehr**: Leistungs- und Skalierbarkeitsprobleme werden erst
  deutlich, wenn echte Benutzer mit dem System interagieren.
- **Komplexe Microservices**: In Staging-Umgebungen fehlen oft die vollständigen
  Abhängigkeiten, die in der Produktion vorhanden sind.

Wenn das QA-Team sogar grundlegende manuelle Rauchtests in der Produktion
überspringt, besteht die Gefahr, dass Änderungen blind gefördert werden, die zu
kritischen Fehlern führen könnten. Die Validierung in der Produktion dient als
letzte Sicherheitsmaßnahme und hilft, unvorhergesehene Probleme zu erkennen, die
in niedrigeren Umgebungen aufgrund von Unterschieden in den Daten,
Verkehrsströmen oder Infrastrukturmerkmalen möglicherweise nicht auftauchen.

## Risiken von Tests in der Produktion und Strategien zur Minderung

| **Risiko**                | **Beschreibung** | **Strategie zur Minderung** |
|---------------------------|------------------|----------------------------|
| **Unbeabsichtigte Benutzerbeeinträchtigung** | Testaktionen könnten unbeabsichtigt sichtbare Änderungen für den Benutzer auslösen, wie Benachrichtigungen, E-Mails oder veränderte UI-Zustände. | Testkonten und Feature-Flags verwenden, reale Benutzer aus Testszenarien ausschließen und benutzerseitige Effekte überwachen. |
| **Datenkorruption**       | Testdurchführungen könnten echte Produktionsdaten ändern oder löschen und so Berichterstattung und Geschäftsoperationen beeinträchtigen. | Strikte Datenisolierung implementieren, synthetische oder nur-lesende Testdaten verwenden und geeignete Rollback-Mechanismen sicherstellen. |
| **Leistungsbeeinträchtigung** | Automatisierte oder manuelle Tests könnten zusätzliche Last erzeugen und das System für reale Benutzer verlangsamen. | Tests während Zeiten mit wenig Verkehr planen, Systemauslastung überwachen und Ratenbegrenzungen für Testdurchführungen setzen. |
| **Sicherheitsrisiken**    | Testdaten oder Skripte könnten unbeabsichtigt sensible Informationen offenlegen oder Sicherheitslücken auslösen. | Sicherheitsbest Practices befolgen, Testdaten säubern und geeignete Authentifizierung sowie Zugriffskontrollen für Tests verwenden. |
| **Falsch-positive/negative Ergebnisse** | Unterschiede zwischen Test- und realem Benutzerverhalten könnten zu irreführenden Testergebnissen führen, was unnötige Rollbacks oder unentdeckte Probleme zur Folge hat. | A/B-Tests, Shadow-Tests durchführen und Testergebnisse mit historischen Produktionsdaten vergleichen. |

## Vorteile von Testing in der Produktion

Trotz seiner Risiken hat Testing in der Produktion erhebliche Vorteile:

- **Validierung in der realen Welt**: Erfasst echte Interaktionen, die in
  niedrigeren Umgebungen nicht nachgebildet werden können.
- **Frühe Fehlererkennung**: Findet versteckte Probleme, bevor sie alle Benutzer
  betreffen.
- **Bessere Leistungsanalyse**: Stellt die Skalierbarkeit sicher, indem es unter
  realen Verkehrsbedingungen getestet wird.
- **Datengetriebene Entscheidungen**: Ermöglicht A/B-Tests und den Vergleich von
  Funktionen.

## Beispiele aus der Praxis, bei denen Testing in der Produktion entscheidend war

Testing in Produktionsumgebungen kann Probleme aufdecken, die beim Testen in
Vorproduktionsumgebungen möglicherweise nicht auftreten. Hier sind zwei reale
Szenarien, die dies veranschaulichen:

### Abstürze der mobilen App aufgrund von Netzwerkvariabilität

Die mobile App einer politischen Kampagne, "Campaign Sidekick", erlebte
erhebliche Probleme während der Feldoperationen. Die App, die dazu entwickelt
wurde, Wahlhelfer zu verfolgen, war auf eine schnelle Internetverbindung
angewiesen. In ländlichen Gebieten mit langsamen oder unzuverlässigen
Internetverbindungen hatte die App Schwierigkeiten, richtig zu funktionieren,
was zu Daten-Upload-Fehlern und Fehlern bei der Aufgaben-Neuzuweisung führte.
Dies führte zu verschwendeten Bemühungen und unvollständigen Wählerkontakten.
Die Probleme wurden durch die Unfähigkeit der App, effektiv offline zu arbeiten,
verschärft, was die Bedeutung von Tests unter verschiedenen realen Bedingungen
unterstreicht.  
[Quelle](https://www.tothenew.com/blog/how-network-variability-impacts-mobile-applications)

### Windows Blue Screen of Death (BSOD) durch fehlerhaftes Update

Im August 2014 veröffentlichte Microsoft ein Sicherheitsupdate (MS14-045), das
darauf abzielte, Schwachstellen im Windows-Kernel zu beheben. Das Update führte
jedoch unbeabsichtigt dazu, dass einige Systeme abstürzten und den "Blue Screen
of Death" (BSOD) anzeigten. Dieses Problem wurde auf einen fehlerhaften Patch
zurückgeführt, der in Produktionsumgebungen nicht ausreichend getestet worden
war. Der Vorfall unterstreicht die kritische Notwendigkeit für gründliches
Testing von Updates in der Produktion, um Systeminstabilität und Datenverlust zu
verhindern.  
[Quelle](https://testrigor.com/blog/microsofts-blue-screen-of-death/)

Diese Beispiele zeigen die Bedeutung von Tests in der Produktion, um Probleme zu
erkennen, die in kontrollierten Testumgebungen möglicherweise nicht
offensichtlich sind. Die Implementierung robuster Testprotokolle und
Überwachungssysteme kann helfen, solche Risiken zu mindern.

## Fazit

Testing in der Produktion ist keine leichtsinnige Praxis mehr, sondern eine
Notwendigkeit in der modernen Softwareentwicklung. Wenn es richtig durchgeführt
wird, hilft es, reale Probleme frühzeitig zu erkennen, die Softwarequalität zu
verbessern und die Benutzererfahrung zu optimieren. Es erfordert jedoch die
richtigen Werkzeuge, Best Practices und ein gut vorbereitetes Team.  
Durch die Kombination von Automatisierung, Überwachung und Feature-Flags können
Unternehmen sicher in der Produktion testen und hochwertige Software mit
Vertrauen liefern.
