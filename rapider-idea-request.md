<!-- Rapider Official App SOW & Specification | App #127: Shipping Label & Fulfillment Management (shipping-label-and-fulfillment-management) | Domain: COM -->

# #127 Shipping Label & Fulfillment Management (`shipping-label-and-fulfillment-management`) — Scope of Work (SOW)

- **App Number & Name:** `#127` — Shipping Label & Fulfillment Management
- **Proposed App Key:** `shipping-label-and-fulfillment-management`
- **Suite / Domain Key & Model Prefix:** `COM` (`Com*`)
- **Function Category:** 13. E-Commerce & Retail
- **Representative Product (`P1`):** ShipStation
- **Combined Feature Catalog:** [../combined-features/127-shipping-label-and-fulfillment-management-features.md](../combined-features/127-shipping-label-and-fulfillment-management-features.md)
- **Target Release Posture:** Multi-Carrier Rate Shopping, Scan-to-Print Pack Bench, Batch ZPL Thermal Label Printing, Paperless Customs & Post-Purchase Tracking Microapplication

---

## 1. Executive Outcome & Target Audience

### 1.1 Business Problem Solved
Fulfillment operations lose margin and customer trust when staff manually copy addresses into carrier portals, guess carton sizes that trigger dimensional-weight (DIM) penalties, miss cross-border HS-code electronic customs filings, or leave buyers in the dark with "Where Is My Order?" (WISMO) tickets. **Rapider Shipping Label & Fulfillment Management (`shipping-label-and-fulfillment-management`)** turns released `OpsCustomerOrder`s and completed `OpsWarehouseTask` picks into verified, rate-shopped, ZPL thermal-printed parcel shipments—automating carrier selection, paperless trade compliance, end-of-day manifests, live tracking normalization, and carrier freight-bill variance auditing.

### 1.2 Target Personas & Progressive Disclosure
- **SME Fast-Path Defaults (Merchant Founders, Small Warehouse Teams):** Activate pre-configured box presets, connect UPS/USPS/FedEx/DHL accounts in minutes, apply a single default "Cheapest Service Meeting Delivery Date" rule, batch-print 50 `4x6` thermal labels + packing slips at once, and automatically email tracking links to buyers.
- **Enterprise Progressive Disclosure (VP Logistics, Multi-Node Fulfillment Managers, Trade Compliance Analysts):** Unlock barcode scan-to-verify pack stations with USB scale capture, multi-corridor ETD/IOSS/EORI DDP customs automation, hazardous-goods holds, 500-order high-throughput ZPL batches, end-of-day carrier manifests, stalled-parcel `SupTicket` escalation, and line-level carrier invoice DIM-surcharge reconciliation.

### 1.3 Measurable Business Outcomes
- **12%–22% Reduction in Parcel Freight Spend:** Automated multi-carrier Rate Shopper (`CHEAPEST` / `MEETS_SLA_CHEAPEST`) plus cartonization box presets eliminate over-boxing DIM surcharges and sub-optimal service selection.
- **4x Faster Pack-Bench Throughput:** Barcode scan-to-verify + instant ZPL thermal label output cuts per-parcel pack time below 18 seconds with near-zero wrong-item shipments.
- **40% Fewer Inbound WISMO Support Tickets:** Real-time normalized `ComTrackingEvent` milestones and proactive delay notifications resolve delivery questions before customers contact `support`.

### 1.4 Benchmark Summary (`P1`–`P6`)
Synthesizes the Rate Shopper, If/Then automation playbooks, and 500-label ZPL batch engine of **`P1` ShipStation**, Pack & Go barcode verification and European PUDO locker support of **`P2` Sendcloud**, street-level AVS, AI lane decisioning, and bill-audit rigor of **`P3` EasyPost**, agile multi-piece & customs automation of **`P4` Shippo**, enterprise cartonization and GL freight accrual of **`P5` Oracle NetSuite Ship Central**, and seamless Sales-to-Delivery-to-Invoice ERP flow of **`P6` Odoo Delivery**.

---

## 2. In-Scope, Out-of-Scope & Suite Ownership Boundaries

### 2.1 In-Scope Capabilities
1. BYOC and postage-wallet carrier account management (`ComCarrierAccount`) with warehouse origin mapping and `PDF_4X6` / `ZPL_203DPI` / `ZPL_300DPI` format configuration.
2. Reusable carton/mailer preset library (`ComPackagePreset`), street-level Address Verification Service (AVS) with residential classification, and PUDO locker metadata persistence on `ComShipment`.
3. Live multi-carrier Rate Shopper and ordered If/Then shipping automation rules (`ComShippingRule`) for checkout quoting (`ComCart`) and back-office dispatch.
4. Barcode scan-to-verify pack bench, multi-parcel splitting, single & 500-order idempotent batch label purchasing, one-click label void/refund (`ComShippingLabel`), and end-of-day scannable `ComCarrierManifest` / USPS SCAN forms.
5. Paperless Electronic Trade Documents (ETD/PLT), HS tariff / country-of-origin validation, and DDP/DDU tax ID governance (`ComCustomsDeclaration`).
6. Normalized multi-carrier webhook tracking (`ComTrackingEvent`), proactive WISMO email/SMS triggers, stalled-parcel `SupTicket` escalation, and quoted-vs-billed carrier freight audit.

