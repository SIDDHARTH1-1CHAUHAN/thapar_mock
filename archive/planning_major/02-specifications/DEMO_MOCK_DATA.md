# Demo Mock Data - TradeOptimize AI
## Fallback Data for Flawless Demo Presentation

This file contains pre-computed demo data to use if APIs fail or for practicing the demo.

---

## 1. HS Classification Demo Data

### Demo Product 1: Solar Portable Charger with LED Flashlight

```json
{
  "product_description": "Solar-powered portable charger with integrated LED flashlight, 10000mAh lithium battery, USB-C output",
  "classification": {
    "hs_code": "8504.40.95",
    "confidence": 94,
    "description": "Static converters; power supplies for automatic data processing machines and units thereof",
    "chapter": "Chapter 85 - Electrical machinery and equipment",
    "section": "Section XVI - Machinery and mechanical appliances",
    "gir_applied": "GIR 3(b) - Essential character determined by charging function",
    "alternatives": [
      { "code": "8513.10.40", "description": "Portable flashlights", "confidence": 23 },
      { "code": "8506.50.00", "description": "Lithium primary cells", "confidence": 15 }
    ],
    "rulings": [
      { "id": "N302451", "type": "CROSS Ruling", "date": "2022-03-15" },
      { "id": "NY N293245", "type": "NY Ruling", "date": "2021-11-08" }
    ]
  },
  "ocr_extracted": {
    "model_number": "SPC-10000",
    "manufacturer": "Shenzhen PowerTech",
    "specs": ["10000mAh", "USB-C PD 18W", "Solar 5V/200mA", "LED 3W"]
  }
}
```

### Demo Product 2: Bluetooth Wireless Earbuds

```json
{
  "product_description": "True wireless Bluetooth earbuds with active noise cancellation, charging case, touch controls",
  "classification": {
    "hs_code": "8518.30.20",
    "confidence": 97,
    "description": "Headphones and earphones, whether or not combined with microphone",
    "chapter": "Chapter 85 - Electrical machinery and equipment",
    "section": "Section XVI - Machinery and mechanical appliances",
    "gir_applied": "GIR 1 - Terms of heading 8518",
    "alternatives": [
      { "code": "8471.60.80", "description": "Input/output units", "confidence": 8 }
    ]
  }
}
```

### Demo Product 3: Stainless Steel Water Bottle

```json
{
  "product_description": "Double-wall vacuum insulated stainless steel water bottle, 32oz capacity, BPA-free lid",
  "classification": {
    "hs_code": "7323.93.00",
    "confidence": 99,
    "description": "Table, kitchen or other household articles; stainless steel",
    "chapter": "Chapter 73 - Articles of iron or steel",
    "section": "Section XV - Base metals and articles of base metal",
    "gir_applied": "GIR 1 - Clearly household article of stainless steel"
  }
}
```

---

## 2. Landed Cost Calculator Demo Data

### Route: Shenzhen, China → Los Angeles, USA

```json
{
  "product": {
    "hs_code": "8504.40.95",
    "description": "Solar portable charger",
    "quantity": 5000,
    "unit_value": 12.50,
    "total_value": 62500.00,
    "currency": "USD",
    "weight_kg": 1250,
    "origin_country": "CN",
    "destination_country": "US"
  },
  "costs": {
    "product_value": 62500.00,
    "base_duty": {
      "rate": 0.0,
      "amount": 0.00,
      "note": "Column 1 rate for HS 8504.40.95"
    },
    "section_301_tariff": {
      "rate": 0.25,
      "amount": 15625.00,
      "note": "List 3 products - 25% additional duty"
    },
    "mpf": {
      "rate": 0.003464,
      "amount": 216.50,
      "min": 31.67,
      "max": 614.35,
      "note": "Merchandise Processing Fee"
    },
    "hmf": {
      "rate": 0.00125,
      "amount": 78.13,
      "note": "Harbor Maintenance Fee"
    },
    "freight": {
      "sea_freight": 1850.00,
      "insurance": 312.50,
      "customs_clearance": 450.00,
      "inland_trucking": 680.00
    },
    "totals": {
      "total_duties": 15841.50,
      "total_freight": 3292.50,
      "landed_cost": 81634.00,
      "cost_per_unit": 16.33,
      "effective_duty_rate": 0.2535
    }
  },
  "exchange_rate": {
    "usd_cny": 7.24,
    "as_of": "2026-02-18T09:00:00Z"
  }
}
```

