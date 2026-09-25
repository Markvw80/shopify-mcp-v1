# SEO-audit tanterie.nl — 25 september 2026

## Aanpak en databronnen

Er is geen aparte SEO-audit-skill geïnstalleerd (gezocht in de enabled en beschikbare skills), dus de audit is gedaan met de tools die in deze sessie werken:

| Bron | Status | Gebruikt voor |
|---|---|---|
| Shopify Admin API (GraphQL) | ✅ | Alle 706 actieve producten, 44 collecties, 19 pagina's, 11 blogartikelen, 31 redirects, menu's, markten |
| Theme-code (hoofdtheme "Fix-bunzlau-voorraad-per-variant 2026-09-11") | ✅ | Meta-tags, canonical/hreflang, structured data, H1's, paginering |
| Shopify Analytics (ShopifyQL) | ✅ | Organisch verkeer, landingspagina's, conversie |
| Web search | ✅ (beperkt) | Steekproef van de indexatie |
| Live crawl van tanterie.nl | ❌ geblokkeerd door de netwerkpolicy van de sessie | — |
| Ahrefs | ❌ "Insufficient plan" | — |
| Semrush | ❌ geen API-units meer | — |

**Gevolg:** Core Web Vitals, backlinks, zoekwoordposities en de echte gerenderde HTML (statuscodes, redirects op domeinniveau) zijn **niet** gemeten. Die punten staan onderaan als "handmatig controleren".

---

## Samenvatting

Het organische verkeer groeit sinds juli flink (van ~15 naar ~300 zoeksessies per maand). Het leverde in de afgelopen 90 dagen echter maar **3 online bestellingen (€57)** op. Er zijn drie grote hefbomen:

1. **Collectiepagina's tonen maximaal 50 producten.** De custom collectiesecties gebruiken geen `paginate`. Daardoor zijn **84 van de 134 theeën** op `/collections/thee` en **29 van de 79 Bunzlau-producten** vanaf hun hoofdcollectie niet te zien en niet te crawlen.
2. **De productdata is onvolledig.** Van de 409 gepubliceerde producten hebben er 85 geen SEO-titel, 86 geen meta-description, 235 (57%) minder dan 50 woorden tekst, 14 geen enkele afbeelding en 24 geen publieke collectie.
3. **De structured data is inconsistent.** Er staat dubbel Product-schema op de Rice-, Joeff- en Waterfles-templates. Juist de theeproducten (de kern van de winkel) missen `shippingDetails` en `hasMerchantReturnPolicy`.

---

## 🔴 Kritiek

### 1. Collecties tonen maximaal 50 producten (geen paginering)
- `sections/tr-collection-redesign.liquid:259`, `tr-collection-bunzlau.liquid:331`, `tr-collection-rice.liquid:311` en de andere `tr-collection-*`-secties lopen met `{% for product in collection.products %}` door de producten, zonder `{% paginate %}`. Shopify geeft dan maximaal 50 producten terug.
- Er is ook geen "meer laden"-knop of paginalink.
- Getroffen: **/collections/thee** (134 gepubliceerd, 84 onzichtbaar) en **/collections/bunzlau** (79 gepubliceerd, 29 onzichtbaar). Andere collecties komen in de buurt (groene thee en zwarte thee hebben er elk 42).
- **Fix:** zet de productloop in `{% paginate collection.products by 48 %}` en voeg paginalinks toe (`<a href>`, geen JS-only). Alternatief: een "Toon meer"-knop die gewone `?page=2`-links gebruikt.

### 2. `webshop.tanterie.nl` staat in de Google-index
- Web search vindt `webshop.tanterie.nl/`, `/pages/contact` en `/products/mooie-dag` als aparte resultaten naast `tanterie.nl`.
- **Controleer** in Shopify → Instellingen → Domeinen dat `webshop.tanterie.nl` (en `www.`) als *redirect naar primair domein* staat. Staat de subdomein-DNS nog op een oude host, stel dan een 301 naar `https://tanterie.nl` in. Vraag daarna in Search Console verwijdering aan of laat de 301 het werk doen.
- Oude WordPress-URL's zoals `www.tanterie.nl/over-tante-rie/` (met slash aan het eind) worden ook nog gevonden. Controleer dat ze met een 301 doorsturen.

### 3. Organisch verkeer converteert bijna niet
- Zoeksessies per maand in 2026: jan 14 · feb 20 · mrt 15 · apr 13 · mei 11 · jun 38 · **jul 304 · aug 303 · sep 230** (tot en met de 25e).
- Conversie uit zoekverkeer: 0,35%. Bestellingen met search als referrer (90 dagen): 3, samen €57,48.
- 64% van de zoeklandingen gaat naar de homepage (444 van 692). Collecties en producten krijgen weinig directe zoekentree. Het merk rankt dus wel, maar de categorie- en productzoekwoorden (nog) niet.
- Desktop bouncet 80% (mobiel 59%).

