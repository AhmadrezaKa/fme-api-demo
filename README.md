# FME REST API — Client Portal Demo

A browser-based client portal that demonstrates how to trigger **FME Server workflows via the REST API** from a frontend web application. Built for [Hack Rentmeesters](https://www.hackrentmeesters.nl), a Dutch land management and real estate firm.

Live demo: [Rentmeester Assistent]([https://fme-api-demo.vercel.app](https://www.rentmeesterassistent.nl/))

---

## What It Does

This project is a multi-page web application that allows authenticated users to trigger FME Server automations directly from a browser — no GIS software required. It was built to give non-technical staff and clients a simple interface to run spatial data workflows on demand.

The portal consists of three applications accessible from a central dashboard:

### 1. Situatietekeninggenerator (Situation Drawing Generator)
Users enter one or more cadastral parcel identifiers (gemeente, sectie, perceelnummer) along with their contact details. On submission, the form calls a backend proxy which forwards a job request to FME Server via the REST API. FME then generates a situatietekening (cadastral situation drawing) and delivers it to the user by email.

### 2. Factsheet Generator
A similar workflow that generates a detailed property factsheet for a given parcel, again triggered through the FME REST API.

---

## Architecture

```
Browser (HTML/JS)
    │
    ▼
Vercel Serverless API (proxy + auth)
    │
    ▼
FME Server REST API
    │
    ▼
FME Workspace (spatial processing)
    │
    ▼
Output delivered to user (email/PDF)
```

**Authentication** is handled via a JWT-based login flow. The browser sends credentials to a Vercel serverless endpoint (`/api/login`), receives a token, and includes it as a `Bearer` header on all subsequent API calls (`/api/call`). The FME Server credentials are never exposed to the client.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML, CSS, JavaScript |
| Fonts | Google Fonts (Oswald) |
| Hosting | Vercel |
| Backend proxy | Vercel Serverless Functions |
| Automation engine | FME Server (Safe Software) |
| API | FME REST API v3 |

---

## Key Features

- JWT-based login and session management
- Dynamic parcel form — users can add or remove multiple parcels in a single request
- Cluster numbering system that auto-activates when more than one parcel is entered
- Responsive layout with branding from Hack Rentmeesters
- Clean separation between frontend form logic and backend API credentials
- Dutch-language interface (field labels, placeholders, validation messages)

---

## FME REST API Integration

The core of the project is the API call that triggers an FME workspace. The payload structure follows the FME Server REST API `jobs/submit` format:

```json
{
  "applicationType": "situatietekening",
  "publishedParameters": [
    { "name": "Een_of_meer_percelen", "value": "1" },
    { "name": "GEMEENTE", "value": "Utrecht" },
    { "name": "SECTIE", "value": "A" },
    { "name": "PERCNR", "value": "1234" },
    { "name": "NaamNP", "value": "Jan de Vries" },
    { "name": "TelNr", "value": "0612345678" },
    { "name": "Recepient", "value": "client@example.com" }
  ]
}
```

The backend proxy handles authentication with FME Server and forwards this payload, keeping server credentials out of the browser entirely.

---

## Project Structure

```
fme-api-demo/
├── index.html          # Situatietekeninggenerator form
├── dashboard.html      # Application selection dashboard
├── factsheet.html      # Factsheet generator form
├── login.html          # Login page
├── api/                # Vercel serverless functions (login + proxy)
└── suit-experience/    # Additional FME application interface
```

---

## Context

This was built as a proof-of-concept to demonstrate how FME Server's REST API can be exposed to end users through a lightweight portal — removing the need for any client-side GIS tooling. The approach is directly applicable to any organization running FME Server that wants to offer self-service spatial data workflows to non-technical users or external clients.

---

## Related

- [FME REST API Documentation](https://docs.safe.com/fme/html/FME_REST/apidoc/v3/)
- [Hack Rentmeesters](https://www.hackrentmeesters.nl)
