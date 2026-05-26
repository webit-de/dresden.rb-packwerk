# Packwerk Talk — Demo App Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Eine minimale Rails-App bauen, die die dresdenrb.onruby.de-Domänen modelliert, bewusste Packwerk-Violations enthält und als Live-Demo-Grundlage für den Vortrag dient.

**Architecture:** Die App enthält 5 Domains (users, events, topics, ticketing, billing) zunächst als flache `app/models/`-Struktur mit eingebetteten Boundary-Verletzungen. Während der Demo werden Packs schrittweise aktiviert, bis Violations sichtbar werden.

**Tech Stack:** Ruby 3.x, Rails 8 (minimal), Packwerk 2.x, SQLite

---

## Dateistruktur

```
demo/
├── app/models/
│   ├── user.rb
│   ├── event.rb
│   ├── topic.rb
│   ├── ticket.rb
│   ├── invoice.rb
│   └── subscription_status.rb   # private — soll nur users-Pack nutzen
├── packs/
│   └── billing/
│       └── package.yml           # wird in Demo angelegt
├── package.yml                   # Root-Package
├── packwerk.yml
└── Gemfile
```

**Violations im Startcode:**
- `Event#create_invoice_for_attendee` ruft `Invoice.create!` direkt auf → `events` kennt `billing`
- `Topic#available_for_premium?` ruft `SubscriptionStatus.new` auf → `topics` greift auf private Klasse zu

---

## Task 1: Rails Demo App anlegen

**Files:**
- Create: `demo/` (via `rails new`)

- [ ] **Schritt 1: Rails App erstellen**

```bash
cd /home/reinke/Projekte/webit/dresden.rb-packwerk
rails new demo --minimal --skip-test --database=sqlite3
```

Erwartete Ausgabe: `Bundle complete!`

- [ ] **Schritt 2: Verzeichnis prüfen**

```bash
ls demo/app/models/
```

Erwartete Ausgabe: `application_record.rb`

- [ ] **Schritt 3: Commit**

```bash
cd demo
git add .
git commit -m "chore: new minimal rails app for packwerk demo"
```

---

## Task 2: Domain-Modelle mit Violations anlegen

**Files:**
- Create: `demo/app/models/user.rb`
- Create: `demo/app/models/event.rb`
- Create: `demo/app/models/topic.rb`
- Create: `demo/app/models/ticket.rb`
- Create: `demo/app/models/invoice.rb`
- Create: `demo/app/models/subscription_status.rb`
- Create: `demo/db/migrate/` (6 Migrationen)

- [ ] **Schritt 1: Migrationen erstellen**

```bash
cd /home/reinke/Projekte/webit/dresden.rb-packwerk/demo
bin/rails generate model User name:string email:string
bin/rails generate model Event title:string date:datetime location:string
bin/rails generate model Topic title:string user:references event:references
bin/rails generate model Ticket user:references event:references confirmed:boolean
bin/rails generate model Invoice user:references event:references amount:decimal paid:boolean
bin/rails db:migrate
```

- [ ] **Schritt 2: `app/models/user.rb` schreiben**

```ruby
class User < ApplicationRecord
  has_many :tickets
  has_many :events, through: :tickets
  has_many :topics
end
```

- [ ] **Schritt 3: `app/models/event.rb` schreiben — enthält Violation**

```ruby
class Event < ApplicationRecord
  has_many :tickets
  has_many :topics

  # VIOLATION: Event kennt Billing-Interna direkt
  def create_invoice_for_attendee(user)
    Invoice.create!(user: user, event: self, amount: 10.0, paid: false)
  end
end
```

- [ ] **Schritt 4: `app/models/subscription_status.rb` schreiben — soll private bleiben**

```ruby
# Interne Klasse — soll nur vom users-Pack genutzt werden
class SubscriptionStatus
  def initialize(user)
    @user = user
  end

  def active?
    user.tickets.where(confirmed: true).exists?
  end

  private

  attr_reader :user
end
```

- [ ] **Schritt 5: `app/models/topic.rb` schreiben — enthält zweite Violation**

