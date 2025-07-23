# Configuration & Environment Setup

## Overview

The SVCSIM01 program is configured for operation in a Tandem NonStop environment with specific system settings, file definitions, and library dependencies required for steel production simulation and order acceptance processing.

## System Configuration

### Computer Settings
- **Source Computer**: TNS2 system
- **Object Computer**: TNS2 system
- **Decimal Point**: Comma notation for numeric values

### Special Names Configuration
The program defines three critical system file references:
- `$system.azutil.eoblib` as coblib (system utilities)
- `$data1.skeobj.rcanor1` as cobacc (accounting objects)
- `$data1.cedobj.e0data` as cobli1 (data objects)

## File System Configuration

### Primary Data Files
The program accesses multiple data files through COPY statements from the accDICT.COPYLIB library:
- **dfaf281**: Productivity data by sub-area
- **dfaf282**: Characteristics data by sub-area  
- **dfaf283**: Sub-area to macro relationships
- **dfaf286**: Train caliber data by station
- **dfcf140**: Positions to accept and/or accepted positions
- **dfcfs90**: Additional position data
- **dfmtabel**: System tables
- **dfaf091**: Transmission data for local tandem systems

### Cross-Reference Files
Additional files from acqdict and cadict libraries:
- **dfof122/dfmf132**: Order management data
- **dfof121/dfmf131**: Order processing data
- **dfsaree**: Area management data

### Message Processing Files
- **message-in**: Input messages (1-8000 characters)
- **message-out**: Output messages (1-8000 characters)

## Tandem NonStop Specific Configuration

### System Identification
The program identifies its operating environment through:
- System number retrieval via TAL call `mysystemnumber`
- System name extraction via TAL call `getsystemname`
- System name parsing to extract 2-character system identifier

### Process Management
The program validates its execution context by checking ancestor processes:
- Accepts execution from `$SM02`, `$PM02`, or `$QM02` processes only
- Implements error handling for unauthorized execution attempts

### Pathsend Configuration
Communication setup includes variables for:
- Server class names and lengths
- Pathmon process names and lengths
- Message buffers (2020 characters)
- Request and reply length management
- Timeout settings and error handling

## Compiler Directives

### Preprocessing Settings
- `?ENV COMMON`: Common environment settings
- `?symbols`: Symbol table generation
- `?notrap2`: Trap handling configuration

## Library Dependencies

### Primary Libraries
- **accDICT.COPYLIB**: Accounting dictionary copybooks
- **clidict.copylib**: Client dictionary copybooks
- **acqdict.copylib**: Acquisition dictionary copybooks
- **cadict.copylib**: CA dictionary copybooks

### System Libraries
- **azutil.copylib**: System utility copybooks
- **cortino1.msglib**: Message library for communication
- **reclib**: Record structure libraries

## Configuration Variables

### System Variables
- System identification fields (wk-nome-sistema, system-id)
- Process identification variables
- File operation status indicators

### Communication Variables
- Pathsend configuration parameters
- Message buffer management variables
- Error handling and timeout settings

### File Processing Variables
- Record counters and indices
- File status indicators
- Lock management variables

## Environment Validation

The program performs runtime validation to ensure:
- Proper ancestor process authorization
- System resource availability
- File accessibility and permissions
- Communication pathway establishment

This configuration supports the program's role in steel production order processing within the Tandem NonStop distributed computing environment.