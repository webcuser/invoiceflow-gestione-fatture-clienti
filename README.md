# invoiceflow-gestione-fatture-clienti

Gestione clienti, preventivi e fatture per freelance e piccole aziende.

## Overview

# Product Requirements Document (PRD)
## InvoiceFlow – Gestione fatture e clienti per freelance e piccole aziende

---

## 1. Project Overview

**InvoiceFlow** è una web application SaaS progettata per freelance, professionisti e piccole aziende che necessitano di uno strumento semplice, veloce e centralizzato per la gestione di clienti, fatture e pagamenti. L’obiettivo è ridurre la complessità amministrativa, velocizzare la creazione di documenti commerciali e fornire una panoramica finanziaria immediata dell’attività.

---

## 2. Goals & Success Metrics

### Obiettivi Principali

- **Riduzione del tempo di creazione fattura:** Gli utenti devono poter generare una fattura completa in meno di 2 minuti.
- **Centralizzazione dati:** Tutti i clienti e le fatture sono gestiti in un unico sistema multi-azienda.
- **Monitoraggio pagamenti:** Stato delle fatture sempre aggiornato e visibile.
- **Dashboard finanziaria:** Panoramica chiara e aggiornata su fatturato, scadenze e clienti principali.

### Metriche di Successo

- **Onboarding:** 80% degli utenti completa la registrazione e crea la prima fattura entro 24h.
- **Tempo medio di creazione fattura:** < 2 minuti.
- **Uptime applicativo:** ≥ 99,9%.
- **Performance API:** 95% delle richieste CRUD < 300ms.
- **Tasso di invio email riuscito:** ≥ 98%.
- **Adozione:** 1.000 aziende registrate, 10.000 utenti, 100.000 fatture archiviate entro il primo anno.

---

## 3. Target Users

- **Freelance e professionisti**: Necessitano di uno strumento rapido e intuitivo per la gestione amministrativa.
- **Piccole aziende e agenzie digitali**: Richiedono gestione multi-utente, multi-azienda e collaborazione tra membri del team.
- **Contesti con esigenze di semplicità**: Utenti senza personale amministrativo dedicato, che vogliono evitare software complessi.

**Esigenze principali:**
- Creare e inviare fatture in modo semplice.
- Tenere traccia di pagamenti e scadenze.
- Gestire più aziende e team.
- Accedere a report finanziari essenziali.

---

## 4. Core Features

### 4.1 Registrazione, Autenticazione e Gestione Utenti

- **Registrazione account** (email/password, conferma email)
- **Login/logout**
- **Recupero password**
- **Gestione profilo utente**
- **Gestione multi-azienda**:
  - Creazione nuove aziende
  - Invito utenti (collaboratori/viewer)
  - Selezione azienda attiva

### 4.2 Gestione Azienda

- **Dati profilo aziendale**:
  - Nome azienda
  - Partita IVA
  - Indirizzo
  - Email
  - Logo aziendale

### 4.3 Gestione Clienti

- **CRUD clienti**:
  - Creazione, modifica, eliminazione, ricerca
- **Dati cliente**:
  - Nome/Ragione sociale
  - Email
  - Telefono
  - Indirizzo
  - Codice fiscale
  - Partita IVA
  - Note

### 4.4 Gestione Fatture

- **Creazione e modifica fatture**:
  - Associazione a cliente e azienda
  - Data emissione, data scadenza
  - Stato: bozza, inviata, pagata, scaduta, annullata
  - Numerazione progressiva per azienda e anno
- **Righe fattura**:
  - Descrizione, quantità, prezzo unitario
  - Aliquota IVA per riga (supporto multi-aliquota)
  - Calcolo imponibile, IVA e totale per aliquota
- **Gestione pagamenti**:
  - Registrazione pagamento manuale
  - Stato aggiornato in base ai pagamenti

### 4.5 Generazione e Invio PDF

- **Generazione automatica PDF**:
  - Layout professionale con logo, dati azienda/cliente, dettagli prodotti/servizi, totali, note
