# Date & Time Processing Documentation

## Overview

The date and time processing functionality in `svcsim01` handles delivery date calculations, week/year determinations, and date validations for manufacturing orders. This system manages date-related operations for order scheduling, delivery planning, and production timeline management.

## Core Date Processing Routines

### Week Calculation (`imposta-sett`)
Calculates ISO format week and year values for delivery dates:
- **Input**: Delivery date components (`msv10-cons-aa`, `msv10-cons-mm`, `msv10-cons-gg`)
- **Process**: Uses `g0data` function with parameter 19 for ISO week calculation
- **Output**: Sets week values in `f140-set-aa-cons-a` and `f140-set-cons-a`
- **Integration**: Called during order acceptance processing (function 10)

### Month Visualization Preparation (`prep-mesi-visual`)
Prepares 8-month visualization data for delivery scheduling:
- **Process**: Calculates consecutive months starting from delivery date
- **Data**: Populates arrays `rsv20-cons-aa-vid`, `rsv20-cons-mm-vid`, `rsv20-cons-gg-vid`
- **Usage**: Supports production planning displays and capacity management

### Starting Month Calculation (`imp-part-mese-visual`)
Determines the starting month for 8-month visualization period:
- **Logic**: Handles year transitions and month calculations
- **Variables**: Uses `com-mm-cons`, `com-aa-cons`, `com-gg-cons`
- **Purpose**: Establishes baseline for production scheduling displays

### Delivery Date Recalculation (`ricalcola-data-cons`)
Recalculates delivery dates when modifications occur:
- **Function**: Uses `g0data` with parameter 5 for date arithmetic
- **Context**: Applied when order modifications affect delivery schedules
- **Data Structure**: Processes through `param-g0data` structure

### Hot/Cold Processing Determination (`calcola-caldo-freddo`)
Determines processing type based on date and product characteristics:
- **Method**: Table lookup using `f010-tipo = "1"` and `f010-n-tab = "0004"`
- **Result**: Sets processing flag in `f140-cal-fre`
- **Application**: Used for production planning and resource allocation

## Date Data Structures

### System Date Capture
```cobol
01 datadelgiorno.
   02 anno                    pic 9(02).
   02 mese                    pic 9(02).
   02 giorno                  pic 9(02).
```
Captures current system date for processing operations.

### Extended Date Format
```cobol
01 data-day.
   02 year                    pic 9(04)   comp.
   02 month                   pic 9(02).
   02 to-day                  pic 9(02).
```
Provides 4-digit year format for year 2000 compliance.

### Delivery Date Components
```cobol
01 com-data-cons.
   02 com-aa-cons             pic 9999.
   02 com-mm-cons             pic 99.
   02 com-gg-cons             pic 99.
```
Working storage for delivery date calculations and manipulations.

## Date Validation and Processing

### Year 2000 Handling
- **Century Logic**: Automatic century determination based on 2-digit year values
- **Rule**: Years < 80 assigned to 21st century (20xx), others to 20th century (19xx)
- **Implementation**: Applied during system date acceptance and processing

### Month Table Processing
- **Structure**: 48-character string containing month data (`tab-mesi`)
- **Format**: Each month entry contains month number and days count
- **Usage**: Supports date arithmetic and validation operations

### Date Format Conversions
- **System Integration**: Accepts dates from system in various formats
- **Standardization**: Converts to consistent internal format for processing
- **Validation**: Ensures date integrity throughout processing chain

## Integration Points

### Transaction Processing Integration
- **Function 10**: Week calculation during order acceptance
- **Function 20/21**: Date preparation for production scheduling
- **Function 25**: Date handling for order modifications

### Database Updates
- **F140 Records**: Updates delivery dates and week calculations
- **F132 Records**: Modifies delivery dates for order changes
- **Temporal Tracking**: Maintains date audit trails for order processing

### External System Interactions
- **g0data Calls**: Leverages external date calculation services
- **Parameter Passing**: Uses `param-g0data` structure for date operations
- **Error Handling**: Manages date calculation failures and invalid dates

## Error Handling

### Date Calculation Errors
- **g0data Failures**: Handles external date service errors
- **Invalid Dates**: Manages out-of-range or malformed date values
- **System Integration**: Provides error messages for date-related failures

### Data Consistency Checks
- **Date Sequence Validation**: Ensures logical date progressions
- **Cross-Reference Verification**: Validates dates against related records
- **Business Rule Enforcement**: Applies date-based business constraints

## Processing Context

### Order Management
Date processing supports order lifecycle management from initial entry through delivery scheduling and modification handling.

### Production Planning
Date calculations enable capacity planning, resource allocation, and production timeline management across multiple manufacturing facilities.

### Delivery Coordination
Week calculations and date formatting support delivery scheduling and customer communication requirements.