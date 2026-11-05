---
title: "Quorivell: Lokale Entscheidungen mit On-Device-KI"
image: "quorivell.png"
draft: false
comments: false
layout: "project_page"
---
## Hobbyprojekt

Beruflich arbeite ich als freiberuflicher DevOps- und Quality-Engineering-Berater.
**Quorivell** ist mein persönliches Flutter-Nebenprojekt: eine private, lokal-first App,
die unübersichtliche Gesprächstexte in prüfbare Entscheidungen und Commitments verwandelt
— mit Evidenz, die auf dem Gerät bleibt.

**Claim:** Entscheidungen mit Evidenz — auf Ihrem Gerät.

---

## Was die App kann

- **Erfassen** von Gesprächstext als Quelldatensätze
- **Extrahieren** von Kandidaten on-device mit lokalem GGUF-Modell (llama.cpp)
- **Prüfen** vor dem Speichern — nichts schreibt automatisch ins Ledger
- **Ledger** für Entscheidungen, Commitments, Owner und Fälligkeiten
- **Chat** in freien Threads auf demselben lokalen Modell
- **Sprachen:** Englisch, Deutsch und Arabisch (Material 3)

Privatsphäre ist das Produkt: Drift-SQLite auf dem Gerät ist kanonisch, Inferenz bleibt
lokal, und Quellinhalte verlassen das Telefon nicht ohne einen späteren, expliziten
Remote-Flow.

---

## Stack (kurz)

Flutter · Riverpod · GoRouter · Drift · Freezed · llama.cpp (`llama_cpp_dart`) ·
Firebase-Auth-Grundlage (keine Cloud-KI für Capture/Extract)

---

## Links

- **GitHub:** [cherifsahraoui/Quorivell](https://github.com/cherifsahraoui/Quorivell)
- **Google Play (geschlossener Test):** [com.quorivell.app](https://play.google.com/store/apps/details?id=com.quorivell.app)

In aktiver Entwicklung — mit erwartbaren Kanten, besonders bei großen Modell-Downloads
auf dem Gerät.