### 2.2 Out-of-Scope Capabilities (Delegated to Adjacent Rapider Apps)
- **Warehouse bin locations, wave/zone picking, cycle counts, and receiving putaway:** Owned by `warehouse` (`OpsWarehouseTask`) and `inventory` (`OpsInventoryItem`, `OpsInventoryPosition`).
- **Order capture, backorder allocation, and split-warehouse sourcing logic:** Owned by `ordermanagement` (`OpsCustomerOrder`).
- **Marketplace-specific carrier taxonomy mapping & Seller Central feed pushes:** Owned by `#126 Marketplace Seller Management` (`marketplace-seller-management`), which subscribes to `com.shipment.label_purchased`.
- **Customer-facing return eligibility rules, exchange incentives, and RMA grading:** Owned by `#130 Returns & Exchanges Management` (`returns-and-exchanges-management`), which calls `#127` to mint return postage labels.
- **Full Truckload (FTL) fleet telematics, driver HOS logs, and route dispatch:** Owned by dedicated Fleet/TMS apps in the Logistics suite.

### 2.3 Cross-Suite Ownership & Integration Contract

| Domain / App | Owns (Canonical Models) | Integration Contract with `shipping-label-and-fulfillment-management` |
| :--- | :--- | :--- |
| **`shipping-label-and-fulfillment-management` (`#127` — This App)** | `ComCarrierAccount`, `ComPackagePreset`, `ComShippingRule`, `ComShipment`, `ComShippingLabel`, `ComCustomsDeclaration`, `ComCarrierManifest`, `ComTrackingEvent` | Executes AVS, rate shopping, pack-station verification, ZPL/PDF label purchase/void, ETD customs, manifests, and parcel tracking telemetry. |
| **`ordermanagement` (`OPS` Suite)** | `OpsCustomerOrder` | Consumes released `OpsCustomerOrder` records to stage `ComShipment`s; publishes `com.shipment.dispatched` and `com.shipment.delivered` back to update order fulfillment state. |
| **`warehouse` & `inventory` (`OPS` Suite)** | `OpsWarehouseTask`, `OpsInventoryItem`, `OpsInventoryPosition` | Pack bench consumes completed pick `OpsWarehouseTask` tote IDs and reads SKU net weights/hazmat flags without mutating warehouse bin stock ledgers directly. |
| **`productcatalog` & `ecommerce` (`COM` Suite)** | `ComProduct`, `ComVariant`, `ComCart`, `ComSite` | Reads HS codes, country of origin, and unit dimensions from `ComProduct`/`ComVariant`; serves live checkout shipping rate quotes and PUDO selections to `ComCart`. |
| **`marketplace-seller-management` (`#126`)** | `ComMarketplaceOrderSync` | Emits `com.shipment.label_purchased` with carrier/service/tracking so `#126` pushes compliant tracking numbers before marketplace ship-by SLA expiry. |
| **`product-reviews-and-ratings` (`#128`)** | `ComReviewRequest` | Emits verified `com.shipment.delivered` events so `#128` triggers post-delivery review invitations only after physical parcel receipt. |
| **`returns-and-exchanges-management` (`#130`)** | `ComReturnRequest` | Exposes guarded `com.shipping.generateReturnLabel` command to mint pay-on-scan or prepaid RMA return labels (`isReturnLabel: true`). |
| **`email`, `sms` (`MKT`) & `support` (`SUP`)** | `MktEmailMessage`, `MktSmsMessage`, `SupTicket` | Triggers branded tracking milestone messages via `MKT` and opens/updates `SupTicket`s automatically on `DELIVERY_EXCEPTION` or stalled parcels. |
| **`accpayable` (`FIN` Suite)** | `FinSupplierBill` | Audits carrier freight `FinSupplierBill` line charges against `ComShippingLabel.quotedTotalAmount` to flag DIM/surcharge variances via Home `WorkItem`s. |

---

## 3. Primary Web Pages & Sidebar Navigation Architecture (12 Pages)

Every visible sidebar section strictly enforces Rapider's **Minimum 3 Visible Items Rule** (`Fulfillment Desk`: 3 items, `Tracking & Closeout`: 3 items, `Configuration & Audit`: 3 items; detail/workbench routes set `displayOnMenu: false`).