```ruby
class Topic < ApplicationRecord
  belongs_to :user
  belongs_to :event

  # VIOLATION: Topic greift auf private Klasse SubscriptionStatus zu
  def available_for_premium?
    SubscriptionStatus.new(user).active?
  end
end
```

- [ ] **Schritt 6: `app/models/ticket.rb` schreiben**

```ruby
class Ticket < ApplicationRecord
  belongs_to :user
  belongs_to :event
end
```

- [ ] **Schritt 7: `app/models/invoice.rb` schreiben**

```ruby
class Invoice < ApplicationRecord
  belongs_to :user
  belongs_to :event
end
```

- [ ] **Schritt 8: Rails-Konsole — kurz prüfen ob alles lädt**

```bash
cd /home/reinke/Projekte/webit/dresden.rb-packwerk/demo
bin/rails runner "puts Event.new.class"
```

Erwartete Ausgabe: `Event`

- [ ] **Schritt 9: Commit**

```bash
git add .
git commit -m "feat: add domain models with intentional boundary violations"
```

---

## Task 3: Packwerk installieren und konfigurieren

**Files:**
- Modify: `demo/Gemfile`
- Create: `demo/packwerk.yml`
- Create: `demo/package.yml`

- [ ] **Schritt 1: Gem hinzufügen**

In `demo/Gemfile` am Ende einfügen:

```ruby
gem "packwerk", "~> 2.3"
```

- [ ] **Schritt 2: Bundle installieren**

```bash
cd /home/reinke/Projekte/webit/dresden.rb-packwerk/demo
bundle install
```

Erwartete Ausgabe: `Bundle complete!`

- [ ] **Schritt 3: `packwerk.yml` erstellen**

```yaml
include:
  - "{app,packs,engines}/**/*.rb"
exclude:
  - "{bin,node_modules,script,tmp,vendor}/**/*"
package_paths:
  - "packs/*"
load_paths:
  - app/models
```

- [ ] **Schritt 4: Root-`package.yml` erstellen**

```yaml
# Root package — enthält alles was noch nicht in einem Pack ist
enforce_privacy: false
enforce_dependencies: false
```

- [ ] **Schritt 5: Packwerk initialisieren**

```bash
cd /home/reinke/Projekte/webit/dresden.rb-packwerk/demo
bundle exec packwerk init
```

- [ ] **Schritt 6: Ersten Check ausführen — soll noch grün sein**

```bash
bundle exec packwerk check
```

Erwartete Ausgabe: `No offenses detected` (noch keine Packs definiert, nichts enforced)

- [ ] **Schritt 7: Commit**

```bash
git add .
git commit -m "feat: install and configure packwerk"
```

---

## Task 4: Billing-Pack anlegen (Demo-Schritt)

**Files:**
- Create: `demo/packs/billing/package.yml`

Dies ist der entscheidende Demo-Schritt: sobald `billing` als eigener Pack mit Enforcement definiert wird, werden die Violations sichtbar.

- [ ] **Schritt 1: Pack-Verzeichnis anlegen**

```bash
mkdir -p /home/reinke/Projekte/webit/dresden.rb-packwerk/demo/packs/billing/app/public
```

- [ ] **Schritt 2: `packs/billing/package.yml` erstellen**

```yaml
enforce_privacy: true
enforce_dependencies: true
```

- [ ] **Schritt 3: `Invoice` in den billing-Pack verschieben**

Datei `demo/packs/billing/app/public/invoice.rb` erstellen:

```ruby
class Invoice < ApplicationRecord
  belongs_to :user
  belongs_to :event
end
```

Originaldatei `demo/app/models/invoice.rb` löschen.

- [ ] **Schritt 4: `packwerk check` ausführen — Violations müssen erscheinen**

```bash
cd /home/reinke/Projekte/webit/dresden.rb-packwerk/demo
bundle exec packwerk check
```

Erwartete Ausgabe (mindestens):
```
app/models/event.rb:5:4
Privacy violation: '::Invoice' is private to 'packs/billing'
```

- [ ] **Schritt 5: Commit**

```bash
git add .
git commit -m "feat: add billing pack with enforcement — violations now visible"
```

---

## Task 5: Demo-Flow dokumentieren und zweiten Pack vorbereiten

