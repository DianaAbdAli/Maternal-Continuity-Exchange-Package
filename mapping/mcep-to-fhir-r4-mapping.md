# MCEP to FHIR R4 mapping matrix

## Subject

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Subject | personId | MCEP-PAT-0001 | Patient | identifier | Direct | Primary patient identifier |
| Subject | localIds.system | hospital-mrn | Patient | identifier.system | Direct | Local identifier system |
| Subject | localIds.value | WH-204583 | Patient | identifier.value | Direct | Local MRN value |
| Subject | fullName | Sara Ahmed | Patient | name.text | Direct | Human-readable name; could later be decomposed into given/family if structured name parts are available |
| Subject | dateOfBirth | 1992-08-14 | Patient | birthDate | Direct | Date of birth |
| Subject | sex | female | Patient | gender | Approximate | Administrative gender in base FHIR R4 |
| Subject | contactInfo.phone | +971-50-555-0184 | Patient | telecom | Direct | Represent through Patient.telecom with system = phone and the corresponding contact value. |
| Subject | nationality | UAE | Patient | extension | Requires extension/profile | Not a core Patient field in base R4 |
| Subject | residenceRegion | Dubai | Patient | address or extension | Needs decision | Depends on how region is represented |

## Maternal episode context

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Maternal episode context | episodeId | MCEP-EP-2025-GDM-001 | EpisodeOfCare | identifier | Best-fit direct | Best longitudinal fit for a pregnancy pathway spanning multiple encounters |
| Maternal episode context | pregnancyStatus | delivered | Observation or derived episode status | Observation.valueCodeableConcept or derived status | Needs decision | In this example, `delivered` is more likely derived from delivery event plus pregnancy outcome than represented as one stable base R4 field |
| Maternal episode context | careStage | postpartum | Extension/profile or derived context | extension or derived from related resources | Requires extension/profile | Maternal stage is analytically important but not a standard single base R4 field |
| Maternal episode context | estimatedDueDate | 2026-04-05 | Observation | valueDateTime | Direct | Best represented as an estimated date of delivery observation |
| Maternal episode context | deliveryDate | 2026-03-30 | Procedure or Observation | performedDateTime or valueDateTime | Needs decision | Depends on whether delivery is modeled as a procedure/event or as a summary observation |
| Maternal episode context | pregnancyOutcome | live birth | Observation or delivery-related event summary | valueCodeableConcept or derived | Needs decision | May be represented through outcome-related observation or derived from delivery/birth event context rather than a single fixed base field |
| Maternal episode context | managingOrganization | Example Women and Birth Hospital | EpisodeOfCare | managingOrganization | Direct | Clean fit for organization responsible for the ongoing maternal episode |

Note: Unlike Subject, maternal episode context does not map cleanly to a single FHIR R4 resource. It is better understood as a longitudinal context layer assembled from EpisodeOfCare plus selected clinical event and observation resources.

## Clinical summary

### Conditions

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Clinical summary.conditions | conditionId | COND-001 | Condition | identifier | Direct | Local identifier for the condition entry |
| Clinical summary.conditions | code | gestational-diabetes-mellitus | Condition | code | Direct in structure | Logical condition code; terminology binding would be refined later |
| Clinical summary.conditions | display | Gestational diabetes mellitus | Condition | code.text or coding.display | Direct | Human-readable condition label |
| Clinical summary.conditions | clinicalStatus | resolved | Condition | clinicalStatus | Direct | Pregnancy-specific condition has resolved after delivery |
| Clinical summary.conditions | verificationStatus | confirmed | Condition | verificationStatus | Direct | Diagnosis is clinically confirmed |
| Clinical summary.conditions | onsetDate | 2025-11-18 | Condition | onsetDateTime | Direct | Date condition was first recognized |
| Clinical summary.conditions | stageOfRelevance | postpartum | Condition | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 condition field |
| Clinical summary.conditions | recordedDate | 2025-11-18 | Condition | recordedDate | Direct | Date condition was entered in the source record |
| Clinical summary.conditions | recorder | obstetric-team | Condition | recorder | Best-fit direct | Best represented as `Condition.recorder`; provenance may additionally support authorship or traceability context |
| Clinical summary.conditions | note | Pregnancy complicated by GDM requiring active monitoring during pregnancy. | Condition | note | Direct | Narrative clarification supporting continuity |

