# MOBI - Compliance Engine Simulation Strategy
## Validating Compliance Logic Before Full Implementation

**Version**: 1.0  
**Date**: October 2025  
**Status**: Implementation Ready  
**Priority**: **CRITICAL - Execute First**

---

## 🎯 EXECUTIVE SUMMARY

**You are absolutely correct**: Compliance validation should be **one of the first things built**, not an afterthought.

**Strategy**: Build a **standalone compliance simulator** (Weeks 1-2) that validates all regulatory logic **before** integrating with the full platform. This approach:

✅ **De-risks** regulatory compliance (zero risk of launching non-compliant)  
✅ **Validates** business model legality (invite-only drivers, multi-city operations)  
✅ **Proves** to lawyer our understanding is correct  
✅ **Documents** all edge cases before production  
✅ **Saves** R$50.000+ by catching issues early  

**Investment**: R$ 5.000 - R$ 8.000 (2 weeks development + legal review)  
**Timeline**: Weeks 1-2 of project  
**Confidence**: 95% (low risk, high value)

---

## 🏗️ WHAT IS THE COMPLIANCE SIMULATOR?

A **standalone CLI/web application** that:

1. Loads city configuration JSON files (already created)
2. Simulates driver/trip scenarios
3. Evaluates compliance rules
4. Outputs pass/fail/warnings for each scenario
5. Generates compliance reports

**NOT** integrated with:
- WhatsApp/CRM (comes later)
- Payment system (comes later)
- Driver app (comes later)

**IS** integrated with:
- City config JSONs (from `/docs/v2/city-configs/`)
- Compliance rule engine
- Simulated data generator

---

## 📐 ARCHITECTURE

### System Components

```
┌────────────────────────────────────────────────────────────┐
│            COMPLIANCE SIMULATOR (Standalone)                │
└────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────┐
│  1. Config Loader                                         │
│     - Read JSON files from city-configs/                 │
│     - Validate schema                                     │
│     - Cache in memory                                     │
└──────────────────────────────────────────────────────────┘
              ↓
┌──────────────────────────────────────────────────────────┐
│  2. Scenario Generator                                    │
│     - Create test drivers (compliant + non-compliant)    │
│     - Create test trips (various cities/conditions)      │
│     - Edge cases (expired credentials, wrong city, etc)  │
└──────────────────────────────────────────────────────────┘
              ↓
┌──────────────────────────────────────────────────────────┐
│  3. Compliance Engine Core                                │
│     - Driver eligibility checker                          │
│     - Vehicle compliance checker                          │
│     - Tax calculator                                      │
│     - Report generator                                    │
│     - Mode: Advisory vs Enforcement                       │
└──────────────────────────────────────────────────────────┘
              ↓
┌──────────────────────────────────────────────────────────┐
│  4. Output Dashboard                                      │
│     - Pass/Fail summary                                   │
│     - Rule evaluation details                             │
│     - Edge case results                                   │
│     - Lawyer review report (PDF)                          │
└──────────────────────────────────────────────────────────┘
```

---

## 🧪 TEST SCENARIOS TO SIMULATE

### **Category 1: Driver Eligibility** (Rio vs Niterói vs Brasília vs SP)

#### Scenario 1.1: Rio Driver (No Municipal Requirements)
```typescript
Driver: {
  name: "João Silva",
  tier: "SOLO_INVITE_ONLY",
  operational_cities: ["RJ_RIO"],
  vehicle: {
    plate: "ABC1234",
    year: 2020,  // 5 years old
    license_jurisdiction: "RJ"
  },
  compliance: {
    RJ_RIO: {
      credenciamento_number: null,  // NOT REQUIRED in Rio
      cnh_valid: true,
      ear_notation: true,
      criminal_background_clear: true
    }
  }
}

Expected Result: ✅ PASS (Advisory mode, no municipal requirements)
```

