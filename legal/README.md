# Legal & Regulatory Documentation

This folder contains all legal and regulatory documentation essential for MOBI's compliance platform.

## 🗂️ Contents

### 1. Regulatory Assessment
- **`Parecer Jurídico - MOBI - Análise Regulatória - 31.10.2025.pdf`** - Initial legal assessment by lawyer
- **`Aditivo ao Parecer Jurídico - MOBI - 03.11.2025.pdf`** - Addendum to initial assessment

### 2. Project Briefing
- **`MOBI Legal Context Briefing for Regulatory Assessment - v1.md`** - Project overview provided to lawyer
  - Describes MOBI's concept and future vision
  - Outlines specific legal questions
  - Defines MVP and future phases

### 3. City-Specific Compliance Configurations
- **`city-configs/`** - Machine-readable JSON configurations for each target city
  - `rio-de-janeiro.json` - Currently in regulatory vacuum
  - `niteroi.json` - 3% tax, SMU credenciamento required
  - `brasilia.json` - Enhanced security requirements
  - `sao-paulo.json` - Most complex requirements

## 🎯 Key Legal Findings

Based on the lawyer's assessment, MOBI's legal status is:

### ✅ What MOBI CAN DO:
- Provide SaaS infrastructure for OS generation
- Process driver data for compliance purposes
- Support subscription-based pricing model
- Maintain invite-code closed relationships

### ❌ What MOBI MUST NOT DO:
- Act as a transport intermediary (no driver-passenger matching)
- Handle payments directly (only record transactions)
- Control pricing or driver-client relationships
- Position as a ride-hailing platform

## 🔒 Compliance Requirements by City

| City | Municipal Tax | Registration | Security Requirements | Status |
|------|---------------|--------------|----------------------|--------|
| Rio de Janeiro | 0% | None | None | Regulatory vacuum |
| Niterói | 3% | SMU credenciamento | Standard | Active regulations |
| Brasília | 1% | SEMOB credenciamento | SOS button + photo verification | Strict regulations |
| São Paulo | 0% | CSVAPP registration | Conduapp course only | Complex regulations |

## 📋 Critical Next Steps

### Immediate (This Week)
1. Validate Rio's regulatory vacuum status
2. Contact Niterói SMU for credenciamento process
3. Review LGPD compliance with data protection lawyer

### Short Term (Next Month)
1. Architecture for GDPR-compliant data processing
2. Payment flow design (external vs. integrated)
3. Municipal reporting system design

## 📞 Key Contacts

### Rio de Janeiro
- Monitor: Câmara Municipal website
- Track: PL 671/2021 and PL 672/2021 status

### Niterói
- Authority: Secretaria Municipal de Urbanismo e Mobilidade (SMU)
- Service: Credenciamento application process

### Brasília
- Authority: SEMOB (Secretaria de Estado de Transporte e Mobilidade)
- Service: Integration with GDF security forces

### São Paulo
- Authority: CMUV (Conselho Municipal de Trânsito e Transporte)
- Service: CSVAPP annual inspection tracking

---

**Prepared by**: MOBI Legal Team  
**Last Updated**: November 2025