| # | Route | Page Title | Sidebar Section | Menu Visible | Key Components & UX |
| :--- | :--- | :--- | :--- | :--- | :--- |
| 1 | `dashboard` | Fulfillment Command Center | `Fulfillment Desk` | `true` (Order 1) | `countSWR`/`findSWR` KPI strip (Awaiting Shipment, Ship-Today SLA Urgent, Address/Customs Holds, In-Transit Exceptions, Avg Cost per Parcel `\$`), carrier lane on-time chart, and quick batch launcher. |
| 2 | `shipments` | Shipments Queue | `Fulfillment Desk` | `true` (Order 2) | Bulk `findSWR` shipment workbench & `<rapider-kanban>` board (`AWAITING_PACK`, `ON_HOLD_AVS`, `ON_HOLD_CUSTOMS`, `READY_TO_PRINT`, `SHIPPED`, `DELIVERED`, `EXCEPTION`), `#activeFilterTagsBar`, multi-select `Batch Rate & Print`, clickable Shipment `#` -> `shipment-detail`. |
| 3 | `pack-bench` | Scan-to-Print Pack Station | `Fulfillment Desk` | `true` (Order 3) | Barcode-first pack station: scan tote/order barcode -> scan `ComVariant` GTINs -> select `ComPackagePreset` -> capture weight -> live Rate Shopper comparison -> 1-click ZPL/PDF print. |
| 4 | `batches-and-labels` | Label Batches & Postage Ledger | `Tracking & Closeout` | `true` (Order 1) | Searchable `ComShippingLabel` ledger showing Tracking `#`, Carrier/Service, Quoted Postage (`\$`), Format (`ZPL`/`PDF`), Status (`PURCHASED`/`PRINTED`/`VOID_REQUESTED`/`REFUNDED`), reprint & one-click `Void Label`. |
| 5 | `manifests` | End-of-Day Manifests | `Tracking & Closeout` | `true` (Order 2) | `ComCarrierManifest` manager grouping un-manifested labels by warehouse + carrier; `Close & Generate SCAN Form` primary button and downloadable driver barcode manifest PDF. |
| 6 | `tracking-exceptions` | Live Tracking & Exceptions | `Tracking & Closeout` | `true` (Order 3) | Real-time `ComTrackingEvent` exception monitor (`STALLED_72H`, `DAMAGED`, `ADDRESS_UNDELIVERABLE`, `CUSTOMS_DELAY`), linked `SupTicket` badge, and AI claim/WISMO drafter. |
| 7 | `carriers` | Carrier Accounts & Wallets | `Configuration & Audit` | `true` (Order 1) | `ComCarrierAccount` directory (UPS, FedEx, DHL, USPS, DPD, Sendcloud), credential health, ZPL DPI settings, origin warehouse phone (`<rapider-phone-input>`), and wallet balance alerts. |
| 8 | `packages-and-rules` | Box Presets & Shipping Rules | `Configuration & Audit` | `true` (Order 2) | Dual-tab manager for `ComPackagePreset` (LxWxH, tare, max weight) and ordered If/Then `ComShippingRule` automation policies with live rule simulator drawer. |
| 9 | `freight-audit` | Carrier Bill Variance Audit | `Configuration & Audit` | `true` (Order 3) | Quoted `ComShippingLabel` postage vs. billed carrier invoice (`FinSupplierBill`) variance table highlighting unexpected DIM-weight, residential, and late-delivery GSR refund claims. |
| 10 | `shipment-detail` | Shipment, Rate & Customs Detail | *(Hidden)* | `false` | Complete `ComShipment` inspector: AVS diff, multi-box parcels, live carrier rate comparison drawer, `ComCustomsDeclaration` ETD editor, tracking timeline, and `Back to Shipments` state restoration. |
| 11 | `manifest-detail` | Carrier Manifest Inspector | *(Hidden)* | `false` | Detailed `ComCarrierManifest` view listing every included tracking number, total parcel/pallet count, driver pickup timestamp, and scannable master barcode. |
| 12 | `customs-workbench` | Cross-Border Customs Inspector | *(Hidden)* | `false` | Focused `ComCustomsDeclaration` remediation view for `ON_HOLD_CUSTOMS` international orders (HS code lookup, EORI/IOSS/ITN validation, DDP/DDU toggle, CN23 preview). |

**UI Standards Compliance:**
- All initial data loads invoke `findSWR` / `countSWR` with clean filters (zero manual `isDeleted` where clauses) and defensive client `item.isDeleted !== true` filtering.
- `#activeFilterTagsBar` with escaped chips and `Clear all` appears whenever search or filter pills are active across all tabular/kanban views.
- Tables never render a `View` button in `Actions`; Shipment `#`, Tracking `#`, Carrier Name, and Manifest `#` navigate directly to detail views; row actions strictly use icon+text `Edit` and icon-only `Delete`.
- Primary/danger buttons enforce `#ffffff !important` white text; `<rapider-phone-input>` normalizes shipper and recipient courier SMS numbers; native browser `alert`/`confirm`/`prompt` calls are strictly prohibited.

---

## 4. Canonical Data Models (`Com*` — 8 Models)

All models omit `projectId`, use platform UUIDs (`00000000-xxxx-xxxx-xxxx-xxxxxxxxxxxx` in deterministic seeds), maintain optimistic `version`, and store all monetary, weight, and dimensional amounts as `Decimal128`.

