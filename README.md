# Migros Threat Feeds
[![CC BY 4.0][cc-by-shield]][cc-by]

Migros is an attractive target for cybercriminals because its brand carries exceptionally high recognition and trust in Switzerland. That trust makes brand misuse—such as phishing sites, fake social media profiles, counterfeit promotions, and fraudulent customer service accounts—more convincing and effective. With a large customer base and strong digital presence, attackers can reach many people quickly and exploit loyalty programs, e-commerce touchpoints, and gift card flows to harvest credentials, payments, or personal data. The brand’s high reputation increases the likelihood that victims will engage with fraudulent content, which is why vigilant monitoring and reporting via official channels are so important.

All the threat feeds within this repository are provided to help defenders block and investigate related threats.

This repository contains a collection of curated indicators of compromise (IOCs) identified by the Migros Cyber Defense Center (CDC), with a strong focus on digital brand abuse (e.g., phishing domains, impersonation sites, malicious links abusing Migros brands). The goal is to help protect Migros customers, partners, and the broader community by sharing timely, high‑confidence IOCs.


## Table of Contents
- Disclaimer
- What’s in This Repository
- Repository Structure
- Data Formats
    - Simple
    - Enriched
- Intended Use
- Update Cadence
- Quality and Caveats
    - Recommendations
- How to Consume
    - SIEM/SOAR Ingestion
    - DNS Response Policy Zones
    - Proxy and Mail Security
- Frequently Asked Questions (FAQ)
- Contribution
- License
- Sharing
- Contact


## Disclaimer
> [!WARNING]
> Feeds are provided “as-is” without warranties or guarantees of completeness or effectiveness for a particular purpose. Use at your own risk. Enforcement decisions should consider local context and risk tolerance. The Migros CDC may add, modify, or remove indicators at any time.

> [!NOTE]
> Existing entries may be modified at any time. However, entries must be explicitly marked as invalid and therefore skipped by the consumer if needed. Entries that are no longer active (i.e., a potential threat) are not automatically deleted.


## What’s in This Repository
Curated IOC lists centered on Migros-related threats including enriched formats with metadata.

- Malicious domains
- Malicious URLs
- Malicious IPv4 and IPv6 addresses


## Repository Structure
```
.
├─ feeds/
│  ├─ csv/
│  |  └─ migros-threat-feed.csv                   # A list including metadata
│  ├─ json/
|  |  ├─ migros-threat-feed.json                  # An array of JSON objects
|  |  └─ migros-threat-feed.jsonl                 # One JSON object per line including metadata
│  ├─ stix/
|  |  └─ migros-threat-feed.json                  # A JSON STIX bundle
│  └─ txt/
|     ├─ migros-threat-feed.txt                   # One domain, URL or IP address per line
|     ├─ migros-threat-feed.domains.txt           # One domain per line
│     ├─ migros-threat-feed.urls.txt              # One URL per line
│     ├─ migros-threat-feed.ip-addresses.txt      # One IPv4 or IPv6 address per line
│     ├─ migros-threat-feed.ip-addresses_v4.txt   # One IPv4 address per line
│     └─ migros-threat-feed.ip-addresses_v6.txt   # One IPv6 address per line
└─ README.md
```


## Data Formats
This section outlines two ways to consume the published IOCs. Use the provided **simple** formats (TXT) for fast, tool-agnostic blocking (one indicator per line) and **enriched** formats (CSV/JSON/STIX) including metadata (e.g., id, type, created_at, source, etc.). Choose simple formats for quick deployment, or enriched formats for more selective, automated, and auditable workflows.

### Simple
Use when you need fast, hassle-free ingestion. Each file contains one indicator per line, making them ideal for direct use in gateways, DNS Response Policy Zones (RPZ), proxies, and mail filters. 

Simple lists are:
- Easy to fetch and deploy on a schedule
- Tool-agnostic and human-readable
- Minimal in context (no timestamps or confidence), so apply with appropriate caution and validation
- Best for quick blocking, pilot rollouts, and environments that don’t require metadata.


### Enriched
Choose these when you need context for smarter decisions and automation. The CSV, JSON and JSONL feeds include various metadata next to the actual indicator. Enriched formats enable:

- Selective enforcement (e.g., only confidence ≥ `80`)
- SIEM/SOAR correlation and threat hunting
- Aging and decay strategies based on observation windows
- Consistent sharing and auditing across teams and tools

Best for mature pipelines, analytics, and workflows that benefit from richer intel.

Field overview:
- **id**: Unique ID (e.g., `1c5c4a2e-89c6-4170-811a-40e3b2382b3e`)
- **type**: `DOMAIN NAME` | `URL` | `IPV4 ADDRESS` | `IPV6 ADDRESS` (e.g., `URL`)
- **indicator**: The actual IOC (e.g., `https://www.facebook.com/profile.php?id=61577038141130`)
- **created_at**: ISO 8601 (UTC) (e.g., `2025-09-08T12:34:56Z`)
- **confidence**: -1 (i.e., n/a), 0–100 (e.g., `70`)
- **source**: Short source descriptor (e.g., `MIGROS-CDC`)
- **tags**: List of tags (e.g., `phishing, brand-abuse, migros`)
- **tlp**: TLP label for sharing (e.g., `TLP:CLEAR`)
- **notes**: Optional notes (e.g., `n/a`)

