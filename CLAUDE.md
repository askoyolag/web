# askoyolag/web

Nettsiden til Askøy orienteringslag: https://askoyolag.no/

Statisk Hugo-side med temaet [hugo-scroll](https://themes.gohugo.io/themes/hugo-scroll/),
lastet inn som git-submodul i `scroll/themes/hugo-scroll`. All Hugo-konfigurasjon og
alt innhold ligger under `scroll/`. `public/` er byggeoutput og committes ikke inn
(bygges av CI).

## Struktur

- `scroll/config.toml` — siteconfig (tittel, favicon, baseURL, metadata).
- `scroll/content/_index.md` — cover-tittel og ingress (`header_headline`,
  `header_subheadline`) og toppbildet (`header_image`).
- `scroll/content/homepage/*.md` — én fil per seksjon på forsiden. Hver fil blir
  sitt eget `<article>`-bånd på siden, i rekkefølge etter `weight` (lavest
  først). `title` i front matter blir automatisk overskriften i seksjonen (ikke
  legg inn en ekstra `## Tittel` i selve teksten — det gir dobbel overskrift).
  Sett `header_menu: true` for å i tillegg få en knapp i toppmenyen — bruk det
  sparsomt (typisk «Bli med» og «Kontakt»).
- `scroll/assets/` — bilder og annet som skal kjøres gjennom Hugos
  ressurspipeline (`resources.Get`), f.eks. `header_image` og favicon.
  **Statiske bilder i `<img>`/`{{< figure >}}` (som ikke trenger
  responsiv/webp-behandling) ligger fortsatt i `scroll/static/images/`.**
- `scroll/layouts/` — prosjektnivå-overstyringer av temaet (IKKE inni
  `scroll/themes/hugo-scroll`). `scroll/layouts/partials/custom_head.html` er
  temaets offisielle utvidelsespunkt for ekstra `<head>`-innhold, og er der
  klubbfargene lastes inn.

## Klubbfarger

Definert som CSS-variabler i `scroll/assets/css/klubbfarger.css` og lastet inn
via `scroll/layouts/partials/custom_head.html`. **Rediger denne filen for
fargeendringer — ikke temafiler.** Siden filen lastes etter temaets eget
stilark, overstyrer den temaets CSS-variabler (definert i
`scroll/themes/hugo-scroll/assets/css/variables.scss`) uten at temaet endres.

```
--aol-gronn:  #1E5B3A   overskrifter, seksjonsbånd (mørk alternerende bakgrunn), footer
--aol-rod:    #E40038   knapper, aksenter, lenker ved hover (i lyse seksjoner)
--aol-marine: #032E49   cover-tittel/ingress, knapp-hover — mørke flater der grønt blir for svakt
```

Merk: rødt på det grønne seksjonsbåndet/footeren gir for lav kontrast
(~1.7:1), så der brukes lys krem i stedet for rødt på hover-lenker og
overskrifter — se kommentarene i `klubbfarger.css` for detaljene.

**Ved fargeendringer:** regn ut kontrastforhold (WCAG AA: ≥4.5:1 for normal
brødtekst, ≥3:1 for stor tekst/UI-elementer) for hver tekst/bakgrunn-
kombinasjon som endres, ikke bare anta at det ser bra ut.

## Språk og tone

- Norsk bokmål, du-form, korte setninger. Unngå klisjeer som «vi tilbyr et
  variert tilbud for hele familien».
- Klubben bruker **emiTag** (trådløs registrering) — skriv aldri at man
  «stempler».
- «Nærløp i Bergensregionen», ikke «rundt Bergen».
- Torsdagstreningene går hele året (helårs, ikke sesongbasert). Det er
  nærløpene og vinterens innendørstilbud som er sesongbaserte.
- Idrettsskolen/innetreningen er på **Tveit skole**, ikke Kleppe.
- Ingen personnavn, ingen medlemstall, ingen postadresse på siden.
- Verdiene («AOL»-akronymet: Allsidige/Opplevelser/Læring) skal gjengis
  ordrett fra klubbhåndboken (kap. 4.2) ved endringer — spør om ordlyden hvis
  du er usikker på om den er oppdatert.
- E-post er `leder@askoyol.org` — ikke bytt til en `@askoyolag.no`-adresse før
  noen bekrefter at den er satt opp.
- Org.nr. 983 509 398.

## Bygg og publisering

- Lokalt: `cd scroll && hugo server` (utvikling) eller
  `hugo --minify -d ../public/` (produksjonsbygg).
- Krever Hugo v0.147.3+ **extended**, og **Dart Sass** i PATH (temaet bruker
  `transpiler: "dartsass"` for CSS — se `scroll/themes/hugo-scroll/layouts/
  partials/head.html`). Uten Dart Sass feiler bygget med «no Dart Sass binary
  found in $PATH». Installer f.eks. med `npm install -g sass` eller last ned
  en native dart-sass-binær fra GitHub-releasene til `sass/dart-sass` og legg
  den i PATH som `dart-sass`.
- CI (`.github/workflows/pages.yml`) installerer Dart Sass med
  `sudo snap install dart-sass`, bygger med `peaceiris/actions-hugo`, og
  deployer til GitHub Pages via `actions/deploy-pages`. **Ikke rediger denne
  workflow-filen** uten eksplisitt beskjed — den trigges automatisk på push
  til `main`, og en feil her tar ned auto-deploy.
- **Ikke rediger filer inni `scroll/themes/hugo-scroll/` (git-submodul).**
  Prosjektnivå-overstyringer legges i `scroll/layouts/` eller
  `scroll/assets/`, ikke i temaet.
- Custom-domenet `askoyolag.no` er satt opp i GitHub Pages-innstillingene
  (ikke i repoet) med `scroll/static/CNAME`.

## Arbeidsflyt for innholdsendringer

1. Les gjeldende `scroll/content/homepage/*.md`, `scroll/content/_index.md`
   og `scroll/assets/` før du endrer noe, og rapporter kort hva som finnes.
2. Gjør endringene i egen branch.
3. Bygg lokalt (se over) og sjekk at siden ser riktig ut før du foreslår å
   pushe — spesielt seksjonsrekkefølge og at ingen seksjon får dobbel
   overskrift.
4. Push/merge til `main` kun når brukeren har bedt om det — `main` er live
   og trigges automatisk av Pages-workflowen.
