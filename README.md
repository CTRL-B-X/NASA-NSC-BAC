# Broken Access Control in NASA Safety Center (nsc.nasa.gov)
**Bypassing NASA-Only Network Restrictions via Client-Controlled Parameter Manipulation (P3, Patched)**

## Metadata
- **Target**: `nsc.nasa.gov`
- **Category**: Web Application Vulnerability
- **Vulnerability Type**: Broken Access Control (BAC)
- **Priority**: P3 (Medium)
- **Status**: Resolved

##   Executive Summary

During a security assessment of the **NASA Safety Center (NSC)** web infrastructure, I identified a logic flaw while testing network-based access controls from a public internet connection. The application relied on client-controllable URL parameters to determine whether a request originated from an internal NASA network or the public internet. By manipulating a specific query parameter, the access control mechanism could be tricked into granting "Internal" status to an external request. This bypass allowed unauthorized access to restricted resources intended solely for NASA personnel.

##   Vulnerability Details
The **NASA Safety Center (NSC)** web application implemented a security gateway intended to restrict access to internal resources based on network origin. When accessed from a public (non-NASA) network, the site correctly displayed a blocking notice:
"You are accessing this site from a public network. This is a NASA-Only site."
However, the authorization logic was fundamentally flawed. Instead of performing robust server-side validation (such as checking authenticated session tokens, verified VPN headers, or trusted network origin indicators), the application derived the user's "internal" or "public" status primarily from client-controllable query parameters.
By appending a specific parameter, an external user could force the application to treat the request as originating from an authorized internal NASA network. This represents a classic Broken Access Control issue stemming from improper trust in client-supplied data.

##   Steps to Reproduce
1. **Initial Access:**
   From a public internet connection, navigate to a restricted resource:
https://nsc.nasa.gov/resources/annual-reports
2. **Observe Restriction:**
   The application correctly enforces the network check and displays the blocking notice:
"You are accessing this site from a public network. This is a NASA-Only site."
3. **Inject Bypass:**
   Modify the URL by appending the bypass parameter (example redacted for safety):
https://nsc.nasa.gov/resources/annual-reports?**[REDACTED_PARAMETER]**.

4. **Execution:**
   Load the crafted URL.
   
6. **Confirmation:**
   The blocking notice is bypassed, and the internal NASA resources (such as annual reports and documentation) load successfully.

##   Impact
* **Information Disclosure:** Unauthorized external users could view internal documentation and reports that were not cleared for public release, potentially exposing non-public operational or safety-related insights.
* **Perimeter Failure:** The bypass completely neutralized the intended network-based access restrictions, rendering the "NASA-Only" protection ineffective.
* **Security Circumvention:** Any external actor could masquerade as internal NASA personnel simply by modifying a URL parameter, undermining the site's access control model.

##   Remediation
NASA successfully remediated the vulnerability by properly enforcing access controls on the server side.
- **Recommendation:** Authorization decisions should never rely on client-controlled data such as query strings. Instead, implement:

Verified session tokens or JWT claims.
Authenticated network headers (e.g., VPN or internal proxy headers).
Strict server-side validation with a deny-by-default approach.
- **Result:** NASA has since patched this vulnerability following responsible disclosure via their Vulnerability Disclosure Program.

##   References
- **VRT Mapping:** Broken Access Control (CWE-284)
- **Program:** NASA Vulnerability Disclosure Program (VDP)

---
*Disclaimer: This report is for portfolio purposes. The vulnerability has been formally reported to and patched by the NASA security team.*
