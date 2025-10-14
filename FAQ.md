# Frequently Asked Questions (FAQ)
**Q: Which files should I use for immediate blocking?**

A: Use the simple TXT lists. These contain one indicator per line and are suitable for DNS firewalls, proxies, and mail filters.

---

**Q: Some of the listed feeds do not contain any entries?**

A: The provided indicators are manually curated and therefore have a certain quality. We do not derive any indicators from other indicators (e.g., deriving domains names from URLs) due to potential false positives.

---

**Q: What’s the difference between simple and enriched feeds?**

A: Simple TXT feeds are tool-agnostic lists with one indicator per line. Enriched feeds include metadata like id, type, created_at, source, tags, tlp, notes and optionally confidence. Use enriched feeds when you need selective enforcement, correlation, or auditing.

---

**Q: How often are feeds updated?**

A: Feeds are updated regularly based on brand‑abuse activity seen by the Migros CDC. Use scheduled fetches and rely on timestamps (created_at) for aging/decay strategies.

---

**Q: What does confidence with a value of -1 mean?**

A: `-1` indicates no confidence rating is available for that record. You should apply your local validation or thresholds, or treat it as “unknown confidence”.

---

**Q: Are entries ever removed?**

A: Entries may be modified as threats evolve. Entries are not automatically deleted when a threat becomes inactive. Please use timestamps and your own policy to decide enforcement and decay.

---

**Q: What is the TLP marking for sharing?**

A: Default sharing is `TLP:CLEAR` unless otherwise specified.

---

**Q: Can I pin to a specific version of the lists?**

A: Yes. Use raw GitHub URLs with a commit SHA instead of `main` to pin to an immutable version.

```text
https://raw.githubusercontent.com/migros/migros-threat-feed/<COMMIT_SHA>/feeds/txt/migros-threat-feed.domains.txt
```

---

**Q: Do the URL lists block full paths?**

A: DNS-based controls (e.g., Pi-hole, RPZ) operate at the domain level, not at URL path level. Convert URLs to their registrable domains for DNS enforcement, and use proxies/WAFs for path-level blocking.

---

**Q: How do I use the blocklist with Pi-hole?**

A: It is recommended to use the domains file [feeds/txt/migros-threat-feed.domains.txt](feeds/txt/migros-threat-feed.domains.txt)

1) Pi-hole Web UI: `Group Management` > `Lists`
2) Add a new subscribed list
    - Address: `https://raw.githubusercontent.com/migros/migros-threat-feed/main/feeds/txt/migros-threat-feed.domains.txt`
    - Comment: `Migros-CDC`
    - Group assignment: `Default`
3) Click `Add blocklist`

Notes:
- Pi-hole is DNS-based; it cannot block specific URL paths. Combine with a secure web gateway or proxy for path-level enforcement.
- Keep gravity updated on a schedule to reflect feed changes (e.g., a daily cron using `pihole -g`).

---

**Q: Can I report false positives or request de-listing?**

A: Use the contact details in the “Contact” section to reach out with supporting evidence and context.

---

**Q: How should I validate before enforcement?**

A: Stage in monitor-only mode, correlate with internal telemetry, and apply thresholds based on type, confidence, and age (`created_at`). Prefer gradual rollout with observation and tuning.

---

**Q: Why are we publishing on GitHub instead of Malware Information Sharing Platform (MISP) ?**

A: Our immediate objective is broad, low‑friction distribution of brand‑abuse indicators to diverse consumers (DNS firewalls, proxies, SWGs, SIEM/SOAR jobs, mail gateways). GitHub optimizes for this use case today:

- **Zero access overhead**: Public raw URLs work with curl/wget/cron without accounts, API keys, or specialized MISP client tooling.
- **Format neutrality**: We can publish TXT/CSV/JSON/JSONL side‑by‑side, matching what downstream controls natively ingest (plain lists for DNS/proxy, CSV/JSON for SIEM/SOAR).
- **Fast, reliable delivery**: raw.githubusercontent.com is backed by a global CDN, easy to whitelist, and highly available.
- **Auditable versioning**: Git history provides immutable provenance (diffs, commits). Consumers can pin to specific SHAs for reproducibility and rollback.
- **Operational simplicity**: No external platform to deploy/maintain for feed hosting (patching, backups, user management). CI can validate schemas and publish atomically.
- **Broad compatibility**: Many controls accept simple lists directly; MISP typically requires connectors/transforms or middleware to map attributes to enforcement formats.
- **Transparent sharing**: Straightforward to mirror, fork, or archive; governance is clear and traceable via commits and reviews.

MISP remains valuable for rich CTI workflows. Once our cadence, schema, and governance stabilize, we will evaluate mirroring into MISP/TAXII to complement the GitHub feeds while preserving the current ease‑of‑consumption.

---

**Q: Do you provide STIX/TAXII or MISP exports?**

A: Not at this time. The current focus is broad, simple distribution via TXT/CSV/JSON/JSONL for immediate enforcement and analytics. We generate machine-readable feeds optimized for common controls. STIX/TAXII and MISP mirroring are on the roadmap once cadence and governance are stable, to complement the GitHub feeds.

---

**Q: How do you handle duplicates and normalization across feeds?**

A: We do not currently apply additional canonicalization or de-duplication beyond what is provided.

Consumers should:
- De-duplicate on ingest (e.g., using set semantics in SIEM/SOAR pipelines).
- Normalize domains (lowercase FQDN) and trim trailing dots.

This approach mirrors practices from other TI projects and avoids unintentional over-normalization that could increase false positives.

---

**Q: Do you publish cryptographic signatures or checksums?**

A: Not yet. For integrity and reproducibility today:
- Pin to a specific commit SHA in raw GitHub URLs.
- Track versions via Git history if added.

We plan to might add release artifacts with checksums (e.g., SHA256) and optionally GPG signing, aligning with best practices used by projects such as abuse.ch and Spamhaus.

---

**Q: What indicators are in scope and what is out of scope?**

A: In scope: brand-abuse web IOCs (domains, URLs and IPv4/IPv6). Out of scope (currently): file hashes, email addresses, ASNs, and malware families.

This mirrors a focused-scope strategy common in brand-protection-oriented feeds, trading breadth for precision and lower false positives.

---

**Q: What is the expected latency from detection to publication? Do you provide an SLA?**

A: No SLA is provided. Publication is best-effort and regular. Use timestamps (`created_at`) in enriched feeds to build your own aging/decay policies. We prioritize timeliness over formal SLAs.

---

**Q: How long do you retain indicators? Do you expire entries automatically?**

A: Indicators might be retained until updated or de-listed based on our curation process. We do not automatically remove no longer active indicators. Rely on `created_at` and your local policies to age out entries and reduce false positives.

---

**Q: Are subdomains included and should I block wildcards?**

A: Feeds include exact domains or full URLs observed. For DNS-level enforcement (RPZ, Pi-hole, etc.), consider wildcard strategies carefully (e.g., blocking entire registrable domains) to balance coverage vs. collateral.

---

**Q: Is it safe to browse indicators in the feeds?**

A: No. Treat all indicators as potentially malicious. Do not click or browse them directly. Use offline tooling for analysis and ensure your environment applies appropriate safety controls, echoing guidance from established TI sources.

---

**Q: Do you provide category mappings or taxonomies (e.g., ATT&CK, misp-galaxy)?**

A: Not currently. We provide simple tags for downstream mapping.

---

**Q: Why do some feeds include brands that do not directly belong to the Migros Group?**

A: In the spirit of "Migros macht meh für d'Schwiiz" we also publish relevant "bycatch" when it serves the cause.
