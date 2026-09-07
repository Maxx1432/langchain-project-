# RAG Index Map

This file is the **first-pass retrieval router** for the Income Assessment Service knowledge base.

Its purpose is to map a developer question to the **most precise knowledge-base chunk first**, before broader retrieval or repository/code search is performed.

The retrieval strategy should prefer:

1. **Specific flow documentation** for journey-specific questions.
2. **Troubleshooting documentation** for incident/debugging questions.
3. **Business logic** for state, guard, and business-rule questions.
4. **Integration documentation** for external API/system questions.
5. **Configuration documentation** for toggle, version, endpoint, retry, or error-mapping questions.
6. **Database/Kafka documentation** for persistence and asynchronous propagation questions.
7. **API contracts** for exact request/response questions.
8. **Glossary** for terminology and concise definitions.
9. **Architecture** for broad structural questions.

---

## 1. Retrieval principles

### 1.1 Prefer the narrowest relevant chunk

Do not retrieve a broad architecture section when a dedicated flow or troubleshooting section directly answers the question.

For example:

* "How does Zenith work?" → `flows/zenith-flow.md`
* "Why did Zenith BSA return 404?" → `10-troubleshooting.md`
* "What is `enableZenithOrchAPI`?" → `09-configuration.md`
* "What is BSA?" → `11-glossary.md`

### 1.2 Distinguish business flow from integration mechanics

Use:

* `income-assessment-flow.md` for the canonical IA lifecycle.
* `perfios-flow.md` for Perfios-specific execution.
* `zenith-flow.md` for Zenith-specific execution.
* `cap-flow.md` for CAP initiation and CAP status synchronization.

Do not use the generic architecture document as the primary source when one of these dedicated flow documents applies.

### 1.3 Distinguish upstream platforms from journey providers

The following architectural distinction is important for retrieval:


Upstream platform/client
    |
    | initiate IA
    v
Income Assessment Service
    |
    | selects/executes IA journey
    +----> Perfios
    |
    +----> Zenith / AA-Orchestrator
    |
    +----> other IA processing paths
    |
    | optional post-processing synchronization
    v
CAP status synchronization


**CAP is an external upstream platform/client that can initiate IA.**

**Perfios and Zenith are external journey/integration providers used by IA after initiation.**

CAP should therefore not be retrieved as a synonym for the Perfios/Zenith journey.

### 1.4 Treat CAP initiation and CAP sync as different interactions

There are two separate CAP-related interactions:


CAP
  |
  | POST /income-assessment-service/v1/initiate-application
  v
IA
  |
  | IA processing / Perfios / Zenith
  v
IA outcome
  |
  | optional CAP status synchronization
  v
CAP


Questions about the first interaction should route to IA initiation/lifecycle documentation.

Questions about the second interaction should route to `flows/cap-flow.md`.

### 1.5 Use code/config as the final verification layer

Knowledge-base documentation is optimized for retrieval and explanation, but exact runtime behavior should be verified against current code and configuration when required.

Preferred source-of-truth hierarchy:

1. Current implementation at the relevant call site.
2. Current configuration consumed by that implementation.
3. Repository models/enums/state-transition logic.
4. Current controllers, services, gateways, producers, and consumers.
5. Tests/stubs.
6. Knowledge-base documentation.
7. Historical README/design notes.

---

# 2. Question → best chunk mapping