### Obstetric history

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Clinical summary.obstetricHistory | gravidity | 1 | Observation | valueInteger | Best-fit direct | Gravidity is best represented as an observation in the absence of a single core summary element in Patient |
| Clinical summary.obstetricHistory | parity | 1 | Observation | valueInteger | Best-fit direct | Parity is best represented as an observation or pregnancy-history summary element carried as an observation |
| Clinical summary.obstetricHistory | historyNote | This is the first documented pregnancy and it was complicated by gestational diabetes mellitus. | Composition | section.text | Best-fit direct | Best treated as narrative longitudinal history summary unless a more structured obstetric history profile is defined |

Note: Obstetric history can be partly structured through observation-based representation, but concise longitudinal history summaries may still require narrative support unless a dedicated maternity profile constrains these elements more tightly.

### Key observations

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Clinical summary.keyObservations | observationId | OBS-001 | Observation | identifier | Direct | Local identifier for the observation entry |
| Clinical summary.keyObservations | code | body-mass-index | Observation | code | Direct in structure | Logical observation code; terminology binding would be refined later |
| Clinical summary.keyObservations | display | Body mass index | Observation | code.text or coding.display | Direct | Human-readable observation label |
| Clinical summary.keyObservations | value | 29.4 | Observation | valueQuantity.value | Direct | Numeric observation value |
| Clinical summary.keyObservations | unit | kg/m2 | Observation | valueQuantity.unit | Direct | Unit of measure for BMI |
| Clinical summary.keyObservations | effectiveDate | 2026-03-30 | Observation | effectiveDateTime | Direct | Date the observation was clinically relevant or measured |
| Clinical summary.keyObservations | interpretation | borderline | Observation | interpretation | Best-fit direct | Interpretation can be represented, though exact coding would need refinement later |
| Clinical summary.keyObservations | stageOfRelevance | postpartum | Observation | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 observation field |
| Clinical summary.keyObservations | recordedDate | 2026-03-30 | Observation | issued | Approximate | `issued` is the closest base R4 timing field, though not identical to source recorded date |
| Clinical summary.keyObservations | recorder | obstetric-team | Observation | performer | Best-fit direct | Best-fit source attribution at observation level, though provenance may also support this |
| Clinical summary.keyObservations | note | Postpartum BMI remains relevant to metabolic follow-up planning. | Observation | note | Direct | Narrative clarification supporting continuity use |
Note: Key observations map relatively well to FHIR Observation, but some continuity-oriented fields such as stage relevance and source recording context may still require extension-level treatment or supporting provenance.

### Key results

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Clinical summary.keyResults | resultId | RES-001 | Observation | identifier | Direct | Local identifier for the result entry |
| Clinical summary.keyResults | code | oral-glucose-tolerance-test | Observation | code | Direct in structure | Logical result code; terminology binding would be refined later |
| Clinical summary.keyResults | display | Oral glucose tolerance test in pregnancy | Observation | code.text or coding.display | Direct | Human-readable result label |
| Clinical summary.keyResults | value | abnormal | Observation | valueCodeableConcept | Best-fit direct | In this example the result is represented interpretively rather than as a numeric measurement |
| Clinical summary.keyResults | unit | n/a | Observation | valueQuantity.unit or not used | Needs decision | No meaningful unit is needed when the result is represented as a qualitative abnormal finding |
| Clinical summary.keyResults | effectiveDate | 2025-11-18 | Observation | effectiveDateTime | Direct | Date the result became clinically relevant or was produced |
| Clinical summary.keyResults | resultType | laboratory | Observation | category | Best-fit direct | Laboratory is better represented through Observation.category than through a dedicated base R4 result-type field |
| Clinical summary.keyResults | interpretation | abnormal | Observation | interpretation | Direct | Fits standard result interpretation logic |
| Clinical summary.keyResults | referenceRange | Pregnancy diagnostic thresholds | Observation | referenceRange | Approximate | Base FHIR expects structured range components, so narrative threshold wording is only an approximate fit unless further structured |
| Clinical summary.keyResults | stageOfRelevance | postpartum | Observation | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 observation field |
| Clinical summary.keyResults | recordedDate | 2025-11-18 | Observation | issued | Approximate | `issued` is the closest base R4 timing field, though not identical to source recorded date |
| Clinical summary.keyResults | recorder | obstetric-team | Observation | performer | Best-fit direct | Best-fit source attribution at result level, though provenance may also support this |
| Clinical summary.keyResults | note | Diagnostic pregnancy glucose result supporting the diagnosis of gestational diabetes mellitus. | Observation | note | Direct | Narrative clarification supporting continuity use |

