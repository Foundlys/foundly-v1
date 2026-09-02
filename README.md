# Foundly OS v2.9.1 — Railway Safe

Deze build lost de Railway-crash op wanneer GitHub de `config/` en `lib/` bestanden plat in de repository heeft staan. `server.js` ondersteunt nu zowel de nette mapstructuur als een platte GitHub-upload.

# Foundly OS v2.9 — Autonomous Automotive Engine Fabric

Foundly is een hosted Automotive Operating System met een high-end neural dashboard, 12 zelfstandige bedrijfsengines, een gedeelde Data Fabric, lange termijn geheugen en connectorarchitectuur.

## Wat v2.9 verandert
De belangrijkste regel is nu technisch afgedwongen: **ieder hoofdonderdeel kan zelfstandig draaien én gecontroleerd samenwerken via Foundly Core.**

- Inkoop kan zelfstandig de beste inkoopkansen beoordelen.
- Verkoop kan zelfstandig voorraad en verkoopkansen optimaliseren.
- CRM, Agenda, Voorraad, Social Media, Google Ads, Communicatie, Rapportages, Integraties en Automatisering hebben ieder een eigen enginecontract.
- Data is een zelfstandige data-engine die alle andere engines kan voeden.
- Foundly Core is alleen de orchestrator bij cross-module vragen.

## Dashboard
De bestaande neural/cosmic v2.7/v2.8 dashboardrichting is behouden. FOUNDLY blijft centraal, alle hoofdonderdelen en subnodes blijven visueel gekoppeld, wake word blijft **“Yoo bro”**.

## Starten
Node.js 18+:

```bash
npm start
```

Open:

```text
http://localhost:8787
```

Testen:

```bash
npm test
```

## Nieuwe engine API
### Overzicht
`GET /api/engines`

### Zelfstandige engine vraag
`POST /api/engine/inkoop/ask`

```json
{
  "message": "Welke auto gaan we vandaag inkopen?",
  "context": {"tenant_id":"dealer-001","dealer_id":"hoofdvestiging"}
}
```

### Engine actie inplannen
`POST /api/engine/inkoop/execute`

```json
{
  "action": "purchase_alerts",
  "payload": {"threshold_score": 90},
  "tenant_id": "dealer-001",
  "dealer_id": "hoofdvestiging"
}
```

### Samenwerking via Foundly Core
`POST /api/core/ask`

```json
{
  "message": "Welke auto gaan we vandaag inkopen en hebben we daar bestaande klanten voor?",
  "context": {"tenant_id":"dealer-001","dealer_id":"hoofdvestiging"}
}
```

## Echte data koppelen
Data-ingang:
`POST /api/data/ingest`

Connectoren zijn voorbereid voor automotive databronnen, RDW/BPM, Dealer DMS, Google Ads/GA4/Search Console/Calendar, Meta/Instagram/TikTok/LinkedIn, WhatsApp, e-mail, voice en webhooks.

Secrets staan uitsluitend server-side via `.env`/hosting secrets. Zet nooit API-sleutels in `index.html`.

## Multi-tenant data
Iedere onderneming/dealer krijgt fysiek gescheiden stores:

```text
data/tenants/<tenant>/<dealer>/inkoop.json
memory/tenants/<tenant>/<dealer>/inkoop.json
```

Dit is de lokale/MVP opslaglaag. Voor productie op schaal migreert dezelfde contractlaag naar PostgreSQL + vector memory.

## Lange termijn geheugen
Iedere engine en Foundly Core hebben een eigen persistent geheugen. Dealer-voorkeuren, eerdere analyses, strategieën en beslissingen blijven beschikbaar na een server restart.

## Achtergrondprocessen
Iedere engine heeft een eigen worker-definitie. Workers kunnen 24/7 data, voorraad, CRM, agenda, advertenties, social, connectoren en kansen monitoren. Zie `.env.example` voor intervallen.

Meer technische details: `ARCHITECTURE.md`.
