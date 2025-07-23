# File I/O Operations Documentation

## Executive Summary

The COBOL program `svcsim01` implements a comprehensive file I/O architecture supporting 15 different file types with various access patterns including sequential, random, and dynamic access. The system manages production scheduling, order processing, and capacity planning through coordinated file operations with shared access capabilities and robust error handling.

## File Definitions and Access Patterns

### Production and Configuration Files

**dfaf281 (Production Data)**
- Record Structure: `draf281`
- Access: Sequential input with shared access
- Purpose: Stores productivity data by sub-area
- Key Operations: `read-f281-next`, `start-f281`

**dfaf282 (Characteristics Data)**
- Record Structure: `draf282` 
- Access: Input/Output with shared access and locking
- Purpose: Manages characteristics by sub-area
- Key Operations: `read-f282`, `read-f282-lock`, `read-f282-next`, `rewrite-f282`

**dfaf283 (Sub-area Macro Mapping)**
- Record Structure: `draf283`
- Access: Sequential input with shared access
- Purpose: Links sub-areas to macro cycles
- Key Operations: `read-f283` with comprehensive error handling

**dfaf286 (Train Caliber Configuration)**
- Record Structure: `draf286`
- Access: Sequential input with shared access
- Purpose: Manages train caliber settings by station
- Key Operations: `read-f286-next`, `start-f286`

### Order and Position Management Files

**dfcf140 (Position Processing)**
- Record Structure: `drcf140`
- Access: Input/Output with shared access and locking
- Purpose: Central file for order positions and acceptance status
- Key Operations: `read-f140-lock`, `read-f140-next`, `start-f140`, `start-f140-c`, `start-f140-gen-prec`, `rewrite-f140`
- Key Structure: `f140-chiave` (26 characters)

**dfcfs90 (Status Tracking)**
- Record Structure: `drcfs90`
- Access: Input/Output with shared access
- Purpose: Tracks processing status and events
- Key Operations: `write-f090` with `fc90-numrel = -2`

**dfaf091 (Transmission Data)**
- Record Structure: Variable length (1-2003 characters) with `testa-f091` and `ft91-drcf140`
- Access: Input/Output with shared access
- Purpose: Handles transmission data for local tandem operations
- Key Operations: `write-ft91-f140`

### Order Management Files

**dfof122 (Order Details)**
- Record Structure: `drof122`
- Access: Sequential input with shared access
- Purpose: Stores order configuration details
- Key Operations: `read-dfof122` with sequential processing into `comf122`

**dfmf132 (Order Modifications)**
- Record Structure: `drmf132`
- Access: Input/Output with shared access
- Purpose: Manages order modifications and updates
- Key Operations: `read-dfmf132` with position validation

**dfof121 / dfmf131 (Order Headers)**
- Record Structures: `drof121` / `drmf131`
- Access: Sequential input with shared access
- Purpose: Order header information for base orders and modifications

### Reference and Control Files

**dfmtabel (Reference Tables)**
- Record Structure: `drmf010` with `rec-10250` overlay
- Access: Sequential input with shared access
- Purpose: System configuration and reference data
- Special Features: Contains 8-element array `ele-acc` for acceptance codes

**dfsaree (Area Control)**
- Record Structure: `drsaree`
- Access: Input/Output with shared access
- Purpose: Controls active cycle processing
- Key Operations: `write-saree` with duplicate key handling

### Message Processing Files

**message-in / message-out**
- Variable length records (1-8000 characters)
- Multiple message structures for different transaction types
- Handles request/response processing with various message formats

## CRUD Operations Matrix

### CREATE Operations
- `write-f090`: Creates status records with `fc90-numrel = -2`
- `write-ft91-f140`: Creates transmission records for position data
- `write-saree`: Creates area control records with duplicate key tolerance

### READ Operations
- **Sequential Reads**: `read-f281-next`, `read-f286-next`, `read-f282-next`, `read-f140-next`
- **Direct Reads**: `read-f282`, `read-f283` (with comprehensive error handling)
- **Locked Reads**: `read-f282-lock`, `read-f140-lock` for update operations
- **Specialized Reads**: `read-dfof122`, `read-dfmf132` with position validation

### UPDATE Operations
- `rewrite-f140`: Updates position records with automatic unlock
- `rewrite-f282`: Updates characteristic data with automatic unlock
- Both operations include comprehensive error handling and file status validation

### START Operations
- `start-f281`: Positions using `f281-chiave` with "not <" condition
- `start-f286`: Positions using `f286-chiave` with "not <" condition  
- `start-f140`: Positions using `f140-chiave` with "not <" condition
- `start-f140-c`: Positions using `f140-chiave` with ">" condition
- `start-f140-gen-prec`: Generic positioning with `f140-chiave-alt`

## Key Management and Access Patterns

### Primary Key Structures
- **f140-chiave**: 26-character composite key for position records
- **f281-chiave**: Production data key structure
- **f282-chiave**: Characteristics data key structure
- **f283-chiave**: Sub-area macro mapping key
- **f286-chiave**: Train caliber configuration key

### Access Pattern Implementation
- **Sequential Processing**: Used for batch operations and reporting
- **Random Access**: Used for direct record retrieval and updates
- **Dynamic Access**: Combines sequential and random for flexible processing
- **Shared Access**: All files opened with shared access for concurrent processing

## Error Handling and Recovery

### File Status Management
- Comprehensive `file-stat` checking across all operations
- Specific error conditions: `end-of-file`, `record-not-found`, `no-error`
- Dedicated error procedures: `err-12`, `err-4`, `err-5` for different error types

### Error Recovery Procedures
- **err-12**: Handles physical file errors with detailed logging
- **err-4**: Manages record not found conditions
- **err-5**: Processes invalid state conditions
- **err-files**: Generic file error handler with status reporting

### Transaction Integrity
- Lock and unlock sequences ensure data consistency
- Automatic unlock on rewrite operations
- Error rollback capabilities for failed transactions

## Performance and Concurrency Features

### Shared File Access
- All files opened with `shared` attribute for multi-user access
- Coordinated locking mechanisms prevent data corruption
- Optimized for concurrent transaction processing

### Access Optimization
- Strategic use of START operations for efficient positioning
- Sequential processing loops with `perform...until` constructs
- Key-based retrieval patterns minimize I/O operations

### Multi-file Coordination
- Synchronized operations across related files (f140, f282, f283, f286)
- Transaction boundaries maintain consistency across file updates
- Coordinated error handling ensures system stability

## Code Examples

### File Opening Pattern
```cobol
open input  dfaf281   shared
open i-o    dfaf282   shared
open input  dfaf283   shared
open i-o    dfcf140   shared
```

### Locked Read and Update Pattern
```cobol
perform read-f140-lock
if not no-error
   perform err-12 thru end-err-12
   go to error-handling.
perform rewrite-f140
```

### Sequential Processing Pattern
```cobol
perform start-f281
perform read-f281-next
perform process-record thru end-process-record
   until end-of-file or sw-err = "1"
```

This file I/O architecture provides robust, concurrent access to production data while maintaining data integrity through comprehensive error handling and coordinated transaction processing.