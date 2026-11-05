---
title: "Quorivell: Local-First Decisions with On-Device AI"
image: "quorivell.png"
draft: false
comments: false
layout: "project_page"
---
## Hobby project

I work as a freelance DevOps / quality engineering consultant by day. **Quorivell** is a
personal Flutter side project: a private, local-first app that turns messy conversation
text into reviewable decisions and commitments — with evidence kept on the device.

**Tagline:** Decisions, with evidence — on your device.

---

## What it does

- **Capture** conversation text as source records
- **Extract** candidates on-device with a local GGUF model (llama.cpp)
- **Review** before anything is saved — nothing auto-writes the ledger
- **Ledger** for decisions, commitments, owners, and due dates you can audit
- **Chat** in free-form threads on the same local model
- **Locales:** English, German, and Arabic (Material 3)

Privacy is the product: Drift SQLite on device is canonical, inference stays local, and
source content does not leave the phone unless you later opt into an explicit remote flow.

---

## Stack (short)

Flutter · Riverpod · GoRouter · Drift · Freezed · llama.cpp (`llama_cpp_dart`) ·
Firebase Auth foundation (no cloud AI for capture/extract)

---

## Links

- **GitHub:** [cherifsahraoui/Quorivell](https://github.com/cherifsahraoui/Quorivell)
- **Google Play (closed testing):** [com.quorivell.app](https://play.google.com/store/apps/details?id=com.quorivell.app)

Under active development — expect sharp edges, especially around large on-device model
downloads.