- **Download PDF**
- **Invio tramite email al cliente**:
  - Integrazione con Amazon SES
  - Invio asincrono tramite queue
  - Storico invii (destinatario, stato, data, errori)

### 4.6 Dashboard

- **Panoramica finanziaria**:
  - Fatturato mensile
  - Fatture pagate, da incassare, scadute
  - Clienti principali
  - Scadenze imminenti
- **Grafici**:
  - Entrate mensili (ultimi 12 mesi)
  - Stato fatture
  - Andamento annuale
- **Tabelle**:
  - Ultime 10 fatture
  - Paginazione (20 elementi per pagina)

### 4.7 Notifiche

- **Notifiche in-app**:
  - Nuova fattura creata
  - Pagamento registrato
  - Fattura in scadenza
  - Fattura inviata
- **Gestione notifiche**:
  - Stato letto/non letto
  - Paginazione (20 notifiche per pagina)
  - Aggiornamento real-time via WebSocket (fallback polling 60s)
- **Notifiche email**:
  - Promemoria pagamento
  - Invio fattura

### 4.8 Ruoli e Permessi

- **Owner**:
  - Gestione account, fatture, utenti, impostazioni
- **Collaboratore**:
  - Gestione clienti, creazione/modifica fatture, visualizzazione documenti
- **Viewer**:
  - Solo consultazione

---

## 5. Technical Architecture

### 5.1 Stack Tecnologico

- **Frontend**: React 19, TypeScript, Tailwind CSS, TanStack Query, React Router
- **Backend**: Laravel 12 (PHP 8.4), REST API, Laravel Sanctum (auth), Laravel Horizon (queue)
- **Database**: PostgreSQL
- **Storage**: S3 compatible (PDF, logo)
- **Queue**: Redis
- **Email**: Amazon SES (via Laravel Mail)

### 5.2 Modello Dati Principale

#### Users

| Campo      | Tipo      | Note                |
|------------|-----------|---------------------|
| id         | int       | PK                  |
| name       | string    |                     |
| email      | string    | Unique              |
| password   | string    | Hash                |
| created_at | datetime  |                     |

#### Companies

| Campo      | Tipo      | Note                |
|------------|-----------|---------------------|
| id         | int       | PK                  |
| name       | string    |                     |
| vat_number | string    |                     |
| address    | string    |                     |
| logo       | string    | URL S3              |
| owner_id   | int       | FK -> users         |

#### Company_User (multi-tenant, ruoli)

| Campo      | Tipo      | Note                |
|------------|-----------|---------------------|
| id         | int       | PK                  |
| company_id | int       | FK -> companies     |
| user_id    | int       | FK -> users         |
| role       | enum      | owner/collab/viewer |

#### Customers

| Campo      | Tipo      | Note                |
|------------|-----------|---------------------|
| id         | int       | PK                  |
| company_id | int       | FK -> companies     |
| name       | string    |                     |
| email      | string    |                     |
| vat_number | string    |                     |
| phone      | string    |                     |
| address    | string    |                     |
| notes      | text      |                     |

#### Invoices

| Campo        | Tipo      | Note                |
|--------------|-----------|---------------------|
| id           | int       | PK                  |
| company_id   | int       | FK -> companies     |
| customer_id  | int       | FK -> customers     |
| number       | string    | Progressivo         |
| status       | enum      | draft/sent/paid/etc |
| issue_date   | date      |                     |
| due_date     | date      |                     |
| total        | decimal   | Totale documento    |
| currency     | string    | Default: EUR        |
| notes        | text      |                     |

#### Invoice_Items

| Campo      | Tipo      | Note                |
|------------|-----------|---------------------|
| id         | int       | PK                  |
| invoice_id | int       | FK -> invoices      |
| description| string    |                     |
| quantity   | decimal   |                     |
| price      | decimal   |                     |
| tax_rate   | decimal   | % IVA               |
| tax_amount | decimal   | Calcolato           |

#### Invoice_Sequences