| Question pattern                                                | Best file / section                                                                               | Why this chunk first                                                                                                    | Chunking hint                                                      |
| --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| How is the IA service structured?                               | `01-architecture.md` → `## Architecture in one view`, `## Layered component model`                | Explains Controller → Version Resolver → Facade → Service → integrations/persistence.                                   | Keep architecture diagram and layered model together.              |
| What is the overall IA lifecycle?                               | `flows/income-assessment-flow.md` → `## Lifecycle summary`                                        | Canonical end-to-end lifecycle from initiation to terminal outcome.                                                     | Keep lifecycle summary as one chunk.                               |
| What is the main entry point into IA?                           | `flows/income-assessment-flow.md` → initiation section                                            | Establishes `/initiate-application` as the primary IA entry point.                                                      | Keep endpoint + orchestration context together.                    |
| Which APIs are involved in the main IA journey?                 | `flows/income-assessment-flow.md` → `## Main API touchpoints`                                     | Gives the primary endpoint map before diving into vendor-specific flows.                                                | Keep endpoint list together.                                       |
| How does IA choose a version?                                   | `01-architecture.md` → `## Version-routing architecture`                                          | Explains version bucket + resolver + facade strategy.                                                                   | One chunk including the complete routing chain.                    |
| Why did the same request take a different code path?            | `09-configuration.md` → `## Feature-toggle model` + version-routing sections                      | Configuration and version bucket resolution can change runtime behavior.                                                | Retrieve toggle + version routing together when needed.            |
| What is the request/response for `/initiate-application`?       | `03-api-contracts.md` → `### POST /initiate-application`                                          | Exact request/response contract and source citations.                                                                   | Keep complete endpoint subsection intact.                          |
| What does `/validate` return?                                   | `03-api-contracts.md` → `### GET /validate`                                                       | Contains response model and action information.                                                                         | One endpoint chunk.                                                |
| How do `/status` and `/application/status` differ?              | `03-api-contracts.md` → `### GET /status` + `### GET /application/status`                         | Both endpoints are required for an accurate comparison.                                                                 | Merge the two endpoint subsections.                                |
| How does BSA initiation work?                                   | `03-api-contracts.md` → `## BSA initiate API` or `flows/zenith-flow.md` → BSA section             | Contract questions need API docs; execution/debug questions need Zenith flow.                                           | Separate contract and flow retrieval.                              |
| How does Perfios flow work?                                     | `flows/perfios-flow.md`                                                                           | Dedicated Perfios transaction/callback/report lifecycle.                                                                | Prefer entire relevant flow section over generic integration docs. |
| How does Zenith flow work?                                      | `flows/zenith-flow.md`                                                                            | Dedicated Zenith journey/status/report lifecycle.                                                                       | Prefer entire relevant flow section.                               |
| How does CAP interact with IA?                                  | `flows/cap-flow.md`                                                                               | Explicitly documents CAP → IA initiation and IA → CAP status synchronization.                                           | Keep CAP architecture and interaction model together.              |
| Is CAP a journey provider?                                      | `flows/cap-flow.md` → architecture/interaction section                                            | Clarifies CAP is an upstream platform/client, not the Perfios/Zenith journey provider.                                  | Small focused chunk.                                               |
| How does CAP initiate IA?                                       | `flows/cap-flow.md` → CAP initiation section                                                      | Maps CAP → `/initiate-application` → IA lifecycle.                                                                      | Keep initiation endpoint and orchestration context together.       |
| How does IA update CAP status?                                  | `flows/cap-flow.md` → CAP status synchronization section                                          | Explains `CapSyncService`, mapping, request, endpoint, and best-effort behavior.                                        | Keep service + config + status mapping together.                   |
| Why did CAP not receive an updated status?                      | `flows/cap-flow.md` + `10-troubleshooting.md`                                                     | CAP sync requires checking enablement, mapping, invocation, API call, and failure handling.                             | Flow first; troubleshooting second.                                |
| What is `capSyncEnabled`?                                       | `09-configuration.md` → CAP/config section                                                        | Explains how the dynamic toggle affects CAP synchronization.                                                            | Keep toggle resolution path intact.                                |
| What is `capRefId`?                                             | `11-glossary.md` → CAP terms                                                                      | Concise definition and role in CAP synchronization.                                                                     | Glossary chunk.                                                    |
| How are IA statuses mapped to CAP statuses?                     | `flows/cap-flow.md` → status mapping section                                                      | Contains IA status → CAP `EventStatus` mapping.                                                                         | Keep mapping table/list intact.                                    |
| What statuses and guards control business flow?                 | `04-business-logic.md` → `## Core status model`, `## State transition guards`                     | Best source for state names, guards, and business transition rules.                                                     | Two focused chunks.                                                |
| Why was an IA status not updated?                               | `10-troubleshooting.md` → status/state troubleshooting + `04-business-logic.md`                   | Starts with debugging workflow and then verifies state guards/business rules.                                           | Troubleshooting first.                                             |
| Why is the IA stuck in `INCOME_ASSESSMENT_IN_PROGRESS`?         | `10-troubleshooting.md` → stuck-status/callback sections                                          | Designed for reconstructing state progression and async failures.                                                       | Keep decision tree + checklist together.                           |
| Why did a callback not update the application?                  | `10-troubleshooting.md` → callback debugging                                                      | Separates vendor-not-sent, received, event-published, consumer, guard, DB, and next-event failures.                     | Single callback decision-tree chunk.                               |
| How does a late callback behave?                                | `04-business-logic.md` + `10-troubleshooting.md`                                                  | Business logic explains multi-attempt semantics; troubleshooting explains incident reconstruction.                      | Retrieve both when diagnosing a real case.                         |
| Can one IA application have multiple vendor transactions?       | `06-database.md` → application/attempt model + relevant flow                                      | Establishes application vs vendor-attempt identity.                                                                     | Keep identifier and attempt model together.                        |
| Which ID should I use to trace a transaction?                   | `06-database.md` → identifier model                                                               | Distinguishes `incomeAssessmentId`, `applicationReferenceId`, `commonClientTransactionId`, vendor transaction IDs, etc. | One identifier-focused chunk.                                      |
| Which downstream systems does IA call?                          | `05-integrations.md` → `## External systems and their roles`                                      | Maps external systems to their responsibilities.                                                                        | One systems-role chunk.                                            |
| Where is the Perfios endpoint configured?                       | `09-configuration.md` → `## High-impact YAML sections` or `05-integrations.md` → endpoint section | Configuration answers where the value comes from; integration docs explain how it is used.                              | Prefer configuration first for key lookup.                         |
| Why does an external API return 404/405/415?                    | `10-troubleshooting.md` → external API failure section                                            | Provides the diagnostic order: URL/path → method → headers/content type → body → environment/stub.                      | Single troubleshooting chunk.                                      |
| Why did an API return HTML when JSON was expected?              | `08-error-handling.md` + `10-troubleshooting.md`                                                  | Explains primary HTTP failure versus secondary JSON parsing/media-type error.                                           | Retrieve both if RCA is required.                                  |
| How are vendor errors mapped to IA errors?                      | `08-error-handling.md` → `## Error-code mapping through configuration`                            | Connects external response → vendor mapping → IA error → business decision.                                             | Keep mapping chain together.                                       |
| What happens when a vendor API times out?                       | `08-error-handling.md` → retry/error semantics + `10-troubleshooting.md`                          | Explains retryability and impact on state/flow.                                                                         | Error semantics first, troubleshooting second.                     |
| Which config/toggle controls this behavior?                     | `09-configuration.md` → relevant toggle/config family                                             | Configuration is the primary source for runtime behavior changes.                                                       | Retrieve only the relevant config family where possible.           |
| How does dynamic configuration work?                            | `09-configuration.md` → configuration resolution model                                            | Explains context → ConfigFetcher → resolved value → business behavior.                                                  | One complete resolution chunk.                                     |
| How does `iaVersion` affect the flow?                           | `09-configuration.md` + `01-architecture.md`                                                      | Connects dynamic version configuration to `VersionBuckets` and concrete service selection.                              | Retrieve both for complete tracing.                                |
| Where is data stored in MongoDB?                                | `06-database.md` → `## Core collections and entities`                                             | Maps collections to entities and repositories.                                                                          | One collection-focused chunk.                                      |
| What is the main IA application record?                         | `06-database.md` → `## Main application aggregate`                                                | Explains `IncomeAssessmentApplicationDao` and persisted workflow state.                                                 | One aggregate chunk.                                               |
| Why does MongoDB show one state while downstream shows another? | `06-database.md` + `07-kafka-events.md`                                                           | MongoDB and Kafka/downstream propagation are not atomic.                                                                | Retrieve persistence + async propagation together.                 |
| Which Kafka events matter for this journey?                     | `07-kafka-events.md` → `## High-value event IDs`                                                  | Maps important event IDs to workflow stages.                                                                            | One event taxonomy chunk.                                          |
| Which consumer processes this event?                            | `07-kafka-events.md` → `## Consumer landscape`                                                    | Maps event → consumer → processing responsibility.                                                                      | Keep event/consumer mapping together.                              |
| Kafka event exists but application state did not change. Why?   | `07-kafka-events.md` + `10-troubleshooting.md`                                                    | Requires producer → topic → consumer → filter → guard → handler → DB tracing.                                           | Kafka flow first, troubleshooting decision tree second.            |
| Kafka event was not published. Why?                             | `07-kafka-events.md` → producer/debugging section + `08-error-handling.md`                        | Covers producer failure, serialization, routing, and payload-size failures.                                             | Include `RecordTooLargeException` when relevant.                   |
| What does `RecordTooLargeException` mean here?                  | `07-kafka-events.md` → Kafka failure section                                                      | Directly covers oversized event payload and impact.                                                                     | Single focused chunk.                                              |
| What does BSA / CAP sync / version bucket mean?                 | `11-glossary.md` → `## Core business and technical terms`                                         | Concise terminology grounding.                                                                                          | Glossary can be one chunk if within token budget.                  |
| How do I debug Zenith BSA 404 + HTML errors?                    | `10-troubleshooting.md` → Zenith BSA 404 section                                                  | Exact endpoint, method, config, stub, and environment checklist.                                                        | Single troubleshooting chunk.                                      |
| How do I debug a stuck callback?                                | `10-troubleshooting.md` → callback decision tree                                                  | Provides the complete callback-to-state debugging sequence.                                                             | Keep the entire decision tree intact.                              |
| How do I debug a status mismatch?                               | `10-troubleshooting.md` → status mismatch section                                                 | Focuses on state guards, async propagation, and downstream synchronization.                                             | One troubleshooting chunk.                                         |
| How do I investigate an IA production incident?                 | `10-troubleshooting.md` → RCA workflow                                                            | Defines evidence collection and boundary-by-boundary investigation.                                                     | Keep RCA workflow and identifiers together.                        |
| How does Perfios differ from Zenith?                            | `flows/perfios-flow.md` + `flows/zenith-flow.md`                                                  | Both dedicated flow docs are required for an accurate journey comparison.                                               | Retrieve equivalent sections from both.                            |
| How does Perfios flow differ from CAP sync?                     | `flows/perfios-flow.md` + `flows/cap-flow.md`                                                     | Establishes journey-provider flow versus upstream initiation/status synchronization.                                    | Retrieve both flow docs, not glossary alone.                       |

