# Revolvist Library

Contenuto editoriale della **Libreria Revolvist** — checklist pubblicate dall'autore e fetchate live dall'app.

L'app legge `library.json` da:

```
https://cdn.jsdelivr.net/gh/<USERNAME>/revolvist-library@main/library.json
```

jsDelivr serve il file con cache CDN globale e supporta `If-Modified-Since`. Per forzare un refresh immediato dopo un commit, basta cambiare il branch da `@main` a un tag versionato (`@v2`, ecc.) — quel pattern verrà introdotto solo se serve.

## Schema

```jsonc
{
  "version": 1,                    // bump solo su breaking change
  "updatedAt": "2026-05-29T00:00:00Z",
  "featuredId": "trekking-dolomiti-3gg",  // entry mostrata come hero in app
  "entries": [
    {
      "id": "kebab-case",
      "title": "Titolo",
      "sub": "Sottotitolo",
      "glyph": "🏔️",
      "gradient": ["#start", "#end"],
      "accent": "#hex",
      "categoryMeta": {
        "id": "kebab-case",            // identificatore stabile (es. outdoor)
        "name": "Nome categoria",      // label di default (IT) se il client non ha traduzione
        "glyph": "🏕️",
        "gradient": ["#start", "#end"],
        "accent": "#hex"
      },
      "items": [
        { "id": 1, "label": "...", "section": "...", "required": true }
      ]
    }
  ]
}
```

Quando l'utente importa una entry, l'app:
1. Cerca una categoria locale con lo stesso `categoryMeta.name` (case-insensitive).
2. Se non la trova, **crea la categoria** dal `categoryMeta` incluso nell'entry.
3. Aggiunge la checklist alla categoria (esistente o appena creata).

## Workflow di pubblicazione

1. Modifica `library.json` (rispettando lo schema sopra).
2. Aggiorna `updatedAt` con l'ora attuale ISO 8601 UTC.
3. Commit + push su `main`.
4. jsDelivr propaga in ~10 minuti (cache TTL default).

## Linee guida editoriali

- **Entry**: massimo ~30 elementi. Liste più lunghe scoraggiano l'uso.
- **Sezioni**: 3-5 sezioni per entry. Aiutano a scorrere.
- **`required: true`** solo per gli elementi che, se mancanti, renderebbero il viaggio/evento impossibile (documenti, biglietti).
- **Featured**: ruota stagionalmente. L'entry featured guadagna il banner hero in cima alla Libreria.

## Versioning

Lo `version` cambia solo quando aggiungiamo/rimuoviamo campi *obbligatori* dallo schema. L'app vecchia controlla il campo e, se trova una versione che non capisce, fa fallback all'`EMBEDDED_LIBRARY` bundled.