Note: Key results map well to FHIR Observation, but qualitative result representation, maternal stage relevance, and source-recording context may still require profiling choices or supporting provenance.

### Medications

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Clinical summary.medications | medicationId | MED-001 | MedicationStatement | identifier | Direct | Local identifier for the medication entry |
| Clinical summary.medications | code | insulin | MedicationStatement | medicationCodeableConcept | Best-fit direct | Logical medication code; terminology binding would be refined later |
| Clinical summary.medications | display | Insulin therapy during pregnancy | MedicationStatement | medicationCodeableConcept.text or coding.display | Direct | Human-readable medication label |
| Clinical summary.medications | status | stopped | MedicationStatement | status | Direct | Medication use stopped after delivery |
| Clinical summary.medications | dose | Pregnancy regimen per source record | MedicationStatement | dosage.text | Best-fit direct | Free-text dose/regimen summary is better represented as dosage text unless more structured dose data are available |
| Clinical summary.medications | route | subcutaneous | MedicationStatement | dosage.route | Direct | Route of administration |
| Clinical summary.medications | frequency | as prescribed | MedicationStatement | dosage.text or dosage.timing | Needs decision | Free-text frequency is best-fit as dosage text unless structured timing is available |
| Clinical summary.medications | startDate | 2025-11-20 | MedicationStatement | effectivePeriod.start | Best-fit direct | Start of medication use period |
| Clinical summary.medications | endDate | 2026-03-30 | MedicationStatement | effectivePeriod.end | Best-fit direct | End of medication use period at delivery/postpartum transition |
| Clinical summary.medications | stageOfRelevance | postpartum | MedicationStatement | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 medication field |
| Clinical summary.medications | recordedDate | 2026-03-30 | MedicationStatement | dateAsserted | Best-fit direct | Closest base R4 field for when the medication statement was asserted in the source context |
| Clinical summary.medications | recorder | obstetric-team | MedicationStatement | informationSource | Best-fit direct | Best-fit source attribution for the medication statement, though provenance may also support this |
| Clinical summary.medications | note | Pregnancy-related insulin treatment stopped after delivery. | MedicationStatement | note | Direct | Narrative clarification supporting continuity use |

Note: Medications in this continuity package are best represented as MedicationStatement when the aim is to preserve medication use history across transition. If the source artifact were primarily an active order or prescribing instruction, MedicationRequest might become the better mapping target.

### Allergies

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Clinical summary.allergies | allergyId | not present in example | AllergyIntolerance | identifier | Direct if used | Local identifier would map directly when an allergy entry exists |
| Clinical summary.allergies | code | not present in example | AllergyIntolerance | code | Direct if used | Allergy or substance code would map directly when present |
| Clinical summary.allergies | display | not present in example | AllergyIntolerance | code.text or coding.display | Direct if used | Human-readable allergy label |
| Clinical summary.allergies | status | not present in example | AllergyIntolerance | clinicalStatus or verificationStatus | Needs decision | Exact mapping depends on whether MCEP status reflects clinical activity or record validity |
| Clinical summary.allergies | criticality | not present in example | AllergyIntolerance | criticality | Direct if used | Strong fit when allergy criticality is known |
| Clinical summary.allergies | reaction | not present in example | AllergyIntolerance | reaction.manifestation or reaction.description | Best-fit direct | Depends on whether reaction is structured or narrative |
| Clinical summary.allergies | recordedDate | not present in example | AllergyIntolerance | recordedDate | Direct if used | Clean fit when source recorded date is available |
| Clinical summary.allergies | recorder | not present in example | AllergyIntolerance | recorder | Best-fit direct | Source attribution fits reasonably at resource level, with provenance as support if needed |
| Clinical summary.allergies | status | not present in example | AllergyIntolerance | clinicalStatus and/or verificationStatus | Split mapping | Activity-style status maps toward `clinicalStatus`, while record-validity states such as `entered-in-error` map toward `verificationStatus` |

Note: The frozen postpartum GDM example contains no allergy entries, so this mapping block is structural rather than instance-populated. When allergy data are present, AllergyIntolerance is the appropriate primary FHIR R4 target.

