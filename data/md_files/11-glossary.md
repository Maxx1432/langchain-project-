# Glossary

## Core Business and Technical Terms

* **IA (Income Assessment)**: The Income Assessment service and workflow responsible for collecting, processing, validating, and determining income-assessment outcomes.

* **Assessment Medium**: The mechanism or journey through which financial information is obtained for IA, such as statement upload, scan-and-upload, Account Aggregator (AA), or ITR-related journeys.

* **IA Application**: The persisted Income Assessment workflow record representing an IA journey for a customer/application. The primary application state is stored in the `incomeAssessmentApplications` collection.

* **IA Attempt**: An individual attempt within an IA application, particularly relevant when an external vendor journey is retried. Attempt information can include vendor transaction IDs, callback timestamps, status, and retry context.

* **Perfios**: An external vendor/integration used by IA for statement-based and ITR-related journeys, including transaction initiation, user redirection, status processing, callbacks, and report retrieval.

* **Zenith / AA-Orchestrator**: The external integration path used by IA for Account Aggregator (AA) journey orchestration, journey-link generation, transaction-status retrieval, analytics/report retrieval, and BSA-related operations.

* **BSA (Bank Statement Analysis)**: Analysis of bank-statement or Account Aggregator financial data to derive information used during Income Assessment.

* **CAP**: An external platform that can invoke the IA service to initiate an Income Assessment. CAP is an upstream IA consumer/client, similar to other platforms that invoke IA. IA can subsequently synchronize the resulting IA status back to CAP.

* **CAP Initiation**: The CAP → IA interaction in which CAP invokes IA's `/initiate-application` API to start an Income Assessment journey.

* **CAP Sync**: The IA → CAP interaction used to synchronize an IA outcome/status back to CAP. CAP synchronization is configuration-controlled and implemented as a best-effort side effect.

* **CAP `capRefId`**: The CAP-side reference identifier used to correlate the CAP status synchronization request with the corresponding CAP context.

* **CAP `EventStatus`**: The status representation expected by CAP. IA maps its internal IA status to the corresponding CAP `EventStatus` before sending the CAP update-status request.

* **`eventStatus`**: State stored in the IA application representing the status associated with CAP/event synchronization. It is distinct from the primary IA business `status`.

* **Assessment Journey**: The external-data collection and processing path executed after IA initiation. Depending on configuration and application context, this may be a Perfios or Zenith journey.

* **Finacle Flow**: A flow in which account or statement information is obtained from banking-side/Finacle sources before the wider IA processing continues.

* **OmniDocs**: The document-storage integration/path used for persisting or retrieving statements, reports, generated documents, or other IA artifacts.

* **FCU (Fraud Control Unit)**: A downstream/manual verification path that can receive IA outcomes or events for further verification or decisioning.

* **Status**: The primary business state of an IA application, representing where the Income Assessment currently stands or the outcome reached.

* **Status Type**: Additional state classification stored alongside the primary IA status and used to provide more context about the application's state.

* **State Guard**: Business logic that determines whether a particular IA state transition or operation is allowed based on the current application state and other conditions.

* **Partner**: The upstream business/platform context associated with an IA request. Partner information can influence configuration, routing, and behavior.

* **Product**: The product/business context for which IA is being performed. Product configuration can determine journey selection, feature toggles, version routing, and integration behavior.

* **PartnerProductConfiguration**: Configuration associated with a particular partner/product combination that controls IA behavior, including feature toggles and journey-specific settings.

* **`capSyncEnabled`**: Configuration flag determining whether IA should synchronize the IA outcome/status back to CAP for the current application context.

* **`enableZenithOrchAPI`**: Configuration toggle controlling whether the applicable IA flow uses the Zenith / AA-Orchestrator integration.

* **IA Version**: The implementation version of the IA service selected for a request through the configured version-resolution mechanism.

* **Version Bucket**: A logical configuration bucket used by the version resolver before selecting the appropriate IA implementation/facade version.

* **`CommonVersionResolver`**: Component responsible for resolving the applicable IA version based on request/application context and configuration.

* **`ConfigFetcher`**: Component used to resolve dynamic configuration used by IA at runtime.

* **Callback**: An asynchronous notification from an external system such as Perfios or Zenith indicating that an external journey or processing operation has progressed or completed.

* **Callback Timeout**: A condition in which the expected external callback is not received within the configured/allowed time window, potentially causing IA to fall back to status polling, retry logic, or a failure path.

* **Late Callback**: A callback received after IA has already timed out, retried, failed, or started another attempt. Late callbacks must be correlated to the correct IA application and external transaction/attempt.

