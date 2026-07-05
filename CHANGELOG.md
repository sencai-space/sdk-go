# Changelog

Formát dle [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), verzování dle
[Semantic Versioning](https://semver.org/).

## [Unreleased]

### Changed

- Regenerováno proti aktuálnímu `sencai-platform.public.v1.yaml` po přidání nových operací
  jinými Fázi 4 moduly (`authCheckSsoEnforcement`, `cloudInstanceExport`,
  `organisationAutomationScore`, `organisationLeaderboard`) — nové modely/API klienti přibyly,
  `Organisation`/`UserRank` modely dostaly nová pole. Žádná ruční změna v tomto adresáři, jen
  dopad regenerace ze specu (`./scripts/sdk-generate.sh`).

### Added

- Počáteční generování Go klienta (`github.com/sencai/sdk-go`, placeholder import path) z
  konsolidovaného veřejného OpenAPI specu (`go` generátor, `scripts/sdk-generate.sh`) —
  F4.DEVPORTAL.02.
- Getting Started dokumentace (`doc.sencai.space/dev/sdk/go`, F4.DEVPORTAL.04) — instalace,
  inicializace klienta, kompletní `FindOrganisation` + `OrganisationInviteMember` příklad
  ověřený proti živému lokálnímu stacku, konvence zpracování chyb (`GenericOpenAPIError`).

### Fixed

- Regenerováno po opravě `sencai.space/scripts/openapi-merge.ts` (F4.DEVPORTAL.04 — viz jeho
  CHANGELOG): auto-discovered CRUD modely už nejsou zabalené v neexistujícím `attributes`
  wrapperu (Strapi v5 Document Service API je flat) — teď se všechna pole modelu merguje do
  jedné ploché struktury přes `allOf`, přesně podle reálného tvaru odpovědi. `json`-typová
  pole (např. `Organisation.IpAllowlist`) jsou nyní `interface{}` místo vynuceného objektového
  typu. Žádná ruční změna v tomto adresáři, jen dopad regenerace ze specu.
