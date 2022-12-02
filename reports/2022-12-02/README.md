# Hachyderm Security Audit 

Performing a lightweight scan of the edge of Hachyderm, and a light amount of OSINT style research on our security posture. 

## Work Performed 

 - [X] Edge scanning (port knocking)
 - [X] Operating System Detection (nmap)
 - [X] HTTP TLS Audit (nmap)
 - [X] Common Penetration Scanning (nmap)
 - [X] CVE / vulnerability detection (vuln)

## Edge (CDN) Summary 

The overall risk profile of our CDN is **LOW RISK**.
The overall risk profile of our code infrastructure is **MEDIUM RISK**.

The following alias names will be used in the report.

 - esme: esme.hachyderm.io
 - slappy: slappy.hachyderm.io
 - fremont: cdn-fremont-1.hachyderm.io

|          | Esme          | Slappy        | Fremont       |
|----------|---------------|---------------|---------------|
| TLS      | FAILED (HSTS) | FAILED (HSTS) | FAILED (HSTS) |
| OS       | Linux 4.X 5.X | Linux 4.X 5.X | Linux 4.X 5.X |
| Common   | Passed        | Passed        | Passed        |
| TCP Open | 22, 80, 443   | 22, 80, 443   | 22, 80, 443   |
| CVE      | FAILED        | FAILED        | FAILED        |
| NGINX    | FAILED        | FAILED        | FAILED        |


#### TLS Management and Response

None of the CDN nodes are currently serving `Strict-Transport-Security` headers which is a recommended policy to prevent against man-in-the-middle attacks.

#### Perceived Operating System

From an edge scan we have an appropriate level of detection against our operating system. No more information is gathered than is available on common public facing services.

#### Common Penetration Overview

Basic check for exposed ports and services. All servers have passed this check.

#### CVE Detection

[Suggested Reading](https://securitytrails.com/blog/nmap-vulnerability-scan)

All servers have failed a basic CVE scan, however no exploit scripts have been executed to prove we are vulnerable to these attacks.

#### Nginx Response

All servers failed this check. All servers running `nginx 1.22.1` detected remotely.

Currently, we are responding to `<name>.hachyderm.io`  in addition to `hachyderm.io` traffic on our CDN nodes.

## CDN Suggested Mediation 

- [ ] Enable and serve HSTS headers on all CDN nodes.
- [ ] Consider moving to container that has been scanned for vulnerabilities for HTTP(s) serving.
- [ ] Upgrade SSH and disable sFTP/scp on the server.
- [ ] Disable unused nginx features.
- [ ] Disable root SSH login
- [ ] Disable `<name>.hachyderm.io` server in Nginx configuration on CDN nodes.
- [ ] Disable `server_tokens off` and hide the version header.
- [ ] Follow [best practices](https://ssl-config.mozilla.org/#server=nginx&version=1.22.1&config=intermediate&openssl=1.1.1k&guideline=5.6) for our Nginx TLS server/version.

In general I do not believe our nginx or SSH is a risk today. However we should understand that by failing a common CVE scan we increase the chances that our infrastructure will be targetted. We should work towards have a 0 score for CVE scans using the following nmap scripts:

 - `nmap --script vuln`
 - `nmap --script nmap-vulners`
 - `nmap --script=vulscan/vulscan.nse`

## Core Summary 

|           | Alice         | Freud         | Fritz         | Franz         |
|-----------|---------------|---------------|---------------|---------------|
| TLS       | FAILED (HSTS) | Failed (HSTS) | Failed (HSTS) | Failed (HSTS) |
| OS        | Linux 4.X 5.X | Linux 4.X 5.X | Linux 4.X 5.X | Linux 4.X 5.X |
| Common    | Passed        | Passed        | Passed        |               |
| TCP Open  | 22, 80, 443   | 22            | 22, 80, 443   | 22            |
| CVE       | FAILED        | FAILED        | FAILED        | FAILED        |
| NGINX     | FAILED        | Passed        | Passed        | Passed        |
| Bogus SAN | grafana       |               | southbound    |               |

### TLS Management and Response 

None of our core nodes are currently serving `Strict-Transport-Security` headers which is a recommended policy to prevent against man-in-the-middle attacks.

#### Perceived Operating System

From an edge scan we have an appropriate level of detection against our operating system. No more information is gathered than is available on common public facing services.

#### Common Penetration Overview

Basic check for exposed ports and services. All servers have passed this check.

#### CVE Detection

[Suggested Reading](https://securitytrails.com/blog/nmap-vulnerability-scan)

All servers have failed a basic CVE scan, however no exploit scripts have been executed to prove we are vulnerable to these attacks.

### Nginx Response

Alice is the only server returning the `Server: nginx/1.22.1` header.

### Core Suggested Mediation

- [ ] Enable and serve HSTS headers on alice and fritz nodes.
- [ ] Consider moving to container that has been scanned for vulnerabilities for HTTP(s) serving.
- [ ] Upgrade SSH and disable sFTP/scp on the server.
- [ ] Disable unused nginx/ssh features.
- [ ] Disable root SSH login
- [ ] Disable unused SAN certs configuration on core nodes.
- [ ] Disable `server_tokens off` and hide the version header.
- [ ] Follow [best practices](https://ssl-config.mozilla.org/#server=nginx&version=1.22.1&config=intermediate&openssl=1.1.1k&guideline=5.6) for our Nginx TLS server/version.
- [ ] Close TCP port 80 and 443 on alice and fritz

## Notes and Details

No Mastodon scanning or penetration testing was performed at this time.
The edge of hachyderm.io was only audited from a services perspective. 

The current state of Mastodon and federated DDoS attacks and impact is **UNKNOWN**.

See [Federated DDoS Notes](https://medium.com/@kris-nova/experimenting-with-federation-and-migrating-accounts-eae61a688c3c).

I have reached out to Mastodon maintainers regarding federated DDoS vectors without response at this time. 