| Model Name | Key Fields & Types | Relationships & External Refs | Invariants & Lifecycle |
| :--- | :--- | :--- | :--- |
| **`ComCarrierAccount`** | `id` (UUID), `carrierCode` (Enum: `UPS`, `FEDEX`, `DHL_EXPRESS`, `USPS`, `ROYAL_MAIL`, `DPD`, `GLS`, `CUSTOM`), `accountName` (String), `billingMode` (Enum: `BYOC_CONTRACT`, `PREPAID_WALLET`), `originWarehouseId` (UUID), `shipperPhone` (E.164 String), `defaultLabelFormat` (Enum: `PDF_4X6`, `ZPL_203DPI`, `ZPL_300DPI`), `supportsPaperlessEtd` (Boolean), `walletBalance` (`Decimal128`, nullable), `currencyCode` (String), `status` (Enum: `ACTIVE`, `CREDENTIAL_ERROR`, `INACTIVE`), `version` (Int) | `originWarehouseId` -> warehouse location in `warehouse` | API keys/meter numbers stored strictly in platform secret vault reference; `walletBalance >= 0` for `PREPAID_WALLET`. |
| **`ComPackagePreset`** | `id` (UUID), `presetCode` (String), `name` (String), `packageType` (Enum: `BOX`, `POLY_MAILER`, `PADDED_ENVELOPE`, `TUBE`, `PALLET`), `lengthCm` (`Decimal128`), `widthCm` (`Decimal128`), `heightCm` (`Decimal128`), `tareWeightKg` (`Decimal128`), `maxPayloadWeightKg` (`Decimal128`), `isCarrierBranded` (Boolean), `restrictedCarrierCode` (String, nullable), `isActive` (Boolean), `version` (Int) | Referenced by `ComShipment` and `ComShippingRule` | `lengthCm > 0`, `widthCm > 0`, `heightCm > 0`, `maxPayloadWeightKg > 0`; carrier-branded boxes (e.g., USPS Flat Rate) can only rate-shop against `restrictedCarrierCode`. |
| **`ComShippingRule`** | `id` (UUID), `ruleName` (String), `priorityOrder` (Int), `conditionsJson` (JSON: weight band, order value, destination country/zone, channel, hazmat tag), `rateStrategy` (Enum: `SPECIFIC_SERVICE`, `CHEAPEST`, `FASTEST`, `MEETS_SLA_CHEAPEST`), `targetCarrierAccountId` (UUID, nullable), `targetServiceCode` (String, nullable), `defaultPackagePresetId` (UUID, nullable), `signatureMode` (Enum: `NONE`, `STANDARD`, `ADULT_21`), `autoInsureAboveAmount` (`Decimal128`, nullable), `isActive` (Boolean), `version` (Int) | `targetCarrierAccountId` -> `ComCarrierAccount.id`, `defaultPackagePresetId` -> `ComPackagePreset.id` | Unique `priorityOrder` per active rule set; deterministic first-match or composite evaluation trace logged on `ComShipment`. |
| **`ComShipment`** | `id` (UUID), `shipmentNumber` (String), `customerOrderId` (UUID), `warehouseTaskId` (UUID, nullable), `originWarehouseId` (UUID), `packagePresetId` (UUID, nullable), `parcelSequence` (Int, `1..N`), `totalParcelsInOrder` (Int), `recipientName` (String), `recipientPhone` (E.164 String), `shippingAddressJson` (JSON), `avsStatus` (Enum: `VERIFIED`, `AUTO_CORRECTED`, `UNVERIFIED_HOLD`), `isResidential` (Boolean), `pudoPointId` (String, nullable), `grossWeightKg` (`Decimal128`), `dimWeightKg` (`Decimal128`), `estimatedCo2Kg` (`Decimal128`, nullable), `deliverByDeadlineAt` (DateTime, nullable), `shipmentState` (Enum: `AWAITING_PACK`, `ON_HOLD_AVS`, `ON_HOLD_CUSTOMS`, `READY_TO_PRINT`, `SHIPPED`, `IN_TRANSIT`, `DELIVERED`, `EXCEPTION`, `CANCELLED`), `version` (Int) | `customerOrderId` -> `OpsCustomerOrder.id`, `warehouseTaskId` -> `OpsWarehouseTask.id`, `packagePresetId` -> `ComPackagePreset.id` | Cannot purchase outbound label while `shipmentState` is `ON_HOLD_AVS` or `ON_HOLD_CUSTOMS` without explicit human override. |
| **`ComShippingLabel`** | `id` (UUID), `shipmentId` (UUID), `carrierAccountId` (UUID), `batchRunId` (String, nullable), `isReturnLabel` (Boolean), `serviceCode` (String), `serviceName` (String), `trackingNumber` (String), `trackingUrl` (String), `labelFormat` (Enum: `PDF_4X6`, `ZPL_203DPI`, `ZPL_300DPI`), `labelFileId` (UUID), `currencyCode` (String), `basePostageAmount` (`Decimal128`), `surchargesAmount` (`Decimal128`), `insuranceCostAmount` (`Decimal128`), `quotedTotalAmount` (`Decimal128`), `billedActualAmount` (`Decimal128`, nullable), `billingVarianceAmount` (`Decimal128`, nullable), `manifestId` (UUID, nullable), `labelStatus` (Enum: `PURCHASED`, `PRINTED`, `MANIFESTED`, `VOID_REQUESTED`, `VOIDED_REFUNDED`), `purchasedAt` (DateTime), `version` (Int) | `shipmentId` -> `ComShipment.id`, `carrierAccountId` -> `ComCarrierAccount.id`, `manifestId` -> `ComCarrierManifest.id`, `labelFileId` -> Rapider Files | At most one non-voided outbound label (`isReturnLabel = false`) may exist per `ComShipment` parcel at any time; financial amounts are immutable once `PURCHASED`. |
| **`ComCustomsDeclaration`** | `id` (UUID), `shipmentId` (UUID), `incoterms` (Enum: `DDP`, `DAP_DDU`), `contentsType` (Enum: `MERCHANDISE`, `RETURNED_GOODS`, `SAMPLE`, `GIFT`), `nonDeliveryOption` (Enum: `RETURN`, `ABANDON`), `exporterTaxId` (String: EORI/VAT), `iossOrTaxSchemeNumber` (String, nullable), `aesItnNumber` (String, nullable), `customsLinesJson` (JSON: `variantId`, `hsCode`, `originCountry`, `qty`, `unitValue`, `netWeightKg`), `totalDeclaredValue` (`Decimal128`), `currencyCode` (String), `paperlessTransmitted` (Boolean), `commercialInvoiceFileId` (UUID, nullable), `complianceStatus` (Enum: `INCOMPLETE_HOLD`, `READY`, `TRANSMITTED`), `version` (Int) | `shipmentId` -> `ComShipment.id`; line `variantId` -> `ComVariant.id` | Mandatory for cross-border customs zones; every line requires a valid 6–10 digit `hsCode` and ISO-2 `originCountry` before `READY`. |
| **`ComCarrierManifest`** | `id` (UUID), `manifestNumber` (String), `carrierAccountId` (UUID), `originWarehouseId` (UUID), `manifestDate` (Date), `includedLabelCount` (Int), `totalGrossWeightKg` (`Decimal128`), `scanFormBarcode` (String), `manifestPdfFileId` (UUID), `status` (Enum: `OPEN`, `CLOSED_SUBMITTED`, `DRIVER_SCANNED`), `closedAt` (DateTime), `version` (Int) | `carrierAccountId` -> `ComCarrierAccount.id` | Immutable once `CLOSED_SUBMITTED`; prevents voiding labels already inducted via `DRIVER_SCANNED`. |
| **`ComTrackingEvent`** | `id` (UUID), `shippingLabelId` (UUID), `shipmentId` (UUID), `normalizedStatus` (Enum: `LABEL_CREATED`, `ACCEPTED_AT_ORIGIN`, `IN_TRANSIT`, `CUSTOMS_CLEARANCE`, `OUT_FOR_DELIVERY`, `DELIVERED`, `DELIVERY_EXCEPTION`, `RETURNED_TO_SENDER`), `rawCarrierCode` (String), `description` (String), `scanCity` (String), `scanCountry` (String), `occurredAt` (DateTime), `linkedSupportTicketId` (UUID, nullable), `version` (Int) | `shippingLabelId` -> `ComShippingLabel.id`, `shipmentId` -> `ComShipment.id`, `linkedSupportTicketId` -> `SupTicket.id` | Append-only event log; deduplicated on `(shippingLabelId, rawCarrierCode, occurredAt)`. |