#### Scenario 1.2: Same Driver Tries Niterói (Requires Credenciamento)
```typescript
Driver: {
  // Same as above
  operational_cities: ["RJ_RIO", "RJ_NITEROI"],
  compliance: {
    RJ_NITEROI: {
      credenciamento_number: null,  // MISSING (required!)
      credenciamento_status: "pending"
    }
  }
}

Expected Result: ❌ BLOCK (Enforcement mode, credenciamento required)
Warning: "Driver cannot operate in Niterói without SMU credenciamento"
```

#### Scenario 1.3: Driver with Vehicle Too Old for SP
```typescript
Driver: {
  operational_cities: ["SP_SAO_PAULO"],
  vehicle: {
    year: 2019,  // 6 years old (SP limit is 5 years!)
  }
}

Expected Result: ❌ BLOCK (Vehicle age exceeds SP limit)
Warning: "Vehicle 6 years old. São Paulo requires max 5 years."
```

#### Scenario 1.4: Brasília Driver Without Photo Verification
```typescript
Driver: {
  operational_cities: ["DF_BRASILIA"],
  compliance: {
    DF_BRASILIA: {
      photo_verification_completed: false,  // REQUIRED in Brasília!
      sos_button_enabled: false  // Also required!
    }
  }
}

Expected Result: ❌ BLOCK (Security features required)
Warnings:
- "Photo verification mandatory in Brasília (Decreto 42011/2021)"
- "SOS button integration required"
```

---

### **Category 2: Tax Calculation** (0%, 1%, 3% by city)

#### Scenario 2.1: Rio Trip (No Municipal Tax)
```typescript
Trip: {
  origin: "Rio de Janeiro",
  destination: "Rio de Janeiro",
  city_code: "RJ_RIO",
  total_fare: 100.00
}

Expected Tax Calculation:
- Municipal tax rate: 0%
- Municipal tax amount: R$ 0.00
- Driver receives: R$ 100.00 (minus platform fee)
- Platform fee (3%): R$ 3.00
- Driver net: R$ 97.00
```

#### Scenario 2.2: Niterói Trip (3% Municipal Tax)
```typescript
Trip: {
  origin: "Niterói",
  destination: "Niterói",
  city_code: "RJ_NITEROI",
  total_fare: 100.00
}

Expected Tax Calculation:
- Municipal tax rate: 3%
- Municipal tax amount: R$ 3.00
- Platform fee (3%): R$ 3.00
- Driver net: R$ 94.00
- Tax due to SMU Niterói: R$ 3.00 (5th business day deadline)
```

#### Scenario 2.3: Cross-City Trip (Rio → Niterói)
```typescript
Trip: {
  origin: "Rio de Janeiro",  // Rio (0% tax)
  destination: "Niterói",     // Niterói (3% tax)
  total_fare: 100.00
}

Expected Logic:
- Check Niterói config: "applies_to": ["origin_city", "destination_city"]
- Trip touches Niterói → 3% tax applies
- Municipal tax amount: R$ 3.00
- Driver net: R$ 94.00
```

---

### **Category 3: Payment Method Restrictions**

#### Scenario 3.1: Cash Payment in Brasília (PROHIBITED!)
```typescript
Trip: {
  city_code: "DF_BRASILIA",
  payment_method: "cash"
}

Expected Result: ❌ BLOCK (Cash prohibited by Lei 6.582/2020)
Error: "Cash payments prohibited in Brasília. Only digital payments allowed."
```

#### Scenario 3.2: PIX Payment in Brasília (Allowed)
```typescript
Trip: {
  city_code: "DF_BRASILIA",
  payment_method: "pix"
}

Expected Result: ✅ PASS
```

---

### **Category 4: Monthly Reporting**

