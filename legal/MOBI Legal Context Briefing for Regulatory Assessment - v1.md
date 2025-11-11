
**Prepared for:** Luana Jacundi
**Prepared by:** MOBI Founders  
**Date:** October 20, 2025  
**Version:** 1.0

---

## 1. Introduction

## What MOBI Is

MOBI is a compliance technology platform born from the lived experience of professional drivers who face a legal paradox: they want to work independently with their own clients, but lack the infrastructure to generate proper documentation, exposing them to fines and legal vulnerabilities.

MOBI was founded by **Marconi**, a professional driver who experienced firsthand the pressures of ride-hailing platforms—abusive fees, lack of client control, and the legal risks of working "off-platform." His vision:

> _"I lived the reality of a driver who wants to work well, with ethics, but gets pushed into informality. MOBI exists to give drivers the right to work within the law."_  
> — Marconi, CEO & Co-Founder

**MOBI is a SaaS platform that:**

- Digitally generates and registers Ordem de Serviço (OS) for every trip
    
- Does NOT connect passengers to drivers (no marketplace, no matching)
    
- Does NOT charge per-trip fees (subscription-based model)
    
- Acts as technology infrastructure, not a service intermediary
    
- Provides legal protection, traceability, and compliance for independent operators
    

**Core principle:** MOBI does not mediate, contract, or manage trips. It documents and legitimizes services that drivers already provide to their own clients.

## Purpose of This Briefing

This document provides context on MOBI's **MVP** (what we're building first to test with 3 initial drivers) and **future vision** (where the platform may expand) to enable a thorough legal assessment.

**We acknowledge gaps in our understanding and welcome your expertise in identifying risks, alternative structures, or blind spots we haven't considered. What are we missing?**

---

## 2. The Problem Marconi Experienced (And Why MOBI Exists)

Many independent drivers in Brazil work outside ride-hailing platforms like Uber or 99. They have their own regular clients—corporate contracts, elderly passengers, families who trust them—built through years of reputation and personal relationships. These drivers are professional, licensed, insured.

But they face a critical administrative gap: **generating proper Ordem de Serviço (OS) documents for each trip**.

## The Consequences

When stopped by authorities or during routine inspections, drivers without proper OS face:

- Fines (even when fully legitimate)
    
- Vehicle retention
    
- Service interruptions
    
- Legal vulnerability
    

The documentation process is currently:

- Manual (paper logs)
    
- Inconsistent (different formats)
    
- Or simply skipped due to complexity
    

## Why Existing Solutions Don't Work

|Solution|Why It Fails|
|---|---|
|**Uber/99**|Match unknown drivers with unknown passengers. Not relevant for drivers with private client bases. Charge 20-30% per trip.|
|**Onde.app**|White-label platform for cooperatives to build custom apps, but **no compliance features** (no OS generation).|
|**Manual systems**|Paper logs, spreadsheets—error-prone, not instantly accessible during inspections.|

## MOBI's Solution

Automate OS generation so every trip is instantly, correctly documented. Drivers maintain their independence and client relationships while gaining **legal protection**.

---

## 3. How MOBI Works — The MVP

The MVP is a **minimal test** with approximately 3 drivers (including Marconi) to validate that automated OS generation solves the compliance problem before broader rollout.

## User Flow

**Step 1: Driver Setup**

- Driver downloads MOBI Driver App
    
- Creates profile (vehicle, license, routes)
    
- Receives unique invite code: "MARCONI2025"
    

**Step 2: Client Onboarding**

- Driver shares code with existing clients via WhatsApp, SMS, or in-person
    
- Example: "Book trips with me: mobi.app/MARCONI2025"
    
- Client downloads MOBI Consumer App and enters code
    

**Step 3: What Client Sees**

- Consumer app shows ONLY that specific driver (Marconi)
    
- No browsing other drivers, no marketplace, no discovery
    
- Client sees driver's availability calendar, vehicle info, standard routes
    

**Step 4: Booking a Trip**