---

## 5. Core End-to-End User Journeys (6 Journeys)

### Journey 1: SME Batch Rate-Shop & 100-Order Thermal ZPL Print Run
1. Fulfillment Coordinator opens `#/shipments`, filters `Status: "READY_TO_PRINT"`, selects 100 domestic orders (`< 2 kg`), and clicks `Batch Rate & Print`.
2. Automated `ComShippingRule` runs Rate Shopper (`MEETS_SLA_CHEAPEST`) across USPS Ground Advantage, UPS Ground, and FedEx Home Delivery, picking the lowest landed cost (`\$5.42` avg) that meets each order's delivery promise.
3. Batch job idempotently purchases 100 `ComShippingLabel`s, streams a combined `ZPL_203DPI` thermal file + packing slips, updates `OpsCustomerOrder` statuses, and fires `com.shipment.label_purchased` so `#126 Marketplace Seller Management` and `email` notify marketplaces and buyers instantly.

### Journey 2: Barcode Scan-to-Verify Pack Bench Execution
1. Packer at `#/pack-bench` scans pick-tote barcode `TOTE-8841` (`OpsWarehouseTask`), loading `ComShipment` `SHP-2026-0912` containing 2 units of `SKU-THERMOS-BLK`.
2. Packer scans both item UPC barcodes (turning line verification badges green), places items in suggested box `BOX-S-20x15x10`, captures USB scale gross weight `1.14 kg`, and clicks `Purchase & Print ZPL`—printing the thermal label in under 2 seconds.