---

## 🟠 Hoog

### 4. Structured data (JSON-LD)
| Template | Wat er nu gebeurt | Probleem |
|---|---|---|
| `product.rice`, `product.joeff`, `product.waterfles` | `tr-product-schema` **én** `schema-structured-data` (layout) | **2× Product + 2× BreadcrumbList** op één pagina. De skip-lijst in `snippets/schema-structured-data.liquid` bevat alleen `bakmix`, `blond`, `bunzlau` en `cro`. |
| `product` / `product.thee` (tr-product-redesign) en `product.accessoires` | alleen de basis-Product uit `schema-structured-data` | Geen `shippingDetails` en geen `hasMerchantReturnPolicy`, dus waarschuwingen in Search Console (Merchant listings). |
| `product.bunzlau` | `tr-product-schema` met verzending €4,50 NL | De collectietekst zegt dat keramiek **alleen afhaalbaar** is, maar de schema belooft verzending. Dat is inconsistent (risico bij Merchant Center en rich results). |
| Collectiesecties `tr-collection-*` | eigen `WebSite`-blok | Het `WebSite`-schema staat al in `layout/theme.liquid`, dus dit is dubbel. |
| Homepage (`tr-reviews`) | `ItemList` met `Review`s van verschillende producten | Reviews van producten op de homepage komen niet in aanmerking voor rich results. Weinig nut, maar ook geen schade. |

**Fix:** voeg `rice`, `joeff` en `waterfles` toe aan de skip-lijst. Render `tr-product-schema` ook in `tr-product-redesign` en `tr-accessoires-redesign` (dan ook `''`/`thee`/`accessoires` aan de skip-lijst toevoegen). Voor Bunzlau-keramiek: gebruik een `OfferShippingDetails` met `doesNotShip: true`, of laat `shippingDetails` weg.

### 5. Product-SEO-velden (409 gepubliceerde producten)
| Issue | Aantal |
|---|---|
| Geen SEO-titel | 85 (Tante Rie 31, Bunzlau 22, Natural Flower Cards 13, Blond 10, overig 9) |
| Geen meta-description | 86 |
| SEO-titel > 60 tekens (wordt afgekapt) | 85, meest het patroon "Naam – Omschrijving \| Tante Rie" |
| SEO-titel < 30 tekens | 49 |
| Dubbele titels | 3 paren (Thee Maatschepje, Arts & Craft bewaarblik, Mama & Baby thee) |
| Dubbele description-snippets | 5 groepen, samen 12 producten |

Tip: laat `| Tante Rie` weg uit lange titels. Google toont de sitenaam toch al.

### 6. Dunne content en ontbrekende media
- **235 producten (57%)** hebben minder dan 50 woorden beschrijving: Tante Rie 117, Bunzlau 81, Natural Flower Cards 24.
- **41 producten hebben een lege beschrijving**, onder andere `lavendel`, `hibiscus-thee`, `houten-honinglepel`, `thee-ei`, alle `blikje-*` en de Blond-seizoensartikelen.
- **14 gepubliceerde producten hebben geen afbeelding**: `blikje-bij`, `blikje-herfst`, `blikje-paarse-bloemen`, `blikje-paarse-bloemen-rond`, `blikje-vogels`, `beker-vogels`, `beker-paarse-bloem`, `thee-ei-bij`, `thee-ei-kat`, `thee-ei`, `thee-filter-zonder-uitlekschaaltje`, `chocoladebal`, `hibiscus-thee`, `bunzlau-textiel`. Óf aanvullen, óf depubliceren.
- 220 producten hebben maar 1 afbeelding en 66 afbeeldingen zijn kleiner dan 800 px. Google Shopping en Image Search werken beter met ≥ 1200 px.
- Alt-teksten: 21 afbeeldingen zonder alt (Blond 10, Joeff 6, Pineut 3). Bij 82 afbeeldingen is de alt letterlijk gelijk aan de producttitel. Beschrijf beter wat er te zien is (bijv. "zijaanzicht", "sfeerbeeld").

