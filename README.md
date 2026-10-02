# Vedic Astrology MCP Server (Kundli, Panchang) by DivineAPI

![Vedic Astrology MCP Server by DivineAPI](https://raw.githubusercontent.com/DivineAPI/DivineAPI/main/assets/mcp-indian-astrology.png)

Connect Claude, Cursor, VS Code or any MCP client to DivineAPI's Vedic astrology data. Tools cover Panchang, Kundli charts, dashas, doshas, matchmaking (Ashtakoot and Dashakoot), Lal Kitab, Jaimini and Hindu festival dates, returned as JSON (and SVG for charts).

[![Docs](https://img.shields.io/badge/docs-developers.divineapi.com-blue)](https://developers.divineapi.com/indian-api)
[![Trial](https://img.shields.io/badge/14--day%20trial-start-green)](https://divineapi.com/start-trial)
[![MCP](https://img.shields.io/badge/MCP-streamable%20HTTP-purple)](https://divineapi.com/mcp)
[![PyPI](https://img.shields.io/badge/pypi-divineapi--indian--astrology--mcp-orange)](https://pypi.org/project/divineapi-indian-astrology-mcp/)
[![Status](https://img.shields.io/badge/status-status.divineapi.com-brightgreen)](https://status.divineapi.com)
[![License: MIT](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)

Hosted server URL:

```
https://mcp.divineapi.com/indian/mcp
```

One-click install (adds the hosted server with placeholder credentials):

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/install-mcp?name=divineapi-indian-astrology&config=eyJ1cmwiOiJodHRwczovL21jcC5kaXZpbmVhcGkuY29tL2luZGlhbi9tY3AiLCJoZWFkZXJzIjp7IlgtRGl2aW5lLUFwaS1LZXkiOiJZT1VSX0FQSV9LRVkiLCJYLURpdmluZS1BdXRoLVRva2VuIjoiWU9VUl9BVVRIX1RPS0VOIn19)
[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_Server-0098FF?logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect/mcp/install?name=divineapi-indian-astrology&config=%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A%2F%2Fmcp.divineapi.com%2Findian%2Fmcp%22%2C%22headers%22%3A%7B%22X-Divine-Api-Key%22%3A%22YOUR_API_KEY%22%2C%22X-Divine-Auth-Token%22%3A%22YOUR_AUTH_TOKEN%22%7D%7D)

After installing, replace `YOUR_API_KEY` and `YOUR_AUTH_TOKEN` in the server's headers with your own values. In VS Code you can use the `inputs` version of the config below instead, so the values are stored securely.

## Connect in 1 minute

**1. Get your credentials.** Start the [14-day free trial](https://divineapi.com/start-trial) (credit card required to activate the trial), then copy your **API key** and **auth token** from the DivineAPI dashboard.

**2. Add the server to your client.** The hosted server accepts your credentials in one of two ways:

- **Request headers:** `X-Divine-Api-Key` and `X-Divine-Auth-Token` (Cursor, VS Code, Claude Code).
- **Sign-in page (OAuth):** the client opens a DivineAPI page where you paste the API key and auth token once (Claude.ai and Claude Desktop custom connectors).

### Claude.ai and Claude Desktop (custom connector)

Custom connectors added in Claude.ai also appear in Claude Desktop.

1. Open **Customize > Connectors**, click **+ Add**, then **Add custom connector**.
2. Name: `DivineAPI Vedic Astrology`. URL: `https://mcp.divineapi.com/indian/mcp`. Click **Continue**.
3. Keep the detected sign-in settings and click **Add**. When you connect, a DivineAPI sign-in page opens: paste your API key and auth token and click **Connect**.

If your Claude plan offers request headers for custom connectors, you can instead choose no sign-in and add the two headers `X-Divine-Api-Key` and `X-Divine-Auth-Token`.

Team and Enterprise plans: an owner adds the connector under **Organization settings > Connectors** first. Steps from [Claude's custom connector guide](https://support.claude.com/en/articles/11175166).

### Cursor

`~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project):

```json
{
  "mcpServers": {
    "divineapi-indian-astrology": {
      "url": "https://mcp.divineapi.com/indian/mcp",
      "headers": {
        "X-Divine-Api-Key": "YOUR_API_KEY",
        "X-Divine-Auth-Token": "YOUR_AUTH_TOKEN"
      }
    }
  }
}
```

### VS Code (GitHub Copilot agent mode)

`.vscode/mcp.json`. VS Code asks for both values on first start and stores them securely:

```json
{
  "inputs": [
    { "type": "promptString", "id": "divine-api-key", "description": "DivineAPI API key", "password": true },
    { "type": "promptString", "id": "divine-auth-token", "description": "DivineAPI auth token", "password": true }
  ],
  "servers": {
    "divineapi-indian-astrology": {
      "type": "http",
      "url": "https://mcp.divineapi.com/indian/mcp",
      "headers": {
        "X-Divine-Api-Key": "${input:divine-api-key}",
        "X-Divine-Auth-Token": "${input:divine-auth-token}"
      }
    }
  }
}
```

### Claude Code

```bash
claude mcp add --transport http divineapi-indian-astrology https://mcp.divineapi.com/indian/mcp \
  --header "X-Divine-Api-Key: YOUR_API_KEY" \
  --header "X-Divine-Auth-Token: YOUR_AUTH_TOKEN"
```

Run `/mcp` inside Claude Code to check the connection.

### Clients that send only one credential field

If a client can send only a single bearer token and no custom headers, send `Authorization: Bearer YOUR_API_KEY:YOUR_AUTH_TOKEN` (API key, a colon, then the auth token). The server splits it into the two credentials.

## Example prompts

- "What is today's Panchang for New Delhi? Include tithi, nakshatra, yoga, karana and Rahu Kaal."
- "I was born on 15 March 1990 at 14:30 in Mumbai. Show my D1 and D9 charts and my current Vimshottari dasha."
- "Am I Manglik? Is Sadhe Sati active for me right now?"
- "Match these two birth charts with Ashtakoot Milan and explain the guna score."
- "List the Hindu festivals in Kartik month this year, with dates for Delhi."
- "Give me my Lal Kitab planetary positions and the debts (rin) in my chart."

## Tool groups

The table lists 99 of the 104 tools in [`server.py`](server.py) (version 2.5.0); the 5 KP tools are listed after the DivineAPI team confirms KP. The hosted server is updated more often and can list more tools; your MCP client always shows the live list.

| Group | Tools | What they cover |
|---|---|---|
| Panchang | 6 | `divine_get_panchang`, `divine_get_tithi`, `divine_get_nakshatra`, `divine_get_karana`, `divine_get_surya_nakshatra`, `divine_get_yoga_panchang` |
| Panchang extended | 7 | Choghadiya, Nivas and Shool, Ritu and Anaya, Samvat, Chandrabalam and Tarabalam, other calendars and epoch, sun and moon rise/set |
| Timings | 3 | Auspicious timings (muhurat), inauspicious timings, Gowri Panchangam |
| Kundli basics | 9 | Basic astro details, planetary positions, divisional charts (`divine_get_horoscope_chart`: D1, D9, D10 and others, north or south style, SVG), Bhava Kundli, planet analysis, ascendant report, Uday Lagna, Chandramasa, Chandrashtama |
| Doshas | 4 | Manglik dosha, Kaal Sarpa yoga, Pitra dosha, Sadhe Sati |
| Dasha | 6 | Vimshottari dasha, Maha / Antar / Pratyantar dasha analysis, Yogini dasha, Kaal Chakra dasha |
| Yogas and strengths | 8 | Yogas, Nav Pancham yoga, Shadbala, composite friendship, Ghata Chakra, Sudarshana Chakra, Panchak Rahita, gemstone suggestion |
| Ashtakvarga | 3 | Bhinnashtakvarga, Prasthara Chakra, Sarvashtakavarga |
| Sub planets (Upagrahas) | 2 | Sub-planet positions, sub-planet chart |
| Jaimini | 4 | Chara dasha, Karakamsha Lagna, Padas (Arudha), Jaimini planetary positions and karakas |
| Transits | 6 | Kundli transit from ascendant and from Moon, Grah Gochar, planet combustion, nakshatra and retrograde transits |
| Matchmaking (Kundli Milan) | 7 | Ashtakoot Milan, Dashakoot Milan, matching Manglik, basic astro, charts, Vimshottari dasha and planetary positions for both people |
| Festivals | 7 | Festivals by date, by English month, by Hindu month, find a festival, Malayalam, Tamil and Sankranti festivals |
| Lal Kitab | 16 | Planetary positions, chart, house positions, conjunctions, teva, planet analysis, dasha, planet types, mahadasha and antardasha content, debts, house signification, Varsha Pravesh, varshphal positions, Muntha and chart |
| Kundli analysis | 6 | Vargottama planets, Bhava Bala, Ashtama Shani, bhava analysis, bhava group predictions, planet remedies |
| Monthly lists | 5 | Chandramasa list, month nakshatra list, sunrise and sunset list, Surya nakshatra list, tithi list |

All tools are read-only (`readOnlyHint: true`) and each tool call makes one request to the DivineAPI REST API on the `astroapi-1`, `astroapi-2`, `astroapi-3` or `astroapi-8` host.

### Inputs the model needs to know

- Dates are separate `day`, `month`, `year` fields (plus `hour`, `min`, `sec` for birth charts; send `sec` as `0` if unknown).
- `place` is a plain lowercase city string; `lat` and `lon` are decimals.
- `tzone` is a decimal UTC offset, for example `5.5` for India. Never `+5:30`.
- Calculations are sidereal with the Lahiri ayanamsa.
- `lan` sets the response language. Vedic responses support 8 Indian languages with DivineAPI's own codes:

| Language | Code | Language | Code |
|---|---|---|---|
| English | `en` | Tamil | `tm` |
| Hindi | `hi` | Telugu | `tl` |
| Bengali | `bn` | Malayalam | `ml` |
| Marathi | `ma` | Kannada | `kn` |

## Self-hosting

You only need this if you want to run the server yourself instead of using the hosted URL. The server reads your credentials from two environment variables: `DIVINE_API_KEY` and `DIVINE_AUTH_TOKEN`.

### Local (stdio) with uv or pip

```bash
# uv (no install step)
uvx divineapi-indian-astrology-mcp

# or pip
pip install divineapi-indian-astrology-mcp
divineapi-indian-astrology-mcp
```

Claude Desktop local config (`claude_desktop_config.json`; on macOS `~/Library/Application Support/Claude/`, on Windows `%APPDATA%\Claude\`):

```json
{
  "mcpServers": {
    "divineapi-indian-astrology": {
      "command": "uvx",
      "args": ["divineapi-indian-astrology-mcp"],
      "env": {
        "DIVINE_API_KEY": "YOUR_API_KEY",
        "DIVINE_AUTH_TOKEN": "YOUR_AUTH_TOKEN"
      }
    }
  }
}
```

The PyPI package can be newer than the code in this repository. To run exactly this repository's code:

```bash
git clone https://github.com/DivineAPI/mcp-indian-astrology.git
cd mcp-indian-astrology
pip install .
DIVINE_API_KEY=YOUR_API_KEY DIVINE_AUTH_TOKEN=YOUR_AUTH_TOKEN divineapi-indian-astrology-mcp
```

### Remote (streamable HTTP) with Docker

```bash
docker build -t divineapi-indian-mcp .
docker run -p 8000:8000 -e MCP_JWT_SECRET=a-long-random-string divineapi-indian-mcp
```

The MCP endpoint is then `http://localhost:8000/mcp`. Clients send their own `X-Divine-Api-Key` and `X-Divine-Auth-Token` headers. `docker compose up` maps the same server to port 8001.

| Variable | Used for |
|---|---|
| `DIVINE_API_KEY`, `DIVINE_AUTH_TOKEN` | Your DivineAPI credentials. Required in stdio mode; in HTTP mode, per-request headers or the sign-in page override them |
| `MCP_TRANSPORT` | `stdio` (default) or `http` (set by the Dockerfile) |
| `MCP_HOST` | Public host name for the OAuth sign-in flow and allowed hosts (default `mcp.divineapi.com`) |
| `MCP_JWT_SECRET` | Secret that signs session tokens. Set a fixed value so sign-ins survive restarts |

The OAuth sign-in flow expects HTTPS on `MCP_HOST` behind a reverse proxy that serves the server under `/indian/` (see [`nginx.conf`](nginx.conf)). Header authentication works without it.

## Which plan includes this

The tools call the Vedic endpoints, so they use your **Vedic plan** (Vedic Sampoorna, Vedic Ananta or Vedic Prakash); a tool works when your plan includes its endpoint. Compare plans at [divineapi.com/pricing](https://divineapi.com/pricing).

## FAQ

**Do I need a DivineAPI account to use the Vedic tools?**
Yes. Each panchang, kundli or matching tool sends your API key and auth token to the Vedic REST endpoints. Start with the [14-day free trial](https://divineapi.com/start-trial) (credit card required to activate the trial); plans and prices are at [divineapi.com/pricing](https://divineapi.com/pricing).

**Can Claude Desktop or Cursor ask for a kundli directly?**
Yes. Any client that supports remote MCP over streamable HTTP (Claude.ai, Claude Desktop, Claude Code, Cursor, VS Code) can call the kundli and panchang tools from a plain-language prompt. A client that only starts local servers can run `divineapi-indian-astrology-mcp` over stdio (see Self-hosting).

**Why does my client list more Vedic tools than the table?**
The table is counted from this repository's `server.py` and leaves out the 5 KP tools for now. The hosted server and the PyPI package can be newer than this code.

**Which ayanamsa and zodiac does it use?**
Sidereal zodiac with the Lahiri ayanamsa, calculated with Swiss Ephemeris.

**Can it draw charts?**
Yes. Chart tools (`divine_get_horoscope_chart`, `divine_get_sub_planet_chart`, the Lal Kitab and matching chart tools) return an SVG plus a base64 image.

**I want the REST API, not MCP.**
Call the same Vedic endpoints over plain HTTP with the [kundli-api](https://github.com/DivineAPI/kundli-api) and [panchang-api](https://github.com/DivineAPI/panchang-api) quickstarts or the [API reference](https://developers.divineapi.com/indian-api). SDKs: `pip install divineapi`, `npm install divineapi`, `composer require divineapi/divineapi`.

## Related repos

| Repo | What it is |
|---|---|
| [kundli-api](https://github.com/DivineAPI/kundli-api) | Kundli REST API quickstart |
| [kundli-matching-api](https://github.com/DivineAPI/kundli-matching-api) | Kundli matching (Ashtakoot, Dashakoot) REST API |
| [lal-kitab-api](https://github.com/DivineAPI/lal-kitab-api) | Lal Kitab REST API |
| [panchang-api](https://github.com/DivineAPI/panchang-api) | Panchang REST API quickstart |
| [hindu-festival-api](https://github.com/DivineAPI/hindu-festival-api) | Hindu festival dates REST API |
| [astrology-api](https://github.com/DivineAPI/astrology-api) | Overview of all DivineAPI domains |
| [mcp-western-astrology](https://github.com/DivineAPI/mcp-western-astrology) | Western astrology MCP server |
| [mcp-horoscope-numerology](https://github.com/DivineAPI/mcp-horoscope-numerology) | Horoscope, tarot and numerology MCP server |
| [divineapi-python](https://github.com/DivineAPI/divineapi-python) | Python SDK (`pip install divineapi`) |
| [divineapi-node](https://github.com/DivineAPI/divineapi-node) | Node.js / TypeScript SDK (`npm install divineapi`) |
| [divineapi-php](https://github.com/DivineAPI/divineapi-php) | PHP SDK (`composer require divineapi/divineapi`) |

## Support

- MCP overview: [divineapi.com/mcp](https://divineapi.com/mcp)
- API reference: [developers.divineapi.com/indian-api](https://developers.divineapi.com/indian-api)
- Help center: [support.divineapi.com](https://support.divineapi.com)
- API status: [status.divineapi.com](https://status.divineapi.com)

## License

MIT. See [LICENSE](LICENSE).
