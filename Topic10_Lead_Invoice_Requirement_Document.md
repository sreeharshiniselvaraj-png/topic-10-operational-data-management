# Topic 10: Operational Data Management with Google Sheets

## Requirement Document --- Lead and Invoice Status Tracking

**Version:** 1.0\
**Status:** Implemented and tested with an n8n mock data store\
**Workflow:** `Topic 10 - Lead and Invoice Status Tracking`

## 1. Project Overview

This workflow demonstrates operational lead-record management using a
Google-Sheets-style tabular data structure. It reads lead records,
selects only records whose status is `Pending`, marks each selected lead
as `Processing`, simulates invoice processing, and records the final
result as `Completed` or `Failed`.

**Implementation note:** The completed coursework workflow uses an n8n
mock Sheets server backed by workflow static data. It does not currently
connect to a live Google Sheets spreadsheet. The mock server provides
GET and PUT endpoints so the workflow can demonstrate the required read,
update, and audit behaviour.

## 2. Objectives

-   Read all lead records from a tabular data store.
-   Process only leads with `lead_status` equal to `Pending`.
-   Mark a lead as `Processing` before invoice processing.
-   Record the n8n execution ID and update timestamp.
-   Mark successful processing as `Completed`.
-   Mark failed processing as `Failed` and save the error details.
-   Identify records uniquely by `lead_id`.
-   Avoid overlapping updates by processing one lead at a time.

## 3. Data Structure

Each lead record contains the following fields:

  -----------------------------------------------------------------------
  Field                               Purpose
  ----------------------------------- -----------------------------------
  `lead_id`                           Unique identifier for the lead and
                                      update key

  `lead_status`                       Current lifecycle status: Pending,
                                      Processing, Completed, or Failed

  `created_at`                        Record creation timestamp

  `updated_at`                        Timestamp of the latest status
                                      update

  `execution_id`                      n8n execution ID associated with
                                      processing

  `error_details`                     Error message when processing
                                      fails; otherwise blank
  -----------------------------------------------------------------------

## 4. Functional Requirements

  -----------------------------------------------------------------------
  ID                                  Requirement
  ----------------------------------- -----------------------------------
  FR-001                              Read all lead records from the data
                                      store.

  FR-002                              Filter and pass forward only
                                      records where `lead_status` is
                                      exactly `Pending`.

  FR-003                              Before invoice processing, update
                                      the matching record to
                                      `Processing`, set `execution_id` to
                                      the current n8n execution ID, and
                                      update `updated_at`.

  FR-004                              When invoice processing succeeds,
                                      update the matching record to
                                      `Completed` and refresh
                                      `updated_at`.

  FR-005                              When invoice processing fails,
                                      update the matching record to
                                      `Failed`, store the error message
                                      in `error_details`, and refresh
                                      `updated_at`.
  -----------------------------------------------------------------------

## 5. Non-Functional Requirements

  -----------------------------------------------------------------------
  ID                                  Requirement
  ----------------------------------- -----------------------------------
  NFR-001                             Every update must identify its
                                      target record using the unique
                                      `lead_id` key.

  NFR-002                             Avoid unnecessary reads and writes;
                                      process the selected records in a
                                      controlled sequence.
  -----------------------------------------------------------------------

## 6. User Stories

-   **US-001 --- Successful lead:** Given a lead such as L-101 with
    status `Pending`, when invoice processing succeeds, the workflow
    changes its status through `Processing` to `Completed`.
-   **US-002 --- Failed lead:** Given a pending lead with a validation
    or processing failure, the workflow changes its status through
    `Processing` to `Failed` and records the error details.

## 7. Test Case References

  -----------------------------------------------------------------------
  Test Case               Description             Expected behaviour
  ----------------------- ----------------------- -----------------------
  TC-001                  Success Pipeline Status L-101 moves from
                          Transition              Pending to Processing
                                                  to Completed; timestamp
                                                  and execution ID are
                                                  recorded.

  TC-002                  Filter Out Non-Pending  Completed and Failed
                          Records                 records are filtered
                                                  out; no downstream
                                                  write-back occurs.

  TC-003                  Downstream Error Trap   L-102 moves from
                          and Log Write           Pending to Processing
                                                  to Failed; error
                                                  details are recorded.

  TC-004                  Execution ID Audit      Stored `execution_id`
                          Verification            matches the actual n8n
                                                  execution ID.

  TC-005                  Update Collision        Processing one lead
                          Protection (Key         updates only that lead;
                          Isolation)              other lead records
                                                  remain unchanged.
  -----------------------------------------------------------------------

## 8. Scope and Limitations

-   The current implementation is a mock demonstration, not a production
    Google Sheets integration.
-   Invoice processing is simulated inside an n8n Code node. It does not
    call a real invoice API.
-   The failure path uses the simulated message
    `500 - Internal Server Error`.
-   Replacing the mock data store with Google Sheets and replacing the
    simulated invoice step with the trainer's specified API are future
    integration steps.
