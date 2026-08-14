# Morpheus Uplink Plugin

The Morpheus Uplink Plugin integrates one Morpheus appliance with another Morpheus appliance acting as an upstream IPAM and DNS provider. This allows a downstream Morpheus instance to allocate IP addresses and manage DNS records from pools and zones managed in a separate upstream Morpheus instance, using the upstream Morpheus REST API.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Repository structure](#repository-structure)
- [Building the plugin](#building-the-plugin)
- [License](#license)
- [Installing](#installing)
- [Detailed Usage Steps](#detailed-usage-steps)
- [API Endpoints](#api-endpoints)

---

## Features

### Morpheus Uplink IPAM Provider

Allocate and release IP addresses from network pools managed on an upstream Morpheus appliance. Supports automatic next-available IP selection and existing inventory import.

### DNS Record Management

Create and delete DNS records in DNS domains managed on the upstream Morpheus appliance when instances are provisioned or decommissioned on the downstream appliance.

### Cloud Sync

Morpheus synchronises the following resources from the upstream appliance for inventory:

- Network pools and IP allocations
- DNS domains and records

---

## Requirements

| Requirement | Version |
|-------------|---------|
| Morpheus | 7.0.2 or later |
| Java | 11 or later |
| Gradle | Use the included Gradle wrapper (`./gradlew`) |

Additional prerequisites:

- An upstream Morpheus appliance accessible over HTTP or HTTPS from the downstream Morpheus appliance
- A user account on the upstream appliance with API access and sufficient permissions to manage network pools and DNS domains
- Network access from the downstream Morpheus appliance to the upstream appliance on the configured port

---

## Repository structure

```
src/main/groovy/com/morpheusdata/morpheusremote/
├── MorpheusUplinkPlugin.groovy       - Plugin entry point; registers MorpheusUplinkIpamProvider
└── MorpheusUplinkIpamProvider.groovy - IPAMProvider implementation; IPAM and DNS operations, sync, OptionTypes
build.gradle, gradle.properties       - Build configuration and plugin metadata
```

---

## Building the plugin

Run the following command to compile and package the plugin jar:

```bash
./gradlew clean build
```

The packaged jar will be written to `build/libs/`.

To execute tests, use the following command:

```bash
./gradlew test
```

---

## License

This project is licensed under the Apache License 2.0.

See the [LICENSE](LICENSE) file for details.

---

## Installing

1. Build the plugin (see [Building the plugin](#building-the-plugin)) or download a released jar.
2. In Morpheus, navigate to **Administration > Integrations > Plugins**.
3. Click **Add** and upload the `morpheus-morpheusuplink-plugin-<version>.jar` from `build/libs/`.
4. Navigate to **Infrastructure > Networks > IP Pools > Add** and select **Morpheus Uplink** to configure the integration.

---

## Detailed Usage Steps

### Adding a Morpheus Uplink IPAM Integration

1. Go to **Infrastructure > Networks > IP Pools > Add**.
2. Select **Morpheus Uplink** as the pool server type.
3. Enter the **Service URL** of the upstream Morpheus appliance (e.g. `https://upstream-morpheus.example.com`) and credentials for the upstream API user.
4. Save. The downstream Morpheus syncs network pools and DNS domains from the upstream appliance.

### Allocating an IP Address

When provisioning an instance on a network backed by a Morpheus Uplink pool, the downstream Morpheus calls the upstream Morpheus API to allocate the next available IP. DNS records are created on the upstream appliance if DNS is configured.

### Releasing an IP Address

When an instance is decommissioned, the downstream Morpheus calls the upstream API to release the IP and delete associated DNS records.

---

## API Endpoints

This plugin communicates with the **Morpheus REST API** on the upstream Morpheus appliance at the configured service URL. Authentication uses an API access token or username/password.

| Endpoint | Method | Purpose |
|----------|--------|---------|
| `api/networks/pools/` | GET | List network pools |
| `api/networks/pools/{id}/ips` | POST | Allocate an IP address from a pool |
| `api/networks/pools/{id}/ips/{ipId}` | DELETE | Release an IP address |
| `api/networks/domains/` | GET | List DNS domains |
| `api/networks/domains/{id}/records` | GET | List DNS records in a domain |
| `api/networks/domains/{id}/records` | POST | Create a DNS record |
| `api/networks/domains/{id}/records/{recordId}` | DELETE | Delete a DNS record |
