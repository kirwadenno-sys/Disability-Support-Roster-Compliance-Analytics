# Disability Support Services: Roster Compliance & Travel Logistics Analytics

## Project Overview
This end-to-end Business Analysis project addresses operational compliance and cost inefficiencies within a **Disability Support Services / Supported Independent Living (SIL)** organisation. The objective was to eliminate **SCHADS Award fatigue breaches** and prevent costly travel/transport cancellations by establishing automated data governance and monitoring between worker clearances, shift rostering, and transport logistics.

---

## Phase 1: Requirements & Agile Execution (Jira)
To structure the solution, business requirements were gathered and documented using Agile frameworks in Jira:
* **User Story:** *As a Roster Coordinator, I need to identify support workers with expired medical or NDIS screening clearances before scheduling shifts or arranging travel, so that we eliminate compliance breaches and cancellation costs.*
* **Gherkin Acceptance Criteria:**
  * **Given** a support worker's clearance status is `Expired`,
  * **When** a shift or transport booking is created,
  * **Then** the system must flag a compliance exception and compute the associated financial risk.

---

## Phase 2: Data Architecture & Relational Modelling
The data model was structured across three core operational entities, linked in a star-schema architecture:
1. **` Workers Master`:** Personnel details, roles, SCHADS levels, and `MedicalClearanceStatus`.
2. **`Roster Shifts`:** Shift records, dates, site locations, rest hours prior, and `SCHADS_BreachFlag`.
3. **`Travel Logistics`:** Transport bookings, status, exception reasons, and cancellation costs.

---

## Phase 3: Business Intelligence & DAX Implementation (Power BI)
An interactive Power BI dashboard was built to track performance against key executive metrics:

* **Total Cancelled Travel Cost:** Quantified financial loss resulting from non-compliant shift/travel arrangements (`$1,570`).
* **SCHADS Breach Rate:** Tracked the percentage of scheduled shifts violating rest-period guidelines (`28.57%`).
* **Expired Worker Shift Count:** Monitored active assignments tied to expired personnel clearances (`2`).

### Core DAX Measures Used:
```dax
-- Quantifies total financial loss from cancelled transport/travel bookings
Total Cancelled Flight Cost = CALCULATE(SUM('Travel Logistics2'[CharterFlightCost]), 'Travel Logistics2'[FlightStatus] = "Cancelled")

-- Calculates the proportion of shifts violating SCHADS fatigue rules
SCHADS Breach Rate = DIVIDE(CALCULATE(COUNT('Roster Shifts2'[ShiftID]), 'Roster Shifts2'[SCHADS_BreachFlag] = "Yes"), COUNT('Roster Shifts2'[ShiftID]), 0)

-- Counts shifts assigned to workers with expired credentials
Expired Worker Shift Count = CALCULATE(COUNT('Roster Shifts2'[ShiftID]), FILTER('Workers Master2', 'Workers Master2'[MedicalClearanceStatus] = "Expired"))
