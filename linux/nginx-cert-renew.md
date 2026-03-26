# SadServers Challenge: "Geneva" Renew an SSL Certificate

### Scenario Information
*   **Name:** "Geneva": Renew an SSL Certificate
*   **Level:** Easy
*   **OS:** Debian 11
*   **Access:** Root (sudo)
*   **Time Limit:** 10 minutes

### Question / Challenge
There's an Nginx web server running on this machine, configured to serve a simple "Hello, World!" page over HTTPS. However, the SSL certificate is expired. 

Create a new SSL certificate for the Nginx web server with the same Issuer and Subject (same domain and company information).

**Success Criteria:** 
- Certificate should not be expired: `echo | openssl s_client -connect localhost:443 2>/dev/null | openssl x509 -noout -dates`
- The subject of the certificate should be the same as the original one: `echo | openssl s_client -connect localhost:443 2>/dev/null | openssl x509 -noout -subject`
- Running `/home/admin/agent/check.sh` must return success.

---

### Solution Steps

Adhering to DevOps best practices, this solution applies **DRY** (variables to avoid path repetition) and **KISS** (a single compounded command) principles, while ensuring a zero-downtime certificate rotation.

#### 1. In-Place Certificate Renewal
Assuming you have identified the certificate paths (e.g., via `grep -R ssl_certificate /etc/nginx/`), execute the following block. It renews the cert retaining the exact Subject/Issuer, and gracefully reloads Nginx only if the renewal succeeds.

```bash
# Define target paths once (DRY)
CERT="/etc/nginx/ssl/nginx.crt"
KEY="/etc/nginx/ssl/nginx.key"

# Renew in-place and gracefully reload (KISS)
sudo openssl x509 -in "$CERT" -signkey "$KEY" -out "$CERT" -days 365 && \
sudo systemctl reload nginx
```

#### 2. Verify the Solution
Run the validation script to confirm success.

```bash
/home/admin/agent/check.sh
```

---

### Key Concepts
*   **DRY & KISS:** Using variables prevents path hardcoding across multiple commands. Chaining with `&&` ensures Nginx only reloads if the SSL generation process exits cleanly.
*   **Graceful Reload:** `systemctl reload` is strongly preferred over `restart` in production to prevent dropping active client connections.
*   **In-Place Signing:** Operating directly against the existing `.crt` with the associated `.key` keeps the solution minimal by inheriting the existing Subject and Issuer implicitly.
