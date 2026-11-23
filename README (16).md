# Topic 10 --- Lead and Invoice Status Tracking

This repository documents an n8n workflow that manages lead lifecycle
statuses using a mock tabular data store.

## Workflow files

-   `Topic10_Lead_Invoice_Requirement_Document.md` --- project
    requirements and test-case expectations.
-   `Topic10_Lead_Invoice_Implementation_Document.md` --- workflow
    architecture, nodes, logic, and execution sequence.
-   `Topic10_Lead_Invoice_Test_Results.md` --- test evidence and results
    for TC-001 through TC-005.

## Workflows

### Main workflow

`Topic 10 - Lead and Invoice Status Tracking`

Reads lead records, filters Pending leads, processes one lead at a time,
simulates invoice processing, and writes Completed or Failed status
updates.

### Mock server workflow

`Topic 10 - Lead Tracking Mock Sheets Server`

Provides GET and PUT webhook endpoints and stores records using n8n
workflow static data.

## Lead status lifecycle

-   Pending → Processing → Completed (successful processing)
-   Pending → Processing → Failed (failed processing)

Each update uses `lead_id` as the unique key and records `updated_at`
and `execution_id`. Failed records also store `error_details`.

## Test summary

  -----------------------------------------------------------------------
  Test case                           Result
  ----------------------------------- -----------------------------------
  TC-001 --- Success Pipeline Status  PASS
  Transition                          

  TC-002 --- Filter Out Non-Pending   PASS
  Records                             

  TC-003 --- Downstream Error Trap    PASS --- simulated failure path
  and Log Write                       

  TC-004 --- Execution ID Audit       PASS
  Verification                        

  TC-005 --- Update Collision         PASS --- sequential functional test
  Protection (Key Isolation)          
  -----------------------------------------------------------------------

Mixed-outcome execution ID: `716026`.

## Important implementation scope

This coursework version uses an n8n mock data store rather than a live
Google Sheets connection. Invoice processing is simulated in a Code node
rather than calling a real external invoice API. These distinctions are
documented in the implementation and test-results documents.

## Demonstration outline

1.  Explain that the mock server represents the tabular data store.
2.  Show `HTTP_GetLeadRecords` and `Code_FilterPendingLeads`.
3.  Explain the Loop Over Items node with Batch Size 1.
4.  Show the Processing update, invoice simulation, and IF
    success/failure routing.
5.  Show the final persisted lead records and execution ID.
6.  Disclose that Google Sheets and the invoice API are mocked in this
    version.
