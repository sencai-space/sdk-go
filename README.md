# sdk-go (Go)

Go klient pro Sencai Platform API (`/api/v1/*`), generovaný z veřejného OpenAPI specu
([`sencai.space/openapi/sencai-platform.public.v1.yaml`](../sencai.space/openapi/sencai-platform.public.v1.yaml),
výstup F4.DEVPORTAL.01).

> **`go.mod` modul (`github.com/sencai/sdk-go`) je placeholder import cesta.** Finální import
> path je rozhodnutí navázané na budoucí veřejný release (samostatný GitHub repozitář/org), ne
> blokující pro lokální generování a použití uvnitř monorepa. Veřejná distribuce
> (`pkg.go.dev` indexace) je mimo scope F4.DEVPORTAL (viz `PHASE-4-PLAN.md`, modul přehled
> DEVPORTAL).

## Co je (ne)generované

Tento adresář je z většiny **generovaný** (`*.go`, `go.mod`, `go.sum`, `api/openapi.yaml`) přes
[`scripts/sdk-generate.sh`](../scripts/sdk-generate.sh) — `openapi-generator-cli` (`go`
generátor). Ručně psané a **chráněné** přes `.openapi-generator-ignore` (regenerace je nikdy
nepřepíše): tento `README.md`, `CHANGELOG.md`, `.env.example`.

Regenerace po změně specu:

```bash
cd sencai.space && npm run openapi:generate   # F4.DEVPORTAL.01, pokud se spec změnil
cd .. && ./scripts/sdk-generate.sh
```

## Instalace (zatím jen z monorepo cesty / gitu)

```bash
go get github.com/sencai/sdk-go
```

Dokud modul není publikovaný, přidat do `go.mod` konzumujícího projektu `replace` direktivu
mířící na lokální/git cestu tohoto adresáře.

## Použití

```go
package main

import (
    "context"

    sencaisdk "github.com/sencai/sdk-go"
)

func main() {
    cfg := sencaisdk.NewConfiguration()
    cfg.Servers = sencaisdk.ServerConfigurations{
        {URL: "http://api.sencai.localhost/api/v1"}, // nebo https://api.sencai.space/api/v1
    }
    // Bearer JWT (Keycloak) — nebo budoucí API-key header, viz `security` sekce specu.
    cfg.AddDefaultHeader("Authorization", "Bearer <keycloak-jwt>")

    client := sencaisdk.NewAPIClient(cfg)
    _, _, err := client.CloudInstanceAPI.FindCloudInstance(context.Background()).Execute()
    if err != nil {
        panic(err)
    }
}
```

`Configuration.Servers`/`Host` i autentizační hlavičky jsou vždy konfigurovatelné — balíček
neobsahuje žádnou hardcoded produkční URL.

## Build & vet

```bash
go build ./...
go vet ./...
```

## Verzování

Verze balíčku (dokumentovaná v `.openapi-generator/VERSION` a komentářích klienta) je odvozená
z `info.version` veřejného OpenAPI specu při každé regeneraci — Go modulové verzování jde přes
git tagy na samostatném repozitáři, jakmile vznikne (mimo scope tohoto úkolu). Breaking change
v API → zdokumentovat v `CHANGELOG.md` s odkazem na `doc.sencai.space/dev/api-versioning`
(`SUNSET_DATE` konvenci).
