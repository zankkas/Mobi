## **MOBI Development Tasks - Multi-City Scalable Architecture**

### **Phase 1: Core Legal Engine + Multi-City Foundation (Week 1)**

**Task 1.1: Multi-City Data Models**
```
- City model: city_code, regulations_config, reporting_format, tax_rate
- Driver model: CPF, CNH, CNH_validity, name, vehicle_plates[], active_cities[]
- Vehicle model: plate, CRLV, model, year, licensed_cities[]
- Passenger model: name, CPF, phone, preferred_cities[]
- Trip model: origin, destination, city_jurisdiction, start_time, end_time, value, payment_method
- OS model: unique_number, timestamp, city_code, all_participants, legal_status
```

**Task 1.2: Configurable OS Generation Engine**
```
- City-specific OS templates (Rio vs Niterói)
- Municipal regulation configuration system
- Generate unique OS number per trip per city
- Include all mandatory fields per city requirements
- Event tracking: OS_CREATED → OS_CONFIRMED → TRIP_STARTED → TRIP_ENDED → PAYMENT_CAPTURED
- Multi-jurisdiction audit trail logging
```

**Task 1.3: Multi-City CSV Export Module**
```
Rio de Janeiro Format (Anexo II Resolução 48/2021):
- Nome, Placa, CNH, Validade, Conformidade, Valor
- 1.5% tax calculation included

Niterói Format:
- Similar structure, validate specific differences
- No tax calculation currently
- Monthly/quarterly flexibility

Municipal Adapter Framework:
- Pluggable export modules per city
- Configuration-driven field mapping
- Dynamic tax calculation system
```

### **Phase 2: Large-Scale Multi-City Simulation + UI Foundation (Week 2)**

**Task 2.1: Multi-City Simulation Engine**
```
- Generate 50 drivers in Rio + 50 drivers in Niterói
- Simulate 1000+ trips across both cities
- Cross-city trips (Rio ↔ Niterói)
- City-specific regulation testing
- Variable municipal requirements validation
- Edge cases: different city regulations, tax calculations
```

**Task 2.2: Rio/Niterói Regulation Validation**
```
- Test reporting differences between cities
- Validate tax calculation variations (1.5% vs 0%)
- Compare CSV export formats
- Cross-jurisdiction trip handling
- Municipal compliance verification
```

**Task 2.3: Performance & Scalability Testing**
```
- Automated OS creation for 1000+ trips across 2 cities
- Multi-city CSV generation from large datasets
- Database performance under multi-jurisdiction load
- Municipal adapter performance testing
- Error handling at enterprise scale across cities
```

**Task 2.4: UI Template Integration**
```
- Source and customize ride-hailing UI templates
- Multi-city passenger request interface
- Driver app with city selection/switching
- Trip status and navigation interface
- Payment confirmation screens
- City-specific features toggle
```

**Task 2.5: Driver Onboarding Preparation**
```
- Multi-city driver registration system
- Document upload interface per city requirements
- Vehicle registration system with city licensing
- Insurance verification workflow per jurisdiction
```

### **Phase 3: Complete Multi-City System + Real Pilot (Week 3)**

**Task 3.1: End-to-End Multi-City System**
```
- Connect legal engine to UI interfaces
- Real-time OS generation from UI interactions
- City-aware passenger-to-driver workflow
- Multi-jurisdiction payment integration (manual for pilot)
- Cross-city trip tracking and completion flow
```

**Task 3.2: Real Pilot Infrastructure**
```
- Local deployment environment setup
- Driver mobile app deployment (Rio/Niterói capable)
- Passenger mobile app deployment (city selection)
- Admin dashboard for multi-city trip monitoring
- Real-time system monitoring across jurisdictions
```

**Task 3.3: Pilot Launch Preparation**
```
- 3 driver accounts setup (Rio/Niterói operations)
- Test passenger recruitment strategy
- End-to-end workflow validation across cities
- Manual processes documentation per city
- Support and monitoring procedures
- Launch readiness checklist
```

**Task 3.4: Multi-City Validation & Documentation**
```
- OS document completeness verification per city
- Municipal CSV format compliance testing (Rio + Niterói)
- Cross-jurisdiction trip handling validation
- System performance documentation
- Multi-city edge case handling documentation
- Scalability framework for additional cities
```

### **Technical Specifications**

**Multi-City Backend Requirements:**
- Database: PostgreSQL with city partitioning for audit trail compliance
- Configuration: City-specific regulation management system
- Security: HTTPS/TLS, token authentication per jurisdiction
- Logging: 5-year retention system across all cities
- Adapters: Municipal integration framework for CSV/XML generation

**Mobile Applications:**
- Driver app: Multi-city operations, jurisdiction switching
- Passenger app: City selection, cross-city trip requests
- Admin panel: Multi-city monitoring and reporting
- Cross-platform compatibility (iOS/Android)

**Scalability Architecture:**
- Municipal adapter pattern for new cities
- Configuration-driven compliance system
- Pluggable tax and reporting modules
- Dynamic regulation update capability

**Development Strategy:**
- **Week 1:** Multi-city foundation + legal engine
- **Week 2:** Rio/Niterói validation + UI development
- **Week 3:** Complete system integration + real pilot launch

---