### Procedures

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Clinical summary.procedures | procedureId | not present in example | Procedure | identifier | Direct if used | Local identifier would map directly when a procedure entry exists |
| Clinical summary.procedures | code | not present in example | Procedure | code | Direct if used | Procedure or intervention code would map directly when present |
| Clinical summary.procedures | display | not present in example | Procedure | code.text or coding.display | Direct if used | Human-readable procedure label |
| Clinical summary.procedures | status | not present in example | Procedure | status | Direct if used | Strong fit for procedure completion state |
| Clinical summary.procedures | performedDate | not present in example | Procedure | performedDateTime | Direct if used | Clean fit when a single performed date or date-time is available |
| Clinical summary.procedures | outcome | not present in example | Procedure | outcome | Direct if used | Fits when procedure outcome is represented as a summary result |
| Clinical summary.procedures | stageOfRelevance | not present in example | Procedure | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 procedure field |
| Clinical summary.procedures | recordedDate | not present in example | Procedure or Provenance | Provenance.recorded or supporting provenance | Needs decision | No clean core Procedure element captures source recorded date directly, so provenance is the better fit |
| Clinical summary.procedures | recorder | not present in example | Procedure | recorder | Direct if used | Source attribution can be represented at resource level, with provenance as additional support if needed |
| Clinical summary.procedures | note | not present in example | Procedure | note | Direct if used | Narrative clarification when structured procedure data are incomplete |

Note: The frozen postpartum GDM example contains no procedure entries, so this mapping block is structural rather than instance-populated. When procedure data are present, Procedure is the appropriate primary FHIR R4 target, while source recording context may still need supporting provenance.

### Documents

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Clinical summary.documents | documentId | DOC-001 | DocumentReference | identifier | Direct | Local identifier for the document entry |
| Clinical summary.documents | documentType | discharge-summary | DocumentReference | type | Best-fit direct | Best represented through document type coding in DocumentReference |
| Clinical summary.documents | title | Postpartum discharge summary | DocumentReference | content.attachment.title | Best-fit direct | Best fit for the human-readable document title |
| Clinical summary.documents | status | current | DocumentReference | status | Direct | Clean fit for this example; broader MCEP status logic may not always map one-to-one across all cases |
| Clinical summary.documents | authorOrganization | Example Women and Birth Hospital | DocumentReference | author or custodian | Needs decision | Depends on whether the organization is treated as document author, custodian, or both |
| Clinical summary.documents | creationDate | 2026-03-31 | DocumentReference | content.attachment.creation | Best-fit direct | Closest fit for the document creation date itself |
| Clinical summary.documents | stageOfRelevance | postpartum | DocumentReference | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 document-reference field |
| Clinical summary.documents | language | en | DocumentReference | content.attachment.language | Direct | Direct fit for document language |
| Clinical summary.documents | linkReference | doc://postpartum-discharge-summary-001 | DocumentReference | content.attachment.url | Direct | Direct fit for source document link or pointer |
| Clinical summary.documents | note | Contains discharge recommendations and postpartum follow-up instructions. | DocumentReference | description | Best-fit direct | Best treated as a short descriptive summary of the referenced document |

Note: Documents in this continuity package are best represented as DocumentReference when the aim is to preserve traceable access to supporting records across transition. If the artifact were intended to carry the full authored document structure itself, Composition would become the more appropriate primary target.

## Risk interpretation