### Journey 3: Cross-Border DDP Customs Hold -> Remediation -> Paperless ETD Dispatch
1. An international order from the US to Germany (`€140.00` value) arrives missing an HS tariff code on one new accessory SKU, placing `ComShipment` in `ON_HOLD_CUSTOMS` and creating a Rapider Home `WorkItem`.
2. Logistics Specialist clicks the `WorkItem` link to `#/customs-workbench?shipmentId=...`, enters verified HS code `4202.92`, confirms EU IOSS number and `DDP` incoterm on `ComCustomsDeclaration`, and clicks `Clear Hold & Rate Shop`.
3. DHL Express Worldwide label prints with paperless `ETD` transmitted electronically—requiring zero physical customs pouches.

### Journey 4: Mistaken Weight Void & Re-Label Without Double Billing
1. Immediately after printing label `1Z999AA1099887766` (`\$9.10` for `1.0 kg`), packer realizes an extra item belongs in the box (`2.8 kg` total).
2. Packer opens `#/batches-and-labels`, clicks `Void Label` on the un-scanned label (confirmed via custom modal with `#ffffff !important` danger button), transitioning it to `VOID_REQUESTED` / `VOIDED_REFUNDED`, updates weight to `2.8 kg`, and purchases a fresh `ComShippingLabel` cleanly.

### Journey 5: Stalled-in-Transit Exception -> Proactive `SupTicket` & Buyer Care
1. Carrier webhook ingests a `DELIVERY_EXCEPTION` (`Damaged barcode — held at hub`) onto `ComTrackingEvent` for `SHP-2026-0774`.
2. Workflow transitions `ComShipment` to `EXCEPTION`, creates a high-priority `SupTicket` in `support` linked to `CrmContact`, and opens a warehouse Home `WorkItem` so CS can dispatch a replacement order before the customer ever complains.

### Journey 6: End-of-Day Manifest Closeout & Carrier Freight Variance Audit
1. At 17:00, Warehouse Lead opens `#/manifests`, selects `USPS — East Coast DC (248 parcels)`, and clicks `Close & Generate SCAN Form`, producing a single master barcode sheet scanned by the pickup driver.
2. At month-end, `#/freight-audit` compares UPS invoice `FinSupplierBill` lines against `ComShippingLabel.quotedTotalAmount`, flagging 6 parcels billed `+\$14.50` residential/DIM adjustments and raising a `WorkItem` for dispute/box-preset tuning.

---

## 6. Consolidated Feature Priorities & Work Packages (`P0`, `P1`, `P2`)

### 6.1 `P0` Foundation Work Packages (Must-Have Launch Core)
- **WP-P0-1 — Carrier Accounts, AVS & Box Presets (`SLF-001`, `SLF-002`, `SLF-003`):** `ComCarrierAccount` vault-backed connections, street-level AVS + residential detection, `<rapider-phone-input>` E.164 normalization, and `ComPackagePreset` catalog.
- **WP-P0-2 — Live Rate Shopper & If/Then Rules (`SLF-005`, `SLF-006`):** Real-time multi-carrier rate comparison (`CHEAPEST`, `FASTEST`, `MEETS_SLA_CHEAPEST`) and deterministic `ComShippingRule` evaluation engine.
- **WP-P0-3 — Pack Bench, Batch ZPL/PDF Printing, Voiding & Manifests (`SLF-011`, `SLF-012`, `SLF-013`, `SLF-014`):** Barcode scan-to-verify pack station, 500-label idempotent batch purchasing, one-click void/refund, and `ComCarrierManifest` / SCAN closeout.
- **WP-P0-4 — Paperless ETD Customs & Normalized Tracking (`SLF-016`, `SLF-019`, `SLF-020`):** `ComCustomsDeclaration` ETD/CN22/Commercial Invoice generation, `ComTrackingEvent` webhook normalizer, and branded customer tracking notifications.

### 6.2 `P1` Scale & Enterprise Governance Work Packages
- **WP-P1-1 — Checkout Live Quoting, PUDO Lockers & Multi-Parcel Splitting (`SLF-004`, `SLF-007`, `SLF-008`, `SLF-009`):** Sub-400ms `ComCart` rate API, PUDO locker ID persistence, multi-box `1..N` splitting/order combining, and signature/insurance rules.
- **WP-P1-2 — DDP/IOSS Compliance Holds & Return Label API (`SLF-015`, `SLF-017`, `SLF-018`):** Cross-border IOSS/EORI/ITN automation, missing-HS-code `WorkItem` holds, and pay-on-scan return label minting for `#130`.
- **WP-P1-3 — Exception Escalation, Delivered Review Trigger & Bill Audit (`SLF-021`, `SLF-022`, `SLF-023`):** Stalled/exception `SupTicket` creation, `com.shipment.delivered` handoff to `#128`, and carrier `FinSupplierBill` variance auditing.

