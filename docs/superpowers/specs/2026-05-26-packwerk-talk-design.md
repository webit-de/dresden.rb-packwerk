# Design: Packwerk Talk — Dresden.rb

## Kontext

- **Veranstaltung:** Dresden.rb Ruby User Group
- **Zielgruppe:** Techies / Ruby-Entwickler, kennen den Namen Packwerk, haben es aber nicht eingesetzt
- **Dauer:** ~30 Minuten
- **Format:** Vortrag mit Live-Demo
- **Sprecher:** Steve Reinke (webit) — produktiver Einsatz von Packwerk im eigenen Projekt

---

## Titelideen

**Technisch + neugierig:**
- „Modular Monoliths in Rails mit Packwerk"
- „Rails ohne Big Ball of Mud"
- „Grenzen ziehen im Rails-Monolithen"
- „Packwerk in der Praxis"

**Provokativ / Meetup-Style:**
- „Packwerk kann deinen Monolithen nicht retten"
- „Der Mythos des modularen Monolithen"
- „Wie Shopify versucht hat, Rails skalierbar zu halten"

---

## Roter Faden

Der Talk beantwortet eine größere Frage:

> „Wie hält man große Rails-Monolithen langfristig wartbar?"

Packwerk ist das Werkzeug innerhalb dieser Story — kein Feature-Vortrag, sondern ein Architektur-Talk.

**Hauptthese:**

> *„Rails skaliert technisch erstaunlich gut. Was nicht skaliert, sind implizite Abhängigkeiten zwischen Teams und Domänen."*

**Kernsatz:**

> *„Packwerk löst keine schlechte Architektur. Es macht schlechte Architektur sichtbar."*

---

## Hauptbotschaft

Das Publikum verlässt den Raum mit dem Gefühl: „Ich sehe, wie Packwerk echte Architekturprobleme löst."

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
2. **Das Bild des wachsenden Monolithen** — am Anfang alles einfach, nach 3 Jahren:
   - `User` kennt plötzlich `Billing`
   - `Billing` kennt `Notifications`
   - `Notifications` greift auf `Orders` zu
   - Alles hängt von allem ab, niemand weiß mehr was „public API" ist
3. **Konkretes Symptom** — eine Änderung in einem Modell bricht 5 unerwartete Tests in völlig anderen Bereichen
4. **Kernaussage** — „Ruby gibt euch Freiheit. Große Teams bezahlen dafür."
5. **Überleitung** — „Wie macht man unsichtbare Abhängigkeiten sichtbar — und durchsetzbar?"

---

## Block 2: Packwerk-Konzept (5 min)

**Ziel:** Die Kernidee verstehen — ohne in der Doku zu ertrinken.

1. **Herkunft** — Shopify entwickelte es für ihren Multi-Millionen-Zeilen-Monolithen. Nicht wegen Ruby-Syntax — wegen Team- und Skalierungsproblemen.

2. **Analogie in einem Satz** — „Packwerk ist ESLint für Architekturgrenzen."

3. **Drei Kernkonzepte** (je ~30 Sekunden):
   - **Package** — Verzeichnis mit `package.yml`, definiert eine fachliche Einheit (z.B. `packs/billing`)
   - **Privacy** — Klassen können `private` sein: nur das eigene Package darf sie nutzen
   - **Dependency** — ein Package deklariert explizit, welche anderen Packages es nutzen darf

4. **Das Werkzeug** — statischer Analyse-Checker: liest Code, meldet Verstöße — keine Runtime-Magie, kein Framework-Lock-in

5. **`package_todo.yml`** — bestehende Verstöße werden dokumentiert, nicht sofort behoben: pragmatischer Einstieg in Legacy-Projekten

6. **Einordnung im Vergleich:**

| Ansatz | Vorteil | Nachteil |
|---|---|---|
| Klassischer Rails-Monolith | Schnell | Entropie |
| Rails Engines | Stärkere Isolation | Schwergewichtiger |
| Microservices | Klare Grenzen | Operative Hölle |
| Packwerk | Leichte Boundary Enforcement | Nur statische Analyse |

---

## Block 3: Live Demo (10 min)

**Ziel:** Packwerk in Aktion — Violations entstehen, Packages werden definiert, Regeln greifen.

**Empfehlung:** Vorbereitetes Demo-Repo verwenden, nicht den echten webit-Code (sauberer, kein Risiko von Credentials im Screen-Share).

**Demo-Struktur:**
```
packs/
  billing/
  orders/
  users/
```

**Ablauf:**
1. **Ausgangslage zeigen** (1–2 min) — Rails-Projekt ohne Packages, `orders` benutzt intern etwas aus `billing`
2. **Package anlegen & ersten Check** (2 min) — `packs/billing` mit `enforce_privacy: true` und `enforce_dependencies: true`, dann `packwerk check` — Violations erscheinen
3. **Violation lesen** (2 min) — eine Violation erklären: was bedeutet die Meldung, welche Zeile, warum ist das ein Problem?
4. **`packwerk update`** (1 min) — `package_todo.yml` generieren: bestehende Violations dokumentieren statt blocken
5. **Eine Violation beheben** (2 min) — Public API definieren, Dependency erlauben, `packwerk check` wird grün

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
- Teams umgehen Regeln irgendwann — Werkzeug ersetzt keine Teamkultur

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

**Abschluss-Satz mit aktuellem Bezug:**
> *„Wenn AI schneller Code produziert, brauchen wir bessere Grenzen zwischen diesem Code."*

Dann Q&A öffnen.

---

## Was im Vortrag vermieden werden sollte

- Zu viel YAML — langweilt sofort
- Zu viele Internals (AST Parsing etc.) — nur kurz erwähnen
- Zu akademisch — Ruby User Groups mögen pragmatische Talks
- Folienstapel mit 10 Links am Ende