---

## 3. Trade Compliance Demo Data

### Compliance Check: Solar Charger from China

```json
{
  "shipment": {
    "hs_code": "8504.40.95",
    "origin": "CN",
    "destination": "US",
    "supplier": "Shenzhen PowerTech Co., Ltd."
  },
  "compliance_results": {
    "overall_risk_score": 72,
    "risk_level": "MEDIUM",
    "checks": [
      {
        "category": "OFAC Sanctions",
        "status": "CLEAR",
        "details": "No matches found in SDN list",
        "checked_entities": ["Shenzhen PowerTech Co., Ltd."],
        "timestamp": "2026-02-18T09:15:00Z"
      },
      {
        "category": "Entity List (BIS)",
        "status": "CLEAR",
        "details": "Supplier not on Entity List"
      },
      {
        "category": "Section 301 Tariffs",
        "status": "WARNING",
        "details": "Product subject to 25% additional tariff (List 3)",
        "action_required": "Include Section 301 duties in cost calculation"
      },
      {
        "category": "Lithium Battery Regulations",
        "status": "ACTION_REQUIRED",
        "details": "UN38.3 certification required for lithium battery products",
        "documents_required": ["UN38.3 Test Summary", "MSDS", "Battery Declaration"]
      },
      {
        "category": "FCC Compliance",
        "status": "WARNING",
        "details": "Electronic device may require FCC certification",
        "action_required": "Verify FCC ID or supplier of conformity"
      }
    ],
    "required_documents": [
      { "name": "Commercial Invoice", "status": "required" },
      { "name": "Bill of Lading", "status": "required" },
      { "name": "Packing List", "status": "required" },
      { "name": "Certificate of Origin", "status": "recommended" },
      { "name": "UN38.3 Test Summary", "status": "required" },
      { "name": "Material Safety Data Sheet (MSDS)", "status": "required" }
    ]
  }
}
```

---

## 4. Route Optimizer Demo Data

### Route Options: Shenzhen → Los Angeles

```json
{
  "origin": {
    "city": "Shenzhen",
    "country": "CN",
    "port": "CNSZX - Yantian Port"
  },
  "destination": {
    "city": "Los Angeles",
    "country": "US",
    "port": "USLAX - Port of Los Angeles"
  },
  "cargo": {
    "weight_kg": 1250,
    "volume_cbm": 4.2,
    "type": "General Cargo"
  },
  "routes": [
    {
      "id": "route_1",
      "name": "Direct Sea Freight",
      "carrier": "COSCO Shipping",
      "vessel": "CSCL Saturn",
      "transit_days": 18,
      "cost_usd": 1850,
      "emissions_kg_co2": 245,
      "congestion_risk": "HIGH",
      "port_wait_days": 3,
      "recommended": false,
      "waypoints": [
        { "port": "CNSZX", "type": "origin" },
        { "port": "USLAX", "type": "destination" }
      ]
    },
    {
      "id": "route_2",
      "name": "Via Long Beach (RECOMMENDED)",
      "carrier": "Evergreen Marine",
      "vessel": "Ever Given",
      "transit_days": 19,
      "cost_usd": 1720,
      "emissions_kg_co2": 238,
      "congestion_risk": "MEDIUM",
      "port_wait_days": 1,
      "recommended": true,
      "savings": {
        "cost": 130,
        "time_days": -1,
        "reason": "Lower port congestion at Long Beach"
      },
      "waypoints": [
        { "port": "CNSZX", "type": "origin" },
        { "port": "USLGB", "type": "destination" }
      ]
    },
    {
      "id": "route_3",
      "name": "Express Air Freight",
      "carrier": "FedEx",
      "flight": "FX5724",
      "transit_days": 4,
      "cost_usd": 8450,
      "emissions_kg_co2": 1890,
      "congestion_risk": "LOW",
      "recommended": false,
      "waypoints": [
        { "airport": "VHHH - Hong Kong", "type": "origin" },
        { "airport": "KLAX - Los Angeles", "type": "destination" }
      ]
    }
  ],
  "port_congestion": {
    "USLAX": { "utilization": 85, "status": "HIGH", "avg_wait_days": 3.2 },
    "USLGB": { "utilization": 62, "status": "MEDIUM", "avg_wait_days": 1.1 },
    "CNSZX": { "utilization": 71, "status": "MEDIUM", "avg_wait_days": 0.5 }
  }
}
```

