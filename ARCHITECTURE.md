# Foundly OS v2.9 - Autonomous Automotive Engine Fabric

## Kernprincipe
Elk hoofdonderdeel is een zelfstandige engine. Een engine kan zelfstandig data lezen, geheugen gebruiken, beslissen en acties inplannen. Foundly Core schakelt meerdere engines alleen in wanneer een vraag meerdere domeinen vereist.

## 12 zelfstandige engines
1. Inkoop
2. Verkoop
3. Data
4. CRM
5. Agenda
6. Voorraad
7. Social Media
8. Google Ads
9. Automatisering
10. Communicatie
11. Rapportages
12. Integraties

Iedere engine heeft:
- eigen AI-instructies;
- eigen capability-contract;
- eigen tenant-database;
- eigen lang geheugen;
- eigen events, taken, inzichten en beslissingen;
- eigen API-endpoints;
- eigen achtergrondworker;
- expliciete read/write-relaties naar andere engines.

## Zelfstandig voorbeeld - Inkoop
`POST /api/engine/inkoop/ask`

Vraag: `Welke auto gaan we vandaag inkopen?`

De Inkoop Engine blijft eigenaar van de beslissing. Data uit Data, Voorraad of CRM kan als ondersteunende bron worden gelezen, maar de primaire beslissing wordt niet doorgeschoven naar Verkoop of CRM.

## Samenwerking
Een gecombineerde vraag gaat via:
`POST /api/core/ask`

Voorbeeld: `Welke auto gaan we vandaag inkopen en hebben we daar bestaande klanten voor?`

Foundly Core routeert naar Inkoop + CRM en synthesizeert daarna één antwoord. De engines blijven afzonderlijk auditeerbaar.

## Multi-tenant isolatie
Data en geheugen worden fysiek per tenant/dealer in aparte mappen opgeslagen:

`data/tenants/<tenant>/<dealer>/<module>.json`
`memory/tenants/<tenant>/<dealer>/<scope>.json`

API-context kan via headers:
- `X-Foundly-Tenant`
- `X-Foundly-Dealer`

of via JSON `tenant_id` / `dealer_id`.

## Data Fabric
Alle externe data loopt via `/api/data/ingest` of connector-webhooks. De Integration Bus:
1. normaliseert de envelope;
2. koppelt tenant/dealer;
3. dedupliceert;
4. routeert naar relevante engines;
5. schrijft historische records;
6. creëert events en geheugen;
7. publiceert een intern Event Bus-event.

## Background OS
Elke engine heeft een eigen worker heartbeat. Dit is de basis voor 24/7 scanning, CRM follow-up, voorraadbewaking, advertenties, social monitoring, connector health en rapportages. In een productie-hosting kunnen deze workers later naar aparte queue workers/processen worden uitgeschaald zonder het engine-contract te veranderen.

## Lang geheugen
Geheugen is persistent en tenantgescheiden. Recall combineert:
- lexicale relevantie;
- belangrijkheid;
- recency.

Later kan dezelfde interface worden vervangen/uitgebreid met embeddings/pgvector zonder de engines of UI te herschrijven.

## Productie-evolutie
Voor MVP/pitch gebruikt v2.9 bestand-gebaseerde persistente stores. De volgende schaalstap is:
- PostgreSQL voor transactionele data;
- pgvector/vector DB voor semantisch geheugen;
- Redis/BullMQ of cloud queue voor workers;
- secret manager voor OAuth/API-tokens;
- object storage voor foto's/documenten;
- OAuth per tenant voor Google/Meta/Calendar;
- observability + audit logs.