#### Scenario 4.1: Generate Niterói Monthly Report
```typescript
Report: {
  city_code: "RJ_NITEROI",
  month: "October 2025",
  drivers: [
    {
      cpf: "123.456.789-00",
      cnh: "12345678900",
      vehicle_plate: "ABC1234",
      trips_count: 45,
      gross_revenue: 3500.00
    }
  ],
  total_municipal_tax: 105.00,  // 3% of R$ 3500
  deadline: "10th day of November 2025"
}

Expected Output:
- CSV format (SMU specification)
- All required fields present
- Calculated correctly
- Warning if deadline approaching
```

---

### **Category 5: Edge Cases**

#### Edge Case 5.1: Driver Operating in Multiple Cities
```typescript
Driver: {
  operational_cities: ["RJ_RIO", "RJ_NITEROI", "DF_BRASILIA"],
  compliance: {
    RJ_RIO: { /* valid */ },
    RJ_NITEROI: { credenciamento_number: "NIT-12345" },
    DF_BRASILIA: { /* MISSING sos_button */ }
  }
}

Expected Result:
- ✅ Can operate in Rio
- ✅ Can operate in Niterói
- ❌ BLOCKED from Brasília (missing requirements)
```

#### Edge Case 5.2: Expired Credentials
```typescript
Driver: {
  compliance: {
    RJ_NITEROI: {
      credenciamento_number: "NIT-12345",
      credenciamento_expiry: "2024-12-31"  // EXPIRED!
    }
  }
}

Current Date: "2025-10-01"

Expected Result: ❌ BLOCK
Warning: "Credenciamento expired on 2024-12-31. Renewal required."
```

---

## 💻 IMPLEMENTATION PLAN

### **Week 1: Build Simulator Core**

#### Day 1-2: Config Loader + Validator
```typescript
// compliance-simulator/src/config-loader.ts

import fs from 'fs';
import path from 'path';

interface CityConfig {
  city_code: string;
  regulatory_status: string;
  credenciamento: { required: boolean; /* ... */ };
  tax_structure: { enabled: boolean; rate_percentage: number; /* ... */ };
  // ... all other fields from JSON
}

class ConfigLoader {
  private configs: Map<string, CityConfig> = new Map();
  
  loadAll(configDir: string): void {
    const files = fs.readdirSync(configDir);
    
    for (const file of files) {
      if (file.endsWith('.json')) {
        const configPath = path.join(configDir, file);
        const config: CityConfig = JSON.parse(fs.readFileSync(configPath, 'utf8'));
        
        this.validateConfig(config);  // Schema validation
        this.configs.set(config.city_code, config);
      }
    }
    
    console.log(`✅ Loaded ${this.configs.size} city configurations`);
  }
  
  getConfig(cityCode: string): CityConfig | null {
    return this.configs.get(cityCode) || null;
  }
  
  private validateConfig(config: CityConfig): void {
    // Validate required fields, types, etc
    if (!config.city_code) throw new Error('city_code required');
    if (!config.legal_framework) throw new Error('legal_framework required');
    // ... more validation
  }
}
```

