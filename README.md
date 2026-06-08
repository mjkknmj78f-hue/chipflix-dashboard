# ChipFlix Dashboard

iOS 12-compatible Home Assistant dashboard for the ChipFlix home server (HP 250 G6, Ubuntu 24.04).

## Problem solved

iPad Air 1 (iOS 12) cannot run modern ES module JavaScript. Homer and Home Assistant both use Vue 3 / Vite bundles that are invisible on iOS 12 — blank page or logo only.

## Solution

Two nginx reverse proxies on chipflix.local that sniff the User-Agent and route old devices to legacy/compatible versions:

| Port | Service | Modern browser | iOS ≤ 12 |
|------|---------|---------------|----------|
| 80   | Homer   | `b4bz/homer:latest` | `b4bz/homer:v22.06.1` (pre-Vite webpack build) |
| 8123 | Home Assistant | Full HA frontend (proxied to :18123) | `ios-dashboard.html` (vanilla JS) |

## Files

- **`ios-dashboard.html`** — self-contained vanilla JS dashboard (no framework, ES5-safe). Connects to HA WebSocket, shows battery bank (6-pack BMS), weather with 7-day forecast + sun hours, bin collections, grid usage, lights, switches, climate, media, people.
- **`nginx/homer-proxy.conf`** — UA-based routing for Homer (port 80)
- **`nginx/ha-proxy.conf`** — UA-based routing for HA (port 8123), WebSocket proxy to :18123

## ChipFlix setup

- **Server:** chipflix.local (192.168.10.100), SSH: agentchipserver
- **HA internal port:** 18123 (moved from 8123 to make room for the proxy)
- **Homer assets:** `/opt/services/homer/assets/config.yml`
- **Docker compose:** `/opt/services/docker-compose.yml`

## iOS dashboard features

- BMS battery bank: SOC bar, charge/discharge watts, voltage, temp, 6 individual pack bars
- Weather: current conditions, location, sun hours estimate, 7-day forecast strip
- Bin collections: Next Collection, General Waste, Recycling, Garden (highlights if due ≤1 day)
- Grid usage: EON Next smart meter (today, yesterday, period, total)
- Lights, switches, climate, media players, covers, locks, alarm, scenes — tap to toggle
- Sensors collapsed (expandable)
- Login: HA username/password → stores refresh token in localStorage

## To do

- Solar panel output (needs MPPT/inverter sensor added to HA via MQTT or Modbus)
- Lovelace dashboard view option
