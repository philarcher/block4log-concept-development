# Message Specification

## EPCIS Events to express the loading and unloading of load carriers

| Dim | Data Element | V1 | V2 |
| --- | ------------ | -- | -- |
|  | Description | RTIs arrive at a given location | RTIs leave a given location |
|  | Event Type | `ObjectEvent` | `ObjectEvent` |
|  | `action` | `OBSERVE` | `OBSERVE` |
| When | `eventTime` | `Timestamp` of event | `Timestamp` of event |
| What | `quantityList` |
|  | _`quantityElement` |
|  | __`epcClass` | `GRAI` (without serial number) of RTI | `GRAI` (without serial number) of RTI |
|  | __`quantity` | Number of arrived RTIs | Number of departed RTIs |
| Where | `readPoint` | `SGLN` (without GLN extension) of RTI exchange location | `SGLN` (without GLN extension) of RTI exchange location |
| Why | `bizStep` | `Arriving (CBV)` | `Departing (CBV)` |
|  | `bizTransactionList` |
|  | _`bizTransactionID` `type`:`b4l:palletNote` | `GDTI` of pallet note | `GDTI` of pallet note |
|  | `sourceList` |
|  | _`source` `type`:`possessing party (CBV)` | `PGLN` of delivering LSP | n./a. |
|  | _`source` `type`:`owning party (CBV)` | `PGLN` of sending exchange party | `PGLN` of sending exchange party |
|  | `destinationList` |
|  | _`destination` `type`:`possessing party (CBV)` | n./a. | `PGLN` of sending LSP |
|  | _`destination` `type`:`owning party (CBV)` | `PGLN` of receiving exchange party | `PGLN` of receiving exchange party |
|  | _`destination` `type`:`location (CBV)` | n./a. | `SGLN` of receiving exchange location |
| Other | `b4l:tradability` | `true` or `false` |
|  | `b4l:qualityLevel` | `A`, `B`, or `C` |

### Comments
* Note: if there are tradable and non-tradable OR several quality levels in the course of an arriving/departing event, we need to generate several EPCIS events.
Rationale: these criteria entail different business processes/pallet values. Thus, we have to properly distinguish quantities pertaining to different qualities.

## EPCIS Events to express a change of ownership (only applicable for open pool systems)

| Dim | Data Element | V3 |
| --- | ------------ | -- |
|  | Description | RTIs change ownership from one to another organisation |
|  | Event Type | `ObjectEvent` |
|  | `action` | `OBSERVE` |
| When | `eventTime` | `Timestamp` of event |
| What | `quantityList` |
|  | _`quantityElement` |
|  | __`epcClass` | `GRAI` (without serial number) of RTI |
|  | __`quantity` | Number of RTIs changing ownership |
| Why | `bizStep` | `Accepting (CBV)` |
|  | `bizTransactionList` |
|  | _`bizTransactionID` `type`:`inv (CBV)` | Invoice ID (URI, e.g. `GDTI` or `GLN-based URI`) |
|  | `sourceList` |
|  | _`source` `type`:`owning party (CBV)` | `PGLN` of releasing exchange party |
|  | `destinationList` |
|  | _`destination` `type`:`owning party (CBV)` | `PGLN` of receiving exchange party |
| Other | `b4l:qualityLevel` | `A`, `B`, or `C` |

## EPCIS Error Declaration Events

### Case 1: Correcting incorrect data

| Dim | Data Element | V4 | V5 |
| --- | ------------ | -- | -- |
|  | Description | Error Declaration Event | Corrective Event |
|  | Event Type | See original event | See original event |
|  | `eventID` |  | `EPCIS Event Hash ID` |
|  | `errorDeclaration` |
|  | _`declarationTime` | `Timestamp` of error declaration |  |
|  | _`reason` | `Incorrect data (CBV)` |  |
|  | _`correctiveEventIDs` |
|  | __`correctiveEventID` | `EPCIS Event Hash ID` |  |
|  | `action` | See original event | See original event |
| When | `eventTime` | See original event | Possibly corrected `Timestamp` |
| What | `quantityList` - `quantityElement` |
|  |  `epcClass` | See original event | Possibly corrected `GRAI` (without serial number) of RTI |
|  |  `quantity` | See original event | Possibly corrected number of departed RTIs |
| Where | `readPoint` | See original event | Possibly corrected `SGLN` (without GLN extension) of RTI exchange location |
| Why | `bizStep` | See original event| Possibly corrected Business Step |
|  | `bizTransactionList` |
|  | `bizTransactionID` `type`:`b4l:palletNote` | See original event | Possibly corrected `GDTI` of pallet note |
|  | `sourceList` |
|  | `source` `type`:`possessing party (CBV)` | See original event | Possibly corrected `PGLN` |
|  | `source` `type`:`owning party (CBV)` | See original event | Possibly corrected `PGLN` |
|  | `destinationList` |
|  | `destination` `type`:`possessing party (CBV)` | See original event | Possibly corrected `PGLN` |
|  | `destination` `type`:`owning party (CBV)` | See original event | Possibly corrected `PGLN` |
|  | `destination` `type`:`location (CBV)` | See original event | Possibly corrected `SGLN` |
| Other | `b4l:tradability` | See original event | Possibly corrected `true` or `false` |
|  | `b4l:qualityLevel` | See original event | Possibly corrected `A`, `B`, or `C` |

### Case 2: Indicate that event just did not occur
TBD, if required

### Comments
* The above illustrative example pertains to V1.
* The Error Declaration (V4) as well as the Corrective Event (V5) SHOULD be captured in one request. The Error Declaration Event looks identical to the original (i.e. incorrect) event, except for the additional Error Declaration Element reversing its semantics. Further, it also accommodates a forward link to the Corrective Event.
For more information, see EPCIS v. 1.2, section 7.4.1.2.

## Legend/explanation
* owning_party: actual exchange parties (in open pool system systems the actual owner, in closed pool systems the 'lessee')
* LSP - Logistics Service Provider
* RTI - Reusable Transport Item
* GDTI - Global Document Type Identifier
* GRAI - Global Reusable Asset Identifier
* SGLN - Global Location Number with or without extension
* PGLN - Global Location Number of Party