---

# 3. High-value retrieval routes

## 3.1 Initiation questions

For questions involving:

* `/initiate-application`
* who invokes IA
* application creation
* request context
* partner/product resolution
* version selection
* first state

retrieve in this order:


flows/income-assessment-flow.md
        ↓
01-architecture.md
        ↓
03-api-contracts.md
        ↓
09-configuration.md


For CAP-specific initiation:


flows/cap-flow.md
        ↓
flows/income-assessment-flow.md
        ↓
03-api-contracts.md


---

## 3.2 Perfios questions

For Perfios transaction lifecycle:


flows/perfios-flow.md
        ↓
05-integrations.md
        ↓
04-business-logic.md
        ↓
07-kafka-events.md
        ↓
06-database.md


Use `10-troubleshooting.md` when the question is incident-oriented.

High-value concepts:

* `perfiosTransactionId`
* start transaction
* redirect
* upload
* complete transaction
* callback
* callback timeout
* status polling
* report retrieval
* statement validation
* multiple attempts
* late callback
* Kafka event publication

---

## 3.3 Zenith questions

For Zenith/AA journey questions:


flows/zenith-flow.md
        ↓
09-configuration.md
        ↓
05-integrations.md
        ↓
04-business-logic.md
        ↓
07-kafka-events.md


