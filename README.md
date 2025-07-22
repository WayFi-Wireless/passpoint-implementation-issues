# Passpoint, OpenRoaming, and Carrier Offload Implementation Issues on Vendor Access Points.

A public and vendor-agnostic list of issues and limitations observed in the implementation of **Passpoint (Hotspot 2.0)** and **RADSEC/RADIUS** across popular Wi-Fi vendors. This repository is maintained to help network engineers, integrators, and service providers understand real-world challenges and workarounds.

---

## Purpose

The goal of this repository is to document known issues and quirks in vendor implementations to promote transparency and encourage improvements in Passpoint and RADSEC compatibility. Contributions are welcome.

---

## Vendor-Specific Issues

### **Aruba Central**
- Limited to only 3 RCOIs.
- Domain and NAI realm configuration is convoluted; unclear how to include multiple domains.
- RADSEC implementation lacks support for client-side certificates (TLS can only be toggled on/off).

---

### **Ruckus vSZ**
- No control over order of domains, NAI realms, or RCOIs — problematic for prioritizing certain realms or paid offload.
- Operator Icon is now required when setting Operator Identification (was optional in v5.x and v6.x).

---

### **Ubiquiti (UniFi)**
- Known bugs with RADIUS; sessions over 4GB are not recorded (reportedly being fixed).
- NAI realm must currently be specified (expected fix in v9.3+).
- Numerous timing-related issues affecting reliability.
- Fast Roaming and PMF implementation is problematic. Client devices will randomly fail to join or disconnect in many instances. Same devices won't fail on other vendors with same options enabled.

---

### **Alta Labs**
- No native RADSEC or backup server support in UI — requires Power User Config and knowledge of hostapd.
- Some minor timing issues, many actively being resolved through firmware updates.

---

### **Grandstream**
- No RADSEC support.

---

### **Edgecore**
- RADSEC uses simple TLS on/off; does not support client certificates.
- Some timing-related issues noted.

---

### **Cambium**
- Severe timing-related issues.
- Incorrect accounting (e.g., username truncation post-auth).
- Accounting session ID does not persist, even on same AP and IP.
- Class and CUI attributes are not properly handled (can be mitigated with FreeRADIUS `cui` module).
- No RADSEC support.

---

### **Cisco Meraki**
- Timing-related reliability issues.
- RADSEC implementation is fundamentally flawed:
  - Requires proprietary CA to sign all AP certs.
  - Upstream servers must trust that CA (a security concern and non-starter for most ANP/IDP partners).
  - Forum feedback confirms Cisco is unwilling or unable to fix this.
  - Most users are forced to fall back to non-secure RADIUS due to this.

---

### **OpenWRT**
-	Varies significantly by device maintainer and build.
- Passpoint and RADSEC implementations are entirely manual and must be customized per deployment.
- The LuCI web UI does not support scripting or automation for Hotspot 2.0 configuration.
- Only two known pieces of accurate documentation currently exist for proper implementation. (One of those we wrote)
- RADSEC support is only available in recent versions of hostapd; older builds do not support it without also running a local radsecproxy instance on the device.

---

### **Mikrotik**
- Significant configuration differences between RouterOS versions for Wi-Fi 5 and Wi-Fi 6+ devices.
- Command-line interface (CLI) is the only reliable method to configure RADSEC; the CLI syntax has changed drastically in newer versions.
- RADSEC remains unreliable even via CLI—TLS 1.3 often fails to negotiate, requiring fallback to TLS 1.1 or 1.2. Affected versions are unclear.
- Wi-Fi 5 devices have severe accounting bugs; unmitigated deployments can crash FreeRADIUS servers due to malformed attributes or excessive traffic.
- NAS-Port-ID is incorrectly set to the interface name (a string) instead of an integer, possibly violating the standard. Definately violates what FreeRadius expects. This issue is present across all known versions.

---

## Contributing

Found an issue not listed here? Submit a pull request or open an issue to help expand the knowledge base.

---

## Disclaimer

This repository is based on real-world deployments and testing. Vendor behavior may change over time with firmware updates. Please validate findings in your own environment before making production decisions.