#### Day 3-4: Compliance Engine Core
```typescript
// compliance-simulator/src/compliance-engine.ts

type ComplianceMode = 'advisory' | 'enforcement';

interface ComplianceResult {
  passed: boolean;
  mode: ComplianceMode;
  checks: Array<{
    rule: string;
    passed: boolean;
    message: string;
    severity: 'error' | 'warning' | 'info';
  }>;
  action: 'allow' | 'block' | 'warn';
}

class ComplianceEngine {
  constructor(private configLoader: ConfigLoader) {}
  
  evaluateDriverEligibility(
    driver: Driver,
    cityCode: string,
    mode: ComplianceMode
  ): ComplianceResult {
    const config = this.configLoader.getConfig(cityCode);
    if (!config) {
      return { passed: false, message: 'City config not found' };
    }
    
    const checks = [];
    
    // Check credenciamento
    if (config.credenciamento.required) {
      const hasCredenciamento = driver.compliance[cityCode]?.credenciamento_number;
      checks.push({
        rule: 'credenciamento_required',
        passed: !!hasCredenciamento,
        message: hasCredenciamento 
          ? `Credenciamento ${hasCredenciamento} válido`
          : `Credenciamento obrigatório em ${cityCode}`,
        severity: 'error'
      });
    }
    
    // Check vehicle age
    if (config.vehicle_requirements.max_age_years) {
      const vehicleAge = new Date().getFullYear() - driver.vehicle.year;
      const maxAge = config.vehicle_requirements.max_age_years;
      checks.push({
        rule: 'vehicle_age_limit',
        passed: vehicleAge <= maxAge,
        message: `Veículo ${vehicleAge} anos (limite ${maxAge} anos)`,
        severity: 'error'
      });
    }
    
    // Check security features (Brasília)
    if (config.security_features.photo_verification_required) {
      const hasPhoto = driver.compliance[cityCode]?.photo_verification_completed;
      checks.push({
        rule: 'photo_verification',
        passed: !!hasPhoto,
        message: hasPhoto 
          ? 'Verificação foto completa'
          : 'Verificação foto obrigatória',
        severity: 'error'
      });
    }
    
    // ... more checks
    
    const allPassed = checks.every(c => c.passed);
    
    return {
      passed: allPassed,
      mode,
      checks,
      action: mode === 'advisory' ? 'warn' : (allPassed ? 'allow' : 'block')
    };
  }
  
  calculateTripTax(trip: Trip, cityCode: string): TaxCalculation {
    const config = this.configLoader.getConfig(cityCode);
    
    if (!config.tax_structure.enabled) {
      return { amount: 0, rate: 0, city: cityCode };
    }
    
    const rate = config.tax_structure.rate_percentage / 100;
    const amount = trip.total_fare * rate;
    
    return {
      amount,
      rate: config.tax_structure.rate_percentage,
      city: cityCode,
      payment_deadline: config.tax_structure.payment_deadline,
      payable_to: config.credenciamento.authority
    };
  }
}
```

#### Day 5: Scenario Generator
```typescript
// compliance-simulator/src/scenario-generator.ts

class ScenarioGenerator {
  generateAllScenarios(): Scenario[] {
    return [
      ...this.driverEligibilityScenarios(),
      ...this.taxCalculationScenarios(),
      ...this.paymentMethodScenarios(),
      ...this.edgeCaseScenarios()
    ];
  }
  
  private driverEligibilityScenarios(): Scenario[] {
    return [
      {
        name: "Rio Driver - Invite Only (No Municipal Requirements)",
        driver: {
          tier: "SOLO_INVITE_ONLY",
          operational_cities: ["RJ_RIO"],
          vehicle: { year: 2020 },
          compliance: { RJ_RIO: { credenciamento_number: null } }
        },
        cityCode: "RJ_RIO",
        mode: "advisory",
        expectedResult: "PASS"
      },
      {
        name: "Niterói Driver - Missing Credenciamento",
        driver: {
          operational_cities: ["RJ_NITEROI"],
          compliance: { RJ_NITEROI: { credenciamento_number: null } }
        },
        cityCode: "RJ_NITEROI",
        mode: "enforcement",
        expectedResult: "BLOCK"
      },
      // ... more scenarios
    ];
  }
}
```

---

### **Week 2: Dashboard + Legal Review**