For Zenith BSA API failures:


10-troubleshooting.md
        ↓
05-integrations.md
        ↓
09-configuration.md


High-value concepts:

* `enableZenithOrchAPI`
* `get-journey-link`
* `get-journey-transaction-status`
* `get-analytics-report`
* `initiateBSA`
* `/aa-orch/fiu/api/v1/initiateBSA`
* `ZenithOrchestratorGateway`
* `ZenithErrorCodeAndConfigMapping`

---

## 3.4 CAP questions

CAP questions should be split into **initiation** and **status synchronization**.

### CAP initiation


CAP
 ↓
POST /income-assessment-service/v1/initiate-application
 ↓
IA lifecycle
 ↓
Perfios / Zenith / other IA path


Primary retrieval:


flows/cap-flow.md
        ↓
flows/income-assessment-flow.md
        ↓
03-api-contracts.md


### CAP status synchronization


IA outcome
 ↓
capSyncEnabled
 ↓
CapSyncService
 ↓
IA status → CAP EventStatus mapping
 ↓
CAP update-status API


Primary retrieval:


flows/cap-flow.md
        ↓
09-configuration.md
        ↓
05-integrations.md
        ↓
08-error-handling.md


Important terms:

* `CapSyncService`
* `CapSyncServiceFacade`
* `capSyncEnabled`
* `capRefId`
* `EventStatus`
* `eventStatus`
* CAP update-status
* best-effort synchronization

