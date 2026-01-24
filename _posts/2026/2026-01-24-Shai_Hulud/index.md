---
layout: post
title: "Shai Hulud Software Supply Chain Attack"
categories: Supply_Chain_Security
---

## `Shai Hulud (Shai-Hulud) & Shai-Hulud 2.0 Campaigns`
**Shai Hulud**
Shai Hulud was a self-propagating supply chain malware campaign that first emerged around mid September 2025, targeting the npm JavaScript ecosystem with worm-like behavior. The attack compromised legitimate npm packages by injecting malicious code, resulting in:
- Hundreds of npm packages being trojanized. 
- Credential theft including npm tokens, GitHub personal access tokens (PATs), and cloud credentials (AWS, GCP, Azure). 
CISA
- Exfiltration of secrets to attacker-controlled public GitHub repositories. 
- Automatic propagation via compromised tokens — using harvested credentials to republish malicious versions of all packages a compromised maintainer controlled. 

This attack was significant because it wasn’t just a malicious dependency, it behaved like a worm inside the software supply chain, exploiting trust in maintainers and automation through Continuous Integration and Continuous Delivery (CI/CD) pipelines to propagate.


The initial compromise was most likely a developer of an npm package whose package was then poisoned with malware using the the PostInstall script, identical to the method used earlier involving **`Nx`** and then published.

**Shai Hulud 2.0**
In late November 2025, a much more aggressive and automated wave, widely referred to as Shai-Hulud 2.0 or The Second Coming erupted. This evolved campaign dramatically amplified impact and sophistication.

**My thoughts...**
I want to highlight that this campaigns have a lot of similarities in tactics and execution to the `s1ngularity: Nx Build System Supply Chain Attack` and these attacks might be an escalation and continuation of those attacks.

I could also imagine that earlier attacks were a proof of concept to see what was possible and what the reaction/outcome looked like. Then leveraged the results to build the rest of these other attacks on top of those.

### Highlights
- Significant attack on Open Source Software / Cloud-Native ecosystem
- Targeted Continuous Integration & Continuous Delivery (CI/CD) Pipelines & Developer Environments
   - Harvested credentials and secrets
   - Modified hundreds of packages
- Created a randomly named repos with the stolen data
- Destructive: If the malware can't auth to GitHub or NPM, it deletes the users Home directory
- Some commits were made under the name "Linus Torvalds"
- The malware used has resemblance to the one used in the Nx compromise (End of August 2025)
- Malicious scripts used: `setup_bun.js` & `bun_environment.js`
- A reference from the Microsoft Defender Security Research Team blog post said that the `Shai Hulud` name came from the name the attackers gave their GitHub Runner Agent "SHA1Hulud", where Shai Hulud is a reference to the sandworms from Dune.

### Timeline
- August 27, 25: Nx Attack (see **`Nx Supply Chain Attack`** below)
- September 16, 25: First Shai Hulud Strike
- November 24, 25: A second wave of attacks, known as the "Second Coming" (aka Shai Hulud 2.0) was carried out prior to npm's deadline for revoking old tokens

### Code Execution
> WIP: I plan to collect the snippets here of the malicious code leveraged in the attack

<details>
<summary></summary>

</details>


### Indicators of Compromise (IoCs)


Per Microsoft:   
    
|                      |           |                                                               |                   |                  |
| -------------------- | --------- | ------------------------------------------------------------- | ----------------- | ---------------- |
| **Indicator**        | **Type**  | **Description**                                               | **First seen**    | **Last seen**    |
| _setup_bun.js_       | File name | Malicious script that installs the Bun runtime                | November 24, 2025 | December 1, 2025 |
| _bun_environment.js_ | File name | Script that facilitates credential gathering and exfiltration | November 24, 2025 | December 1, 2025 |
    

