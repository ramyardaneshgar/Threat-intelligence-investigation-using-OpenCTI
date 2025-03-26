# THM-OpenCTI
Writeup for TryHackMe OpenCTI Lab - threat intelligence investigation using OpenCTI, leveraging STIX 2.1, MITRE ATT&amp;CK, and entity relationship analysis to examine CaddyWiper malware and APT37 threat actor.

By Ramyar Daneshgar

**Focus:** CaddyWiper Malware & APT37 Threat Actor  
**Platform:** [OpenCTI](https://www.opencti.io/) – STIX2-based Threat Intelligence Platform  

As an analyst, my objective in this lab was to navigate the OpenCTI platform, identify entities, and analyze STIX-defined relationships between malware, TTPs (Tactics, Techniques, and Procedures), threat actors, and tools. Each step was driven by an intelligence-driven methodology to extract and validate structured threat information.

---

### Task 1: What is the earliest date recorded related to CaddyWiper?

#### Step Taken:
1. I searched for **“CaddyWiper”** using OpenCTI’s global search bar and selected the entry categorized as **Malware**.

   **Why:** In OpenCTI, entity classification ensures I am analyzing the correct STIX domain object (`malware`). This also scopes my investigation to relevant attributes and linked intelligence.

2. Within the CaddyWiper malware page, I selected the **“Analysis”** tab.

   **Why:** The “Analysis” tab aggregates all linked intelligence reports, incident summaries, or analytical notes. These often contain metadata such as **first published date**, which is critical for establishing an accurate kill chain timeline and determining adversary operational tempo.

3. I identified the report with the **earliest published timestamp**.

   **Why:** The **publication date** of a threat report is often the first external evidence of activity. Tracking the first observed date allows me to contextualize detection opportunities and historical campaign tracking.

#### Answer:
`2022/03/15`

---

### Task 2: Which attack technique is used by the malware for execution?

#### Step Taken:
1. I navigated to the **“Knowledge”** tab in the CaddyWiper profile.

   **Why:** The Knowledge tab provides a visual and relational mapping of STIX 2.1 relationships. It connects malware to specific MITRE ATT&CK TTPs (`uses`, `has`, `implements`, etc.). For execution-phase mapping, I need to locate techniques under the ATT&CK “Execution” tactic.

2. I filtered or visually scanned for attack patterns under the **Execution** tactic.

   **Why:** Execution techniques describe how malicious code is run. Identifying the exact TTP enables the defensive team to write targeted detection logic (YARA, Sigma, behavioral detection) based on real malware capabilities.

3. I identified the associated technique: **Native API (T1106)**.

   **Why:** “Native API” implies direct interaction with the OS kernel via undocumented or minimally documented functions — a hallmark of stealthy, low-level malware.

#### Answer:
`Native API`

---

### Task 3: How many malware relations are linked to this attack technique?

#### Step Taken:
1. I searched for the **“Native API”** attack pattern (T1106) within the **Attack Patterns** section.

   **Why:** Each ATT&CK technique is represented as a STIX `attack-pattern` object. I needed to examine how many **malware objects** are explicitly linked to T1106 using the `uses` relationship.

2. I opened the **Knowledge** tab within the Native API profile.

   **Why:** This exposes all known relationships — including which malware families are associated with this technique. It is critical to quantify how widespread a technique is across malware ecosystems.

3. I filtered the results by the **Malware** entity type.

   **Why:** OpenCTI stores relationships across multiple entity types (threat actors, tools, malware, etc.). Filtering by malware ensures I only count software-based adversary tools.

#### Answer:
`113`

---

### Task 4: Which 3 tools were used by this technique in 2016?

#### Step Taken:
1. From within the Native API (T1106) profile, I navigated to the **Tools** section under the Knowledge tab.

   **Why:** Tools such as Cobalt Strike, BloodHound, etc., are stored in OpenCTI as `tool` objects. These are typically used by adversaries and often exhibit overlapping TTPs with malware.

2. I sorted the tools by their **first observed (Start Time)** field and filtered for **2016**.

   **Why:** The task specifically requested tools from a historical timeframe. The `start_time` metadata on relationships enables temporal filtering — which is essential when analyzing changes in threat actor TTPs over time.

3. I noted the tools first observed using Native API in 2016.

   **Why:** This provides visibility into the early adoption of specific techniques by offensive frameworks and implants.

#### Answer:
`BloodHound, Empire, ShimRatReporter`

---

### Task 5: What country is APT37 associated with?

#### Step Taken:
1. I searched for **APT37** and opened the Threat Actor profile.

   **Why:** Threat actors are stored as `intrusion-set` or `threat-actor` objects. APT37 is publicly known and should have descriptive metadata available within its STIX profile.

2. I read the **description** and associated **metadata** fields.

   **Why:** The attribution (i.e., nation-state affiliation) is often explicitly mentioned in the description or in an associated country identity object. Attribution is foundational to national-level threat intelligence.

#### Answer:
`North Korean`

---

### Task 6: Which attack techniques are used by APT37 for initial access?

#### Step Taken:
1. While in the APT37 profile, I accessed the **Knowledge** tab to view the MITRE ATT&CK matrix mapping.

   **Why:** This provides direct access to ATT&CK-aligned techniques via `uses` relationships established between APT37 and TTPs.

2. I identified all techniques listed under the **Initial Access** tactic.

   **Why:** Initial Access represents the point of entry into the target network. Knowing which techniques an actor employs at this stage helps define attack surface exposure and prioritize perimeter detection rules.

3. I clicked into each relevant technique to verify its MITRE ID.

   **Why:** MITRE technique IDs (e.g., T1189) are standardized and used across detection platforms (EDR, SIEM, XDR). Using precise IDs ensures interoperability and technical accuracy.

#### Answer:
`T1189, T1566`

---

## Summary Table

| Task | Goal | Methodology | Outcome |
|------|------|-------------|---------|
| 1 | Establish malware origin timeline | Report review in Analysis tab | 2022/03/15 |
| 2 | Identify execution vector | ATT&CK mapping in Knowledge tab | Native API |
| 3 | Quantify technique usage across malware | Filter malware relationships for T1106 | 113 |
| 4 | Identify historical tool usage | Sort by start_time in Tool relationships | BloodHound, Empire, ShimRatReporter |
| 5 | Attribute actor to nation-state | Read actor profile metadata | North Korean |
| 6 | Enumerate initial access vectors | Analyze ATT&CK Initial Access mappings | T1189, T1566 |

---

## Conclusion

Throughout this investigation, I applied a structured intelligence analysis approach using OpenCTI’s STIX2-based relationships and MITRE ATT&CK integration. Each decision point was driven by the need to:

- Validate information provenance
- Establish adversary TTP linkage
- Maintain technical accuracy via MITRE alignment
- Support detection engineering and threat hunting use cases
