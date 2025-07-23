# Modification History & Version Control

## Overview

The COBOL program `svcsim01` implements a comprehensive embedded modification tracking system using comment-based version control. The system maintains two parallel tracking mechanisms: a primary alphanumeric sequence (al01-al16) and a secondary date-based change ID system.

## Primary Modification History (al01-al16)

The program contains a detailed modification log spanning from 2011 to 2024:

| Code | Date | Description | Functional Impact |
|------|------|-------------|-------------------|
| al01 | 22/06/11 | Modifica per gestione CESE | CESE management handling |
| al02 | 14/07/11 | Errore in assegnazione linea SIT | SIT line assignment correction |
| al03 | 15/02/12 | Modifica per CV componenti - tipo impianto T > P | CV component plant type modification |
| al04 | 10/10/11 | Calcolo settimana/anno formato ISO | ISO week/year calculation format |
| al05 | 30/11/15 | Gestione treno = 8 per linea FST | Train handling for FST line |
| al06 | 02/12/15 | Gestione linea FST | FST line management |
| al07 | 30/12/15 | Gestione barre acciaio in DA/FTM | Steel bar handling in DA/FTM |
| al08 | 18/04/18 | Eliminazione simulazione | Simulation elimination |
| al09 | 20/10/18 | Split BATAM | BATAM splitting |
| al10 | 29/04/19 | Diametro curve a 4 interi (SSP/BEN) | 4-integer curve diameter for SSP/BEN |
| al11 | 08/11/19 | Errore in anno sottoarea 2019/2020 | Subarea year error correction |
| al12 | 22/01/20 | Chiamata da SVTACC02 | Call from SVTACC02 |
| al13 | 08/06/20 | Modifica macrociclo da SVTACC02 | Macrocycle modification from SVTACC02 |
| al14 | 09/06/20 | Ricalcolo data consegna | Delivery date recalculation |
| al15 | 09/12/22 | Modifica per Order Hosting SIP | Order Hosting SIP modification |
| al16 | 24/04/24 | Modifica per Conferme x Stock | Stock confirmation modification |

## Secondary Change ID System

The program also tracks modifications using date-based change IDs:

| Date | Change ID | Description |
|------|-----------|-------------|
| 20150909 | - | Test for KPT added |
| 20210323 | ChID: 2145 | Test for ABD added |
| 20210813 | ChID: 2261 | Test for BAO added |
| 20221116 | ChID: 2565 | TET & QIN - acc, SERVER: AE added |
| 20240118 | ChgID: 2874 | GPCA modification |

## Code Implementation Tracking

### Modification al08 - Simulation Elimination
- Introduces `sw-simul` variable for simulation control
- Implements conditional logic: `if sw-simul = "N"`
- Creates separate processing paths for simulation and non-simulation modes
- Affects multiple procedures including `trat-funz-80-no-sim`

### Modification al10 - 4-Integer Diameter Handling
- Changes diameter processing from standard to 4-integer format for curves
- Implements conditional logic: `if f140-cod-tipodimens = 6`
- Affects diameter calculations in multiple procedures
- Specific to SSP/BEN operations

### Modification al14 - Delivery Date Recalculation
- Introduces `sw-data-cons` variable for delivery date control
- Adds `aggiorna-data-cons` procedure for date updates
- Implements order key handling changes
- Affects modification processing when `msv20-num-mod` is not zero

### Modification al16 - Stock Confirmation
- Introduces `com-pri-qualifica` variable
- Implements qualification-based processing logic
- Affects order processing when `com-pri-qualifica = 8`

## Version Control Methodology

### Embedded Marker System
- Modification markers are embedded directly in source code as comments
- Each change is tagged with its corresponding modification code
- Markers appear at specific implementation points throughout the program

### Dual Tracking Approach
- Primary system uses sequential alphanumeric codes (al01-al16)
- Secondary system uses date-based change IDs with optional ChID/ChgID references
- Both systems coexist to provide comprehensive change tracking

### Conditional Compilation Patterns
- Modifications often introduce conditional logic branches
- New functionality is typically added alongside existing code paths
- Legacy code is preserved while new behavior is controlled by switches

## Evolution Analysis

### Major System Changes
- **al08 (2018)**: Fundamental change to simulation handling
- **al10 (2019)**: Diameter processing enhancement for specific operations
- **al14 (2020)**: Delivery date management improvement

### Geographic Expansion
- Multiple modifications add support for new locations (KPT, ABD, BAO, AE)
- Date-based change IDs primarily track geographic expansions

### Functional Enhancements
- Early modifications (2011-2015) focus on core functionality improvements
- Later modifications (2018-2024) emphasize system integration and specialized handling

The modification history demonstrates continuous system evolution with careful preservation of existing functionality while adding new capabilities through controlled conditional logic.