#### Day 6-7: CLI Dashboard
```bash
$ npm run simulate

┌─────────────────────────────────────────────────────────────┐
│          MOBI COMPLIANCE SIMULATOR v1.0                      │
└─────────────────────────────────────────────────────────────┘

✅ Loaded 4 city configs: RJ_RIO, RJ_NITEROI, DF_BRASILIA, SP_SAO_PAULO

Running 47 test scenarios...

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CATEGORY: Driver Eligibility

✅ [1/47] Rio Driver - Invite Only
   Mode: Advisory | Result: PASS | Warnings: 0

✅ [2/47] Rio Driver - Platform Mode (Federal Only)
   Mode: Advisory | Result: PASS | Warnings: 0

❌ [3/47] Niterói Driver - Missing Credenciamento
   Mode: Enforcement | Result: BLOCK
   → Error: Credenciamento obrigatório (Decreto 12977/2018)
   → Action: Cannot operate until credenciamento obtained

✅ [4/47] Niterói Driver - With Credenciamento
   Mode: Enforcement | Result: PASS

❌ [5/47] SP Driver - Vehicle Too Old
   Mode: Enforcement | Result: BLOCK
   → Error: Veículo 6 anos (limite 5 anos - Decreto 58595/2019)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CATEGORY: Tax Calculation

✅ [10/47] Rio Trip - No Municipal Tax
   Total: R$ 100.00 | Tax: R$ 0.00 (0%) | Driver Net: R$ 97.00

✅ [11/47] Niterói Trip - 3% Municipal Tax
   Total: R$ 100.00 | Tax: R$ 3.00 (3%) | Driver Net: R$ 94.00
   → Due to SMU by: 5th business day

✅ [12/47] Rio→Niterói Cross-City
   Total: R$ 100.00 | Tax: R$ 3.00 (applies to destination)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

SUMMARY:
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Total Scenarios: 47
✅ Passed: 38 (81%)
❌ Blocked: 7 (15%)
⚠️  Warnings: 2 (4%)

🎯 Compliance engine validated successfully!

📄 Generating lawyer review report... (compliance-report-2025-10-01.pdf)
```

#### Day 8-10: Lawyer Review + Adjustments
1. Generate PDF report with all scenarios
2. Share with Brazilian transport lawyer
3. Lawyer validates logic is correct
4. Make adjustments to configs/engine based on feedback
5. Re-run all scenarios
6. Get lawyer sign-off

---

## 📊 DELIVERABLES

### Week 2 Output (What You Get)

1. **✅ Validated Compliance Engine** - Proven correct before prod
2. **✅ City Configs Validated** - Lawyer confirms JSON configs accurate
3. **✅ Edge Cases Documented** - All tricky scenarios handled
4. **✅ Test Suite** - 47+ automated tests for ongoing validation
5. **✅ Lawyer Report** - Legal sign-off on compliance approach
6. **✅ Integration Ready** - Engine ready to plug into real platform

---

## 💰 COST BREAKDOWN

| **Item** | **Cost** | **Notes** |
|----------|---------|-----------|
| Developer (2 weeks) | R$ 4.000 - R$ 6.000 | Junior-mid level, focused task |
| Lawyer Review (2 sessions) | R$ 1.500 - R$ 2.500 | 2x 2-hour sessions reviewing scenarios |
| **Total** | **R$ 5.500 - R$ 8.500** | - |

**ROI**: Saves R$ 50.000+ by catching compliance issues **before** building full platform

---

## ✅ SUCCESS CRITERIA

Simulator is "done" when:

1. ✅ All 4 city configs load without errors
2. ✅ All 47+ scenarios execute successfully
3. ✅ Pass/Fail results match expected outcomes
4. ✅ Lawyer confirms logic is legally sound
5. ✅ Edge cases all handled correctly
6. ✅ PDF report generated for audit trail

---

## 🚀 NEXT STEPS AFTER SIMULATION

Once simulator validates compliance:

1. **Extract engine** → Move to NestJS microservice
2. **Integrate** → Connect to NocoBase CRM
3. **Real data** → Test with actual driver/trip data
4. **Monitor** → Log all compliance decisions in prod
5. **Iterate** → Update configs when regulations change

---

**Prepared by**: CTO - MOBI  
**Priority**: CRITICAL - Build This First  
**Confidence**: 95%

---

*This simulation approach validates regulatory compliance with zero risk, ensuring MOBI launches legally compliant from day one.*
