---
title: "Intro to Threat Modeling"
date: 2026-01-25 00:00:00 +0000
categories: [Threat Modeling]
tags: [Threat Modeling]
---
# Intro to Threat Modeling
Welcome to the blog again, today ill run you guys through what **Threat Modeling** is, so lets get started.
## What on Earth is Threat Modeling
If i was to talk to you in layman terms, Threat modeling is similar to sitting down and asking, 

> If someone wanted to break this, how would they do it. and how can we stop them from doing this or what precautions should we take to prevent this.

That is the simplest way i can put this in to words.

To further break this down for you, Think of this as building a house. you don't wait for a thief to rob you right? You always think ahead of time. You consider some of these in your thinking pattern.

- Where are the doors ?
- Are the windows easy to break ?
- Should we add locks, alarms or cameras ?

This is all part of a thinking process. In this thinking pattern what we do is we think like a bad guy so you don't get surprised later on.

## Breaking into Threat Modeling 
In the previous topic i explained what threat modeling is so now let me talk to you in a more techy format so you actually understand how this works in real life. so simply talking this is what we cover in the application security side of things.

1. What are we building?
	-  Is it a App, System, API, Network, Cloud Setup, etc.
2. What are we protecting?
	-  Is it Data, Credentials, Money, Availability or Reputation we're Protecting.
3. What can go wrong?
	- What are the Abuse cases, Misconfigurations, Logic Flaws, Attack Paths.
4. How do we reduce the risk?
	- What are the controls we can put in place, Design Changes, Monitoring, Hardening.

## Why does Threat Modeling matter.
Threat modeling works because it shifts security from a reaction to a mindset. to be more clear this prepares you to all the worst case scenarios and makes sure you are not surprised and ready for whatever the incident could be. Instead of waiting for attacks to expose weaknesses, teams proactively think about how a system could be abused while it is still being designed. This is done by identifying risks early on when changes are cheap and simple, where security becomes part of the foundation rather than an afterthought. This leads to stronger systems, fewer critical vulnerabilities in production and far less chaos when real attackers show up.

Without Threat Modeling:
- Security is reactive.
- Vulns are found after deployment.
- Fixes are expensive and messy

With threat modelling:
- Security is built in.
- High-risk issues are caught during design.
- Red teamers will cry a little :(

## What does Threat Modeling look like in practice
To walk you through the procedure again ill give you a scenario which you can work with and understand easily. Lets use the scenario of a login system and list down the model threats.

> Each Question -> Threat -> Mitigation

**Threats:**
1. Can the credentials be brute forced.
2. Can the tokens be replayed.
3. What if the MFA is bypassed.
4. What is an attacker controls the client.
5. What happens if the service is down.

**Mitigations:**
1. Implement Rate Limiting/ Account Lockout.
2. Use short lived tokens.
3. Monitor unusual login patterns.
4. Never trust the client side data, always validate everything on the server side
5. Implement High availability (redundancy and load balancing)

This is a sample way of how a threat model logic works.

## Threat Modeling Frameworks
Like pentesting frameworks we also have threat modeling frameworks. the main goal of these threat modeling frameworks is to give us an structured way to think like an attacker. These frameworks give you a systematic approach to identify risk, prioritize them and figure out how to mitigate them instead of making us guess what could go wrong. These frameworks provide checklists, categories and visual maps to make sure nothing important is missed.

We'll start with the most common framework of it all.
### STRIDE
**STRIDE stands for:**
- **S – Spoofing:** Pretending to be someone else (fake login).
- **T – Tampering:** Modifying data or code (changing a transaction).
- **R – Repudiation:** Denying actions without trace (no logs).
- **I – Information Disclosure:** Leaking sensitive info (passwords, data).
- **D – Denial of Service:** Making the system unavailable.
- **E – Elevation of Privilege:** Gaining access beyond your permission.

### DREAD
**DREAD stands for:**
- **D – Damage potential:** How bad is it if exploited?
- **R – Reproducibility:** How easy is it to repeat the attack?
- **E – Exploitability:** How easy is it to exploit?
- **A – Affected users:** How many people/systems are affected?
- **D – Discoverability:** How easy is it to find the vulnerability?

### Attack Trees
- **Purpose:** Visual map of how an attacker could achieve a goal.
- **Focus:** Multiple attack paths, from simple to complex.

**How it works:**
- Root = attacker’s main goal (e.g., steal data).
- Branches = different ways to reach it (social engineering, brute force, insider access).
- Leaves = individual actions an attacker can take.

## Where does Threat Modeling fit in the SDLC
Threat modeling would fit in the design phase prior to implementation before coding starts mostly in the architecture and design phase as this not only just focuses on the web application but also on the other components related to the application like databases file storages etc. This model will be updated when new features are added to the web application, if there are infrastructure changes or when new attack trends appear.

Since I've given you guys a basic intro to what threat modeling and what the specific frame works are we can conclude the blog for the day. ill save explaining different frameworks in depth with practical examples in another few blogs. 

## References
- [OWASP Threat Model Process](https://owasp.org/www-community/Threat_Modeling_Process)
- [Stride Introduction - Youtube](https://youtu.be/rEnJYNkUde0)
- [Stride Introduction - Blog](https://www.practical-devsecops.com/what-is-stride-threat-model/?srsltid=AfmBOoowTfJR5GExOQdwLtfriT9TkL5ShHEcZy-8gOHRCKcRBwNbvNGG)
- [Dread Introduction - Blog](https://threat-modeling.com/dread-threat-modeling/)