**Files:**
- Create: `demo/DEMO_SCRIPT.md`
- Create: `demo/packs/users/package.yml` (für zweite Violation)

- [ ] **Schritt 1: `packs/users/` anlegen**

```bash
mkdir -p /home/reinke/Projekte/webit/dresden.rb-packwerk/demo/packs/users/app/models
```

- [ ] **Schritt 2: `subscription_status.rb` in users-Pack verschieben (private)**

Datei `demo/packs/users/app/models/subscription_status.rb` erstellen:

```ruby
# Private — nur vom users-Pack zu nutzen
class SubscriptionStatus
  def initialize(user)
    @user = user
  end

  def active?
    user.tickets.where(confirmed: true).exists?
  end

  private

  attr_reader :user
end
```

Originaldatei `demo/app/models/subscription_status.rb` löschen.

- [ ] **Schritt 3: `packs/users/package.yml` erstellen**

```yaml
enforce_privacy: true
enforce_dependencies: true
```

- [ ] **Schritt 4: `packwerk check` — beide Violations sichtbar**

```bash
bundle exec packwerk check
```

Erwartete Ausgabe:
```
app/models/event.rb:5:4
Privacy violation: '::Invoice' is private to 'packs/billing'

app/models/topic.rb:7:4
Privacy violation: '::SubscriptionStatus' is private to 'packs/users'
```

- [ ] **Schritt 5: `DEMO_SCRIPT.md` schreiben**

```markdown
# Demo-Script: Packwerk Live-Demo

## Startbedingung
- Terminal offen in `demo/`
- Editor offen mit `app/models/event.rb` sichtbar

## Schritt 1: Ausgangslage zeigen (1-2 min)
- Zeige Verzeichnisstruktur: `ls app/models/`
- Zeige `event.rb` — `Invoice.create!` direkt aufgerufen
- Zeige `topic.rb` — `SubscriptionStatus.new` direkt aufgerufen
- Satz: "Das kompiliert, testet grün, läuft — aber ist das gute Architektur?"

## Schritt 2: Pack anlegen & ersten Check (2 min)
- `packs/billing/package.yml` zeigen
- `bundle exec packwerk check` ausführen
- Violations vorlesen und erklären

## Schritt 3: Violation verstehen (2 min)
- Zeige die Fehlermeldung: Datei, Zeile, Art der Violation
- Erklären: "Event hat keine deklarierte Abhängigkeit auf billing"

## Schritt 4: package_todo.yml (1 min)
- `bundle exec packwerk update` ausführen
- `package_todo.yml` zeigen: "So migriert ihr Legacy-Code ohne alles sofort zu fixen"

## Schritt 5: Eine Violation beheben (2 min)
- `packs/events/package.yml` anlegen mit `dependencies: ["packs/billing"]`
- `bundle exec packwerk check` — eine Violation weg
- Satz: "Jetzt ist die Abhängigkeit explizit, sichtbar, überprüfbar."
```

- [ ] **Schritt 6: Commit**

```bash
git add .
git commit -m "feat: add users pack and demo script"
```

---

## Task 6: End-to-End Demo-Durchlauf verifizieren

- [ ] **Schritt 1: Sauberer Ausgangszustand**

```bash
cd /home/reinke/Projekte/webit/dresden.rb-packwerk/demo
bundle exec packwerk check
```

Erwartete Ausgabe: mindestens 2 Violations sichtbar

- [ ] **Schritt 2: Fix durchführen — events-Pack mit Dependency**

```bash
mkdir -p packs/events
```

Datei `packs/events/package.yml` erstellen:

```yaml
enforce_privacy: true
enforce_dependencies: true
dependencies:
  - packs/billing
```

- [ ] **Schritt 3: Check nach Fix**

```bash
bundle exec packwerk check
```

Erwartete Ausgabe: `event.rb`-Violation weg, nur noch `topic.rb`-Violation

- [ ] **Schritt 4: Demo-Timing prüfen**

Den DEMO_SCRIPT.md einmal durchspielen und stoppen. Ziel: unter 10 Minuten.

- [ ] **Schritt 5: Final commit**

```bash
git add .
git commit -m "feat: complete demo flow — violations and fix verified"
```