### 6.3 `P2` Adjacent & Grounded AI Work Packages
- **WP-P2-1 — AI Lane Performance, Cartonization & CO2 Telemetry (`SLF-010`, `SLF-024`):** Historical carrier lane on-time advisor, DIM-waste detection, and per-parcel `kgCO2e` calculation.
- **WP-P2-2 — Grounded AI Exception & Carrier Claim Copilot (`SLF-025`):** Drafts customer WISMO updates and carrier lost/damaged claim packets in `projectChannelMessage` grounded in `ComTrackingEvent` and `ComShippingLabel` records.

---

## 7. Roles, Permissions & Field-Level Governance

| Role | Permitted Actions | Restricted / Field-Level Boundaries |
| :--- | :--- | :--- |
| **Pack-Bench Associate** | Scan totes/SKUs in `#/pack-bench`, capture weights, print single/batch ZPL labels, and close daily `ComCarrierManifest`s. | Cannot modify `ComCarrierAccount` credentials, edit `ComShippingRule` thresholds, or override customs compliance holds. |
| **Fulfillment / Shipping Supervisor** | Manage `ComPackagePreset`s, resolve `ON_HOLD_AVS` / `ON_HOLD_CUSTOMS` `WorkItem`s, split/combine shipments, void unused labels, and triage `ComTrackingEvent` exceptions. | Cannot top up carrier wallets above approved limits or approve `FinSupplierBill` variances in `FIN`. |
| **Logistics & Trade Manager** | Configure `ComCarrierAccount`s, author `ComShippingRule`s, maintain EORI/IOSS/ITN export tax IDs, and review freight variance audits. | Cannot alter historical `PURCHASED` postage costs on `ComShippingLabel` or mutate closed `ComCarrierManifest`s. |
| **Customer Support Agent (`SUP`)** | Read-only view of `ComShipment`, `ComShippingLabel` tracking links, and `ComTrackingEvent` timelines; request replacement/return labels via `#130`. | Cannot batch-purchase outbound warehouse labels or edit carrier contract configurations. |

---

## 8. Technical APIs, Guarded Commands, Events & `requiredWorkflows`

### 8.1 Guarded Domain Commands
All commands enforce `idempotencyKey`, `correlationId`, and `expectedVersion`:
- `com.shipping.quoteCarrierRates` — Computes live rate-shopped quotes across eligible `ComCarrierAccount`s for checkout (`ComCart`) or `ComShipment`.
- `com.shipping.purchaseLabelsBatch` — Idempotently purchases 1..500 `ComShippingLabel`s, transmits ETD `ComCustomsDeclaration`s, and prevents duplicate postage charges on network retry.
- `com.shipping.voidShippingLabel` — Requests carrier label cancellation/refund prior to induction and unlocks `ComShipment` for re-labeling.
- `com.shipping.generateReturnLabel` — Invoked by `#130` to mint a pay-on-scan or prepaid return `ComShippingLabel` (`isReturnLabel: true`).
- `com.shipping.closeCarrierManifest` — Seals eligible daily labels into an immutable `ComCarrierManifest` and generates the master SCAN barcode.

### 8.2 Domain Events Published & Consumed (Kafka)
- **Published:** `com.shipment.staged`, `com.shipment.hold_placed`, `com.shipment.label_purchased`, `com.shipment.dispatched`, `com.shipment.in_transit`, `com.shipment.delivered`, `com.shipment.exception_raised`, `com.shipment.label_voided`.
- **Consumed:** `ops.order.released_for_fulfillment` (`ordermanagement`), `ops.warehouse.pick_task_completed` (`warehouse`), `com.returns.return_label_requested` (`#130`), `fin.ap.carrier_bill_imported` (`accpayable`).

### 8.3 `requiredWorkflows` (Declared in `app.manifest.json`)
1. `wf-shipping-stage-and-rule-apply` — Consumes released `OpsCustomerOrder`s, runs AVS + HS-code checks, applies `ComShippingRule`s, and places invalid records on Home `WorkItem` hold.
2. `wf-shipping-batch-label-processor` — Executes parallel, rate-limited, idempotent carrier label purchases and merges ZPL/PDF print streams.
3. `wf-shipping-tracking-watchdog` — Processes carrier webhooks plus scheduled 72-hour stalled-scan sweeps, opening `SupTicket`s and firing `MKT` milestone notifications.
4. `wf-shipping-carrier-bill-auditor` — Matches incoming `FinSupplierBill` tracking lines against `ComShippingLabel.quotedTotalAmount` and flags DIM/surcharge overcharges.

