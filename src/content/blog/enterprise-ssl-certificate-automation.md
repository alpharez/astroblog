---
title: "Enterprise SSL Certificate Automation: Preparing for the 90-Day Reality"
description: "Certificate lifetimes are shrinking. Here's how to automate SSL/TLS certificate management across enterprise infrastructure before manual renewal becomes impossible."
pubDate: "2026-01-17"
---

The industry is moving toward shorter certificate lifetimes. Apple and Google have pushed for 90-day maximums, and there are proposals to go even shorter. If you're managing certificates manually across dozens or hundreds of systems, you're about to have a problem.

This isn't just about web servers anymore. Load balancers, firewalls, VPN concentrators, API gateways, internal services—anything with a certificate needs a renewal strategy that doesn't involve someone remembering to do it.

## Why Shorter Lifetimes?

Shorter certificate validity windows reduce the exposure time if a private key is compromised. A stolen key for a 1-year cert is useful for up to a year. A stolen key for a 90-day cert has a much smaller window.

The tradeoff is operational complexity. What worked with annual renewals falls apart when you need to renew quarterly—or more frequently.

## The ACME Protocol

ACME (Automatic Certificate Management Environment) is the protocol behind Let's Encrypt and is now supported by many commercial CAs. The flow is straightforward:

1. Client requests a certificate for a domain
2. CA issues a challenge (HTTP, DNS, or TLS-ALPN)
3. Client proves control of the domain
4. CA issues the certificate
5. Client installs the certificate and schedules renewal

For a basic Linux web server, this is solved:

```bash
# Install certbot
apt install certbot python3-certbot-nginx

# Get a certificate and configure nginx
certbot --nginx -d example.com

# Auto-renewal is handled by systemd timer
systemctl list-timers | grep certbot
```

The certificate renews automatically. Done.

## Where It Gets Complicated

Enterprise environments have challenges that certbot on a web server doesn't address:

**Internal CAs**: Many organizations use Microsoft AD CS or other internal PKI. These don't speak ACME by default. You need either an ACME-to-internal-CA bridge or a different automation approach.

**Network Devices**: F5, Palo Alto, Cisco, and other network gear often can't run ACME clients directly. You need external automation that obtains certs and pushes them via API.

**Air-Gapped Networks**: No internet means no public CA validation. Internal PKI with automation is the only path.

**Compliance Requirements**: Some regulations require specific CAs, key storage in HSMs, or approval workflows that don't fit pure automation.

**Wildcard Certificates**: Require DNS-01 validation, which means your automation needs DNS API access.

## Automating Certificate Deployment to Network Devices

Here's a practical example: automating certificate updates on an F5 BIG-IP using their REST API.

```python
#!/usr/bin/env python3
"""
Deploy SSL certificate to F5 BIG-IP via REST API
"""

import requests
import json
import urllib3
from pathlib import Path

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

class F5CertManager:
    def __init__(self, host: str, username: str, password: str):
        self.base_url = f"https://{host}/mgmt/tm"
        self.session = requests.Session()
        self.session.auth = (username, password)
        self.session.verify = False
        self.session.headers.update({"Content-Type": "application/json"})

    def upload_cert(self, name: str, cert_path: str, key_path: str):
        """Upload certificate and key files to F5"""
        cert_content = Path(cert_path).read_text()
        key_content = Path(key_path).read_text()

        # Upload certificate
        cert_payload = {
            "name": name,
            "sourcePath": f"file:/var/config/rest/downloads/{name}.crt",
            "content": cert_content
        }
        self.session.post(
            f"{self.base_url}/sys/crypto/cert",
            json=cert_payload
        )

        # Upload key
        key_payload = {
            "name": name,
            "sourcePath": f"file:/var/config/rest/downloads/{name}.key",
            "content": key_content
        }
        self.session.post(
            f"{self.base_url}/sys/crypto/key",
            json=key_payload
        )

    def update_client_ssl_profile(self, profile_name: str, cert_name: str):
        """Update a client SSL profile to use the new certificate"""
        payload = {
            "cert": f"/Common/{cert_name}.crt",
            "key": f"/Common/{cert_name}.key"
        }
        response = self.session.patch(
            f"{self.base_url}/ltm/profile/client-ssl/{profile_name}",
            json=payload
        )
        return response.status_code == 200


if __name__ == "__main__":
    f5 = F5CertManager("bigip.example.com", "admin", "password")
    f5.upload_cert("webapp-2026", "/etc/letsencrypt/live/example.com/fullchain.pem",
                   "/etc/letsencrypt/live/example.com/privkey.pem")
    f5.update_client_ssl_profile("webapp_ssl", "webapp-2026")
```

