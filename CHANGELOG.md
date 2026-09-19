# Changelog

Formát dle [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), verzování dle
[Semantic Versioning](https://semver.org/).

## [Unreleased]

### Removed (FORUM-REMOVAL — Sencai Forum retirement, 2026-09-19)

Sencai Forum a `forum-connector` jsou celoplatformně rušeny (rozhodnutí majitele: „forum
zcela smažeme a nebudeme dále používat"). Tenhle balíček není explicitně jmenovaný v
zadaném rozsahu úkolu, ale patří do stejné generované-SDK rodiny jako `sdk-ts`/
`sdk-python` a nese identickou `ForumUserCredential` API/model sadu, takže je zahrnut pro
konzistenci. Regenerace `./scripts/sdk-generate.sh` je dnes nefunkční (zdrojový spec
zanikl se smazáním Strapi 2026-07-30), takže je to ruční úprava:

- Smazáno `api_forum_user_credential.go` a šest `model_*forum*.go` souborů; odregistrováno
  `ForumUserCredentialAPI` z `client.go` (pole + inicializace v `NewAPIClient`).
- `api/openapi.yaml` — smazány `/forum-user-credentials` + `/forum-user-credentials/{id}`,
  tag `Forum User Credential`, schéma `ForumUserCredential` a čtyři pomocná
  `createForumUserCredential_*`/`findForumUserCredential_*` schémata.
- `.openapi-generator/FILES` zbaveno odpovídajících sedmi řádků.
- `Organisation`'s `lemmy_community_id`/`lemmy_community_name` pole **ponechána**
  (shoda s rozhodnutím `sencai-backend`u nechat sloupce bez writeru), jen popis teď
  říká, že jsou zrušené — na obou místech, kde se v `api/openapi.yaml` vyskytují.
- Ověřeno: `go build ./...` a `go vet ./...` (Go 1.23, přes Docker, žádný lokální
  toolchain) oba zelené, `api/openapi.yaml` zůstává validní YAML.

### Changed

- Regenerováno proti aktuálnímu `sencai-platform.public.v1.yaml` po přidání nových operací
  jinými Fázi 4 moduly (`authCheckSsoEnforcement`, `cloudInstanceExport`,
  `organisationAutomationScore`, `organisationLeaderboard`) — nové modely/API klienti přibyly,
  `Organisation`/`UserRank` modely dostaly nová pole. Žádná ruční změna v tomto adresáři, jen
  dopad regenerace ze specu (`./scripts/sdk-generate.sh`).

### Added

- Počáteční generování Go klienta (`github.com/sencai-space/sdk-go`, placeholder import path) z
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