---

# 4. State and status retrieval

For state-related questions, retrieve:


04-business-logic.md
        ↓
06-database.md
        ↓
07-kafka-events.md
        ↓
10-troubleshooting.md


Use this route for questions such as:

* Why is status stuck?
* Why did status revert?
* Why was status not updated?
* Which guard prevented a transition?
* What happens after callback?
* Why is MongoDB state different from downstream state?
* Why did a late callback change the application?
* Which state is terminal?

Key guards include:

* `canUpdateApplicationStatusInDb()`
* `canUpdateApplicationStatusInPerfiosCallback()`
* `canProceedToDocumentsUpload()`
* `canProceedToItrDocumentsUpload(...)`
* `canProceedToItrAssessment()`
* `canProceedForLinkGeneration()`
* `canProceedForStartProcess()`
* `canProceedForStartTransaction()`
* `isTerminalItrStatus()`

---

# 5. Callback debugging route

For any callback-related incident, retrieve:


10-troubleshooting.md
        ↓
04-business-logic.md
        ↓
07-kafka-events.md
        ↓
06-database.md
        ↓
08-error-handling.md


The debugging sequence should be:


Did vendor send callback?
        ↓
Did IA receive callback?
        ↓
Was callback accepted?
        ↓
Was callback event published?
        ↓
Did Kafka consumer receive it?
        ↓
Did filtering/transformation allow processing?
        ↓
Did state guard allow processing?
        ↓
Was MongoDB updated?
        ↓
Did next API/event execute?
        ↓
Did downstream processing succeed?


Do not treat "callback received" as equivalent to "IA state updated".

---

# 6. Multi-attempt and late-callback route

When a case contains retries, repeated user attempts, or contradictory status history, retrieve:


06-database.md
        ↓
04-business-logic.md
        ↓
10-troubleshooting.md
        ↓
07-kafka-events.md


Always correlate:

* `incomeAssessmentId`
* `applicationReferenceId`
* `commonClientTransactionId`
* vendor transaction ID
* attempt metadata
* callback timestamp
* retry count
* current status
* `statusType`
* event ID

Important invariant:

> One IA application can have multiple vendor transaction attempts.

A late callback must therefore be correlated to the **correct vendor transaction/attempt**, not merely the application.

---

# 7. Kafka retrieval route

For Kafka questions:


07-kafka-events.md
        ↓
10-troubleshooting.md
        ↓
06-database.md
        ↓
08-error-handling.md


Trace:


Producer
 ↓
Event object
 ↓
Serialization
 ↓
Topic
 ↓
Consumer group
 ↓
Consumer
 ↓
Filter / transformer
 ↓
Business handler
 ↓