CSV header:
```text
id,type,indicator,created_at,confidence,source,tags,tlp,notes
```

JSON structure:
```json
{
  "id": "1c5c4a2e-89c6-4170-811a-40e3b2382b3e",
  "type": "URL",
  "indicator": "https://www.facebook.com/profile.php?id=61577038141130",
  "created_at": "2025-07-09T09:24:03.291Z",
  "confidence": -1,
  "source": "MIGROS-CDC",
  "tags": [
    "phishing",
    "brand-abuse",
    "migros"
  ],
  "tlp": "TLP:CLEAR",
  "notes": "n/a"
}
```

STIX structure:
```json
{
  "type": "bundle",
  "id": "bundle--1bc05fa3-37a9-4abe-96f2-958ed43bd7bd",
  "objects": [
    {
      "type": "indicator",
      "spec_version": "2.1",
      "id": "indicator--1c5c4a2e-89c6-4170-811a-40e3b2382b3e",
      "created": "2025-07-09T09:24:03.291Z",
      "modified": "2025-07-09T09:24:03.291Z",
      "name": "n/a",
      "description": "n/a",
      "indicator_types": [
        "malicious-activity"
      ],
      "pattern": "[url:value = 'https://www.facebook.com/profile.php?id=61577038141130']",
      "pattern_type": "stix",
      "valid_from": "2025-07-09T09:24:03.291Z"
    }
  ]
}
```


## Intended Use
**Preventive Controls**: Blocklists in secure web gateways, mail gateways, DNS firewalls (RPZ), WAFs, proxies.

**Detection and Hunting**: SIEM enrichment and correlation, EDR/NDR detections and retro-hunting.

**Threat Intelligence Platform**s: Ingest as a TAXII collection alternative or via scheduled fetch of raw files.


## Update Cadence
- Regular updates as new brand‑abuse activity is discovered by the Migros Cyber Defense Center
- Timestamps in the enriched feeds indicate observation windows


## Quality and Caveats
- False positives: possible, especially with dynamic hosting and reused infrastructure
- Indicators may expire: infrastructure can be repurposed or taken down
- Confidence guidance:
    - `80–100`: strong evidence and recent observation
    - `50–79`: likely malicious, validate locally
    - `<50`: suspicious context, use with caution
    - `-1`: No confidence available

### Recommendations
- Stage in monitor‑only mode before enforcement
- Apply controls by type and confidence thresholds
- Respect TLP tags and your organization’s risk appetite


## How to Consume
Fetch on a schedule using e.g., one of the following `curl` commands.

```bash
curl --fail --silent --show-error \
  'https://raw.githubusercontent.com/migros/migros-threat-feeds/refs/heads/main/feeds/txt/migros-threat-feed.urls.txt' \
  --output './migros-threat-feed/urls.txt'

curl --fail --silent --show-error \
  'https://raw.githubusercontent.com/migros/migros-threat-feeds/refs/heads/main/feeds/txt/migros-threat-feed.domains.txt' \
  --output './migros-threat-feed/domains.txt'

curl --fail --silent --show-error \
  'https://raw.githubusercontent.com/migros/migros-threat-feeds/refs/heads/main/feeds/txt/migros-threat-feed.ip-addresses_v4.txt' \
  --output './migros-threat-feed/ip-addresses.txt'
```


### SIEM/SOAR Ingestion
For SIEM/SOAR ingestion, schedule a recurring job to retrieve the enriched feeds, map the fields accordingly.

### DNS Response Policy Zones
For DNS Response Policy Zones, convert `migros-threat-feed.domains.txt` into RPZ format using your preferred tooling,

### Proxy and Mail Security
For proxy and mail security, use `migros-threat-feed.urls.txt` either as a URL blocklist to prevent delivery of known-bad links or as a URL rewrite policy to redirect users away from malicious destinations.


## Frequently Asked Questions (FAQ)
Have questions? Check out our [FAQ](FAQ.md) for answers to common issues, troubleshooting tips, and additional information about the project.


## Contribution
This repository is read-only; pull requests and direct changes will not be accepted. Do not use this repository for general suggestions or bug reports. Reports of brand misuse involving any Migros company must not be submitted via GitHub. Please use the designated reporting channels only.


## License
This work is licensed under a
[Creative Commons Attribution 4.0 International License][cc-by] (CC BY 4.0).

[![CC BY 4.0][cc-by-image]][cc-by]

[cc-by]: http://creativecommons.org/licenses/by/4.0/
[cc-by-image]: https://i.creativecommons.org/l/by/4.0/88x31.png
[cc-by-shield]: https://img.shields.io/badge/License-CC%20BY%204.0-lightgrey.svg


## Sharing
Default sharing marking according to the Traffic Light Protocol (TLP) is `TLP:CLEAR` unless otherwise specified.


## Contact
Please contact [damiano.esposito@mgb.ch](mailto:damiano.esposito@mgb.ch) for issues relating to this repository. Please contact [media@migros.ch](mailto:media@migros.ch) for press inquiries.
