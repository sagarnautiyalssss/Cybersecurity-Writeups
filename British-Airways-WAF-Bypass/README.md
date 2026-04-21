# WAF Bypass via Path Normalization (..;/)

## 📝 Summary
I discovered a Web Application Firewall (WAF) bypass on British Airways' infrastructure (`prelive-onbusiness.britishairways.com`). By using a **Path Normalization** technique, I was able to evade Akamai WAF rules that were protecting administrative endpoints.

## 🛠️ Technical Details
* **Target:** `example.britishairways.com`
* **WAF:** Akamai
* **Bypass Payload:** `..;/`
* **Vulnerability Type:** Authentication Bypass Using an Alternate Path

### The Logic
The WAF was configured to block any direct access to `/admin`. However, the backend server (PingFederate) interprets `..;/` as a directory traversal instruction. 

1. **Blocked Request:** `GET /admin` -> Response: **403 Forbidden** (Blocked by WAF)
2. **Bypassed Request:** `GET /..;/admin` -> Response: **401 Unauthorized** (Reached Backend)

This confirms that the request bypassed the security perimeter and was processed by the internal authentication layer.

## 🚀 Impact
Bypassing the WAF allows an attacker to perform unauthorized reconnaissance on internal endpoints. It enables automated scanning to discover hidden files, administrative interfaces (like Actuator endpoints), or configuration files that are intended to be shielded from the public internet.

> Evidence screenshots showing the transition from 403 Forbidden to 401 Unauthorized/200 OK via the `..;/` payload.

---
**Status:** Reported via HackerOne | Closed as Informational/N/A (Backend was still protected by secondary auth).