* **Vendor Transaction ID**: The transaction identifier generated by an external journey provider such as Perfios or Zenith. It identifies a particular external journey attempt and should not be confused with the IA application identifier.

* **`incomeAssessmentId`**: Identifier associated with the IA application/workflow.

* **`applicationReferenceId`**: Application/business reference used to correlate an IA request with the originating application context.

* **`commonClientTransactionId`**: Client-side/common transaction identifier used for correlation across parts of the request and downstream workflow.

* **`serviceRequestId`**: Service/request-level correlation identifier used for tracing an IA operation across service boundaries.

* **Correlation Identifier**: Any identifier used to connect an application, request, external transaction, event, callback, or downstream operation during debugging. Common identifiers include `incomeAssessmentId`, `applicationReferenceId`, `commonClientTransactionId`, `serviceRequestId`, `capRefId`, and vendor transaction IDs.

* **Kafka Event**: An asynchronous workflow signal published by IA to communicate a state change, processing result, callback, retry, or downstream action to another component or service.

* **Kafka Consumer**: Component that receives and processes an IA Kafka event. Consumer receipt does not necessarily mean that the associated business operation or database update succeeded.

* **Kafka Producer**: Component responsible for publishing IA events to Kafka.

* **Downstream Synchronization**: Communication from IA to another system after IA processing, such as CAP status synchronization or other downstream event/API processing.

* **Best-Effort Side Effect**: An operation whose failure should not automatically change or invalidate the primary business result. CAP status synchronization is an example.

* **Business Rule**: Rule applied to financial/assessment data to determine the IA business outcome, such as income-credit, cheque-bounce, statement-status, or required-history rules.

* **Terminal State**: An IA state from which the normal workflow should not continue through the same transition path. Terminal-state behavior is enforced through business/state guards.

* **RAG Context Pack**: The curated Markdown documentation corpus used by retrieval-augmented developer support. Each document focuses on a specific architectural concern, such as flow, configuration, database state, Kafka events, integrations, errors, or troubleshooting.

---

## Important Architectural Distinctions

### CAP vs IA


CAP
 │
 │ /initiate-application
 ▼
IA


CAP is an external/upstream platform that invokes IA.

### Perfios / Zenith vs CAP


CAP
 │
 ▼
IA
 │
 ├── Perfios
 │
 └── Zenith


Perfios and Zenith are journey/integration providers used by IA after initiation. CAP is the platform that can initiate the IA journey.

### CAP Initiation vs CAP Sync


CAP ──────────────→ IA
       Initiation

IA ───────────────→ CAP
       Status Sync


These are separate integration directions and should be debugged independently.

### IA Status vs CAP EventStatus

IA Business Status
        ↓
Status Mapping
        ↓
CAP EventStatus


`status` and `eventStatus` should not be treated as the same state.

### IA Application vs Vendor Transaction


IA Application
      │
      ├── Vendor Attempt 1
      │
      ├── Vendor Attempt 2
      │
      └── Vendor Attempt N

One IA application can contain multiple external journey attempts.

---

## Common Status Examples

Examples of important IA statuses include:


DOCUMENTS_NOT_UPLOADED
INCOME_ASSESSMENT_IN_PROGRESS
INCOME_ASSESSMENT_SUCCESS
INCOME_ASSESSMENT_FAILED
INCOME_ASSESSMENT_REJECTED
INCOME_ASSESSMENT_REFERRED
POLICY_NORMS_NOT_MET
BANK_STATEMENT_ASSESSMENT_COMPLETED
ITR_DOCUMENTS_NOT_UPLOADED
PROCEED_TO_ITR_ASSESSMENT
BSA_PAUSED_PENDING_USER_DECISION
FINFORT_STATUS_IN_PROGRESS


These represent IA workflow/business states and should not automatically be interpreted as CAP `EventStatus` values.

---

## Common Configuration Terms

Important runtime configuration concepts include:


PartnerProductConfigurations
capSyncEnabled
enableZenithOrchAPI
iaVersion
VersionBuckets
PerfiosErrorCodeAndConfigMapping
ZenithErrorCodeAndConfigMapping
FinacleErrorCodeAndConfigMapping
MaximusErrorCodeAndConfigMapping


Configuration can change runtime behavior without requiring a code change.

---

## Source Citations

* `README.md:15-31`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/domain/Status.kt:3-44`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/repository/IncomeAssessmentRepository.kt:397-412`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/revamp/versionresolvers/CommonVersionResolver.kt:53-69`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/multibank/service/CapSyncService.kt:189-244`
* `src/main/resources/application.yaml:341-349`