This script can be triggered by a certbot deploy hook:

```bash
# /etc/letsencrypt/renewal-hooks/deploy/f5-update.sh
#!/bin/bash
/opt/scripts/f5_cert_deploy.py
```

## Palo Alto Firewall Certificate Updates

Similar approach for Palo Alto using their XML API:

```python
#!/usr/bin/env python3
"""
Deploy SSL certificate to Palo Alto firewall
"""

import requests
import urllib3
from pathlib import Path

urllib3.disable_warnings()

class PaloAltoCertManager:
    def __init__(self, host: str, api_key: str):
        self.base_url = f"https://{host}/api"
        self.api_key = api_key

    def import_certificate(self, name: str, cert_path: str, key_path: str):
        """Import certificate and private key"""
        files = {
            "file": Path(cert_path).read_bytes()
        }
        params = {
            "type": "import",
            "category": "certificate",
            "certificate-name": name,
            "format": "pem",
            "key": self.api_key
        }
        requests.post(self.base_url, params=params, files=files, verify=False)

        # Import private key
        files = {
            "file": Path(key_path).read_bytes()
        }
        params["category"] = "private-key"
        requests.post(self.base_url, params=params, files=files, verify=False)

    def commit(self):
        """Commit the configuration"""
        params = {
            "type": "commit",
            "cmd": "<commit></commit>",
            "key": self.api_key
        }
        requests.post(self.base_url, params=params, verify=False)
```

## Centralized Certificate Management

For larger environments, consider a centralized approach:

**HashiCorp Vault PKI**: Vault can act as an intermediate CA and issue short-lived certificates on demand. Services request certificates via API, and Vault handles issuance and rotation.

```bash
# Enable PKI secrets engine
vault secrets enable pki

# Configure CA
vault write pki/root/generate/internal \
    common_name="Internal CA" \
    ttl=87600h

# Create a role for issuing certificates
vault write pki/roles/web-servers \
    allowed_domains="internal.example.com" \
    allow_subdomains=true \
    max_ttl=2160h

# Issue a certificate
vault write pki/issue/web-servers \
    common_name="app.internal.example.com" \
    ttl=720h
```

**Venafi or Keyfactor**: Commercial platforms that provide certificate lifecycle management, discovery, and automation across hybrid environments.

## Building a Certificate Inventory

Before automating, you need to know what you have. A simple discovery script:

```bash
#!/bin/bash
# Scan network for SSL services and extract certificate info

NETWORKS="10.0.0.0/24 10.0.1.0/24 192.168.1.0/24"
PORTS="443 8443 636 993 995"

for net in $NETWORKS; do
    for port in $PORTS; do
        nmap -p $port --open -oG - $net 2>/dev/null | \
        grep "open" | \
        awk '{print $2}' | \
        while read ip; do
            echo "=== $ip:$port ==="
            echo | timeout 5 openssl s_client -connect $ip:$port 2>/dev/null | \
            openssl x509 -noout -subject -dates -issuer 2>/dev/null
        done
    done
done
```

## Key Takeaways

1. **Audit your current certificate inventory** before the 90-day requirement hits. You can't automate what you don't know about.

2. **Standardize on ACME where possible**. Public-facing services should use Let's Encrypt or a commercial CA with ACME support.

3. **Build API-driven deployment** for network devices. F5, Palo Alto, and most modern gear have APIs—use them.

4. **Consider Vault or similar** for internal PKI. Short-lived certificates issued on demand eliminate the renewal problem entirely.

5. **Start now**. The transition from annual to quarterly renewals is painful if you're not prepared.

The goal is zero-touch certificate renewal. When your certificates renew and deploy without human intervention, you've solved the problem.
