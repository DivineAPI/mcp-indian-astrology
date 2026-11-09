# Changelog

## [2.13.0] - 2026-11-09

### Added

**New tool: `divine_get_graha_drishti` (DV-619).** Wraps the Graha Drishti API, `POST /indian-api/v1/aspects` on astroapi-3 (backend PR Dinesh-Gahlot/astroapi-3.divineapi.com#40, parent ticket DV-608; Vedic Ananta plan). It takes the standard birth details (`KundliInput`) plus the optional `node_type` (`meannode`/`truenode`); an invalid `node_type` is a tool error (`isError: true`).

Parashari drishti on whole-sign houses counted from the Lagna: every graha aspects the 7th house from itself; Mars also the 4th and 8th, Jupiter the 5th and 9th, Saturn the 3rd and 10th, and Rahu and Ketu the 5th and 9th. Only the nine grahas Sun to Ketu are covered. The response has `planets` (name, sign, house, `aspects` with the aspect number, target house, its sign and the planets in it, and `aspected_by`) and `houses` (house, sign, planets, `aspected_by`).

This is a different system from the KP aspects added to `divine_get_kp_planetary_sub` in 2.12.0, which measure aspects in degrees with an orb and give Rahu and Ketu none. The tool description says so, to keep a model from mixing them up.

Verified live on 2026-11-09: `node_type` is honoured by the backend (near Rahu's 2025-05-18 sign change, `truenode` puts Rahu in Pisces where `meannode` puts it in Aquarius), even though the docs page does not list it. `lan` is accepted but ignored, so names always come back in English; the description says so. Tool count: 129 -> 130.

## [2.12.0] - 2026-11-09

### Changed

**`divine_get_kp_cuspal_sub` and `divine_get_kp_planetary_sub` now use the v2 endpoints (DV-589).** Both tools moved from `/indian-api/v1/kp/...` to `/indian-api/v2/kp/...` on astroapi-3 (backend PR Dinesh-Gahlot/astroapi-3.divineapi.com#35, parent ticket DV-579). Parameters are unchanged, and `node_type` and `lan` work on v2 as they did on v1.

v2 adds KP aspect data to each row of `table_data`. Nothing existing is removed or renamed:

| Tool | New fields per row |
| --- | --- |
| `divine_get_kp_cuspal_sub` | `aspected_by`: the planets aspecting that cusp |
| `divine_get_kp_planetary_sub` | `conjunction`: planets within 5 degrees; `aspected_by`: planets aspecting it; `aspect_to`: `{planets, houses}` it aspects |

KP aspects are Vedic aspects measured by degree. Every planet aspects the 7th (180 degrees); Mars also the 4th and 8th, Jupiter the 5th and 9th, and Saturn the 3rd and 10th. Rahu and Ketu cast no aspect. The orb is 8 degrees (Moon 12; Uranus, Neptune and Pluto 5). With `lan` other than `en`, the planet names in these lists are translated; house numbers are not.

The v2 handler's nakshatra spelling fix ("Mrigshira" to "Mrigashira", "Shravana" to "Shravan") applies only to `cuspal`, which `divine_get_kp_cuspal` already called on v2. The significator table stays on v1: v2 does not serve it. Tool count unchanged at 129.

## [2.11.0] - 2026-10-01

### Added

**New tool: `divine_get_jaimini_swamsa_chart` (DV-460).** Wraps the new `/indian-api/v1/swamsa-chart` endpoint (astroapi-3, backend PR Dinesh-Gahlot/astroapi-3.divineapi.com#23). The Jaimini Swamsa chart is the Navamsa (D9) chart's planets recast into houses with the Karakamsha sign (the Atmakaraka's D9 sign) as house 1. It takes the standard birth details (`KundliInput`, including `lan`) plus the optional `node_type` (`meannode`/`truenode`) that its sibling `divine_get_jaimini_karakamsha_lagna` also exposes, and returns `atmakaraka`, `swamsha_sign` / `swamsha_sign_no`, `lagnamsha_sign` / `lagnamsha_sign_no` (the D9 Ascendant's sign), `svg` and `base64_image`. With `lan="hi"` the names come back translated; the sign numbers do not change. `node_type=truenode` only moves Rahu and Ketu in the drawn chart (the Atmakaraka is one of the 7 planets Sun to Saturn, so the karaka and both signs stay the same); an invalid value comes back as a tool error (`isError: true`).

It is a sibling of `divine_get_jaimini_karakamsha_lagna`, not a duplicate: that tool recasts the natal (D1) planets around the same Karakamsha sign, this one recasts the D9 planets. The route is top-level `/indian-api/v1/swamsa-chart`, not under `/jaimini-astrology/`. Endpoint live-verified (success=1) before adding, and the tool live-tested over stdio after. Tool count: 128 -> 129.

## [2.10.1] - 2026-09-21

### Fixed

**`shraavana` festivals now use the v3 endpoint (DV-374).** In v2, four of the sixteen shraavana festivals — `hariyaali_teej`, `gayatri_jayanti`, `narali_purnima` and `sanskrit_divas` — returned their date under `"dates"` (plural) instead of `"date"`, which no other festival and no other Hindu month does. v3 corrects exactly those four. Everything else in the response is identical, and v3 supports `include_name` and `lan` the same way.

`shraavana` is the **only** month with a v3 — all other eleven are v2-only, verified against astroapi-3. The endpoint map now carries the version per month rather than hardcoding `v2` in the URL, so a future month gaining a v3 is a one-word change.

`shraavana_somvaar_vrat` and `mangla_gauri_vrat` still use `"dates"` in v3, and that is **correct** — they fall on every Monday and Tuesday of the month and return four dates as `{"1": ..., "2": ...}`. The v2 bug was single string values mislabelled as plural, not the plural key itself.

## [2.10.0] - 2026-09-17

### Added

**`include_name` and `lan` on all six festival tools (DV-352).** The festival APIs gained two optional parameters; none of the MCP's festival tools could pass either, because the Pydantic models use `extra="forbid"`.

`include_name` adds a human-readable `name` to every festival (`"Anant Chaturdashi"` instead of only the raw key `anant_chaturdashi`) — the gap the client reported in DV-338. `lan` localises those names into eight languages.

Affected tools: `divine_get_festivals_by_date`, `divine_get_english_calendar_festivals`, `divine_get_malayalam_festivals`, `divine_get_tamil_festivals`, `divine_get_sankranti_festivals`, `divine_get_festivals_by_month`.

**`include_name` defaults to `YES` in the MCP**, which differs from the REST API default of `NO`. The API default exists to protect existing REST consumers from a new response field; an MCP result is read by a model, where an extra field cannot break a parser and raw keys like `chhath_puja` are actively worse. Pass `include_name="NO"` for the old shape.

**Five of the six festival tools previously had no `lan` parameter at all**, so festivals could not be localised through the MCP regardless of this change. They do now.

### Fixed

**`PanchangInput.lan` advertised seven language codes the API rejects.** The description read `en, hi, ta, te, kn, ml, bn, gu, mr, pa, or, ur`. Verified 2026-09-17 against astroapi-1 `/find-panchang` and astroapi-3 (every festival endpoint): the APIs accept **`en, hi, bn, ma, tm, tl, ml, kn`** and reject `ta`, `te`, `mr`, `gu`, `pa`, `or`, `ur` with `"Please enter valid language"`.

These are deliberately **not** ISO 639-1 codes — Marathi is `ma` not `mr`, Tamil `tm` not `ta`, Telugu `tl` not `te`. The old description steered callers to codes that always failed. Invalid codes are now rejected locally with the correct list instead of costing a round trip.

## [2.9.1] - 2026-09-17

### Fixed

**Pinned `mcp[cli]` to `<2`.** The dependency was `>=1.2.0` with no upper bound. mcp 2.x renamed `FastMCP` to `MCPServer` and removed `mcp.server.fastmcp`, which `server.py` imports at module level, so any image rebuild would have installed mcp 2.x and crashed the container on startup. Production runs 1.28.1. No behaviour change — this only prevents a future rebuild from breaking.

## [Unreleased]

### Fixed

`divine_get_planet_retrograde_transit` now accepts all **8** planets the live API supports (mercury, venus, mars, jupiter, saturn, uranus, neptune, pluto) instead of the 5 it declared, and validates the path selector against a new `VALID_RETROGRADE_PLANETS` set. Previously it under-declared by 3 (uranus/neptune/pluto), blocking valid calls, and had no validation at all, so rahu/ketu were passed through and the backend answered with an unhandled HTTP 500 instead of a clean error. Live-verified 2026-07-22 during the path-selector sweep.

## [2.8.0] - 2026-07-21

### Added

**Varshphal (annual chart / Tajika) suite - 14 new tools** wrapping `/indian-api/v1/varshphal/*` (astroapi-3): varsha_pravesh, basic_astro_details, planetary_positions, horoscope_chart (D1..D60 chart id in the path), tajika_aspect, muntha, panchadhikari, tri_pataki_chakra, mudda_dasha, yogini_dasha, patyanini_dasha, planetary_strengths, sahams, yogas. Each takes birth details (KundliInput) + `varshphal_year`. Distinct from the existing Lal Kitab varshphal tools (`/lal-kitab/varshphal/*`). All live-verified (success=1). Tool count: 114 -> 128.

## [2.7.0] - 2026-07-21

### Added

**New tool: `divine_get_rudraksha_suggestion`** wrapping `/indian-api/v1/rudraksha-suggestion` (astroapi-3). Takes standard birth details (KundliInput) and returns the recommended life, lucky, and dasha Rudraksha beads. Endpoint live-verified (success=1) before adding. Tool count: 113 -> 114.

## [2.6.0] - 2026-07-21

### Added

**Muhurat Finder API - 9 new tools** wrapping `/indian-api/v1/muhurat/*` (astroapi-3): `divine_get_muhurat_marriage`, `_house_entering`, `_vehicle_purchase`, `_property_purchase`, `_business_start`, `_foundation_laying` (month-scoped: month + location + lan), plus `_do_ghati`, `_hora`, `_jain_pachakkhan` (date-scoped: day + location). Every endpoint live-verified (success=1) before adding. Tool count: 104 -> 113.

### Fixed

`divine_get_grah_gochar` now accepts the three outer planets (uranus, neptune, pluto) in addition to the nine grahas, matching the live API (verified: all 12 planets return success=1). A new `VALID_TRANSIT_PLANETS` set gates this tool only; the shared `VALID_PLANETS` (9 grahas) is unchanged so the Vedic dasha / analysis tools stay restricted. The planet field description now lists all 12.

`divine_get_planet_nakshatra_transit` now validates its planet against `VALID_NAKSHATRA_TRANSIT_PLANETS` (the 10 supported: classical + uranus/neptune/pluto) and rejects rahu/ketu with a clear message. Previously it had no validation and passed the nodes straight through to the backend, which returns HTTP 500 for them (live-verified 2026-07-21). The field description was also corrected: it wrongly listed rahu/ketu and omitted the three outer planets.

## [2.5.0] - 2026-07-10

### Added

**6 new Kundli analysis tools** wrapping new `/indian-api/v1/*` endpoints (astroapi-3):
`divine_get_vargottama_planets` (planets sharing the D1 and D9 sign),
`divine_get_bhav_bala` (numeric strength of the twelve houses: bhavadhipati and disha bala),
`divine_get_shani_ashtam_shani` (Ashtama Shani: Saturn transiting the 8th from natal Saturn, with age-banded content),
`divine_get_bhava_analysis` (house-by-house sign/lord/occupants/aspects), and
`divine_get_bhava_group_predictions` (predictions grouped by kendra/trikone/trishadaya/trik/maraka/dhana) take the standard birth details.
`divine_get_planet_remedies` adds an `analysis_planet` selector (sun..ketu) and returns donation, gemstone, lifestyle, mantra and the weaknesses addressed.
Every endpoint live-verified before adding and every tool live-tested after. Tool count: 98 -> 104.

## [2.4.1] - 2026-07-08

### Added

Add server-level instructions (how-to-use note read at connect) and dasha-chain hints (vimshottari to the dasha-analysis tools, lal-kitab mahadasha to antardasha).

## [2.4.0] - 2026-07-08

### Added

**New tool: `divine_get_sankranti_festivals`.** Wraps
`POST /indian-api/v1/sankranti-festivals` (astroapi-3). Returns the twelve solar
Sankranti transitions (Makar, Kumbha, Meena, Mesha, etc.) for a year with dates,
transition moments, and punya kala windows. Params: year, place, lat, lon, tzone.
Verified live before adding and live-tested after. Tool count: 97 -> 98.

## [2.3.0] - 2026-07-08

### Added

**Lal Kitab suite: 16 new tools.** Wraps the new /indian-api/v1/lal-kitab/*
endpoints (astroapi-3): planetary positions, horoscope chart, house position,
conjunctions, teva, planet analysis (selector), dasha, planet types,
mahadasha/antardasha content (text readings, no birth data; the antardasha
pair must be valid for the mahadasha and the API guides on mismatch), debts,
house signification (house_no 1-12), and the four varshphal (annual chart)
tools taking varshphal_year. Every endpoint live-verified before adding and
every tool live-tested after (18/18 scenarios). Tool count: 81 -> 97.

## [2.2.1] - 2026-07-08

### Fixed

**OAuth: clients registered without a scope are no longer rejected when they
request one.** The metadata advertises scope "astrology", so spec-following
connectors (e.g. ChatGPT) may request it even when their dynamic client
registration omitted it; registration now defaults the client scope to
"astrology" (default_scopes). Verified against the full simulated connector
flow: discovery, registration, PKCE authorize, login, token exchange.

## [2.2.0] - 2026-07-08

### Added

**Single-token authentication: `Authorization: Bearer <api_key>:<auth_token>`.**
For platforms that can send only one credential field and no custom headers
(e.g. the Claude Messages API MCP connector). The middleware splits the value
on the first colon and converts it to the internal JWT, exactly like the
X-Divine header pair. Real OAuth JWTs never contain a colon and pass through
untouched. Existing auth methods are unchanged.

## [2.1.4] - 2026-07-08

Param-parity fix batch. Every change below was verified against the live
backend with curl before coding, and every changed tool passed a live
functional test after coding (28/28).

### Fixed

**`divine_get_samvath` renamed to `divine_get_samvat` (BREAKING, but the old
tool never worked).** The tool called `/indian-api/v1/find-samvath`, which does
not exist (HTTP 404 on every call). The live endpoint is
`/indian-api/v1/find-samvat`. Endpoint and tool name corrected.

**`divine_get_bhava_kundli` selector fixed (BREAKING, but the old tool never
worked).** The tool sent chart ids like `D1`/`chalit`, which the endpoint
rejects with "Please enter valid chart id." on every call. The endpoint accepts
only numeric chart ids `1`-`12`; the schema and validation now reflect that.

**`divine_find_festival` rewired.** It sent a date payload the endpoint
ignores; the endpoint requires a `festival` name identifier (e.g.
`maha_shivratri`) plus year and location, and errored on every previous call.
New signature: `festival` + FestivalInput.

**`divine_get_uday_lagna` no longer demands birth details.** The endpoint is
date+place scoped; full_name/gender/hour/min/sec are ignored by the API
(byte-identical responses). They remain accepted as deprecated optional inputs
for backward compatibility but are not sent upstream.

**Monthly list tools no longer demand `day`.** chandramasa_list,
month_nakshatra_list, month_sunrise_sunset_list, month_surya_nakshatra_list,
month_tithi_list are month-scoped; `day` and `lan` have no effect on
astroapi-8 (verified byte-identical). New `MonthInput` model: day/lan accepted
as deprecated optional inputs, not sent upstream.

**`divine_get_chandrashtama` is month-scoped with a real optional `day`.**
`day` narrows results to one date (verified: output changes); it is now
optional instead of required. `lan` is real and kept.

### Added

**`node_type` on 7 tools** (planetary_positions, kaal_sarpa_yoga,
kp_planetary_positions, kp_planetary_sub, kp_cuspal_significator,
jaimini_karakamsha_lagna, jaimini_planetary_positions): optional
`meannode`/`truenode` Rahu-Ketu calculation selector. The API parses and
validates it on all 7 endpoints.

**`month_type` on chandramasa**: optional `amanta`/`purnimanta` lunar month
system selector (verified: changes output, API validates values).

**Chart styling params** (`chart_type`, `chart_color`, `line_color`,
`planet_color`, `sign_color`, plus `show_planet_degree`/`show_planet_retro`/
`show_modern_planets` where the endpoint supports them) on horoscope_chart,
bhava_kundli, sub_planet_chart, kundli_transit_ascendant, kundli_transit_moon.
**`transit_hour`/`transit_min`/`transit_sec`** on both kundli-transit tools
(verified: changes output).

**`nakshatra_pada` on month_nakshatra_list**: optional flag to include pada
details (verified: changes output).

### Docs

README tool tables: count corrected to 81, malayalam/tamil festival tools
added, renamed samvat row, refreshed descriptions of the fixed tools.

## [Unreleased] - 2026-07-07

### Added

**New tool: `divine_get_tamil_festivals`.**
Wraps `POST /indian-api/v1/tamil-festivals` (astroapi-3). Returns major Tamil
festivals (Thai Pongal, Puthandu, Karthigai Deepam, Vaikuntha Ekadashi, etc.)
for a given year and location. Params: year, place, lat, lon, tzone.
Verified against the live endpoint before adding.

**New tool: `divine_get_malayalam_festivals`.**
Wraps `POST /indian-api/v1/malayalam-festivals` (astroapi-3). Returns major
Kerala festivals (Vishu Kani, Onam, Thrissur Pooram, Guruvayur Ekadashi, etc.)
for a given year and location. Params: year, place, lat, lon, tzone (no lan).
Verified against the live endpoint before adding.

## [Unreleased] - 2026-07-06

### Added

**New tool: `divine_get_gowri_panchangam`.**
Wraps the new backend endpoint `POST /indian-api/v1/find-gowri-panchangam`
(astroapi-3). Returns Gowri Panchangam auspicious/inauspicious day and night
segments plus Nalla Neram periods for a date and location. Standard
`PanchangInput` (date + place), no selector fields. Verified against the live
endpoint before adding.

## [Released] - 2026-06-29

Three MCP-wrapper bugs found while debugging a customer report. All fixed and deployed.

### Fixed

**1. Eight selector-style tools were uncallable.**
These tools proxy to a backend endpoint that requires a selector field (which
planet / which dasha), but the selector was never exposed in the tool's input
schema. Because `params` uses `additionalProperties: false`, clients could not
pass it inside `params`, and a sibling field was silently dropped, so the
backend always replied "please enter valid planet/dasha type." The selectors are
now top-level tool arguments. New input fields:

| Tool | New required field(s) | Valid values |
|---|---|---|
| `divine_get_planet_analysis` | `analysis_planet` (+ birth details) | sun, moon, mars, mercury, jupiter, venus, saturn, rahu, ketu |
| `divine_get_vimshottari_dasha` | `dasha_type` (+ birth details) | maha-dasha, antar-dasha, pratyantar-dasha, sookshma-dasha, prana-dasha, deha-dasha |
| `divine_get_maha_dasha_analysis` | `maha_dasha` | planet (9) — no birth data needed |
| `divine_get_antar_dasha_analysis` | `maha_dasha`, `antar_dasha` | planet (9) each — no birth data needed |
| `divine_get_pratyantar_dasha_analysis` | `maha_dasha`, `antar_dasha`, `pratyantar_dasha` | planet (9) each — no birth data needed |
| `divine_get_kundli_transit_ascendant` | `transit_day`, `transit_month`, `transit_year` (+ birth details) | date parts |
| `divine_get_kundli_transit_moon` | `transit_day`, `transit_month`, `transit_year` (+ birth details) | date parts |
| `divine_get_nav_pancham_yoga` | two-person input (`p1_*`, `p2_*`) | replaced incorrect single-person input |

Conditionals on `divine_get_vimshottari_dasha`:
- `maha_dasha` (planet) required when `dasha_type` is `prana-dasha` or `deha-dasha`
- `antar_dasha` (planet) required when `dasha_type` is `deha-dasha`

"planet (9)" = sun, moon, mars, mercury, jupiter, venus, saturn, rahu, ketu.
All planet/dasha inputs are validated; invalid values return a clear error
listing the accepted options.

**2. Monthly-list tools returned an error that looked like an entitlement gap.**
`divine_get_chandramasa_list`, `divine_get_month_tithi_list`,
`divine_get_month_nakshatra_list`, `divine_get_month_sunrise_sunset_list`, and
`divine_get_month_surya_nakshatra_list` proxy to the astroapi-8 host, which
strictly requires a lowercase `place` form field. The shared payload builders
sent capital `Place`. astroapi-1/2/3 accept either casing, which hid the bug;
astroapi-8 rejected it. This was **not** a plan/entitlement issue — the same key
returns HTTP 200 once `place` is lowercased. Fixed in `_panchang_payload`,
`_festival_payload`, and the inline festival payloads.

**3. Upstream failures were reported as successful (`isError: false`).**
`_call_divine_api` caught every error and returned it as a normal string, so
MCP clients saw a success result whose text merely contained "Error: ...".
It now raises `ToolError` on non-2xx responses, network errors, and on the
HTTP-200 error envelopes used by the backend hosts
(`{"success": 3, ...}` on astroapi-3, `{"status": "error", ...}` on astroapi-8),
so `isError` is `true` and clients can detect failures.

### Known issues (backend, not in this wrapper)

- **astroapi-3 returns HTTP 200 on auth failure** (`{"success": 3, "msg":
  "Invalid authorization token!"}`). This wrapper now detects and surfaces it as
  an error, but the backend should return 401/403. Tracked separately with the
  DivineAPI API team.
