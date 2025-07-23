# Inter-Program Communication

## Overview
The SVCSIM01 program implements multiple communication mechanisms to interact with external systems and programs for manufacturing order simulation and acceptance operations.

## PATHSEND Operations

### Server Communication Infrastructure
The program uses PATHSEND operations to communicate with external server classes through the following variables (lines 1089-1101):

- **Message Buffer Management**: 2020-byte buffer for request/response handling
- **Server Selection Logic**: Automatic server determination based on station codes
- **Timeout Configuration**: 88000 timeout value for all communications
- **Error Handling**: Comprehensive error detection through `SERVERCLASS_SEND_INFO_`

### Server Selection Process (procedure `esegui-pathsend` lines 2847-2890)
```cobol
Default Server: "SVSCS502"
Romania Exception: "SVSIL502" when f140-sta = "RO"
Process Name: Determined by wk-ancestor variable
```

The system automatically routes requests to appropriate servers:
- Standard operations use SVSCS502
- Romanian operations (station "RO") use SVSIL502
- Process names are extracted from ancestor process identification

### Communication Flow
1. **Request Preparation**: Message structure using `mvscs502` format
2. **TAL Call Execution**: `SERVERCLASS_SEND_` with specified parameters
3. **Response Processing**: `rescs502` format for returned data
4. **Error Validation**: `SERVERCLASS_SEND_INFO_` for comprehensive error checking

## External Program Calls

### G0DATA Integration (procedure `call-g0data` line 4598)
**Purpose**: Date and week calculations for manufacturing scheduling
```cobol
call "g0data" in cobli1 using param-g0data
```

**Function Codes Used**:
- **16**: Week calculation operations
- **19**: ISO format week/year calculations (line reference al04)
- **5**: Date arithmetic operations
- **99**: Program termination cleanup

### RCANOR1 Steel Calculation (lines 3456-3458)
**Purpose**: Steel grade and specification calculations
```cobol
call "rcanor1" in cobacc using lnk-a-acciaio1 lk-lotto
```

**Parameters**:
- `lnk-a-acciaio1`: Steel calculation linkage area
- `lk-lotto`: Lot information structure containing cycle, confirmation, and position data

## Message Processing Architecture

### Input/Output File Structure
- **message-in**: Processes incoming requests with 8000-character capacity
- **message-out**: Generates responses with `syncdepth 1` for immediate writing

### Message Type Routing
The system processes different transaction types based on `rq-funz-code` values:

| Function Code | Purpose | Handler Procedure |
|---------------|---------|-------------------|
| 01 | Order acceptance requests | trat-funz-01 |
| 06 | Position acceptance requests | trat-funz-06 |
| 10 | Simulation acceptance | trat-funz-10 |
| 11 | Simulation status verification | trat-funz-11 |
| 16 | Position status inquiry | trat-funz-16 |
| 20 | Position data and productivity | trat-funz-20 |
| 21 | Modified position data requests | trat-funz-21 |
| 25 | Modification data requests | trat-funz-25 |
| 80 | Capacity and load requests | trat-funz-80 |
| 85 | Area modification requests | trat-funz-85 |

### Message Structure Components
- **Request Messages**: `com-mscpc001` with various message types (`mscsv001`, `mscsv006`, etc.)
- **Response Messages**: `com-recpc001` with corresponding response formats
- **Error Messages**: `rem00003` for error condition reporting

## System Integration Points

### System Identification Process
```cobol
enter tal "mysystemnumber" giving system-number
enter tal "getsystemname" using system-number, system-name
```

The program identifies its operating environment and extracts system name components for routing decisions.

### Process Management Validation
The system validates calling processes against authorized ancestors:
- `"$SM02 "` - System Manager process
- `"$PM02 "` - Process Manager process  
- `"$QM02 "` - Queue Manager process

Unauthorized process calls trigger application error handling (`err-def-appl`).

### Transaction Processing Flow
1. **Message Reception**: `leggi-messaggio` reads incoming requests
2. **Function Code Analysis**: Routes to appropriate handler based on transaction type
3. **Processing Execution**: Specific function procedures handle business logic
4. **Response Generation**: `scrivi-risposta` formats and sends replies
5. **Error Management**: Comprehensive error handling with specific error codes

## Communication Error Handling

### PATHSEND Error Management
The system implements multi-level error detection:
- **Primary Error Check**: TAL call return codes
- **Secondary Validation**: `SERVERCLASS_SEND_INFO_` for detailed error analysis
- **File System Errors**: Separate tracking of file system-related issues
- **Timeout Handling**: 88000 timeout value with appropriate error responses

### Error Response Structure
Error conditions generate structured responses through `rem00003` with:
- Error type classification
- Specific error codes
- Detailed error descriptions
- System context information

This communication architecture ensures reliable data exchange between SVCSIM01 and external systems while maintaining comprehensive error handling and system validation.