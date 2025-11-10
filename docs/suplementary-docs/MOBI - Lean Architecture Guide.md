# MOBI - Lean Architecture Guide
## Maximum Value, Minimum Cost - Leveraging Existing Tech Stacks

**Version**: 1.0  
**Date**: October 2025  
**Cost Savings**: R$ 110.000 - R$ 140.000  
**Timeline Reduction**: 3-5 months

---

## 🎯 EXECUTIVE SUMMARY

**Question**: "How can we reduce the size of things we need to do to save money?"

**Answer**: Leverage battle-tested open-source tools instead of building from scratch.

**Savings**: R$ 110.000 - R$ 140.000 (70% cost reduction)  
**Timeline**: 3-4 months vs 6-9 months (50% faster)  
**Trade-off**: Less initial customization control, but **much faster validation**

---

## 💡 LEAN PHILOSOPHY

### **Build vs Buy vs Leverage Decision Matrix**

| **Component** | **Build from Scratch** | **Buy Commercial** | **Leverage Open-Source** | **Recommendation** |
|--------------|----------------------|-------------------|------------------------|-------------------|
| **WhatsApp Integration** | R$ 20.000, 4 weeks | R$ 10.000/year | **FREE** (Evolution API) | ✅ **LEVERAGE** |
| **CRM Foundation** | R$ 40.000, 8 weeks | R$ 15.000/year | **FREE** (NocoBase) | ✅ **LEVERAGE** |
| **Payment Processing** | R$ 15.000, 4 weeks | 2.5% per transaction | 1.5-2.5% per transaction (Mercado Pago) | ✅ **LEVERAGE** |
| **Database** | R$ 10.000, 2 weeks | R$ 8.000/year | **FREE** (PostgreSQL) | ✅ **LEVERAGE** |
| **Compliance Engine** | **R$ 8.000, 2 weeks** | N/A (doesn't exist) | N/A | ✅ **BUILD** (our moat) |
| **Orchestrator Service** | **R$ 12.000, 3 weeks** | N/A | N/A | ✅ **BUILD** (core logic) |
| **Driver App** | **R$ 25.000, 6 weeks** | N/A | Templates (R$ 2.000) | ✅ **BUILD** (differentiation) |

**Total If All Built**: R$ 150.000, 6-9 months  
**Total Lean Approach**: **R$ 40.000, 3-4 months**  
**Savings**: **R$ 110.000, 3-5 months**

---

## 🏗️ DETAILED LEAN STACK

### **1. Evolution API for WhatsApp** ✅ LEVERAGE

#### What It Is
Open-source WhatsApp Web API wrapper. Self-hosted, Docker-ready, free.

**Repository**: https://github.com/EvolutionAPI/evolution-api  
**License**: Apache 2.0 (commercial use allowed)  
**Community**: 5.2k stars, active development, Brazilian maintainers

#### What You Get for FREE
- ✅ Send/receive WhatsApp messages
- ✅ Webhook support (message received, sent, read)
- ✅ Multi-instance (multiple WhatsApp numbers)
- ✅ Media handling (images, documents, audio)
- ✅ QR code authentication
- ✅ Docker deployment ready
- ✅ REST API (easy integration)

#### If You Built This
- **Cost**: R$ 15.000 - R$ 20.000
- **Timeline**: 3-4 weeks
- **Risk**: HIGH (WhatsApp Web protocol changes frequently)

#### How to Use
```bash
# 1. Deploy with Docker Compose
version: '3'
services:
  evolution-api:
    image: atendai/evolution-api:latest
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgresql://...
      - WEBHOOK_URL=http://orchestrator:3000/webhooks/whatsapp
    volumes:
      - evolution_data:/evolution/instances

# 2. Initialize WhatsApp instance
curl -X POST http://localhost:8080/instance/create \
  -H "apikey: YOUR_API_KEY" \
  -d '{"instanceName": "mobi-driver-1"}'

# 3. Get QR code for authentication
curl http://localhost:8080/instance/qrcode/mobi-driver-1

# 4. Send message
curl -X POST http://localhost:8080/message/sendText/mobi-driver-1 \
  -d '{
    "number": "5521999999999",
    "text": "Olá! Sua corrida foi confirmada para amanhã 10h."
  }'
```

#### Integration with MOBI
```typescript
// orchestrator/src/services/whatsapp.service.ts

class WhatsAppService {
  private evolutionApiUrl = 'http://evolution-api:8080';
  private apiKey = process.env.EVOLUTION_API_KEY;
  
  async sendMessage(phone: string, message: string): Promise<void> {
    await fetch(`${this.evolutionApiUrl}/message/sendText/mobi-driver-1`, {
      method: 'POST',
      headers: {
        'apikey': this.apiKey,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        number: phone.replace(/\D/g, ''),  // Remove formatting
        text: message
      })
    });
  }
  
  // Receive webhook from Evolution API
  async handleIncomingMessage(webhook: EvolutionWebhook): Promise<void> {
    const { from, body } = webhook.data.message;
    
    // Process message (extract booking info, etc)
    await this.processCustomerMessage(from, body);
  }
}
```

**Savings**: ~R$ 18.000, 3-4 weeks

---

### **2. NocoBase as CRM Foundation** ✅ LEVERAGE

#### What It Is
Modern no-code/low-code database platform. Think Airtable + Retool combined, but open-source and self-hosted.

**Repository**: https://github.com/nocobase/nocobase  
**License**: AGPL-3.0 (open-source) + Commercial (for SaaS)  
**Community**: 11.5k stars, very active development, excellent documentation

#### What You Get for FREE
- ✅ Visual database designer (tables, relationships)
- ✅ Modern React UI (responsive, mobile-friendly)
- ✅ REST API + GraphQL (auto-generated)
- ✅ Role-based access control
- ✅ Workflow automation (visual builder)
- ✅ Plugin system (extensible)
- ✅ Multi-language support (including Portuguese)
- ✅ Docker deployment

#### If You Built This
- **Cost**: R$ 30.000 - R$ 40.000
- **Timeline**: 6-8 weeks
- **Risk**: MEDIUM (building CRM from scratch is complex)

#### MOBI Data Models in NocoBase

**Collections to Create** (via NocoBase UI):

```javascript
// 1. Customers Collection
{
  name: 'customers',
  fields: [
    { name: 'name', type: 'string', required: true },
    { name: 'phone', type: 'string', unique: true },
    { name: 'email', type: 'string' },
    { name: 'addresses', type: 'json' },  // Array of addresses
    { name: 'customer_type', type: 'select', options: ['standard', 'senior', 'corporate'] },
    { name: 'specialized_profile', type: 'json' },  // Elderly-specific fields
    { name: 'driver_relationships', type: 'hasMany', target: 'customer_driver_links' },
    { name: 'stats', type: 'json' },  // Computed stats
    { name: 'created_at', type: 'date' },
    { name: 'updated_at', type: 'date' }
  ]
}

// 2. Drivers Collection
{
  name: 'drivers',
  fields: [
    { name: 'name', type: 'string', required: true },
    { name: 'phone', type: 'string', unique: true },
    { name: 'tier', type: 'select', options: ['solo_invite_only', 'premium_elderly', 'fleet_coordinator'] },
    { name: 'operational_cities', type: 'array' },
    { name: 'business_model', type: 'json' },  // Pricing, fees, etc
    { name: 'compliance', type: 'json' },  // Per-city compliance status
    { name: 'vehicle', type: 'json' },
    { name: 'stats', type: 'json' },
    { name: 'created_at', type: 'date' }
  ]
}

// 3. Trips Collection
{
  name: 'trips',
  fields: [
    { name: 'customer', type: 'belongsTo', target: 'customers' },
    { name: 'driver', type: 'belongsTo', target: 'drivers' },
    { name: 'city_code', type: 'string' },  // RJ_RIO, RJ_NITEROI, etc
    { name: 'trip_mode', type: 'select', options: ['invite_only', 'platform'] },
    { name: 'pickup_address', type: 'json' },
    { name: 'dropoff_address', type: 'json' },
    { name: 'scheduled_for', type: 'date' },
    { name: 'status', type: 'select', options: ['requested', 'accepted', 'completed', 'cancelled'] },
    { name: 'pricing', type: 'json' },  // Base fare, taxes, platform fee
    { name: 'payment', type: 'json' },  // Method, status, PIX transaction ID
    { name: 'compliance_snapshot', type: 'json' },  // Rules applied at trip creation
    { name: 'created_at', type: 'date' }
  ]
}

// 4. WhatsApp Messages Collection (Audit Trail)
{
  name: 'whatsapp_messages',
  fields: [
    { name: 'from', type: 'string' },
    { name: 'to', type: 'string' },
    { name: 'body', type: 'text' },
    { name: 'direction', type: 'select', options: ['incoming', 'outgoing'] },
    { name: 'customer', type: 'belongsTo', target: 'customers', nullable: true },
    { name: 'related_trip', type: 'belongsTo', target: 'trips', nullable: true },
    { name: 'created_at', type: 'date' }
  ]
}
```

#### Accessing NocoBase via API (From Orchestrator)

```typescript
// orchestrator/src/services/crm.service.ts

class CrmService {
  private nocobaseUrl = 'http://nocobase:13000/api';
  private apiToken = process.env.NOCOBASE_API_TOKEN;
  
  async createCustomer(data: CreateCustomerDto): Promise<Customer> {
    const response = await fetch(`${this.nocobaseUrl}/customers:create`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${this.apiToken}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(data)
    });
    
    return response.json();
  }
  
  async findCustomerByPhone(phone: string): Promise<Customer | null> {
    const response = await fetch(
      `${this.nocobaseUrl}/customers:list?filter[phone]=${phone}`,
      {
        headers: { 'Authorization': `Bearer ${this.apiToken}` }
      }
    );
    
    const data = await response.json();
    return data.data[0] || null;
  }
  
  async createTrip(tripData: CreateTripDto): Promise<Trip> {
    // Calculate compliance before creating
    const complianceResult = await this.complianceEngine.evaluateTripCreation(tripData);
    
    if (!complianceResult.allowed) {
      throw new Error(`Trip creation blocked: ${complianceResult.reason}`);
    }
    
    // Create trip with compliance snapshot
    return this.create('trips', {
      ...tripData,
      compliance_snapshot: complianceResult
    });
  }
}
```

#### NocoBase Dashboard Views (No Code!)

Create in UI:
- **Driver Dashboard**: Today's trips, earnings, compliance status
- **Operations Dashboard**: Active trips, pending requests, city metrics
- **Compliance Dashboard**: Drivers by city, credential expiry warnings
- **Financial Dashboard**: Revenue by city, tax owed, payment reconciliation

**Savings**: ~R$ 35.000, 6-8 weeks

---

### **3. Mercado Pago for PIX Integration** ✅ LEVERAGE

#### What It Is
Brazil's leading payment processor. PIX native, comprehensive API, excellent docs (Portuguese).

**Why Not Direct Bank Integration?**
- Direct integration: R$ 10.000+ dev cost, 4-6 weeks, complex homologation
- Mercado Pago: R$ 2.000 integration, 1 week, simpler approval process

**Fees**:
- PIX: 1.5% - 2.5% (negotiable based on volume)
- Credit Card: 3.5% - 4.5%
- No monthly fees for small volumes

#### Integration (Simple!)

```typescript
// orchestrator/src/services/payment.service.ts

import mercadopago from 'mercadopago';

class PaymentService {
  constructor() {
    mercadopago.configure({
      access_token: process.env.MERCADOPAGO_ACCESS_TOKEN
    });
  }
  
  async createPixPayment(trip: Trip): Promise<PixPayment> {
    const payment = await mercadopago.payment.create({
      transaction_amount: trip.total_fare,
      description: `MOBI - Corrida ${trip.id}`,
      payment_method_id: 'pix',
      payer: {
        email: trip.customer.email,
        first_name: trip.customer.name
      },
      notification_url: `${process.env.API_URL}/webhooks/mercadopago`
    });
    
    // Return QR code + PIX copy-paste code
    return {
      qr_code: payment.point_of_interaction.transaction_data.qr_code,
      qr_code_base64: payment.point_of_interaction.transaction_data.qr_code_base64,
      ticket_url: payment.point_of_interaction.transaction_data.ticket_url,
      expires_at: payment.date_of_expiration
    };
  }
  
  // Webhook handler (Mercado Pago notifies when paid)
  async handlePaymentWebhook(notification: MercadoPagoNotification): Promise<void> {
    const payment = await mercadopago.payment.get(notification.data.id);
    
    if (payment.status === 'approved') {
      // Update trip payment status
      await this.crmService.updateTrip(payment.external_reference, {
        payment: {
          status: 'completed',
          pix_transaction_id: payment.id,
          paid_at: new Date(payment.date_approved)
        }
      });
      
      // Send WhatsApp confirmation to customer
      await this.whatsappService.sendMessage(
        payment.payer.phone,
        'Pagamento confirmado! Obrigado por usar MOBI.'
      );
    }
  }
}
```

**Savings**: ~R$ 8.000, 3-4 weeks

---

### **4. PostgreSQL + TimescaleDB** ✅ LEVERAGE

#### What It Is
Battle-tested open-source relational database + time-series extension.

**Why PostgreSQL?**
- ✅ Free, open-source, rock-solid
- ✅ JSONB support (flexible schemas)
- ✅ Full-text search (Portuguese)
- ✅ Partitioning (by city for compliance isolation)
- ✅ PostGIS (geospatial queries for routing)
- ✅ TimescaleDB extension (optimized for trip history time-series)

#### If You Built Custom Database Layer
- **Cost**: R$ 8.000 - R$ 10.000
- **Timeline**: 2-3 weeks

#### MOBI Schema Design (With Partitioning)

```sql
-- Enable TimescaleDB extension
CREATE EXTENSION IF NOT EXISTS timescaledb;

-- Trips table (partitioned by city + time)
CREATE TABLE trips (
  id UUID PRIMARY KEY,
  customer_id UUID REFERENCES customers(id),
  driver_id UUID REFERENCES drivers(id),
  city_code VARCHAR(20) NOT NULL,  -- RJ_RIO, RJ_NITEROI, etc
  created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
  
  -- Pricing
  base_fare DECIMAL(10,2),
  municipal_tax_amount DECIMAL(10,2),
  municipal_tax_rate DECIMAL(5,2),
  platform_fee_amount DECIMAL(10,2),
  driver_earnings DECIMAL(10,2),
  
  -- Compliance snapshot (JSONB)
  compliance_snapshot JSONB,
  
  -- Other fields...
) PARTITION BY LIST (city_code);

-- Create partitions per city
CREATE TABLE trips_rio PARTITION OF trips FOR VALUES IN ('RJ_RIO');
CREATE TABLE trips_niteroi PARTITION OF trips FOR VALUES IN ('RJ_NITEROI');
CREATE TABLE trips_brasilia PARTITION OF trips FOR VALUES IN ('DF_BRASILIA');
CREATE TABLE trips_sp PARTITION OF trips FOR VALUES IN ('SP_SAO_PAULO');

-- Convert to hypertable (TimescaleDB) for time-series optimization
SELECT create_hypertable('trips_rio', 'created_at');
SELECT create_hypertable('trips_niteroi', 'created_at');
SELECT create_hypertable('trips_brasilia', 'created_at');
SELECT create_hypertable('trips_sp', 'created_at');

-- Indexes for common queries
CREATE INDEX idx_trips_driver ON trips(driver_id, created_at DESC);
CREATE INDEX idx_trips_customer ON trips(customer_id, created_at DESC);
CREATE INDEX idx_trips_city_status ON trips(city_code, status);

-- Compliance logging (JSONB queries)
CREATE INDEX idx_trips_compliance ON trips USING GIN (compliance_snapshot);
```

**Benefits**:
- ✅ **Isolation**: Rio data physically separated from Niterói (compliance)
- ✅ **Performance**: Queries only scan relevant city partition
- ✅ **Scalability**: Add new cities without schema changes
- ✅ **Time-series**: Efficient queries on historical trip data

**Savings**: ~R$ 9.000, 2-3 weeks

---

## 🏗️ WHAT WE MUST BUILD (Core Differentiation)

### **1. Orchestrator Service** 🏗️ BUILD

**Why Build**: This is your **core business logic** - how WhatsApp, CRM, compliance, payments work together.

**Investment**: R$ 8.000 - R$ 12.000  
**Timeline**: 2-3 weeks  
**Tech Stack**: Node.js + Express (rapid development)

#### Architecture

```
┌─────────────────────────────────────────────────────────────┐
│              ORCHESTRATOR SERVICE (Node.js)                  │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Webhooks Controller                                         │
│  ├─ /webhooks/whatsapp (Evolution API incoming messages)   │
│  ├─ /webhooks/mercadopago (Payment notifications)          │
│  └─ /webhooks/driver-app (Driver accepts/completes trip)   │
│                                                              │
│  Message Processing Service                                  │
│  ├─ Extract booking intent from WhatsApp message            │
│  ├─ Natural language processing (date/time/location)        │
│  └─ Create trip request in NocoBase                         │
│                                                              │
│  Trip Lifecycle Service                                      │
│  ├─ Trip creation (compliance check first!)                 │
│  ├─ Driver assignment/matching                              │
│  ├─ Status updates (accepted → on_way → completed)          │
│  └─ Automated WhatsApp notifications                        │
│                                                              │
│  Payment Orchestration Service                               │
│  ├─ Generate PIX payment (Mercado Pago)                     │
│  ├─ Handle payment webhooks                                  │
│  ├─ Calculate municipal tax withholding                      │
│  └─ Update driver earnings                                   │
│                                                              │
│  Integrations                                                │
│  ├─ WhatsAppService → Evolution API                         │
│  ├─ CrmService → NocoBase API                               │
│  ├─ ComplianceEngine → Validate rules                       │
│  └─ PaymentService → Mercado Pago                           │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

#### Sample Code Structure

```typescript
// orchestrator/src/app.ts

import express from 'express';
import { WhatsAppController } from './controllers/whatsapp.controller';
import { PaymentController } from './controllers/payment.controller';

const app = express();

app.use(express.json());

// Webhooks
app.post('/webhooks/whatsapp', WhatsAppController.handleIncoming);
app.post('/webhooks/mercadopago', PaymentController.handleNotification);

app.listen(3000, () => {
  console.log('Orchestrator running on port 3000');
});
```

```typescript
// orchestrator/src/controllers/whatsapp.controller.ts

export class WhatsAppController {
  static async handleIncoming(req: Request, res: Response) {
    const webhook: EvolutionWebhook = req.body;
    
    // 1. Find or create customer
    const customer = await crmService.findOrCreateCustomer(webhook.data.key.remoteJid);
    
    // 2. Log message
    await crmService.logWhatsAppMessage({
      from: webhook.data.key.remoteJid,
      body: webhook.data.message.conversation,
      customer_id: customer.id
    });
    
    // 3. Process message (if it's a booking request)
    if (isBookingIntent(webhook.data.message.conversation)) {
      const booking = extractBookingInfo(webhook.data.message.conversation);
      await tripService.createTripRequest(customer, booking);
    }
    
    res.sendStatus(200);
  }
}
```

**Key Functions**:
1. **Message Router**: WhatsApp → Parse → Action (create trip, answer FAQ, etc)
2. **Trip Coordinator**: Compliance check → Driver match → Notifications
3. **Payment Handler**: PIX generation → Webhook processing → Earnings calculation
4. **Automated Responses**: Confirmations, status updates, receipts

---

### **2. Compliance Engine** 🏗️ BUILD

**Why Build**: This is your **competitive moat** - no one else has multi-city compliance automation.

**Investment**: R$ 5.000 - R$ 8.000  
**Timeline**: 2 weeks (with simulation)  
**Tech Stack**: NestJS + TypeScript (type safety critical for compliance)

**(Already covered in Compliance Simulation Strategy document)**

---

### **3. Driver App (React Native)** 🏗️ BUILD (Phase 1B+)

**Why Build**: Core UX differentiation for drivers.

**Investment**: R$ 15.000 - R$ 25.000  
**Timeline**: 4-6 weeks  
**Tech Stack**: React Native + Expo (cross-platform, fast iteration)

**Can Leverage**:
- ✅ Expo (R$ 5.000 savings vs native iOS+Android)
- ✅ React Native Maps (R$ 3.000 savings)
- ✅ Expo Notifications (R$ 2.000 savings)
- ✅ UI libraries (NativeBase, React Native Paper)

**Must Build**:
- Trip request handling
- Driver-specific dashboards
- Integration with orchestrator

---

## 💰 FINAL COST COMPARISON

| **Component** | **Build Scratch** | **Lean Approach** | **Savings** |
|--------------|------------------|-------------------|-------------|
| WhatsApp Integration | R$ 18.000 | **FREE** (Evolution API) | R$ 18.000 |
| CRM Foundation | R$ 35.000 | **FREE** (NocoBase) | R$ 35.000 |
| Payment Processing | R$ 10.000 | R$ 2.000 (Mercado Pago) | R$ 8.000 |
| Database Layer | R$ 9.000 | **FREE** (PostgreSQL) | R$ 9.000 |
| **MUST BUILD** ||||
| Orchestrator Service | R$ 12.000 | R$ 12.000 | R$ 0 |
| Compliance Engine | R$ 8.000 | R$ 8.000 | R$ 0 |
| Driver App (Phase 1B+) | R$ 30.000 | R$ 18.000 (Expo templates) | R$ 12.000 |
| **TOTAL PHASE 1A** | **R$ 92.000** | **R$ 22.000** | **R$ 70.000** |
| **TOTAL FULL MVP** | **R$ 152.000** | **R$ 42.000** | **R$ 110.000** |

---

## 🎯 IMPLEMENTATION PRIORITIES

### **Week 1-2: Compliance Simulation** (R$ 5.000-8.000)
✅ Validate compliance engine  
✅ Lawyer sign-off  
**Output**: Compliance engine validated

### **Week 3-4: Infrastructure + Orchestrator** (R$ 10.000-15.000)
✅ Docker Compose: PostgreSQL + NocoBase + Evolution API  
✅ Orchestrator service (basic webhooks)  
✅ WhatsApp ↔ CRM integration  
**Output**: MVP backend functional

### **Week 5-6: Payment + Beta** (R$ 5.000-8.000)
✅ Mercado Pago PIX integration  
✅ Beta test with your brother  
✅ Iterate based on feedback  
**Output**: Phase 1A complete

### **Week 7-12: Driver App + Premium Features** (R$ 18.000-25.000)
✅ React Native driver app  
✅ Premium features (family portal, medical notes)  
**Output**: Phase 1B complete

---

## ✅ SUCCESS CRITERIA

Lean approach succeeds when:

1. ✅ **Cost**: Total spend <R$ 45.000 for full MVP
2. ✅ **Timeline**: Phase 1A functional in <6 weeks
3. ✅ **Quality**: Beta with 3-5 drivers successful
4. ✅ **Scalability**: Architecture supports 100+ drivers without rewrites
5. ✅ **Compliance**: Zero regulatory issues

---

## ⚠️ TRADE-OFFS & MITIGATION

### Trade-off 1: Less Initial Customization
**Issue**: NocoBase/Evolution API have their own UX  
**Mitigation**: Good enough for Phase 1, can always migrate later when proven

### Trade-off 2: Vendor Lock-in Risk
**Issue**: Dependent on open-source projects  
**Mitigation**: All open-source, can fork if needed. APIs well-documented for migration.

### Trade-off 3: Learning Curve
**Issue**: Team needs to learn NocoBase/Evolution APIs  
**Mitigation**: Excellent documentation, active communities, Brazilian maintainers

---

**Prepared by**: CTO - MOBI  
**Recommendation**: **STRONGLY APPROVE** lean approach  
**Confidence**: 93%

---

*This lean architecture reduces costs by 70% and timeline by 50%, enabling rapid market validation with minimal investment.*