- Client opens app, enters:
    
    - Pickup location (can save favorites: "My home," "Hospital")
        
    - Destination
        
    - Date and time
        
    - Optional: Book for family member ("For my mother")
        
- Client clicks "Confirm Booking"
    

**Step 5: Automatic OS Generation**

- MOBI backend instantly:
    
    - Extracts trip data (driver, client, route, time, vehicle)
        
    - Applies municipal OS template (Rio, SP, Brasília, BH, etc.)
        
    - Generates PDF Ordem de Serviço
        
    - Stores with unique ID + timestamp
        
    - Sends to Driver App
        

**Step 6: Driver Has Proof**

- Driver's app shows trip with OS attached
    
- OS includes: Driver info, client info, route, date/time, vehicle details, service description
    
- If stopped by police: Opens app, shows OS on screen or prints it
    

## Payment Flow (Legal Guidance Needed)

We envision three scenarios—seeking your advice on which is safest:

**Option A: External Payment**

- Client pays driver via PIX/cash directly
    
- Driver marks "paid" in MOBI app
    
- MOBI generates OS without transaction proof
    

**Option B: Integrated Payment**

- Client pays via MOBI app (PIX/cards through Stripe/Pagar.me gateway)
    
- Payment goes directly to driver's account (MOBI doesn't hold money)
    
- MOBI records transaction ID, attaches receipt to OS
    
- **Legal question: Does facilitating this make MOBI a "payment intermediary"?**
    

**Option C: Hybrid**

- Both options available; driver chooses per client preference
    

## Privacy & Data (LGPD Compliance)

|Element|How MOBI Handles It|
|---|---|
|**Driver consent**|At subscription: Driver agrees to data processing for OS generation|
|**Consumer consent**|At app download + booking: Consumer agrees to data usage (name, location, phone)|
|**Data collection**|All explicit (form submissions, bookings)—no passive monitoring, no conversation reading|
|**User rights**|Access, correction, deletion. Drivers can export client lists.|
|**Encryption**|TLS 1.3 (transmission), AES-256 (storage)|

**Legal question:** Is MOBI a "data processor" (driver controls data, we process it) or "data controller" (we control platform logic)?

## Key Technical Details

- **Invite-code system**: Consumer locked to drivers whose codes they have. No algorithm matches them to others. No search/browse features.
    
- **Municipal templates**: OS format adapts to city requirements (e.g., Rio may require vehicle inspection certificate; SP different fields).
    
- **Document storage**: OS stored indefinitely, encrypted, with audit trail (who, when, from what data).
    
- **Calendar integration**: Driver sets availability; consumer sees open slots.
    
- **Family accounts**: Consumer adds dependents (elderly parent, child) and books on their behalf.
    

---

## 4. Future Vision — Where We See MOBI Growing

Once MVP proves the concept, MOBI expands through phases aligned with driver needs:

## Phase 2: Enhanced Driver Tools

- Real-time GPS tracking (required in some municipalities like São Paulo)
    
- Multi-trip batching (generate 10 OS documents at once)
    
- Automated reminders (client notified 1 hour before; driver when client confirms)
    
- Recurring bookings ("Every Monday 8am, home → office")
    

## Phase 3: Professional Brand & The "Selo MOBI"

**The MOBI Seal of Approval** is Marconi's vision for creating a recognizable symbol of legal, professional transport service.

**What it represents:**

**For Passengers:**

- Safety assurance (verified documentation)
    
- Legal protection (every trip has OS)
    
- Professional trust (driver operates within the law)
    

**For Drivers:**

- Professional recognition and market differentiation
    
- Business credibility with corporate clients
    
- Pride in working legally
    

**How it works:** Driver uploads documentation (CNH/EAR, insurance, vehicle registration). MOBI verifies completeness and validity. Driver receives Selo MOBI badge visible in their app profile and to passengers before booking.

**Legal question:** Does implementing verification create liability for service quality or driver conduct? Our intent is to verify documentation only, not guarantee service quality. What disclaimers are sufficient?

## Phase 4: Strategic Partnerships

- **Insurance**: APP coverage, liability insurance (discounted rates)
    