### 7. Wees-producten en voorraad
- **24 gepubliceerde producten zitten in geen enkele publieke collectie**, dus er linkt intern niets naar ze. Het gaat vooral om Blond-seizoensartikelen (Sint, Halloween, "Even bijkletsen") plus `coldbrew-theefles-kaki`, `opa`, `chocoladebal` en `bunzlau-textiel`.
- 59 gepubliceerde producten hebben voorraad ≤ 0 (Bunzlau 18, Tante Rie 15, Natural Flower Cards 15). Zijn ze tijdelijk uitverkocht, laat ze dan staan (schema meldt OutOfStock). Zijn ze definitief weg, stuur dan met een 301 door naar de collectie.
- 297 van de 706 actieve producten staan niet op het Webshop-kanaal. Dat is prima als het bewust POS-only is.
- 20 handles hebben `-kopie`/`-1`-suffixen (zoals `english-breakfast-1`, `china-sencha-bio-1`, `proefpakket-groene-thee-kopie` en 4× `pineut-…-kopie`). Hernoem ze naar een schone handle en laat Shopify automatisch een redirect aanmaken.
- 171 producten hebben geen producttype. Dat raakt ook `tr-product-schema`: de retour-logica (`is_thee`) kijkt naar `product.type`, en Google Shopping gebruikt het type ook.

### 8. Collecties
- **Gepubliceerd zonder SEO-titel, description en intro-tekst:** `kaartjes`, `tegels-anita` (staat al in Google als "Tegels Anita – Tante Rie"), `nfc-natural-flower-cards`, `glazen`, `dutch-tea-maestro`, `koffiekaravaan`, `return-to-sender`. Ook `bakzolder`, `my-flame`, `geven-is-leuker` en `centsweets` hebben geen intro-tekst (die zijn wel ongepubliceerd).
- **`frontpage` staat gepubliceerd met 0 producten**, met als titel "Losse Thee & Thee Accessoires | Tante Rie – Voor Elk Theemoment". Dit is een lege pagina die indexeerbaar is. Depubliceren.
- **Collectietitels met SEO-streepjes worden de H1:** "Seizoensfavorieten Thee | Perfecte Thee voor Elk Seizoen – Tante Rie". Gebruik als titel alleen "Seizoensfavorieten" en zet de rest in het SEO-titelveld.
- `oolong-thee` (1 product) en `sri-lanka` (2 producten) zijn erg dun als losse landingspagina.
- Afbeeldingen van collectie-hero's zonder alt: `kruidenthee`, `zwarte-thee`, `rooibos` (lege string) en `thee`, `witte-thee`, `thee-bewaarblikken`, `theefilters-en-maatschepjes` (null).
- `maak-je-eigen-theemix` verwijst naar template-suffix `maak-je-eigen-theemix`, maar dat template bestaat niet (het valt terug op de standaard).
- SEO-titels boven 60 tekens: `vruchtenthee` (64, en met het automatische " – Tante Rie" erbij 76), `zwarte-thee` (61), `juffen-en-meesters-cadeaus` (66), `bakzolder` (67).

---

## 🟡 Middel