### Risk items

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Risk interpretation.riskItems | riskId | RISK-001 | RiskAssessment | identifier | Direct | Local identifier for the interpreted risk entry |
| Risk interpretation.riskItems | riskType | metabolic | RiskAssessment | extension | Requires extension/profile | Risk category is analytically important in MCEP but does not have a single clean base R4 element in RiskAssessment |
| Risk interpretation.riskItems | display | Postpartum dysglycaemia and future diabetes risk after GDM | RiskAssessment | prediction.outcome | Best-fit direct | Best fit for the named risk being assessed |
| Risk interpretation.riskItems | riskLevel | moderate | RiskAssessment | prediction.qualitativeRisk | Best-fit direct | Best fit for interpreted risk severity or concern level |
| Risk interpretation.riskItems | basis | Confirmed gestational diabetes mellitus during pregnancy with abnormal glucose testing and pregnancy treatment history. | RiskAssessment | basis | Needs decision | In this example the basis is expressed narratively; a full FHIR representation would more likely combine linked supporting references with explanatory text rather than map this sentence directly to a single base element |
| Risk interpretation.riskItems | timeHorizon | long-term | RiskAssessment | prediction.whenRange or extension | Needs decision | Categorical horizon such as `long-term` does not map neatly unless represented through a structured time range or extension |
| Risk interpretation.riskItems | clinicalMeaning | The woman remains at elevated risk of postpartum dysglycaemia and future type 2 diabetes, so follow-up must continue after discharge rather than ending with the birth episode. | RiskAssessment | note or prediction.rationale | Best-fit direct | Best represented as explanatory interpretation attached to the assessed risk |
| Risk interpretation.riskItems | recommendedAttention | planned-follow-up | RiskAssessment | mitigation | Best-fit direct | Best fit when represented as risk-response or mitigation guidance |
| Risk interpretation.riskItems | dueBy | 2026-05-11 | RiskAssessment | extension or linked follow-up timing | Needs decision | This behaves more like action timing than intrinsic risk timing, so extension or linkage to follow-up planning may be needed |
| Risk interpretation.riskItems | stageOfRelevance | postpartum | RiskAssessment | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 risk-assessment field |
| Risk interpretation.riskItems | recordedDate | 2026-03-31 | RiskAssessment | occurrenceDateTime | Best-fit direct | Best fit for when the risk assessment was recorded or made |
| Risk interpretation.riskItems | recorder | obstetric-team | RiskAssessment | performer | Direct | Clean fit for the actor performing or authoring the risk assessment |
| Risk interpretation.riskItems | note | Risk should remain visible to primary care at the time of handoff. | RiskAssessment | note | Direct | Narrative clarification supporting continuity use |

Note: Risk items in MCEP are best represented through RiskAssessment because they express interpreted future-oriented clinical significance rather than raw findings alone. Some continuity-oriented fields, especially risk category, maternal stage relevance, and follow-up-oriented timing, may still require extension-level treatment or linkage to follow-up planning.

### Risk summary

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Risk interpretation.riskSummary | riskSummary | Pregnancy was complicated by gestational diabetes mellitus. Although the pregnancy has ended, metabolic follow-up remains necessary in primary care because postpartum dysglycaemia and longer-term diabetes risk remain clinically relevant. | Composition | section.text | Best-fit direct | Best treated as a narrative synthesis of the interpreted risk section rather than as a separate structured risk resource |

Note: `riskSummary` is best understood as narrative synthesis that supports rapid interpretation of structured risk entries, rather than as a separate computable risk artifact.

## Handoff context

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Handoff context | handoffReason | Postpartum discharge after pregnancy complicated by gestational diabetes mellitus, with need for ongoing metabolic follow-up in primary care. | Task | reasonCode or description | Best-fit direct | Best represented as the clinical reason motivating the handoff; may also contribute to narrative handoff description |
| Handoff context | sendingContext | Obstetric or postpartum hospital team | Task | requester | Best-fit direct | Best fit if the sending side is represented as an organization, team, or practitioner role reference rather than free text |
| Handoff context | receivingContext | Primary care physician | Task | owner or performerType | Needs decision | `owner` fits when a specific receiving actor is assigned; `performerType` fits better when the target is a role such as primary care physician |
| Handoff context | transitionType | obstetric-to-primary-care | Task | code or extension | Needs decision | The handoff classification is important in MCEP but does not have a single stable base R4 element unless constrained through profiling |
| Handoff context | urgency | routine | Task | priority | Direct | Clean fit for handoff urgency or priority |
| Handoff context | handoffSummary | The woman is being discharged from obstetric care after delivery. Because the pregnancy was complicated by gestational diabetes mellitus, postpartum glucose follow-up and continued metabolic risk awareness must be preserved in primary care. | Task | description | Best-fit direct | Best represented as a concise narrative description of the handoff context |

Note: Handoff context does not map perfectly to a single base FHIR R4 resource, but Task is the best fit when the transition is treated as an explicit coordination or transfer artifact. A full Task instance would still require operational fields such as `status` and `intent`, which are not currently explicit MCEP fields and would need to be supplied by implementation rules or profiling.

## Follow-up plan and responsibility

