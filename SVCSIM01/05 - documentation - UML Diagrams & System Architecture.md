# UML Diagrams & System Architecture

## System Overview

The Manufacturing Simulation Engine (`svcsim01`) is a comprehensive COBOL-based system that manages order acceptance, production scheduling, and capacity planning across multiple manufacturing facilities. The system processes various transaction types to simulate production workflows and calculate resource requirements.

## Component Architecture

```mermaid
flowchart TD
    MainSim["Manufacturing Simulation Engine"] --> OrderMgmt["Order Management System"]
    MainSim --> ProductionData["Production Area Database"]
    MainSim --> CaliberData["Caliber Configuration System"]
    MainSim --> CapacityMgmt["Capacity Management System"]
    
    OrderMgmt --> OrderDB["Order Database (dfof122/dfmf132)"]
    ProductionData --> AreaDB["Production Area Data (dfaf281/dfaf282)"]
    ProductionData --> MacroData["Macro Cycle Data (dfaf283)"]
    CaliberData --> CaliberDB["Caliber Train Data (dfaf286)"]
    CapacityMgmt --> PositionDB["Position Database (dfcf140)"]
    CapacityMgmt --> ScheduleDB["Schedule Database (dfcfs90)"]
    
    MainSim --> MessageSys["Message Processing System"]
    MessageSys --> ClientSys["Client Systems"]
    
    MainSim --> TableSys["Configuration Tables (dfmtabel)"]
    MainSim --> TransmissionSys["Transmission System (dfaf091)"]
```

## Transaction Processing Flow

```mermaid
sequenceDiagram
    participant Client as "Client System"
    participant SimEngine as "Simulation Engine"
    participant OrderSys as "Order Management"
    participant ProductionSys as "Production System"
    participant CapacitySys as "Capacity System"
    
    Client->>SimEngine: Submit Transaction Request
    SimEngine->>SimEngine: Validate Transaction Code
    
    alt Order Acceptance Request (Code 01)
        SimEngine->>OrderSys: Query Available Orders
        OrderSys-->>SimEngine: Return Order List
    else Position Data Request (Code 06/20)
        SimEngine->>ProductionSys: Query Production Data
        ProductionSys-->>SimEngine: Return Manufacturing Details
        SimEngine->>CapacitySys: Calculate Capacity Requirements
        CapacitySys-->>SimEngine: Return Capacity Analysis
    else Simulation Acceptance (Code 10)
        SimEngine->>ProductionSys: Update Production Schedule
        SimEngine->>CapacitySys: Reserve Capacity
        ProductionSys-->>SimEngine: Confirm Updates
    end
    
    SimEngine-->>Client: Return Processing Results
```

## Data Model

```mermaid
classDiagram
    class ManufacturingController {
        +processOrderAcceptance()
        +calculateProductionCapacity()
        +managePositionData()
        +executeSimulationAcceptance()
        +validateTransactionCode()
        -updateProductionSchedule()
    }
    
    class OrderData {
        +orderNumber
        +customerDescription
        +quantity
        +deliveryDate
        +orderStatus
        +productSpecifications
    }
    
    class ProductionAreaData {
        +areaCode
        +areaDescription
        +productivityValue
        +capacityHours
        +workingTurns
        +unitOfMeasure
    }
    
    class PositionManagement {
        +positionNumber
        +progressNumber
        +positionStatus
        +quantityInPieces
        +quantityInMeters
        +quantityInKilograms
    }
    
    class CaliberConfiguration {
        +caliberCode
        +trainNumber
        +stationCode
        +lineCode
        +diameterRange
        +thicknessRange
    }
    
    ManufacturingController --> OrderData
    ManufacturingController --> ProductionAreaData
    ManufacturingController --> PositionManagement
    ManufacturingController --> CaliberConfiguration
    
    OrderData --> PositionManagement
    ProductionAreaData --> PositionManagement
```

## Main Process Flow

```mermaid
flowchart TD
    Start["Initialize Manufacturing Server"] --> ReadMsg["Read Transaction Message"]
    ReadMsg --> ValidateApp{"Valid Application?"}
    ValidateApp -->|No| ErrorApp["Application Error Response"]
    ValidateApp -->|Yes| ValidateCode{"Valid Transaction Code?"}
    
    ValidateCode -->|Code 01| ProcessOrders["Process Order Acceptance"]
    ValidateCode -->|Code 06| ProcessPositions["Process Position Requests"]
    ValidateCode -->|Code 10| ProcessAcceptance["Process Simulation Acceptance"]
    ValidateCode -->|Code 20/21| ProcessMultiple["Process Multiple Data Requests"]
    ValidateCode -->|Code 25| ProcessModification["Process Modification Requests"]
    ValidateCode -->|Code 80/85| ProcessCapacity["Process Capacity Analysis"]
    ValidateCode -->|Invalid| ErrorCode["Invalid Transaction Error"]
    
    ProcessOrders --> UpdateData["Update Manufacturing Data"]
    ProcessPositions --> CalculateCapacity["Calculate Production Capacity"]
    ProcessAcceptance --> ReserveCapacity["Reserve Production Capacity"]
    ProcessMultiple --> CalculateCapacity
    ProcessModification --> CalculateCapacity
    ProcessCapacity --> GenerateAnalysis["Generate Capacity Analysis"]
    
    UpdateData --> SendResponse["Send Response to Client"]
    CalculateCapacity --> SendResponse
    ReserveCapacity --> SendResponse
    GenerateAnalysis --> SendResponse
    ErrorApp --> SendResponse
    ErrorCode --> SendResponse
    
    SendResponse --> ReadMsg
```

## Integration Points

The Manufacturing Simulation Engine integrates with several external systems:

### External System Connections
- **Order Management Systems**: Interfaces with order databases (dfof122, dfmf132) for order processing
- **Production Planning Systems**: Connects to production area databases (dfaf281, dfaf282, dfaf283) for capacity planning
- **Configuration Management**: Accesses caliber and equipment configuration data (dfaf286)
- **Client Applications**: Processes requests from various manufacturing client systems
- **Transmission Systems**: Manages data transmission for remote facilities (dfaf091)

### Data Flow Architecture
- **Input Processing**: Receives transaction requests through message-based communication
- **Data Validation**: Validates transaction codes and application authorization
- **Business Logic Execution**: Processes manufacturing-specific calculations and simulations
- **Database Operations**: Performs read/write operations across multiple manufacturing databases
- **Response Generation**: Returns structured responses based on transaction type
- **Error Handling**: Comprehensive error management with detailed error codes and descriptions

### System Capabilities
- **Multi-facility Support**: Handles manufacturing operations across different stations and production lines
- **Real-time Processing**: Processes manufacturing requests in real-time with immediate responses
- **Capacity Management**: Calculates and manages production capacity across multiple time periods
- **Order Simulation**: Simulates order acceptance scenarios before actual commitment
- **Production Scheduling**: Updates production schedules based on accepted orders and capacity constraints