- **Accounting**: MEI formalization, tax reporting
    
- **Corporate clients**: Executive transport contracts
    
- **Vehicle services**: Maintenance, fuel cards
    

## Phase 5: Geographic Expansion

- **Pilot**: Rio de Janeiro & Niterói
    
- **Phase 2**: São Paulo, Brasília, Belo Horizonte
    
- **Phase 3**: Curitiba, Porto Alegre, Salvador, Recife, Fortaleza, Manaus
    

## Phase 6: Fleet & Cooperative Support

- B2B subscriptions for small fleets or cooperatives
    
- Each driver keeps individual app; fleet admin sees aggregated data
    
- MOBI's role unchanged: OS generation tool
    

---

## 5. Business Model

From Marconi's vision: **"Drivers should keep what they earn. MOBI is infrastructure, not an intermediary."**

- SaaS subscription (pricing TBD post-MVP validation)
    
- No per-trip fees (unlike Uber's 20-30%)
    
- No fare control (driver sets pricing)
    
- No driver-passenger matching
    

**MOBI provides:** Technology infrastructure for compliance  
**Driver controls:** Pricing, clients, service delivery, business decisions

---

## 6. Why MOBI Keeps Its Brand Visible

Unlike white-label solutions (Onde.app), MOBI's brand is visible to drivers and consumers.

**Why this matters legally:**

- Signals "infrastructure provider" (like Stripe, Shopify)
    
- Clear separation: MOBI = platform, Driver = service provider
    

**Why this matters strategically:**

- Trust builds across drivers
    
- Network effect: Consumer downloads once, can add multiple drivers
    
- Compliance expertise tied to brand
    

**Legal question:** Does visible branding affect our classification?

---

## 7. Legal Questions We Need Answered

## **MVP-Critical (Need Answers Before Launch):**

1. **Platform Classification**  
    Does our invite-code model (no driver discovery, closed relationships) classify MOBI as a "compliance tool" or a "transport intermediary"? What regulatory obligations apply to each classification?
    
2. **Payment Integration**  
    Can MOBI facilitate payments via third-party gateways (Stripe, Pagar.me), where 
    payment flows directly to the driver's account and MOBI only records transaction proof (e.g., PIX txid), without triggering:
	- Classification as a "payment intermediary"
	- Consumer protection obligations (refund handling, dispute resolution)
	- Tax reporting or withholding obligations
    
	If such obligations do arise, what is the minimum structure required to comply?
    
3. **OS Validity**  
    What minimum information must an Ordem de Serviço contain to be legally recognized by authorities? Are digitally auto-generated OS documents legally valid? Is payment evidence required for OS validity?
    
4. **LGPD Classification**  
    Is MOBI a data "controller" or "processor" when generating OS on behalf of drivers? What are the implications of each classification?
    
5. **Municipal Requirements**  
    What are the specific OS format and content requirements for Rio de Janeiro, São Paulo, Brasília, and Belo Horizonte?
    

## Future Planning

6. 6. **Document Verification (Selo MOBI)**  
    If MOBI verifies driver documentation (CNH, insurance) for completeness but not authenticity, does this create liability for service quality? What disclaimers are sufficient?



---

## 8. What We Need from This Assessment

**For MVP:** Confirmation on classification, payment structure, OS requirements, LGPD framework, blockers

**For Planning:** Implications of each expansion phase, feature sequencing recommendations

---

## 9. What Are We Missing?

Beyond our questions: What blind spots exist? What precedents should guide us? What safer structures should we consider?

We're open to reshaping our model based on your expertise to ensure long-term viability for independent drivers like Marconi.

---

## 10. Next Steps

We welcome the opportunity to discuss this briefing in detail and provide any additional context needed. Please feel free to reach out with questions or to schedule a consultation.

We look forward to your guidance in ensuring MOBI launches with a solid legal foundation that truly empowers independent drivers to work within the law.

---

**Prepared by:**

**MOBI**  
Legalizing independent transport, one driver at a time.  
Rio de Janeiro - Frankfurt
October 20, 2025