State guard
 ↓
MongoDB / external API
 ↓
Next event


Important invariant:

> Kafka publication success does not mean consumer processing success, MongoDB mutation success, downstream API success, or final IA success.

---

# 8. External API failure retrieval route

For 4xx/5xx/network failures:


10-troubleshooting.md
        ↓
05-integrations.md
        ↓
08-error-handling.md
        ↓
09-configuration.md


For `404`:


HTTP status
 ↓
Base URL
 ↓
Exact path
 ↓
HTTP method
 ↓
Headers
 ↓
Content type
 ↓
Request body
 ↓
Environment
 ↓
Stub/mock


For `405`:


Verify HTTP method first.


For `415`:


Verify Content-Type + request body + gateway expectations.


For HTML returned where JSON was expected:


Primary failure:
HTTP endpoint/path/environment problem

Secondary failure:
JSON parsing / UnsupportedMediaTypeException


Always diagnose the primary HTTP failure before treating the parsing exception as the root cause.

---

# 9. Configuration retrieval route

For questions involving toggles, endpoint configuration, version routing, retries, or error mappings:


09-configuration.md
        ↓
01-architecture.md
        ↓
05-integrations.md
        ↓
04-business-logic.md


Use this conceptual chain:


Request context
 ↓
Partner / Product / Journey context
 ↓
Configuration key
 ↓
ConfigFetcher / configuration source
 ↓
Resolved value
 ↓
Caller interpretation
 ↓
Runtime branch
 ↓
DB / Kafka / external side effect


Important configuration families include:

* `PartnerProductConfigurations`
* `IncomeAssessment`
* `Callback`
* `VersionBuckets`
* `PerfiosErrorCodeAndConfigMapping`
* `ZenithErrorCodeAndConfigMapping`
* `FinacleErrorCodeAndConfigMapping`
* `MaximusErrorCodeAndConfigMapping`

High-impact toggles include:

* `enableZenithOrchAPI`
* `iaVersion`
* `retryErrorMsgScreen`
* `fetchAndSaveRawStatement`
* `fetchAndSaveRawStatementForIAFailed`
* `enablePopUpForRetry`
* `limitMultipleRetryAttempt`
* `autoRedirectScreen`
* `capSyncEnabled`

---

# 10. API contract retrieval

For exact request/response questions, prefer:


03-api-contracts.md


Do not use flow documentation as the primary source when the question specifically asks for:

* request JSON
* response JSON
* request model
* response model
* HTTP method
* endpoint path
* field names
* validation rules

Keep endpoint subsections intact with:

* HTTP method
* endpoint path
* request model
* request sample
* response model
* response sample
* error response
* source citations

---

# 11. Database retrieval

For persistence questions:


06-database.md
        ↓
04-business-logic.md
        ↓
07-kafka-events.md


Highest-priority workflow collection:


incomeAssessmentApplications


Important identity distinction:


incomeAssessmentId
        ≠
applicationReferenceId
        ≠
commonClientTransactionId
        ≠
vendor transaction ID


Do not assume these identifiers are interchangeable.

For an incident, retrieve the persisted application state first and then correlate it with events, vendor transactions, and downstream calls.

---

# 12. Error-handling retrieval

For error/RCA questions:


08-error-handling.md
        ↓
10-troubleshooting.md
        ↓
09-configuration.md
        ↓
05-integrations.md


Use the following chain:


External/API/Kafka/Mongo error
        ↓
Gateway / consumer / persistence layer
        ↓
Error mapping
        ↓
IA error
        ↓
Business decision
        ↓
State / event / response
        ↓
Downstream impact


Do not assume that an exception in logs means the overall user journey failed.

Check whether the error was:

* propagated
* mapped
* retried
* suppressed
* handled by `onErrorResume`
* transformed by `onErrorMap`
* bypassed by a state guard
* treated as best-effort

---

# 13. Troubleshooting retrieval

`10-troubleshooting.md` is the primary retrieval source whenever the user asks:

* "Why did this fail?"
* "Why is this stuck?"
* "Why didn't this update?"
* "Where should I debug?"
* "What should I check?"
* "What could be the RCA?"
* "How do I trace this incident?"
* "Why is Mongo different from Kafka/downstream?"
* "Why did the callback not process?"
* "Why did the API return 404/405/415?"

General troubleshooting model:


Symptom
 ↓
Identify application
 ↓
Identify attempt
 ↓
Identify expected transition
 ↓
Inspect current state
 ↓
Check configuration
 ↓
Check external integration
 ↓
Check Kafka
 ↓
Check persistence
 ↓
Check downstream side effect
 ↓
Find first boundary where actual behavior diverged


The key RCA question is:

> **At which step did the actual journey first diverge from the expected flow?**

---

# 14. Glossary retrieval

Use `11-glossary.md` for short terminology questions:

* IA
* IA Application
* IA Attempt
* Assessment Medium
* Perfios
* Zenith / AA-Orchestrator
* BSA
* CAP
* CAP Initiation
* CAP Sync
* `capRefId`
* CAP `EventStatus`
* `eventStatus`
* Callback
* Callback Timeout
* Late Callback
* Vendor Transaction ID
* `incomeAssessmentId`
* `applicationReferenceId`
* `commonClientTransactionId`
* `serviceRequestId`
* Partner
* Product
* `PartnerProductConfiguration`
* `capSyncEnabled`
* `enableZenithOrchAPI`
* IA Version
* Version Bucket
* `ConfigFetcher`
* Kafka Event
* State Guard
* Best-Effort Side Effect
* Terminal State

Prefer glossary retrieval only when the question is definitional. For behavior questions, retrieve the relevant flow/business/configuration document instead.

---

# 15. Primary code anchors for contract verification

Use current repository code to verify exact API contracts, implementation behavior, and runtime routing.

### Core controllers