### 8.4 Grounded Rapider AI Governance
- AI lane & cartonization advisor cites empirical workspace `ComTrackingEvent` transit durations and `ComShippingLabel` DIM variances—never fabricating carrier tariffs.
- AI WISMO & claim copilot operates in `projectChannelMessage` threads, respects RBAC, and **never** purchases postage, voids labels, or transmits customs declarations without explicit user confirmation.

---

## 9. Security, Privacy, Compliance & Non-Functional Requirements

- **Export & Customs Audit Retention:** Electronic `ComCustomsDeclaration` payloads, HS tariff snapshots, commercial invoices, and `ComCarrierManifest` closeouts are retained immutably for 7 years to satisfy customs/export audit mandates (CBP, HMRC, EU Customs).
- **Recipient PII Protection (GDPR / CCPA):** Recipient street addresses and E.164 phone numbers are encrypted at rest, transmitted strictly to the selected carrier for parcel delivery, and honor workspace GDPR erasure/anonymization workflows post-retention.
- **Carrier Secret Isolation:** Carrier OAuth tokens, meter IDs, and API keys reside exclusively in the Rapider secret vault—never exposed in client state or `ComCarrierAccount` JSON responses.
- **Performance, Accessibility & Responsive Design:** Pack-bench scan-to-print latency `< 1.5s` (excluding external carrier API RTT); WCAG 2.2 AA compliant keyboard/scanner workflows (`#ffffff !important` primary/danger buttons); responsive across `390px` handheld scanners, `768px` tablets, and `1440px` pack-station monitors.

---

## 10. Deterministic Acceptance Criteria (14 Testable Criteria)

1. **Sidebar Section Threshold:** All 3 visible sidebar sections (`Fulfillment Desk`, `Tracking & Closeout`, `Configuration & Audit`) contain >= 3 visible items (`3, 3, 3`), while `shipment-detail`, `manifest-detail`, and `customs-workbench` set `displayOnMenu: false`.
2. **SWR & Clean Query Compliance:** Every page mount uses `findSWR` / `countSWR` without manual `isDeleted` API query filters and filters `item.isDeleted !== true` defensively in memory.
3. **Active Filter Tags & Clear All:** Searching or filtering on `shipments`, `batches-and-labels`, `manifests`, `tracking-exceptions`, `carriers`, or `freight-audit` displays `#activeFilterTagsBar` with escaped chips and functional `Clear all`.
4. **Table Action & Click-Through Standard:** Zero `View` buttons appear in `Actions`; Shipment `#`, Tracking `#`, and Manifest `#` navigate to detail routes and `Back` preserves filter state; row actions strictly use icon+text `Edit` and icon-only `Delete`.
5. **Drawer Lifecycle & `finally` Reset:** Saving/cancelling carrier, box preset, shipping rule, or customs drawers auto-closes the drawer, resets button state inside `finally`, invalidates SWR caches, and updates tables/kanban optimistically.
6. **AVS & Customs Hold Gate:** Attempting to purchase an outbound label for a `ComShipment` in `ON_HOLD_AVS` or `ON_HOLD_CUSTOMS` (missing HS code on international lane) deterministically blocks purchase until remediated or explicitly overridden.
7. **Idempotent Single-Outbound-Label Invariant:** Re-submitting `com.shipping.purchaseLabelsBatch` with the same `idempotencyKey` never purchases a second carrier label or double-debits wallet balance.
8. **Void-Before-Re-Label Lifecycle:** Voiding an un-manifested `ComShippingLabel` transitions it to `VOID_REQUESTED` / `VOIDED_REFUNDED` and allows purchasing a replacement outbound label on the same `ComShipment`; labels on a `DRIVER_SCANNED` `ComCarrierManifest` reject voiding.
9. **Marketplace & Review Event Handoff:** Purchasing a label emits `com.shipment.label_purchased` (consumed by `#126`), and ingesting a `DELIVERED` scan emits `com.shipment.delivered` (consumed by `#128`).
10. **Exception-to-Support Escalation:** Ingesting a `DELIVERY_EXCEPTION` `ComTrackingEvent` transitions `ComShipment` to `EXCEPTION` and links a created/updated `SupTicket`.
11. **Exact `Decimal128` Freight Audit Math:** `billingVarianceAmount` equals exact `Decimal128` `billedActualAmount - quotedTotalAmount`, raising a Home `WorkItem` when variance exceeds tolerance (e.g., `> \$2.00`).
12. **Custom Elements & Dialog Ban:** `<rapider-kanban>` powers the shipment lifecycle board, `<rapider-phone-input>` handles shipper/recipient phone fields, primary/danger buttons enforce `#ffffff !important` white text, and zero `window.alert`/`confirm`/`prompt` calls exist.
13. **Dark & Light Theme Compliance:** All 12 pages pass `validate_theme` contrast checks in both dark and light modes.
14. **6-Pillar E2E Expectations Coverage:** `e2e/expectations.json` verifies all clickable actions, `findSWR`/`countSWR` payloads, click-through navigation, theme checks, form mutation payloads, and zero runtime errors.