### Blogs
#### Shai Hulud
- [StepSecurity - Shai-Hulud: Self-Replicating Worm Compromises 500+ NPM Packages](https://www.stepsecurity.io/blog/ctrl-tinycolor-and-40-npm-packages-compromised)
- [CISA - Widespread Supply Chain Compromise Impacting npm Ecosystem](https://www.cisa.gov/news-events/alerts/2025/09/23/widespread-supply-chain-compromise-impacting-npm-ecosystem?utm_source=chatgpt.com)
- [Shai-Hulud: Ongoing Package Supply Chain Worm Delivering Data-Stealing Malware]()

#### Shai Hulud 2.0
- [Wiz - Shai-Hulud 2.0 Supply Chain Attack: 25K+ Repos Exposing Secrets](https://www.wiz.io/blog/shai-hulud-2-0-ongoing-supply-chain-attack)
- [CyberSecureFox - SHAI-HULUD 2.0: MASSIVE NPM SUPPLY CHAIN ATTACK EXPOSES GITHUB AND CLOUD SECRETS](https://cybersecurefox.com/en/shai-hulud-2-npm-supply-chain-attack/?utm_source=chatgpt.com)

#### NPM compromise specific
- [Popular Tinycolor npm Package Compromised in Supply Chain Attack Affecting 40+ Packages - socket.dev](https://socket.dev/blog/tinycolor-supply-chain-attack-affects-40-packages)
- [Updated and Ongoing Supply Chain Attack Targets CrowdStrike npm Packages - socket.dev](https://socket.dev/blog/ongoing-supply-chain-attack-targets-crowdstrike-npm-packages)

### Videos
- [Shai-hulud Q&A from ReversingLabs](https://www.linkedin.com/feed/update/urn:li:activity:7373747903267360770)
- [ThePrimeagen: this may be the worst one](https://youtu.be/69F9IuBWb-E?si=D-7djxoAe8_NOSfG): Got to have the quick and dirty :)
- [Low Level: the npm malware is a hacking masterpiece](https://youtu.be/lqZo4waMB3c?si=t-PlBG4WRP0wRnZf)

### Shai Hulud Threat Actors (TAs)
Unknown

<br>

---   

## `s1ngularity: Nx Build System Supply Chain Attack`
The **`Nx`** package was compromised introducing malware through a malicious post-installation script (`telemetry.js`) which was published to the npm registry.  The scripts leverage AI CLI Tools to do most of the heavy lifting of recon and data exfiltration by providing a prompt to perform such actions, with the goal of stealing credentials and secrets (SSH keys, auth tokens, env vars, etc.) which were then published to a publicly accessible repos. It also attempted to steal cryptocurrency wallets if it found the files. This was a 2 phase attack, check the Highlights for further details.

Direct quote from socket.dev [Nx npm Packages Compromised in Supply Chain Attack Weaponizing AI CLI Tools](https://socket.dev/blog/nx-packages-compromised) blog post:
> "On August 26, 2025, multiple malicious versions of the popular Nx build system were published to npm containing malware that abused AI CLI developer tools (Claude, Gemini, Q) for reconnaissance and data theft, making this one of the first documented supply chain attacks to do so."

### **Highlights & Timeline**
- The malicious script leveraged Developer AI CLI tools (Claude, Gemini and Q) for recon and data exfiltration.
  - First known attack leveraging Dev AI Tools
- This was a 2 phase attack:
  - In the initial phase attack on: 
    - August 26, 25: Phase 1 - Malicious versions of the Nx system package were published to the NPM registry.
    - Stolen data was published to publicly accessible GitHub repos created by the attacker within the victim's GitHub account.
    - August 27, 25: GitHub disabled all the Threat Actor's created repos to prevent further exposure of stolen data.
      - However there was an 8-hour exposure of the data, giving the attacker plenty of time to harvest it.
  - In the second phase attack: 
    - August 28, 25: Phase 2 - The attacker used stolen GitHub tokens (obtained from Phase 1) to:
      -  Change private repositories to public, exposing additional sensitive data.
      -  Rename the repos following his pattern `s1ngularity-repository-{random-string}`.
      -  Forked repos to harvest data.
   -  August 29, 25: Attack suspended.
   -  Impact: 400 users/orgs and over 5500 repositories.
- The stolen data was double Base64 encoded.
- Only targets non-Windows systems.
- [Nx](https://nx.dev) is a build platform.

### Code
<details>
<summary>The dirty code... (Click to expand)</summary>  

**`package.json:`** - Sourced from the StepSecurity post
Here you can see the `scripts.postinstall` instruction of `node telemetry.js` which leads to the execution of the malicious script.
```json
{
  "name": "nx",
  "version": "21.5.0",
  "private": false,
  "description": "The core Nx plugin contains the core functionality of Nx like the project graph, nx commands and task orchestration.",
  "repository": {
    "type": "git",
    "url": "https://github.com/nrwl/nx.git",
    "directory": "packages/nx"
  },
 ...
  "main": "./bin/nx.js",
  "types": "./bin/nx.d.ts",
  "type": "commonjs",
  "scripts": {
    "postinstall": "node telemetry.js"
  }
}
```

> NOTE: For further tidbits I encourage you to check out the StepSecurity [s1ngularity: Popular Nx Build System Package Compromised with Data-Stealing Malware](https://www.stepsecurity.io/blog/supply-chain-security-alert-popular-nx-build-system-package-compromised-with-data-stealing-malware#technical-analysis) Technical Analysis section.

</details>


### Indicators of Compromise (IoCs) - Artifacts
#### From Wiz:
**File Artifacts (not unique to this attack):**
- `~/.bashrc,` `~/.zshrc` modified with `sudo shutdown -h 0`
- `/tmp/inventory.txt` (sensitive file paths)
- `/tmp/inventory.txt.bak`
- `2379ac0e03b1a67c4ca5693136eff4945e644a91` (`telemetry.js` variant SHA1)
- `e5d1f3c45ee7cca6ae59cf64e0573050bbe136ec` (`telemetry.js` variant SHA1)
- `b4f20b39aa6df1002872f07973024d85aa49abaf` (`telemetry.js` variant SHA1)
- `d2438106211ebd12c4f0a248848bc9864c97a3c0` (`telemetry.js` variant SHA1)

**Network/Account Artifacts:**
- Outbound API calls to api.github.com (`/user/repos`, /`repos/*/contents/results.b64`)
- Public GitHub repositories named `s1ngularity-repository`,  `s1ngularity-repository-0`, or  `s1ngularity-repository-1`
- File `results.b64` containing base64-encoded data


### Blogs
- [Nx npm Packages Compromised in Supply Chain Attack Weaponizing AI CLI Tools](https://socket.dev/blog/nx-packages-compromised)
- [s1ngularity: supply chain attack leaks secrets on GitHub: everything you need to know](https://www.wiz.io/blog/s1ngularity-supply-chain-attack)
- [s1ngularity: Popular Nx Build System Package Compromised with Data-Stealing Malware](https://www.stepsecurity.io/blog/supply-chain-security-alert-popular-nx-build-system-package-compromised-with-data-stealing-malware)
- [Hackers Target Popular Nx Build System in First AI-Weaponized Supply Chain Attack](https://www.securityweek.com/hackers-target-popular-nx-build-system-in-first-ai-weaponized-supply-chain-attack/)
- [Over 6,700 Private Repositories Made Public in Nx Supply Chain Attack](https://www.securityweek.com/over-6700-private-repositories-made-public-in-nx-supply-chain-attack/)

---    
