# Doorgevoerde fixes — 25 september 2026

## ✅ Live doorgevoerd (winkeldata)

| Onderdeel | Wijziging |
|---|---|
| Blog | Titel "Top 5 accessoire**ss**…" gecorrigeerd naar "Top 5 accessoires die je echt moet hebben" |
| Blog | Handle `een-reis-door-de-theeregios-japan-witte-thee` → `een-reis-door-theeregios-china-witte-thee` (automatische 301) |
| Blog | Handle `blogreeks-een-reis-door-door-de-verschillende-thee-regios` → `blogreeks-een-reis-door-de-verschillende-theeregios` (automatische 301) |
| Redirect | Keten opgeheven: `/products/cadeaupakket` wijst nu direct naar `/collections/cadeaus` |
| Collectie | Titel/H1 "Seizoensfavorieten Thee \| Perfecte Thee voor Elk Seizoen – Tante Rie" → "Seizoensfavorieten" (handle en SEO-titel ongewijzigd) |
| Collecties | SEO-titels ingekort tot ≤ 60 tekens: `vruchtenthee`, `zwarte-thee`, `juffen-en-meesters-cadeaus`, `thee-accessoires` |
| Collecties | Alt-tekst toegevoegd aan de hero-afbeelding van `thee`, `zwarte-thee`, `kruidenthee`, `rooibos`, `witte-thee`, `thee-bewaarblikken`, `theefilters-en-maatschepjes` (dezelfde afbeelding, geen nieuwe upload) |
| Pagina's | Meta-titel + meta-description ingevuld voor `cold-brew-thee`, `theewinkel-borne-twente`, `over-ons`, `veelgestelde-vragen`, `contact`, `verzendbeleid`, `retourbeleid-tante-rie`, `algemene-voorwaarden`, `privacy-policy` |

## 🧪 Klaar in de theme-kopie "SEO-audit-fixes 2026-09-25" (nog NIET live)

Theme-ID `202361864456`, een kopie van het live theme van 25-09. Publiceren moet vanuit Shopify-admin (Online Store → Themes); de API-tool mag dat niet.

| Bestand | Wijziging |
|---|---|
| `snippets/tr-pagination.liquid` (nieuw) | Crawlbare paginering met `<a href>`-links, `rel="prev/next"`, `aria-current`. Filters en sortering blijven behouden. |
| `sections/tr-collection-redesign.liquid` | Productgrid in `{% paginate collection.products by 48 %}`. Hiermee toont /collections/thee alle 134 theeën (3 pagina's) in plaats van 50. Geldt ook voor cadeaus en de andere collecties die dit template gebruiken. |
| `sections/tr-collection-bunzlau.liquid` | Idem. Alle 79 Bunzlau-producten zijn nu bereikbaar (2 pagina's). |
| `snippets/schema-structured-data.liquid` | Precies één Product- en BreadcrumbList-schema per productpagina. Rice, Joeff en Waterfles krijgen geen dubbel schema meer. Het standaard-, thee- en accessoire-template krijgen het volledige `tr-product-schema` (met verzending en retour). |
| `templates/index.json` | Homepage "Over ons"-blok: "sinds 2024" / "opende in 2024" → **2014** |
| `sections/tr-page-lokaal.liquid` | Lokale Borne-pagina: "opende in 2024" → **2014** |
| `templates/llms.txt.liquid` | "opende in 2024" → **2014**; blog-links bijgewerkt naar de nieuwe handles (witte thee China, blogreeks) |
| `snippets/tr-product-schema.liquid` | Bunzlau- en Blond-templates: `doesNotShip` + `OnSitePickup` + retour in de winkel, conform "alleen afhalen". |

Geverifieerd: de MD5-checksums (ook van de 2014-correctie) van alle geüploade bestanden komen overeen met de lokale versies, behalve `tr-collection-bunzlau.liquid`. Daar verschilt alleen het aantal decoratieve `─`-tekens in een paar commentaarregels; de code is identiek.

**Test vóór publicatie (preview van het theme):**
1. `/collections/thee`: onderaan staan paginalinks; pagina 2 en 3 tonen andere theeën; filters blijven werken.
2. `/collections/bunzlau`: 2 pagina's, en de decorfilter werkt nog.
3. Een theeproduct, een Rice-product en een Bunzlau-product in de [Rich Results Test](https://search.google.com/test/rich-results): precies één `Product`.

Nog niet gedaan: de overige `tr-collection-*`-secties (groene thee 42, zwarte thee 42, blond 40, …) zitten nu onder de 50 producten. Pas dezelfde wrap toe zodra een collectie groeit. De aangepaste versies staan klaar in de scratchpad.

## ⛔ Niet uitgevoerd (geblokkeerd of jouw beslissing)

- **Collectie `frontpage` depubliceren**: de tool blokkeert unpublish-acties. Doe dit in de admin: Collecties → frontpage → Verkoopkanalen → Webshop uit.
- **SEO-titels en meta-descriptions voor ~52 producten**: bulkwijziging op live producten, geweigerd door de sessiepermissies.
- **34 lege of kassa-achtige producten online** (bijv. `blikje-bij`, `thee-ei-kat`, `chocoladebal`, Sint/Halloween-items, "Tante Rie Cadeaubon" met beschrijving "x§x"): moeten die online staan?
- **Kale gepubliceerde collecties** `kaartjes`, `glazen`, `return-to-sender` (bevat producten van €0,00), `dutch-tea-maestro`, `koffiekaravaan`, `tegels-anita`, `nfc-natural-flower-cards`: online houden (dan SEO-tekst schrijven) of depubliceren?
- **`webshop.tanterie.nl`**: controleren in Instellingen → Domeinen (redirect naar het primaire domein).

## Correctie op het auditrapport
- Het `WebSite`-blok in de collectiesecties is een `isPartOf`-verwijzing binnen `CollectionPage`, geen dubbel schema. Die bevinding vervalt.
- Nieuw gevonden: `tr-collection-bunzlau` linkt naar `/collections/losse-thee` (een redirect naar `/collections/thee`). Maak hier een directe link van bij een volgende theme-wijziging.