### Follow-up items

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Follow-up plan and responsibility.followUpItems | followUpId | FU-001 / FU-002 | Task | identifier | Direct | Local identifier for the follow-up entry |
| Follow-up plan and responsibility.followUpItems | actionType | laboratory-test / primary-care-follow-up | Task | code | Best-fit direct | Best fit for the type of follow-up action being requested or expected |
| Follow-up plan and responsibility.followUpItems | display | Postpartum glucose reassessment / Primary care review of postpartum metabolic risk | Task | description | Best-fit direct | Human-readable description of the follow-up action |
| Follow-up plan and responsibility.followUpItems | status | requested | Task | status | Best-fit direct | Closest fit for the current action state, though exact status vocabulary may need profiling alignment |
| Follow-up plan and responsibility.followUpItems | dueBy | 2026-05-11 | Task | restriction.period.end or executionPeriod.end | Needs decision | Best fit depends on whether due date is modeled as a requested deadline or an execution timing boundary |
| Follow-up plan and responsibility.followUpItems | responsibleActor | primary-care-physician | Task | owner or performerType | Needs decision | `owner` fits if responsibility is assigned to a specific actor; `performerType` fits better when responsibility is role-based |
| Follow-up plan and responsibility.followUpItems | responsibleOrganization | Example Family Medicine Clinic | Task | owner or restriction.recipient | Needs decision | Depends on whether organizational responsibility is treated as task owner, recipient, or both |
| Follow-up plan and responsibility.followUpItems | triggerReason | Prior gestational diabetes mellitus in the index pregnancy. / Need to continue metabolic risk follow-up after obstetric discharge. | Task | reasonCode or description | Best-fit direct | Best represented as the reason motivating the follow-up action |
| Follow-up plan and responsibility.followUpItems | linkedRisk | RISK-001 | Task | basedOn or supportingInfo or extension | Needs decision | The link to interpreted risk is important but does not have one universally clean base R4 Task linkage pattern here |
| Follow-up plan and responsibility.followUpItems | stageOfRelevance | postpartum | Task | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 task field |
| Follow-up plan and responsibility.followUpItems | recordedDate | 2026-03-31 | Task | authoredOn | Best-fit direct | Best fit for when the follow-up task was created or asserted |
| Follow-up plan and responsibility.followUpItems | recorder | obstetric-team | Task | requester | Best-fit direct | Best fit for the sending actor initiating the follow-up task |
| Follow-up plan and responsibility.followUpItems | note | Primary care follow-up should include postpartum glucose testing within the expected postpartum follow-up window. / Responsibility for ongoing metabolic follow-up transfers to primary care after discharge. | Task | note | Direct | Narrative clarification supporting continuity use |

Note: Follow-up items in MCEP align well with Task because they express requested next actions, responsibility, and timing across transition. Some fields, especially role-based responsibility, linked risk, and deadline semantics, may still require profiling or implementation-level constraints.

### Responsibility summary

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Follow-up plan and responsibility.responsibilitySummary | responsibilitySummary | At discharge, obstetric care closes the birth-related episode, but responsibility for postpartum metabolic follow-up passes to primary care. The receiving primary care physician is expected to ensure postpartum glucose reassessment and ongoing risk-aware follow-up. | Composition | section.text | Best-fit direct | Best treated as a narrative synthesis of responsibility transfer and shared continuity expectations rather than as a separate structured action resource |

Note: `responsibilitySummary` is best understood as narrative synthesis that clarifies transfer or distribution of responsibility across settings, complementing the structured follow-up task entries rather than replacing them.

## Related records

### Related record items

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Related records.relatedRecordItems | relatedRecordId | not present in example | RelatedPerson or List or extension-based linkage | identifier | Needs decision | Depends on whether the linked context is represented as a person relationship, a record link, or a profiled linkage pattern |
| Related records.relatedRecordItems | relationType | not present in example | RelatedPerson or extension | relationship or extension | Needs decision | Base FHIR supports person relationships, but some MCEP linkage categories may require profiling |
| Related records.relatedRecordItems | targetRecordId | not present in example | Patient, RelatedPerson, EpisodeOfCare, or DocumentReference | identifier or reference | Needs decision | Depends on what type of linked record is being referenced |
| Related records.relatedRecordItems | targetRecordType | not present in example | extension | extension | Requires extension/profile | MCEP target record type is analytically useful but does not have one single clean base R4 element |
| Related records.relatedRecordItems | linkageStatus | not present in example | extension | extension | Requires extension/profile | Linkage certainty or validity is important in MCEP but not a standard single base R4 field across linked-record patterns |
| Related records.relatedRecordItems | clinicalRelevance | not present in example | RelatedPerson or Composition | note or section.text | Best-fit direct | Best represented as explanatory narrative unless a more formal linkage model is profiled |
| Related records.relatedRecordItems | stageOfRelevance | not present in example | extension | extension | Requires extension/profile | Continuity-oriented maternal stage relevance is not a standard single base R4 linked-record field |
| Related records.relatedRecordItems | recordedDate | not present in example | Provenance or supporting linkage metadata | Provenance.recorded or extension | Needs decision | Source recording context for record linkage is better handled through provenance or profiled metadata |
| Related records.relatedRecordItems | recorder | not present in example | Provenance or RelatedPerson-linked attribution | agent or extension | Needs decision | Attribution for linkage creation is better handled through provenance support |
| Related records.relatedRecordItems | note | not present in example | RelatedPerson or Composition | note or section.text | Best-fit direct | Narrative clarification supporting continuity use when linkage is clinically important |

