# File Definitions & Data Structures

## File Overview

The SVCSIM01 program manages 13 primary data files for production simulation and order acceptance processing:

| File | Purpose | Library |
|------|---------|---------|
| dfaf281 | Productivity data by sub-area | accdict.reclib |
| dfaf282 | Characteristics by sub-area | accdict.reclib |
| dfaf283 | Sub-area to macro mapping | accdict.reclib |
| dfaf286 | Train caliber by station | accdict.reclib |
| dfcf140 | Positions to accept/accepted | accdict.reclib |
| dfcfs90 | Position transmission data | accdict.reclib |
| dfmtabel | System tables | clidict.reclib |
| dfaf091 | Transmission for local tandems | accdict.reclib |
| dfof122 | Order data | acqdict.reclib |
| dfmf132 | Order modifications | acqdict.reclib |
| dfof121 | Order headers | acqdict.reclib |
| dfmf131 | Order modification headers | acqdict.reclib |
| dfsaree | Area control data | cadict.reclib |

## Detailed File Definitions

### Production Data Files

**dfaf281 (Productivity)**
- Label records: omitted
- Copybook: `draf281`
- Contains productivity values per sub-area for capacity calculations

**dfaf282 (Characteristics)**
- Label records: omitted  
- Copybook: `draf282`
- Stores sub-area characteristics and configuration data

**dfaf283 (Sub-area Mapping)**
- Label records: omitted
- Copybook: `draf283`
- Maps sub-areas to macro production cycles

**dfaf286 (Train Caliber)**
- Label records: omitted
- Copybook: `draf286`
- Defines train and caliber combinations by station and line

### Position Management Files

**dfcf140 (Positions)**
- Label records: omitted
- Copybook: `drcf140`
- Central file for order positions requiring acceptance or already accepted
- Contains delivery dates, quantities, and production specifications

**dfcfs90 (Transmission)**
- Label records: omitted
- Copybook: `drcfs90`
- Handles position data transmission between systems

### System Configuration

**dfmtabel (Tables)**
- Label records: omitted
- Copybook: `drmf010`
- Additional structure: `rec-10250` with 8 occurrences of `ele-acc`
- Contains system configuration tables and lookup data

### Transmission Files

**dfaf091 (Local Tandems)**
- Label records: omitted
- Record size: 1 to 2003 characters
- Copybooks: `testa-f091`, `ft91-drcf140`
- Manages transmission data for ANT/RIT local tandem operations

### Order Processing Files

**dfof122 (Orders)**
- Label records: omitted
- Copybook: `drof122`
- Base order information and specifications

**dfmf132 (Order Modifications)**
- Label records: omitted
- Copybook: `drmf132`
- Tracks modifications to existing orders

**dfof121 (Order Headers)**
- Label records: omitted
- Copybook: `drof121`
- Order header information

**dfmf131 (Modification Headers)**
- Label records: omitted
- Copybook: `drmf131`
- Header information for order modifications

### Control Files

**dfsaree (Area Control)**
- Label records: omitted
- Copybook: `drsaree`
- Controls active production cycles by area

## Message Structures

### Input Messages
**message-in**
- Record size: 1 to 8000 characters
- Structure: `com-mscpc001`
- Includes request headers and multiple message types:
  - `com-testa-rq` (request header)
  - `mscsv001`, `mscsv006`, `mscsv010`, `mscsv020`, `mscsv011` (request messages)
  - `coa-mscsvtab` (table requests)

### Output Messages
**message-out**
- Record size: 1 to 8000 characters
- Structure: `com-recpc001`
- Includes response headers and corresponding reply messages:
  - `com-testa-sr` (response header)
  - `recsv001`, `recsv006`, `recsv020`, `recsv011` (response messages)
  - `coa-rscsvtab` (table responses)

## Key Working Storage Structures

### Error Management
- **tabella-err**: 38 error messages, 64 characters each
- **taberr**: Redefined structure with `elem-err` occurring 38 times
- Provides detailed error descriptions with variable field layouts

### Control Fields
- **com-key-f140**: 26-character key for position file access
- **confronto-r**: Comparison record with society, station, line, and confirmation fields
- **com-area-tra**: 5-character area transfer field

### Date/Time Management
- **datadelgiorno**: Current date structure (year, month, day)
- **com-anno**: Year structure with century and year components
- **data-day**: Date structure with computational fields
- **orario**: Time structure with hours, minutes, seconds, centiseconds

### Lookup Tables
- **tab-mesi**: 48-character month table with days per month
- **tab-ftm-cal**: FTM caliber table with 10 entries for weight calculations
- **tab-scacchi**: Chess table with 15 entries for production scheduling
- **msv50-dati**: Message structure with 216 occurrences for area data

## Data Flow Relationships

The files interconnect through key relationships:
- **dfcf140** serves as the central hub, linking to order files (dfof122/dfmf132) via confirmation numbers
- **dfaf283** maps sub-areas from **dfaf282** to production macros
- **dfaf281** provides productivity data corresponding to sub-areas in **dfaf282**
- **dfaf286** supplies caliber data based on station/line combinations from **dfcf140**
- **dfmtabel** provides configuration lookup data for all processing functions

## Key Field Structures

### Position Keys
- Society + Station + Line + Confirmation + Modification + Position + Progress
- Used across dfcf140, dfof122, dfmf132 for order tracking

### Area Keys  
- Society + Table + Sub-area + Year + Month
- Links dfaf281, dfaf282 for productivity and characteristic data

### Caliber Keys
- Society + Table + Station + Line + Diameter + Thickness + Caliber Type
- Used in dfaf286 for train and caliber determination