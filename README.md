# Morpheus Morpheus Uplink Plugin

This plugin allows different [Morpheus](https://morpheusdata.com) instances to sync data. It currently enables network pool sync, DNS zone and record sync, IPv4 and IPv6 pool types, optional existing IP inventory, and shared host record allocation.

## Requirements

| Component | Minimum Version |
|-----------|----------------|
| Morpheus | 7.0.2 |

## Installation

1. Download the latest `.jar` from the [Releases](https://github.com/HewlettPackard/morpheus-morpheusuplink-plugin/releases) page, or [build it yourself](#building).
2. In Morpheus, navigate to **Administration → Integrations → Plugins**.
3. Click **Browse** and upload the `.jar` file.
4. The **Morpheus Uplink** IPAM and DNS integration will appear after the plugin loads.

## Configuration

When adding a Morpheus Uplink IPAM integration in Morpheus (**Infrastructure → Network → IPAM Integrations**), provide the following:

| Field | Description |
|-------|-------------|
| **API Url** | Remote Morpheus API endpoint URL, e.g. `https://x.x.x.x/`. |
| **Credentials** | Select whether to use local username/password values or a stored Morpheus username/password credential. |
| **Username** | Remote Morpheus username when using local credentials. |
| **Password** | Remote Morpheus password when using local credentials. |
| **Throttle Rate** | Optional request throttle rate for remote Morpheus API calls. |
| **Disable SSL SNI Verification** | Disable SSL SNI verification for the remote Morpheus endpoint. |
| **Inventory Existing** | Sync existing remote Morpheus IP and DNS records into local Morpheus inventory. |
| **Zone Filter** | Optional comma-delimited list of DNS zones to sync. |
| **Network Filter** | Optional comma-delimited list of network pools to sync. |

## Features

### IPAM Integration

The plugin registers an `IPAMProvider` named **Morpheus Uplink**. The following resources are discovered and kept in sync from the remote Morpheus appliance:

- **Network Pools** — remote Morpheus network pools are synchronized into local Morpheus network pools.
- **IPv4 and IPv6 Pool Types** — separate Morpheus Uplink and Morpheus Uplink IPv6 pool types are registered.
- **Pool Ranges** — CIDR, start address, end address, and address count details are mapped onto Morpheus network pool ranges.
- **Existing IP Inventory** — when enabled, remote network pool IP records are synchronized into local Morpheus pool IP records.
- **Network Filters** — optional network pool filters limit which remote pools are synchronized.

### DNS Integration

The plugin also implements `DNSProvider`. The following DNS resources are discovered and kept in sync from the remote Morpheus appliance:

- **DNS Zones** — remote network domains are synchronized into local Morpheus domains.
- **DNS Records** — when existing inventory is enabled, A, AAAA, PTR, TXT, CNAME, and MX records are synchronized.
- **Zone Filters** — optional zone filters limit which remote domains are synchronized.

### Host Record Management

Morpheus can allocate and release addresses through the remote Morpheus appliance during provisioning workflows. Supported operations include:

- Create a requested IPv4 or IPv6 address in a remote network pool.
- Allocate the next available address from a remote network pool.
- Update hostnames on remote network pool IP records.
- Delete remote network pool IP records when releasing addresses.

## Building

```bash
./gradlew shadowJar
```

The plugin JAR will be written to `build/libs/`.

## License

Copyright 2022 Morpheus Data, LLC. Licensed under the [Apache License, Version 2.0](LICENSE).