---

## 5. AI Assistant Demo Conversations

### Conversation 1: Product Classification Help

```json
{
  "messages": [
    {
      "role": "assistant",
      "content": "Hello! I've analyzed your recent imports. Would you like to review the classification for the \"Wireless Headphones\" pending in your queue, or start a new product inquiry?",
      "suggestions": ["REVIEW_PENDING", "NEW_CLASSIFICATION", "IMPORT_POLICY_QUERY"]
    },
    {
      "role": "user",
      "content": "I need to check the HS code for a \"Solar-powered portable charger with integrated LED flashlight\" shipping from Shenzhen to Los Angeles."
    },
    {
      "role": "assistant",
      "content": "Processing technical specifications for solar-integrated power banks.\n\nBased on General Interpretive Rule (GIR) 3(b), composite goods are classified by the component that provides the essential character.\n\nI am leaning towards Heading 8504 (Static Converters) rather than 8513 (Flashlights). The charging function defines the essential character of this product.\n\n**Recommended HS Code: 8504.40.95**\n\nThis classification is supported by CROSS Ruling N302451 for similar solar charging devices.\n\nWould you like me to calculate the landed cost for this shipment?",
      "suggestions": ["CALCULATE_LANDED_COST", "VIEW_RULING_DETAILS", "CHECK_COMPLIANCE"]
    },
    {
      "role": "user",
      "content": "What documents do I need for this shipment?"
    },
    {
      "role": "assistant",
      "content": "For importing HS 8504.40.95 (solar chargers with lithium batteries) from China to the US, you'll need:\n\n**Required Documents:**\n1. Commercial Invoice (showing value, quantity, HS code)\n2. Bill of Lading (sea freight) or Airway Bill (air freight)\n3. Packing List\n4. **UN38.3 Test Summary** - Critical for lithium batteries\n5. **Material Safety Data Sheet (MSDS)** - For battery products\n\n**Recommended Documents:**\n- Certificate of Origin (for potential FTA benefits)\n- Product specifications/datasheets\n- FCC Declaration of Conformity (electronic device)\n\n**⚠️ Important:** Without UN38.3 certification, your shipment may be held at customs. This is the #1 cause of delays for battery products.",
      "suggestions": ["GET_UN38.3_TEMPLATE", "CALCULATE_DUTIES", "FIND_CUSTOMS_BROKER"]
    }
  ]
}
```

---

## 6. Cargo Tracking Demo Data

### Active Shipment: TCLU1234567

```json
{
  "shipment": {
    "container_id": "TCLU1234567",
    "bill_of_lading": "COSCO2024SZX0815",
    "vessel": "CSCL Saturn",
    "voyage": "034W",
    "carrier": "COSCO Shipping",
    "origin": {
      "port": "Yantian, Shenzhen",
      "country": "CN",
      "departed": "2026-02-10T08:00:00Z"
    },
    "destination": {
      "port": "Long Beach, CA",
      "country": "US",
      "eta": "2026-02-28T14:00:00Z"
    },
    "cargo": {
      "description": "Solar Portable Chargers",
      "hs_code": "8504.40.95",
      "weight_kg": 1250,
      "value_usd": 62500
    }
  },
  "current_position": {
    "latitude": 21.3069,
    "longitude": -157.8583,
    "location_name": "Pacific Ocean, near Hawaii",
    "speed_knots": 18.5,
    "heading": 75,
    "timestamp": "2026-02-18T12:00:00Z"
  },
  "timeline": [
    {
      "status": "CONTAINER_LOADED",
      "location": "Yantian Port",
      "timestamp": "2026-02-09T14:30:00Z",
      "completed": true
    },
    {
      "status": "DEPARTED_ORIGIN",
      "location": "Yantian Port",
      "timestamp": "2026-02-10T08:00:00Z",
      "completed": true
    },
    {
      "status": "IN_TRANSIT",
      "location": "Pacific Ocean",
      "timestamp": "2026-02-18T12:00:00Z",
      "completed": true,
      "current": true
    },
    {
      "status": "ARRIVAL_AT_DESTINATION",
      "location": "Long Beach Port",
      "timestamp": "2026-02-28T14:00:00Z",
      "completed": false,
      "estimated": true
    },
    {
      "status": "CUSTOMS_CLEARANCE",
      "location": "Long Beach Port",
      "timestamp": "2026-02-29T10:00:00Z",
      "completed": false,
      "estimated": true
    },
    {
      "status": "DELIVERED",
      "location": "Los Angeles Warehouse",
      "timestamp": "2026-03-02T16:00:00Z",
      "completed": false,
      "estimated": true
    }
  ],
  "alerts": [
    {
      "type": "INFO",
      "message": "Vessel on schedule - ETA unchanged",
      "timestamp": "2026-02-18T06:00:00Z"
    },
    {
      "type": "WARNING",
      "message": "Port of Long Beach congestion expected - possible 1-day delay",
      "timestamp": "2026-02-17T09:00:00Z"
    }
  ]
}
```

