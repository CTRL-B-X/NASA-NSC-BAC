# Vulnerability Research: Broken Access Control in NASA Safety Center (nsc.nasa.gov) – Bypassing NASA-Only Network Restrictions via Client-Controlled Parameter Manipulation (P3, Patched)

###  Metadata
| Attribute | Value |
| :--- | :--- |
| **Target** | `nsc.nasa.gov` |
| **Category** | Web Application |
| **Vulnerability** | Broken Access Control (BAC) |
| **Priority** | P3 (Medium) |
| **Status** | Resolved |

---

###   Executive Summary

During a security assessment of the **NASA Safety Center (NSC)** web infrastructure, I identified a logic flaw while testing network-based access controls from a public internet connection. The application relied on client-controllable URL parameters to determine whether a request originated from an internal NASA network or the public internet. By manipulating a specific query parameter, the access control mechanism could be tricked into granting "Internal" status to an external request. This bypass allowed unauthorized access to restricted resources intended solely for NASA personnel.

---

###   Vulnerability Details
The NASA Safety Center (NSC) web application implemented a security gateway intended to restrict access to internal resources based on network origin. When accessed from a public (non-NASA) network, the site correctly displayed a blocking notice:
"You are accessing this site from a public network. This is a NASA-Only site."
However, the authorization logic was fundamentally flawed. Instead of performing robust server-side validation (such as checking authenticated session tokens, verified VPN headers, or trusted network origin indicators), the application derived the user's "internal" or "public" status primarily from client-controllable query parameters.
By appending a specific parameter, an external user could force the application to treat the request as originating from an authorized internal NASA network. This represents a classic Broken Access Control issue stemming from improper trust in client-supplied data.

---

###   Steps to Reproduce
1. **Initial Access:**
   Navigate to a restricted resource via a public connection:
   `https://nsc.nasa.gov/resources/annual-reports`
2. **Observe Restriction:**
   The site displays the notice: *"You are accessing this site from a public network. This is a NASA-Only site."*
3. **Inject Bypass Payload:**
   Modify the URL on step number one by appending the custom query parameters.

4. **Execution:**
   Navigate to the crafted URL.
   
6. **Confirmation:**
   The restriction is bypassed, and the internal NASA resources are now accessible.

---

###   Impact
* **Information Disclosure:** Unauthorized access to internal documentation and reports not cleared for public release.
* **Perimeter Failure:** This bypass renders the network-based access restrictions obsolete.
* **Security Circumvention:** Allows any external actor to masquerade as internal NASA personnel by simply modifying a URL string.

---

###   Remediation
The vulnerability was remediated by enforcing access controls on the server side.
- **Recommendation:** Authorization should be based on verified session tokens or authenticated network headers (e.g., VPN headers) rather than trust-based query strings.
- **Result:** NASA has since patched this vulnerability.

---

###   References
- **VRT Mapping:** Broken Access Control
- **Program:** NASA Vulnerability Disclosure Program (VDP)

---
*Disclaimer: This report is for portfolio purposes. The vulnerability has been formally reported to and patched by the NASA security team.*