### 9. Pagina's
- **Geen meta-titel of meta-description** op: `cold-brew-thee` (19 zoeksessies, de beste niet-homepagina onder de pagina's!), `theewinkel-borne-twente` (de lokale SEO-pagina!), `over-ons`, `veelgestelde-vragen`, `contact`, `verzendbeleid`, `retourbeleid-tante-rie`, `algemene-voorwaarden` en `privacy-policy`.
- **Kannibalisatie door oude WordPress-pagina's:** `/pages/thee-en-koffie` versus `/collections/thee`, `/pages/serviezen` versus `/collections/servies`, `/pages/cadeaus-en-lekkers` versus `/collections/cadeaus`. Ze staan in het footermenu en krijgen nog zoekverkeer (13 en 9 sessies). Voeg de unieke content samen in de collectie en zet een 301. Of maak er duidelijke gidsen van die naar de collecties linken.
- `muuss-van-joeff-…` begint in de body met inline CSS (`.joeff-landing{…}`), wat Shopify als samenvatting en snippet oppakt. Zet de CSS in een asset.

### 10. Blog
- Er zijn 10 artikelen gepubliceerd. **Het laatste is van 22 juni 2026.** Het concept "Herfstthee: welke thee past bij de herfst?" staat nog ongepubliceerd (zonder meta-tags en afbeelding). Nu is precies het moment om het te publiceren.
- De handle `een-reis-door-de-theeregios-japan-witte-thee` hoort bij een artikel over **China**. Hernoem die met een redirect.
- De handle `blogreeks-een-reis-door-door-de-verschillende-thee-regios` bevat "door door".
- Typfout in de titel: "Top 5 accessoiress die je echt moet hebben".
- Negen van de tien artikelen hebben geen tags. Link vanuit elk artikel naar de relevante collectie en producten (Sri Lanka → `/collections/sri-lanka`, Japan → `/collections/groene-thee`).

### 11. Feitelijke consistentie (E-E-A-T en entity)
- De homepage (`about_title`), `tr-page-lokaal` en `llms.txt` zeggen allemaal **"geopend in 2024"**. Externe bronnen (Borne Boeit, RTV Borne "bestaat 5 jaar", bedrijvengidsen) zeggen dat Anke Kamp de winkel in **2014** opende, en het eigen blog "Even voorstellen" vertelt over de overdracht van Anke aan Nikkie. Maak het verhaal consistent ("sinds 2014, sinds 2026 onder Nikkie"). Google en AI-assistenten wegen tegenstrijdige feiten mee.
- `llms.txt` noemt Gyokuro, Matcha en Dragonwell als assortiment. In de catalogus staat alleen de blend "Matcha Princess". Pas dit aan, zodat AI-antwoorden geen producten beloven die er niet zijn.

### 12. Redirects (31 stuks)
- **Keten:** `/products/cadeaupakket` → `/products/cadeaupakket-beste-van-borne` → `/collections/cadeaus`. Laat de eerste direct naar `/collections/cadeaus` wijzen.
- Redirects naar de homepage (`/overige`, `/home`, `/pages/zakelijk-bestellen`) ziet Google als soft 404. Stuur ze liever naar een relevante pagina.

---

## 🟢 Laag / technisch in orde

- ✅ Canonical-tags, unieke `<title>`-opbouw, OG- en Twitter-tags zijn goed ingericht (`snippets/meta-tags.liquid`).
- ✅ Eén H1 per template. Homepage-H1: "Losse thee, vers verpakt in Borne" (goed).
- ✅ `LocalBusiness`-schema met NAP, openingstijden, geo en `sameAs`. Artikelschema op blogposts. `CollectionPage` en `BreadcrumbList` op collecties.
- ✅ De Judge.me-reviews worden server-side gerenderd en in `aggregateRating` opgenomen.
- ✅ Alle theme-afbeeldingen hebben `width`/`height` en `loading="lazy"` (goed voor CLS).
- ℹ️ `hreflang="nl-NL"` en `x-default` wijzen naar dezelfde URL, terwijl de markt België actief is zonder eigen URL. Overweeg `hreflang="nl"` (dekt NL en BE).
- ℹ️ `<meta name="theme-color" content="">` is leeg. Vul het in of verwijder het.
- ℹ️ `SearchAction` (sitelinks-zoekvak), `HowTo` en de meeste `FAQPage`-rich results toont Google niet meer. Ze kunnen blijven staan, maar leveren niets op.

---

## Prioriteitenlijst

| # | Actie | Impact | Moeite |
|---|---|---|---|
| 1 | Paginering in alle `tr-collection-*`-secties | Hoog | Laag |
| 2 | Redirect `webshop.`/`www.` naar het primaire domein controleren | Hoog | Laag |
| 3 | Skip-lijst schema uitbreiden + `tr-product-schema` op thee- en accessoire-templates | Hoog | Laag |
| 4 | SEO-titel en description voor 85 producten + 9 pagina's (worklist in CSV) | Hoog | Middel |
| 5 | `frontpage` depubliceren, 7 kale collecties voorzien van titel, description en intro | Middel | Laag |
| 6 | 14 producten zonder afbeelding aanvullen of depubliceren, 24 wees-producten in een collectie zetten | Middel | Laag |
| 7 | Herfstthee-blog publiceren, blog-handles en typfouten fixen, interne links toevoegen | Middel | Laag |
| 8 | "Sinds 2024" en `llms.txt` corrigeren | Middel | Laag |
| 9 | Beschrijvingen uitbreiden voor 235 dunne producten (begin bij de theeën met verkeer) | Hoog | Hoog |
| 10 | Oude `/pages/thee-en-koffie`, `/serviezen` en `/cadeaus-en-lekkers` samenvoegen en doorsturen | Middel | Middel |

## Handmatig controleren (niet meetbaar in deze sessie)
- PageSpeed Insights / Core Web Vitals (homepage, `/collections/thee`, een theeproduct): de theme laadt `base.css` (104 kB) plus Google Fonts DM Sans.
- Google Search Console: dekking/indexering, Merchant listings-waarschuwingen, zoekwoorden per pagina.
- Backlinks en zoekwoordposities: Semrush heeft extra API-units nodig, of een Ahrefs-plan met API-toegang.

## Bijlage
- `producten-actiepunten.csv`: 315 gepubliceerde producten met minstens één actiepunt (puntkomma-gescheiden, opent in Excel).
