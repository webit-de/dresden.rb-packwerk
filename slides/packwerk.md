---
marp: true
theme: webit
paginate: true
---

<!-- _class: title -->

<div class="tag">Dresden.rb · 2026</div>

# Rails ohne Big Ball of Mud

<div class="subtitle">Packwerk in der Praxis</div>
<div class="speaker">Steve Reinke & Christoph Wagner · webit!</div>

<div class="deco"><span></span><span></span></div>

---

## Agenda

1. **Motivation** — Warum reicht Rails-Struktur allein nicht?
2. **Packwerk-Konzept** — Was ist es, wie funktioniert es?
3. **Wie setzen wir Packwerk bei webit! ein?** — Praxis & Erfahrungen
4. **Fazit & Q&A**

---

<!-- _class: section -->
<!-- header: "Motivation" -->

# Motivation

## Der Schmerz eines wachsenden Monolithen

---

## Wer kennt das?

> „Wer hat schon mal eine Codeänderung gemacht, bei der plötzlich Dinge umgefallen sind, mit denen du nichts zu tun haben wolltest?"

---

![bg contain](cohesion.png)

---

## Rails gibt Struktur — aber nicht genug

> **„Rails gibt euch eine Struktur für Dateien.**
> **Wer mit wem reden darf — das regelt niemand."**

MVC und Convention over Configuration lösen nicht das Problem von Abhängigkeiten.

---

## Warum nicht einfach Disziplin?

- Menschen vergessen Regeln
- Neue Entwickler kennen sie nicht
- Zeitdruck gewinnt immer

> **„Code Reviews skalieren schlechter als automatische Regeln."**

→ Wie macht man Abhängigkeiten **sichtbar — und durchsetzbar?**

---

<!-- _class: section -->
<!-- header: "Packwerk-Konzept" -->

# Packwerk-Konzept

## Was ist es, wie funktioniert es?

---

## Woher kommt Packwerk?

Shopify entwickelte es für ihren großen Rails-Monolithen — nicht wegen Ruby-Syntax, sondern wegen Team- und Skalierungsproblemen.

Veröffentlicht **September 2020.**

> **„Packwerk ist ein Linter für Architekturgrenzen."**

---

## Drei Kernkonzepte

**Paket** — Verzeichnis mit `package.yml`, definiert eine fachliche Einheit
`packs/billing`, `packs/users`, …

**Privacy** — Klassen können `private` sein: nur das eigene Paket darf sie nutzen

**Dependency** — ein Paket deklariert explizit, welche anderen Pakete es nutzen darf

---

## Das Werkzeug

**Statischer Analyse-Checker:** liest Code, meldet Verstöße
Keine Runtime-Magie · Kein Framework-Lock-in

*Packwerk bindet den Code nicht enger an Rails — und auch nicht an Packwerk selbst.*

**`package_todo.yml`**

Bestehende Verstöße werden dokumentiert, nicht sofort behoben — pragmatischer Einstieg in Legacy-Projekten.

---

## Einordnung im Vergleich

| Ansatz | Vorteil | Nachteil |
|--------|---------|----------|
| Rails-Monolith | Schnell | Entropie |
| Rails Engines | Stärkere Isolation | Schwergewichtiger |
| Microservices | Klare Grenzen | Operative Hölle |
| **Packwerk** | **Leichte Boundary Enforcement** | **Nur statische Analyse** |

---

<!-- _class: section -->
<!-- header: "webit! Einsatz" -->

# Wie setzen wir Packwerk bei webit! ein?

## Paketstruktur & Erfahrungen aus der Praxis

---

## JACK — Paketabhängigkeiten

JACK ist eine Plattform zur Planung und Auslieferung von Lebensmitteln für Großküchen.

```
ROOT / app/  (Controllers, Views, Jobs)
        │  nur über public/ und Events
        ├──▶ order_management
        ├──▶ portioning  ↔  material_tracking
        └──▶ commissioning  ↔  logistics

        alle abhängig von:
        events → architecture_core → core
```

---

## Aufbau eines Domänenpakets

Packwerk erzwingt Grenzen zwischen Domänen — und zwischen den **Schichten innerhalb** einer Domäne:

```
lib/packages/portioning/
├── public/           ← sichtbar für alle anderen Pakete
├── domain/           ← eigenes Paket · visible_to: [portioning]
├── infrastructure/   ← eigenes Paket · visible_to: [portioning]
├── application/      ← eigenes Paket · visible_to: [portioning]
└── core_extension/   ← geteilte Basistypen für andere Pakete
```

Cross-Paket-Referenzen nur über UUIDs — keine ActiveRecord-Assoziationen.

---

## JACK bei webit! — Kontext

- **23 Pakete** insgesamt
- **5 Domänenpakete** (order_management, portioning, commissioning, material_tracking, logistics)
- je mit Unterpaketen: `domain/`, `infrastructure/`, `application/`, `core_extension/`
- **3 Basis-Pakete:** `core`, `architecture_core`, `events`
- Packwerk **von Beginn an** im Einsatz

---

<!-- _class: quote -->

# „Packwerk löst keine schlechte Architektur. Es macht schlechte Architektur sichtbar."

---

## Was gut funktioniert hat

- Lose Kopplung der Domänenaspekte durch Pakete mit klar definierter public API
- Strikte Architekturregeln durch Unterpakete — Domänenschicht frei von externen Abhängigkeiten (DDD)
- Aufgaben gut parallelisierbar durch klare Paketgrenzen
- Refactoring innerhalb eines Pakets ohne Außenwirkung — dank stabiler public API

---

## Herausforderungen

- Die richtige Paketstruktur und -größe zu finden ist nicht trivial — falsche Einteilung kostet beim Umschneiden Zeit
- Teamkonsens zu Architekturregeln notwendig — fehlendes Wissen erzeugt Reibungsverluste
- UI-Aggregation über mehrere Pakete hinweg aufwändiger als im klassischen Monolithen

---

<!-- _class: section -->
<!-- header: "Fazit" -->

# Fazit & Q&A

## Wann lohnt es sich?

---

## Wann lohnt sich Packwerk?

**Ja, wenn:**
- Langfristige Lebensdauer eines Rails-Monolithen, vermutlich insbesondere, wenn mehrere Teams daran arbeiten
- Wenn Architektur-Smells auffallen, z.B. durch [Rubrowser](https://github.com/emad-elsaid/rubrowser)
- Wenn langfristig Services angestrebt werden, aber der Zeitpunkt noch nicht gekommen ist

**Eher nicht, wenn:**
- Projekte in früher Phase — erst das Produkt finden, dann Struktur

---

## Ressourcen & Abschluss

**GitHub:** [Shopify/packwerk](https://github.com/Shopify/packwerk)
**Blog:** [Shopify Engineering Blog](https://shopify.engineering/enforcing-modularity-rails-apps-packwerk)

> „KI schreibt Code schneller, als Menschen ihn in der Tiefe reviewen und prüfen können. Gut strukturierter Code macht das leichter. Packwerk setzt diese Struktur durch."

**Fragen?**