* `src/main/kotlin/com/axis/lending/incomeassesmentservice/controller/IncomeAssessmentApplicationController.kt`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/controller/InitiateBsaController.kt`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/bankStatement/controller/BankStatementController.kt`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/itr/controller/ItrController.kt`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/controller/IAConsolidationController.kt`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/finfort/controller/FinFortController.kt`

### Important orchestration/configuration anchors

* `IncomeAssessmentApplicationServiceV3.kt`
* `CommonVersionResolver.kt`
* `ConfigFetcher.kt`
* `PartnerProductConfigurations`
* `CapSyncService.kt`
* `CapSyncServiceFacade.kt`
* `ZenithOrchestratorGateway`
* `IncomeAssessmentApplicationDao`
* `IncomeAssessmentRepository`
* `IncomeAssessmentApplicationEvent`
* `IncomeAssessmentApplicationEventProducer`

**Source citations**

* `src/main/kotlin/com/axis/lending/incomeassesmentservice/controller/IncomeAssessmentApplicationController.kt:44-395`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/controller/InitiateBsaController.kt:54-83`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/bankStatement/controller/BankStatementController.kt:27-67`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/itr/controller/ItrController.kt:28-91`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/controller/IAConsolidationController.kt:30-159`
* `src/main/kotlin/com/axis/lending/incomeassesmentservice/finfort/controller/FinFortController.kt:41-117`

---

# 16. Knowledge-base file map

| File                              | Primary question answered                                             |
| --------------------------------- | --------------------------------------------------------------------- |
| `01-architecture.md`              | **How is IA structured?**                                             |
| `03-api-contracts.md`             | **What are the exact APIs and payloads?**                             |
| `04-business-logic.md`            | **Why does IA transition between states?**                            |
| `05-integrations.md`              | **How does IA communicate with external systems?**                    |
| `06-database.md`                  | **Where is workflow state stored and how is it identified?**          |
| `07-kafka-events.md`              | **How does asynchronous workflow propagation work?**                  |
| `08-error-handling.md`            | **How are failures translated, retried, suppressed, and propagated?** |
| `09-configuration.md`             | **Which configuration controls runtime behavior?**                    |
| `10-troubleshooting.md`           | **Where and how should an incident be debugged?**                     |
| `11-glossary.md`                  | **What does a domain/technical term mean?**                           |
| `flows/income-assessment-flow.md` | **What is the canonical IA lifecycle?**                               |
| `flows/perfios-flow.md`           | **How does the Perfios journey execute?**                             |
| `flows/zenith-flow.md`            | **How does the Zenith journey execute?**                              |
| `flows/cap-flow.md`               | **How does CAP initiate IA and receive synchronized IA status?**      |

---

# 17. Cross-document retrieval rules

Some questions require multiple documents.

### "Why did my status get stuck?"

Retrieve:


10-troubleshooting.md
04-business-logic.md
06-database.md
07-kafka-events.md


### "Why did Perfios callback not complete the journey?"

Retrieve:


flows/perfios-flow.md
10-troubleshooting.md
07-kafka-events.md
06-database.md


### "Why did Zenith BSA fail?"

Retrieve:


flows/zenith-flow.md
10-troubleshooting.md
05-integrations.md
09-configuration.md
08-error-handling.md


### "Why didn't CAP receive the IA result?"

Retrieve:


flows/cap-flow.md
09-configuration.md
08-error-handling.md
05-integrations.md
10-troubleshooting.md


### "Why did CAP initiation fail?"

Retrieve:


flows/cap-flow.md
flows/income-assessment-flow.md
03-api-contracts.md
10-troubleshooting.md


### "Why is MongoDB correct but downstream status wrong?"

Retrieve:


06-database.md
07-kafka-events.md
10-troubleshooting.md
08-error-handling.md


### "Why did the same application have contradictory vendor results?"

Retrieve:


06-database.md
04-business-logic.md
flows/perfios-flow.md or flows/zenith-flow.md
10-troubleshooting.md
07-kafka-events.md


---

# 18. Core retrieval invariants

The following concepts should be preserved during retrieval and should not be collapsed into broader synonyms:

1. `/initiate-application` is the primary IA entry point.
2. CAP is an external upstream platform/client that can initiate IA.
3. CAP is **not** a Perfios/Zenith journey provider.
4. Perfios and Zenith are external journey/integration providers used by IA.
5. CAP initiation and CAP status synchronization are separate interactions.
6. CAP status synchronization occurs from IA back to CAP after relevant IA processing/state transitions.
7. `capSyncEnabled` controls whether CAP synchronization is enabled.
8. IA `status` and CAP `EventStatus` are different concepts.
9. `eventStatus` should not automatically be treated as the primary IA business status.
10. One IA application can contain multiple vendor transaction attempts.
11. `incomeAssessmentId`, `applicationReferenceId`, `commonClientTransactionId`, and vendor transaction IDs are distinct identifiers.
12. Callback receipt does not guarantee callback processing.
13. Kafka publication does not guarantee consumer processing.
14. MongoDB persistence and downstream propagation are not atomic.
15. Link generation does not mean the external journey completed.
16. Vendor transaction success does not automatically mean IA business success.
17. Configuration can change runtime behavior without a code change.
18. Configured retry does not mean every error is retryable.
19. An exception in logs does not automatically mean the overall journey failed.
20. Best-effort side effects must be distinguished from business-critical state transitions.
21. External HTTP failures should be diagnosed at the HTTP boundary before secondary parsing errors.
22. Current code and runtime configuration take precedence over historical documentation.

---

# 19. Preferred RAG mental model

When answering an IA developer question, reconstruct the smallest useful chain:


Question
 ↓
Business concept / API / flow / incident
 ↓
Most specific knowledge-base chunk
 ↓
Supporting cross-document chunk(s)
 ↓
Code anchor
 ↓
Configuration
 ↓
Runtime state / Kafka / Mongo / external API evidence
 ↓
Answer


For incident questions, use:


Symptom
 ↓
Application identity
 ↓
Attempt identity
 ↓
Expected transition
 ↓
Actual transition
 ↓
First divergent boundary
 ↓
Root cause
 ↓
State/event/downstream impact


The goal of retrieval is not merely to find related text.

The goal is to identify the **smallest set of authoritative context required to reconstruct the correct IA behavior**.