---

## 7. Analytics Dashboard Demo Data

```json
{
  "period": "Last 30 Days",
  "summary": {
    "total_classifications": 847,
    "total_shipments": 124,
    "total_value_usd": 2340000,
    "duties_paid_usd": 187200,
    "duties_saved_usd": 42350,
    "compliance_score": 94
  },
  "classifications_by_chapter": [
    { "chapter": "85 - Electrical", "count": 312, "percentage": 36.8 },
    { "chapter": "84 - Machinery", "count": 198, "percentage": 23.4 },
    { "chapter": "39 - Plastics", "count": 145, "percentage": 17.1 },
    { "chapter": "73 - Iron/Steel", "count": 89, "percentage": 10.5 },
    { "chapter": "Other", "count": 103, "percentage": 12.2 }
  ],
  "top_trade_routes": [
    { "route": "CN → US", "shipments": 67, "value": 1450000 },
    { "route": "VN → US", "shipments": 23, "value": 340000 },
    { "route": "IN → US", "shipments": 18, "value": 280000 },
    { "route": "DE → US", "shipments": 16, "value": 270000 }
  ],
  "monthly_trend": [
    { "month": "Jan 2026", "classifications": 756, "shipments": 98 },
    { "month": "Feb 2026", "classifications": 847, "shipments": 124 }
  ],
  "ai_accuracy": {
    "overall": 94.2,
    "by_confidence": [
      { "range": "90-100%", "accuracy": 98.7, "count": 512 },
      { "range": "80-90%", "accuracy": 91.3, "count": 234 },
      { "range": "70-80%", "accuracy": 84.6, "count": 101 }
    ]
  }
}
```

---

## 8. Using This Data

### In Development (Mock API)

```typescript
// src/lib/mockData.ts
import classificationData from './data/classification.json'
import landedCostData from './data/landedCost.json'
import complianceData from './data/compliance.json'
import routeData from './data/routes.json'
import cargoData from './data/cargo.json'

export const mockApi = {
  classify: async (product: string) => {
    await delay(1500) // Simulate API latency
    return classificationData
  },
  calculateLandedCost: async (params: any) => {
    await delay(2000)
    return landedCostData
  },
  checkCompliance: async (params: any) => {
    await delay(1800)
    return complianceData
  },
  getRoutes: async (params: any) => {
    await delay(1200)
    return routeData
  },
  trackCargo: async (containerId: string) => {
    await delay(800)
    return cargoData
  }
}

// Toggle between mock and real API
export const api = process.env.NEXT_PUBLIC_USE_MOCK === 'true' ? mockApi : realApi
```

### During Demo

Set environment variable:
```bash
NEXT_PUBLIC_USE_MOCK=false  # Use real APIs
```

If API fails during demo:
```bash
NEXT_PUBLIC_USE_MOCK=true   # Switch to mock data
```

---

## 9. Demo Product Images

Keep these images ready for upload demos:

1. **Solar Charger**: Any generic solar power bank image
2. **Wireless Earbuds**: AirPods-style earbuds
3. **Stainless Steel Bottle**: Hydro Flask style
4. **Laptop Stand**: Aluminum laptop stand

These are common products with clear HS classifications for confident demo.

---

## 10. Quick Reference - HS Codes for Demo

| Product | HS Code | Confidence |
|---------|---------|------------|
| Solar Charger | 8504.40.95 | 94% |
| Wireless Earbuds | 8518.30.20 | 97% |
| Water Bottle (Steel) | 7323.93.00 | 99% |
| Laptop Stand | 8473.30.51 | 91% |
| Phone Case (Plastic) | 3926.90.99 | 96% |
| Cotton T-Shirt | 6109.10.00 | 99% |

---

**Use this data to ensure a flawless demo regardless of API status.**
