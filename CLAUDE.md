# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page static website (`index.html`) informing neighbours in Sulgen (Thurgau, Switzerland) about
forming a Lokale Elektrizitätsgemeinschaft (LEG) under the revised Swiss StromVG ("Mantelerlass",
in force since 1.1.2026). It's shared via a WhatsApp link, not an official municipal publication.

## Commands

No build step, no dependencies to install, no test suite.

- Preview: open `index.html` directly in a browser, or serve the directory with any static file server.
- Deploy: commit and push `index.html` to `main`. GitHub Pages (legacy build, repo `Sw1zero/leg-oberdorf-sulgen`)
  rebuilds automatically.
  - The legacy Pages build occasionally stalls in `"building"` for several minutes with no error. If so,
    kick it manually: `gh api repos/Sw1zero/leg-oberdorf-sulgen/pages/builds -X POST`
  - Check build status: `gh api repos/Sw1zero/leg-oberdorf-sulgen/pages/builds/latest --jq .status`
  - Live URL: https://sw1zero.github.io/leg-oberdorf-sulgen/

## Architecture

Everything lives in one file: `index.html` with inline `<style>` and two `<script>` tags at the end (Leaflet
init only — there is no other JavaScript). No framework, no CSS/JS build pipeline.

Page sections in order: hero → Was ist eine LEG → Der Vorteil (tariff table) → Rechenbeispiel (two
persona cards) → Vorteile/Zu beachten → Wer kann mitmachen (street list + map) → Wie geht es weiter →
footer.

**Map**: `#leg-map` is initialized by inline JS using Leaflet (loaded from unpkg CDN with SRI hashes) with
OpenStreetMap raster tiles, centered on hardcoded coordinates (`47.5404, 9.1904`, geocoded via Nominatim
for Ebnetstrasse/Mattenstrasse) with a circle overlay marking the approximate LEG coverage area.

**Season toggle pattern**: elsewhere used and removed again — if reintroduced, the established pattern is
CSS-only tabs via hidden radio inputs + `:has()` selectors, no JavaScript.

## The Rechenbeispiel numbers

The two persona cards ("Ohne eigene Solaranlage", "Mit eigener Solaranlage") are illustrative calculations,
not measurements, built from:

- Current Technische Betriebe Sulgen tariffs (Energielieferung 15 Rp./kWh; Netznutzung HT 10.30 / NT 7.00
  Rp./kWh + 5 CHF/Monat Grundpreis; Rückliefervergütung 6+1 Rp./kWh) — exkl. MWST throughout.
- A 40% Netznutzung discount on the LEG-internal share (this part is the actual, legally mandated benefit;
  automatic, no private agreement needed).
- Two additional assumptions with no real-world basis yet (LEGs only exist since 2026): an internal
  trading price of 11 Rp./kWh (midpoint between the 7 Rp. feed-in tariff and the 15 Rp. retail tariff), and
  a 30% "LEG-interner Anteil" (borrowed from typical PV self-consumption ratios without storage as a proxy).
  Both are stated as explicit assumptions in the page's own disclaimer text — keep that disclaimer in sync
  if the assumptions change.
- "Ohne eigene Solaranlage" persona: 4'500 kWh/year household (EnergieSchweiz/BFE average). "Mit eigener
  Solaranlage" persona: 8 kWp system, ~8'000 kWh/year yield, 30% self-consumption (Swissolar rules of thumb).

When recomputing any figure, keep the HT/Niedertarif split ratio (~22.1% / 77.9%, taken from the
household's real Q2/2026 invoice) and the exkl.-MWST convention used throughout, so numbers stay internally
consistent and comparable across the two persona cards.

The four Q3/2025–Q2/2026 invoice PDFs and the two LEG info PDFs in the repo root are source material only
(untracked in git, not deployed) — they informed the tariffs and legal claims on the page but aren't
referenced by the site itself.
