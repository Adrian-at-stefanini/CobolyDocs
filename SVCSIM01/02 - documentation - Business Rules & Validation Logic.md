# Business Rules & Validation Logic

## Transaction Processing Rules

The system enforces specific business rules based on transaction codes that determine how orders are processed:

**Order Request Processing (Transaction 01)**
- Only accepts orders in state "1" (pending acceptance)
- Validates plant and line combinations before processing
- Filters orders based on plant type for Romania operations (SIL/SIT lines)
- Distinguishes between base orders and modifications based on document structure

**Position Acceptance Processing (Transaction 06)**
- Retrieves positions ready for acceptance from confirmed orders
- Excludes positions in state "4" (completed) from processing
- Validates position status and delivery dates
- Checks plant-specific processing requirements

**Simulation Acceptance (Transaction 10)**
- Requires positions to be in state "1" before acceptance
- Validates delivery date consistency across production areas
- Transitions accepted positions to state "2" (in production) or "3" (completed)
- Updates production scheduling and capacity allocation

## Order Status Validation Rules

**State Transition Controls**
- State "1": Orders available for acceptance
- State "2": Orders in production planning
- State "3": Orders completed and ready for delivery
- State "4": Orders fully processed
- Prevents processing of orders not in the correct state for each transaction

**Acceptance Criteria**
- Validates that orders have not been previously accepted
- Checks for conflicting order modifications
- Ensures delivery dates are realistic and achievable
- Verifies production capacity availability

## Product and Manufacturing Validation

**Steel Type Validation**
- Validates steel grades against plant capabilities using table "1" "2916"
- Checks for critical steel types requiring special handling
- Ensures steel specifications match production line requirements
- Validates steel grade compatibility with customer specifications

**Dimension and Specification Validation**
- Validates product dimensions against manufacturing capabilities
- Checks caliber and train combinations for feasibility
- Ensures product specifications match order requirements
- Validates dimension compatibility with production equipment

**Plant and Line Validation**
- Enforces plant-specific processing rules for stations: DA, AR, RO, CV, SA, PI, ID, AS, KZ, UK, CN, AE
- Validates line-specific capabilities: FTM, FAS, FAP, ACC, SIL, SIT, etc.
- Ensures product types are compatible with selected production lines
- Checks equipment availability and capability

## Capacity and Production Planning Rules

**Production Capacity Validation**
- Calculates required production hours based on order quantities
- Validates available capacity against production schedules
- Ensures delivery commitments can be met within capacity constraints
- Checks for production bottlenecks and resource conflicts

**Delivery Date Validation**
- Validates requested delivery dates against production schedules
- Ensures adequate lead time for manufacturing processes
- Checks for delivery date conflicts with existing commitments
- Validates week and date calculations for production planning

**Unit of Measure Validation**
- Accepts only valid units: NR (pieces), PZ (pieces), MT (meters), KG (kilograms)
- Converts between different measurement units as required
- Validates quantity calculations based on product specifications
- Ensures measurement consistency across production areas

## Quality and Specification Controls

**Product Type Validation**
- Validates product categories: tubes, bars, poles, cylinders, curves, accessories
- Ensures product specifications match manufacturing capabilities
- Checks for special handling requirements
- Validates product classification accuracy

**Quality Standards Validation**
- Validates quality requirements against production capabilities
- Checks for special testing or certification requirements
- Ensures compliance with customer quality specifications
- Validates marking and packaging requirements

## Error Handling and Business Constraints

**Data Consistency Rules**
- Validates date consistency across production areas
- Ensures order data completeness before processing
- Checks for conflicting order modifications
- Validates customer and product information accuracy

**Business Constraint Enforcement**
- Prevents processing of incomplete or invalid orders
- Enforces minimum and maximum quantity limits
- Validates customer credit and delivery terms
- Ensures compliance with business policies and procedures

**Simulation Control**
- Determines whether to run full production simulation based on table "9" "0251"
- Controls simulation behavior for different plants and production lines
- Manages simulation data accuracy and reliability
- Validates simulation results before acceptance

The system maintains strict validation controls to ensure only valid, feasible orders proceed through the production planning process, protecting both manufacturing efficiency and customer satisfaction.