| Campo         | Tipo      | Note                |
|---------------|-----------|---------------------|
| id            | int       | PK                  |
| company_id    | int       | FK -> companies     |
| year          | int       |                     |
| prefix        | string    | Es: FT-2026-        |
| current_number| int       |                     |
| created_at    | datetime  |                     |
| updated_at    | datetime  |                     |

#### Notifications

| Campo      | Tipo      | Note                |
|------------|-----------|---------------------|
| id         | int       | PK                  |
| user_id    | int       | FK -> users         |
| type       | string    |                     |
| message    | string    |                     |
| read_at    | datetime  |                     |
| created_at | datetime  |                     |

#### Email_Logs

| Campo         | Tipo      | Note                |
|---------------|-----------|---------------------|
| id            | int       | PK                  |
| invoice_id    | int       | FK -> invoices      |
| recipient     | string    | Email destinatario  |
| subject       | string    |                     |
| status        | enum      | sent/failed         |
| sent_at       | datetime  |                     |
| error_message | text      | nullable            |

### 5.3 API Principali

#### Clienti

- `GET /api/customers`
- `POST /api/customers`
- `PUT /api/customers/{id}`
- `DELETE /api/customers/{id}`

#### Fatture

- `GET /api/invoices`
- `POST /api/invoices`
- `GET /api/invoices/{id}`
- `POST /api/invoices/{id}/pdf`
- `POST /api/invoices/{id}/send`

**Esempi payload e risposte**: vedi sezione Q&A (inclusi codici 201, 401, 403, 404, 422, 500).

---

## 6. Non-Functional Requirements

### 6.1 Performance

- **API CRUD**: < 300ms per il 95% delle richieste
- **Query dashboard**: < 1s
- **Generazione PDF**: < 5s
- **Invio email**: asincrono, non blocca UI

### 6.2 Scalabilità

- **Target MVP**: 1.000 aziende, 10.000 utenti, 100.000 fatture
- **Throughput**: 500 richieste API/minuto
- **Storage**: S3 per documenti, Redis per caching e queue

### 6.3 Sicurezza

- **Autenticazione**: Laravel Sanctum
- **Autorizzazioni**: Policy Laravel, multi-tenant isolation
- **Validazione input**: lato backend e frontend
- **Rate limiting**: API protette da throttling
- **Audit log**: modifiche critiche tracciate
- **Backup**: database e storage giornalieri

### 6.4 Affidabilità

- **Uptime**: ≥ 99,9%
- **Gestione errori**: fallback e messaggi chiari per errori API, email e notifiche
- **Notifiche**: eventi salvati prima dell’invio per evitare perdite

### 6.5 Privacy & Compliance

- **Isolamento dati**: ogni dato è sempre collegato a una company_id
- **Gestione multi-tenant**: nessun dato visibile tra aziende diverse
- **Conformità GDPR**: dati personali gestiti secondo normativa europea

---

## 7. Out of Scope (v1)

- **Gestione preventivi**: modulo preventivi escluso dall’MVP, previsto in release future
- **Fatturazione elettronica SDI**: non inclusa nell’MVP
- **Pagamenti online/Stripe**: non inclusi
- **Multi-valuta**: solo EUR supportato
- **Report finanziari avanzati**: solo dashboard base
- **App mobile**: non prevista per MVP
- **Automazioni AI**: non previste per MVP
- **Import/export massivo dati**
- **Integrazione con sistemi fiscali esterni**

---

## 8. Open Questions

- **Personalizzazione layout PDF**: Sarà necessario supportare template personalizzati già nell’MVP?
- **Gestione multi-lingua**: L’applicazione dovrà essere localizzata in più lingue già dal lancio?
- **Limiti storage S3**: Definire policy di retention e limiti per allegati/documenti.
- **Gestione errori email**: Quale sarà il flusso utente in caso di fallimento invio email (retry manuale, notifica)?
- **Gestione cambio anno fiscale**: La numerazione fatture dovrà supportare logiche personal