Note: The frozen postpartum GDM example contains no related-record entries, so this mapping block is structural rather than instance-populated. In future maternal-infant continuity examples, related-record mapping would likely require a combination of standard references, provenance support, and profile-level linkage rules rather than a single base FHIR resource.

### Related record summary

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Related records.relatedRecordSummary | relatedRecordSummary | not present in example | Composition | section.text | Best-fit direct | Best treated as a narrative synthesis of clinically important linked-record context when present |

## Provenance

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Provenance | authorRole | obstetric-team | Provenance | agent.type or agent.role | Best-fit direct | Best fit for the role responsible for creating or authorizing the package content |
| Provenance | authorOrganization | Example Women and Birth Hospital | Provenance | agent.who or agent.onBehalfOf | Needs decision | Depends on whether the organization is represented as the direct authoring agent or as the organization on whose behalf the agent acted |
| Provenance | sourceEncounterId | ENC-POSTPARTUM-001 | Provenance | target or entity.what or supporting Encounter reference | Needs decision | Depends on whether the encounter is treated as a source context reference or a directly linked supporting entity |
| Provenance | sourceSystemReference | EHR-PPD-2026-0001 | Provenance | entity.what.identifier or extension | Best-fit direct | Best represented as a source-system reference attached to the provenance context |
| Provenance | verificationNote | Package generated from the postpartum discharge context and reviewed by the responsible obstetric team. | Provenance | activity or reason or note via extension | Needs decision | Base R4 Provenance does not provide one single clean note field for this exact meaning, so extension or profiled usage may be needed |

Note: MCEP Provenance aligns conceptually with FHIR Provenance, but some package-level source and verification details may require profiled representation because the MCEP package is a continuity artifact rather than a single native FHIR resource.

## Narrative summary

| MCEP section | MCEP field | Example value | Candidate FHIR resource | Candidate FHIR element | Mapping status | Notes |
|---|---|---|---|---|---|---|
| Narrative summary | summaryText | Woman discharged from obstetric care after delivery following a pregnancy complicated by gestational diabetes mellitus. Postpartum and longer-term metabolic follow-up remain necessary. Primary care is expected to arrange postpartum glucose reassessment and continue risk-aware follow-up after discharge. | Composition | section.text | Best-fit direct | Best treated as narrative synthesis of the continuity package rather than as a separate computable resource |
| Narrative summary | summaryFocus | risk-and-follow-up | Composition | section.code or extension | Needs decision | Focus can be partly represented through section coding, but MCEP summary-focus categories may need profiling |
| Narrative summary | summaryDate | 2026-03-31 | Composition | date | Best-fit direct | Best fit for the date of the authored summary section or document-level summary context |

Note: Narrative summary is best represented through Composition because its role is to synthesize the continuity meaning of the package in human-readable form. It should complement, not replace, the structured resource-level mappings.

## Overall mapping interpretation

The MCEP maps partially and unevenly to base FHIR R4, which is consistent with the design goal of the artifact. Core clinical content such as patient identity, conditions, observations, medication-use statements, documents, and follow-up actions can be aligned with established FHIR resources including Patient, Condition, Observation, MedicationStatement, DocumentReference, Task, RiskAssessment, Composition, and Provenance. However, the continuity-oriented logic of the package is not carried by single resources alone. Maternal episode context, stage-specific relevance, interpreted longitudinal risk, handoff meaning, responsibility transfer, and related-record continuity often require multi-resource assembly, narrative support, and in several places profile-level extensions or constrained implementation rules. The mapping therefore supports the claim that MCEP is FHIR-aligned, but not reducible to a simple one-to-one resource translation without loss of continuity meaning.
