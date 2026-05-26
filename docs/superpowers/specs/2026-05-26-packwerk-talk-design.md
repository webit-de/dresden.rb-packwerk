# Design: Packwerk Talk — Dresden.rb

## Kontext

- **Veranstaltung:** Dresden.rb Ruby User Group
- **Zielgruppe:** Techies / Ruby-Entwickler, kennen den Namen Packwerk, haben es aber nicht eingesetzt
- **Dauer:** ~30 Minuten
- **Format:** Vortrag mit Live-Demo
- **Sprecher:** Steve Reinke (webit) — produktiver Einsatz von Packwerk im eigenen Projekt

## Hauptbotschaft

> Das Publikum verlässt den Raum mit dem Gefühl: „Ich sehe, wie Packwerk echte Architekturprobleme löst."

Kernsatz für den Vortrag:

> *„Packwerk löst keine schlechte Architektur. Es macht schlechte Architektur sichtbar."*

---

## Struktur

| Block | Inhalt | Zeit |
|---|---|---|
| 1. Hook & Problem | Der Schmerz eines wachsenden Monolithen | 3–4 min |
| 2. Packwerk-Konzept | Was ist es, wie funktioniert es (begrifflich) | 5 min |
| 3. Live Demo | Packwerk in Aktion am echten Code | 10 min |
| 4. Erfahrungen aus der Praxis | Webit-Learnings + ehrliche Nachteile | 7–8 min |
| 5. Fazit & Q&A | Wann lohnt es sich, Ressourcen | 3 min |
| **Gesamt** | | **~28–30 min** |

---

## Block 1: Hook & Problem (3–4 min)

**Ziel:** Das Publikum fühlt den Schmerz, bevor die Lösung gezeigt wird.

1. **Einstiegsfrage** — „Wer hat schon mal in einem Rails-Projekt gearbeitet, wo niemand mehr wusste, was man noch anfassen darf?"
2. **Das Bild des wachsenden Monolithen** — am Anfang alles einfach, nach 3 Jahren hängt `User` an `Order` an `Billing` an `User` — niemand traut sich mehr zu refactoren
3. **Konkretes Symptom** — eine Änderung in einem Modell bricht 5 unerwartete Tests in völlig anderen Bereichen
4. **Überleitung** — „Wie macht man unsichtbare Abhängigkeiten sichtbar — und durchsetzbar?"

---

## Block 2: Packwerk-Konzept (5 min)

**Ziel:** Die Kernidee verstehen — ohne in der Doku zu ertrinken.

1. **Herkunft** — Shopify entwickelte es für ihren Multi-Millionen-Zeilen-Monolithen
2. **Drei Kernkonzepte** (je ~30 Sekunden):
   - **Package** — Verzeichnis mit `package.yml`, definiert eine fachliche Einheit (z.B. `packs/billing`)
   - **Privacy** — Klassen können `private` sein: nur das eigene Package darf sie nutzen
   - **Dependency** — ein Package deklariert explizit, welche anderen Packages es nutzen darf
3. **Das Werkzeug** — statischer Analyse-Checker: liest Code, meldet Verstöße — keine Runtime-Magie, kein Framework-Lock-in
4. **`package_todo.yml`** — bestehende Verstöße werden dokumentiert, nicht sofort behoben: pragmatischer Einstieg in Legacy-Projekten

---

## Block 3: Live Demo (10 min)

**Ziel:** Packwerk in Aktion — Violations entstehen, Packages werden definiert, Regeln greifen.

**Empfehlung:** Vorbereitetes Demo-Repo verwenden, nicht den echten webit-Code (sauberer, kein Risiko von Credentials im Screen-Share).

1. **Ausgangslage zeigen** (1–2 min) — Rails-Projekt mit gewachsenen Abhängigkeiten, noch ohne Packages
2. **Package anlegen & ersten Check** (2 min) — `packs/billing` mit `enforce_privacy: true` und `enforce_dependencies: true`, dann `packwerk check` — Violations erscheinen
3. **Violation lesen** (2 min) — eine Violation erklären: was bedeutet die Meldung, welche Zeile, warum ist das ein Problem?
4. **`packwerk update`** (1 min) — `package_todo.yml` generieren: bestehende Violations dokumentieren statt blocken
5. **Eine Violation beheben** (2 min) — live Umstrukturierung, `packwerk check` wird grün

---

## Block 4: Erfahrungen aus der Praxis (7–8 min)

**Einstieg mit dem Kernsatz** (groß auf der Folie, kurze Pause):

> *„Packwerk löst keine schlechte Architektur. Es macht schlechte Architektur sichtbar."*

**Kontext** (30 sek) — Projektgröße und Einsatzdauer bei webit

**Was gut funktioniert hat** (2–3 min)
- `package_todo.yml` als schrittweiser Einstieg war entscheidend
- *(Platzhalter — eigene Learnings)*
- *(Platzhalter — eigene Learnings)*

**Was schwieriger war als erwartet** (2–3 min)
- Die erste Package-Struktur war falsch — Umschneiden kostet Zeit
- Team-Buy-in braucht Überzeugungsarbeit
- *(Platzhalter — eigene Learnings)*

**Ehrliche Nachteile**
- Package-Grenzen sind sozial, nicht nur technisch — das ist eine Kommunikationsaufgabe, kein Tooling-Problem
- `package_todo.yml` kann zur Ausrede werden, Violations nie wirklich zu beheben
- Zu viele Packages machen alles schlimmer — Micro-Packaging ist ein Anti-Pattern
- „Modular Monolith" kann Buzzword werden ohne die Disziplin dahinter

**Schluss des Blocks:** „Ich zeige euch das nicht um euch abzuschrecken — sondern damit ihr realistisch einsteigt."

---

## Block 5: Fazit & Q&A (3 min)

**Wann lohnt sich Packwerk?**
- Rails-Monolith mit mehreren Teams oder mehr als einem Entwickler pro Bereich
- Wenn Architektur-Diskussionen immer wieder dieselben Probleme zeigen
- Wenn langfristig Services angestrebt werden, aber der Zeitpunkt noch nicht gekommen ist

**Wann lohnt es sich nicht?**
- Kleine Teams mit einem Codebase-Owner
- Projekte in früher Phase — erst das Produkt finden, dann Struktur

**Ressource:** Shopify Engineering Blog + GitHub-Repo (ein Link, kein Folienstapel)

**Abschluss-Satz:**
> *„Packwerk zeigt euch, was ihr schon immer wusstet — aber nie beweisen konntet."*

Dann Q&A öffnen.
