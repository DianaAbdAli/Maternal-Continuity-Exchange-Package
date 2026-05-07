# Maternal Continuity Exchange Package (MCEP)

## Purpose
A lightweight, JSON-readable, FHIR R4-aligned maternal continuity package for preserving clinically meaningful information across maternal care transitions in HIE environments.

## Design goal
To preserve longitudinal meaning across transitions, especially episode context, risk interpretation, handoff reason, follow-up intent and responsibility.

## Scope
This is not a full maternity record and not a national implementation guide.
It is a minimum continuity-focused exchange package.

## Core sections
1. Envelope
2. Subject
3. Maternal episode context
4. Clinical summary
5. Risk interpretation
6. Handoff context
7. Follow-up plan and responsibility
8. Related records
9. Provenance
10. Narrative summary

## 1. Envelope

### Purpose
Identifies the package itself and the context in which it was created and shared.

### Fields
- `artifactType`
- `artifactVersion`
- `profile`
- `generatedDate`
- `useCase`
- `exchangeCareStage`
- `sendingOrganization`
- `receivingOrganization`
- `sourceSystem`

### Notes
This section does not contain clinical content.
It provides the metadata needed to understand what kind of package this is, when it was generated and in what transition context it is being used.

`exchangeCareStage` refers to the stage of care at the time the package is created or exchanged.
It is distinct from the `careStage` field in Maternal episode context, which describes the maternal episode being represented.

### Field priority

#### Mandatory
- `artifactType`
- `artifactVersion`
- `generatedDate`
- `useCase`

#### Recommended
- `exchangeCareStage`
- `sendingOrganization`
- `sourceSystem`

#### Optional
- `profile`
- `receivingOrganization`

### Field definitions

- `artifactType`: Type name of the continuity package artifact.
- `artifactVersion`: Version of the package specification used to generate the artifact.
- `profile`: Named profile or package variant when applicable.
- `generatedDate`: Date or date-time when the package was generated.
- `useCase`: Intended continuity use case for which the package was created.
- `exchangeCareStage`: Stage of care at the time the package is created or exchanged.
- `sendingOrganization`: Organization or operational entity transmitting the package to the receiving setting.
- `receivingOrganization`: Intended receiving organization or operational recipient in the exchange workflow.
- `sourceSystem`: Name of the originating system from which the package is sent.

## 2. Subject

### Purpose
Identifies the woman whose continuity information is being exchanged across systems and care settings.

### Fields
- `personId`
- `localIds`
- `fullName`
- `dateOfBirth`
- `sex`
- `contactInfo`
- `nationality`
- `residenceRegion`

### Notes
This section supports patient identification and cross-system matching.
It should contain only the minimum identity data needed for safe continuity and record linkage.

### Field priority

#### Mandatory
- `personId`
- `dateOfBirth`
- `sex`

#### Recommended
- `localIds`
- `fullName`

#### Optional
- `contactInfo`
- `nationality`
- `residenceRegion`

### Field definitions

- `personId`: Primary identifier used to represent the woman in the continuity package.
- `localIds`: Additional local or source-specific identifiers associated with the woman across systems or organizations.
- `fullName`: Human-readable full name of the woman.
- `dateOfBirth`: Date of birth of the woman.
- `sex`: Administrative or recorded sex of the woman as represented in the source system.
- `contactInfo`: Contact information relevant to continuity when appropriate to include.
- `nationality`: Nationality of the woman when relevant to the exchange context.
- `residenceRegion`: Region or jurisdiction of residence when relevant to continuity, cross-regional transfer or access context.

### Allowed values

#### `sex`
Allowed values:
- `female`
- `male`
- `other`
- `unknown`

Definition notes:
- `female`: recorded sex is female.
- `male`: recorded sex is male.
- `other`: recorded sex is represented as another category in the source system.
- `unknown`: recorded sex is not known reliably.

## 3. Maternal episode context

### Purpose
Preserves the pregnancy-specific and stage-specific context needed to interpret maternal information longitudinally rather than as isolated encounter data.

### Fields
- `episodeId`
- `pregnancyStatus`
- `careStage`
- `estimatedDueDate`
- `gestationalAge`
- `deliveryDate`
- `pregnancyOutcome`
- `managingOrganization`

### Notes
This section is essential for continuity because maternal information often loses meaning when it is detached from pregnancy context, timepoint and stage of care.
It helps distinguish a longitudinal maternal episode from a single encounter or document.

### Field priority

#### Mandatory
- `episodeId`
- `pregnancyStatus`
- `careStage`

#### Recommended
- `estimatedDueDate`
- `gestationalAge`
- `managingOrganization`

#### Conditional
- `deliveryDate`
- `pregnancyOutcome`

### Priority rules
Mandatory fields must be present in every maternal continuity package.
Recommended fields should be included whenever they are known and relevant.
Conditional fields are included only when the clinical stage or scenario makes them relevant.

### Field definitions

- `episodeId`: Unique identifier for the maternal episode or pregnancy pathway.
- `pregnancyStatus`: Current status of the pregnancy or maternal episode.
- `careStage`: Current maternal stage at the time of exchange.
- `estimatedDueDate`: Expected date of delivery when the pregnancy is ongoing.
- `gestationalAge`: Pregnancy age at the time of exchange, preferably structured as weeks and days.
- `deliveryDate`: Date of birth or delivery when already occurred.
- `pregnancyOutcome`: Outcome of the pregnancy when known and relevant.
- `managingOrganization`: Organization primarily responsible for the current maternal episode or transition.

### Allowed values

#### `pregnancyStatus`
Allowed values:
- `planned`
- `suspected`
- `confirmed`
- `ongoing`
- `delivered`
- `ended`
- `unknown`

Definition notes:
- `planned`: pregnancy not yet confirmed but maternal episode initiated in a preconception or fertility-related context.
- `suspected`: pregnancy suspected but not yet clinically confirmed.
- `confirmed`: pregnancy confirmed and active.
- `ongoing`: active pregnancy continuing through antenatal or intrapartum care.
- `delivered`: pregnancy ended in birth and maternal follow-up continues postpartum.
- `ended`: pregnancy ended without ongoing postpartum continuity in the current episode.
- `unknown`: status not known reliably.

#### `careStage`
Allowed values:
- `preconception`
- `antenatal`
- `intrapartum`
- `postpartum`
- `cross-stage`
- `unknown`

Definition notes:
- `preconception`: before confirmed pregnancy, where clinically relevant maternal continuity begins upstream.
- `antenatal`: pregnancy care before labour and birth.
- `intrapartum`: labour, delivery and immediate birth-related care.
- `postpartum`: maternal care after birth.
- `cross-stage`: exchange package summarises more than one stage at once.
- `unknown`: current stage cannot be reliably determined.

## 4. Clinical summary

### Purpose
Provides the minimum clinically relevant maternal information needed to preserve continuity across transitions without reproducing the full source record.

### Fields
- `conditions`
- `obstetricHistory`
- `keyObservations`
- `keyResults`
- `medications`
- `allergies`
- `procedures`
- `documents`

### Notes
This section is continuity-focused rather than exhaustive.
It should include only clinically meaningful information that helps the receiving setting understand the woman’s status, risks, prior relevant events and current management context.

### Field definitions

- `conditions`: Clinically relevant maternal diagnoses or problems that need to remain visible across transitions.
- `obstetricHistory`: Prior pregnancy and birth history that remains relevant for interpreting current or future maternal care.
- `keyObservations`: Important structured observations that help describe current maternal status.
- `keyResults`: Important laboratory or diagnostic results that affect continuity and follow-up.
- `medications`: Current or recent medications relevant to ongoing maternal care.
- `allergies`: Known allergies or intolerances relevant to safe treatment.
- `procedures`: Major clinical procedures or events relevant to the maternal pathway.
- `documents`: Linked summaries, reports or discharge documents that support continuity when structured detail alone is insufficient.

#### `conditions`

##### Purpose
Carries clinically relevant maternal conditions that the receiving setting must understand in order to interpret history, risk and follow-up needs correctly.

##### Subfields
- `conditionId`
- `code`
- `display`
- `clinicalStatus`
- `verificationStatus`
- `onsetDate`
- `abatementDate`
- `stageOfRelevance`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `code`
- `display`
- `clinicalStatus`

###### Recommended
- `verificationStatus`
- `onsetDate`
- `stageOfRelevance`
- `recordedDate`

###### Optional
- `conditionId`
- `abatementDate`
- `recorder`
- `note`

##### Subfield definitions
- `conditionId`: Local identifier for the condition entry when available.
- `code`: Structured code representing the condition.
- `display`: Human-readable name of the condition.
- `clinicalStatus`: Current clinical status of the condition.
- `verificationStatus`: Degree of diagnostic certainty.
- `onsetDate`: Date when the condition began or was first recognized.
- `abatementDate`: Date when the condition resolved, if applicable.
- `stageOfRelevance`: Maternal care stage in which the condition is especially relevant.
- `recordedDate`: Date when the condition was recorded in the source system.
- `recorder`: Role or organization that recorded the condition.
- `note`: Short explanatory note when structured data alone is insufficient.

##### Allowed values

###### `clinicalStatus`
Allowed values:
- `active`
- `resolved`
- `inactive`
- `unknown`

Definition notes:
- `active`: condition is currently relevant or ongoing.
- `resolved`: condition has ended but may still matter historically.
- `inactive`: condition is not currently active but remains clinically relevant.
- `unknown`: status cannot be determined reliably.

###### `verificationStatus`
Allowed values:
- `confirmed`
- `provisional`
- `refuted`
- `entered-in-error`
- `unknown`

Definition notes:
- `confirmed`: diagnosis is clinically established.
- `provisional`: diagnosis is suspected but not yet fully confirmed.
- `refuted`: condition was considered but ruled out.
- `entered-in-error`: condition entry should not be treated as valid.
- `unknown`: verification status is not known reliably.

#### `obstetricHistory`

##### Purpose
Carries prior pregnancy and birth history that remains clinically relevant for interpreting the current maternal episode, risk profile and follow-up needs.

##### Subfields
- `gravidity`
- `parity`
- `previousPregnancyComplications`
- `previousBirthMode`
- `previousPretermBirth`
- `previousPregnancyLoss`
- `historyNote`

##### Field priority

###### Mandatory
- `gravidity`
- `parity`

###### Recommended
- `previousPregnancyComplications`
- `previousBirthMode`
- `previousPretermBirth`
- `previousPregnancyLoss`

###### Optional
- `historyNote`

##### Subfield definitions
- `gravidity`: Total number of pregnancies in the woman’s reproductive history.
- `parity`: Number of births reaching viable gestation, according to local clinical convention.
- `previousPregnancyComplications`: Clinically important complications in prior pregnancies that remain relevant to current or future care.
- `previousBirthMode`: Mode of previous birth when relevant to continuity or risk interpretation.
- `previousPretermBirth`: Indicates whether there is a history of preterm birth.
- `previousPregnancyLoss`: Indicates whether there is a history of miscarriage, stillbirth or other pregnancy loss, where clinically relevant.
- `historyNote`: Short narrative clarification when structured entries do not fully capture the relevant obstetric history.

##### Allowed values

###### `previousBirthMode`
Allowed values:
- `vaginal`
- `assisted-vaginal`
- `caesarean`
- `mixed-history`
- `unknown`

Definition notes:
- `vaginal`: prior birth occurred vaginally without instrumental assistance.
- `assisted-vaginal`: prior birth occurred vaginally with instrumental assistance.
- `caesarean`: prior birth occurred by caesarean section.
- `mixed-history`: prior births include more than one mode.
- `unknown`: prior birth mode is not known reliably.

###### `previousPretermBirth`
Allowed values:
- `yes`
- `no`
- `unknown`

Definition notes:
- `yes`: there is a known history of preterm birth.
- `no`: there is no known history of preterm birth.
- `unknown`: history of preterm birth is not known reliably.

###### `previousPregnancyLoss`
Allowed values:
- `yes`
- `no`
- `unknown`

Definition notes:
- `yes`: there is a known history of pregnancy loss.
- `no`: there is no known history of pregnancy loss.
- `unknown`: pregnancy loss history is not known reliably.

#### `keyObservations`

##### Purpose
Carries important structured maternal observations that help the receiving setting understand current status, trajectory and continuity-relevant clinical context across transitions.

##### Subfields
- `observationId`
- `code`
- `display`
- `value`
- `unit`
- `effectiveDate`
- `interpretation`
- `stageOfRelevance`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `code`
- `display`
- `value`
- `effectiveDate`

###### Recommended
- `unit`
- `interpretation`
- `stageOfRelevance`
- `recordedDate`

###### Optional
- `observationId`
- `recorder`
- `note`

##### Subfield definitions
- `observationId`: Local identifier for the observation entry when available.
- `code`: Structured code representing the observation.
- `display`: Human-readable name of the observation.
- `value`: Recorded observation value.
- `unit`: Unit of measure when applicable.
- `effectiveDate`: Date or date-time at which the observation was measured or became clinically relevant.
- `interpretation`: Clinical interpretation of the observation when available.
- `stageOfRelevance`: Maternal care stage in which the observation is especially relevant.
- `recordedDate`: Date when the observation was recorded in the source system.
- `recorder`: Role or organization that recorded the observation.
- `note`: Short explanatory note when structured entries do not fully capture the observation context.

##### Allowed values

###### `interpretation`
Allowed values:
- `normal`
- `abnormal`
- `critical`
- `borderline`
- `unknown`

Definition notes:
- `normal`: observation is within expected clinical range or interpretation.
- `abnormal`: observation is outside the expected range or clinically concerning.
- `critical`: observation indicates urgent or high-severity concern.
- `borderline`: observation is near a threshold or requires watchful follow-up.
- `unknown`: interpretation is not known or not reliably available.

#### `keyResults`

##### Purpose
Carries important laboratory, imaging or diagnostic results that remain clinically relevant for continuity, risk interpretation and follow-up across maternal care transitions.

##### Subfields
- `resultId`
- `code`
- `display`
- `value`
- `unit`
- `effectiveDate`
- `resultType`
- `interpretation`
- `referenceRange`
- `stageOfRelevance`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `code`
- `display`
- `value`
- `effectiveDate`
- `resultType`

###### Recommended
- `unit`
- `interpretation`
- `stageOfRelevance`
- `recordedDate`

###### Optional
- `resultId`
- `referenceRange`
- `recorder`
- `note`

##### Subfield definitions
- `resultId`: Local identifier for the result entry when available.
- `code`: Structured code representing the result.
- `display`: Human-readable name of the result.
- `value`: Recorded result value.
- `unit`: Unit of measure when applicable.
- `effectiveDate`: Date or date-time at which the result was produced or became clinically relevant.
- `resultType`: Category of result, such as laboratory, imaging or other diagnostic output.
- `interpretation`: Clinical interpretation of the result when available.
- `referenceRange`: Reference interval or comparison range when relevant to interpreting the result.
- `stageOfRelevance`: Maternal care stage in which the result is especially relevant.
- `recordedDate`: Date when the result was recorded in the source system.
- `recorder`: Role or organization that recorded the result.
- `note`: Short explanatory note when structured entries do not fully capture the result context.

##### Allowed values

###### `resultType`
Allowed values:
- `laboratory`
- `imaging`
- `diagnostic`
- `screening`
- `other`
- `unknown`

Definition notes:
- `laboratory`: result generated from a laboratory test or assay.
- `imaging`: result generated from an imaging study.
- `diagnostic`: result generated from another diagnostic procedure or assessment.
- `screening`: result generated from a screening process or protocol.
- `other`: result type does not fit the main categories above.
- `unknown`: result type is not known reliably.

###### `interpretation`
Allowed values:
- `normal`
- `abnormal`
- `critical`
- `borderline`
- `unknown`

Definition notes:
- `normal`: result is within expected range or interpretation.
- `abnormal`: result is outside expected range or clinically concerning.
- `critical`: result indicates urgent or high-severity concern.
- `borderline`: result is near a threshold or requires follow-up attention.
- `unknown`: interpretation is not known or not reliably available.

#### `medications`

##### Purpose
Carries current or recent medications that remain clinically relevant for maternal continuity, safety and follow-up across care transitions.

##### Subfields
- `medicationId`
- `code`
- `display`
- `status`
- `dose`
- `route`
- `frequency`
- `startDate`
- `endDate`
- `stageOfRelevance`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `code`
- `display`
- `status`

###### Recommended
- `dose`
- `route`
- `frequency`
- `startDate`
- `stageOfRelevance`
- `recordedDate`

###### Optional
- `medicationId`
- `endDate`
- `recorder`
- `note`

##### Subfield definitions
- `medicationId`: Local identifier for the medication entry when available.
- `code`: Structured code representing the medication.
- `display`: Human-readable name of the medication.
- `status`: Current medication status.
- `dose`: Dose amount when available.
- `route`: Route of administration when relevant.
- `frequency`: Frequency or schedule of administration when relevant.
- `startDate`: Date when the medication started.
- `endDate`: Date when the medication ended, if applicable.
- `stageOfRelevance`: Maternal care stage in which the medication is especially relevant.
- `recordedDate`: Date when the medication was recorded in the source system.
- `recorder`: Role or organization that recorded the medication.
- `note`: Short explanatory note when structured entries do not fully capture the medication context.

##### Allowed values

###### `status`
Allowed values:
- `active`
- `completed`
- `stopped`
- `on-hold`
- `unknown`

Definition notes:
- `active`: medication is currently being taken or administered.
- `completed`: medication course has been completed.
- `stopped`: medication has been discontinued before completion or is no longer being taken.
- `on-hold`: medication is temporarily paused.
- `unknown`: medication status is not known reliably.

#### `allergies`

##### Purpose
Carries known allergies or intolerances that remain clinically relevant for maternal safety and treatment decisions across care transitions.

##### Subfields
- `allergyId`
- `code`
- `display`
- `status`
- `criticality`
- `reaction`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `code`
- `display`
- `status`

###### Recommended
- `criticality`
- `reaction`
- `recordedDate`

###### Optional
- `allergyId`
- `recorder`
- `note`

##### Subfield definitions
- `allergyId`: Local identifier for the allergy or intolerance entry when available.
- `code`: Structured code representing the allergy, intolerance or causative substance.
- `display`: Human-readable name of the allergy, intolerance or substance.
- `status`: Current validity status of the allergy entry.
- `criticality`: Degree of potential clinical harm if exposure occurs.
- `reaction`: Known reaction or manifestation when available.
- `recordedDate`: Date when the allergy entry was recorded in the source system.
- `recorder`: Role or organization that recorded the allergy entry.
- `note`: Short explanatory note when structured entries do not fully capture the allergy context.

##### Allowed values

###### `status`
Allowed values:
- `active`
- `inactive`
- `entered-in-error`
- `unknown`

Definition notes:
- `active`: allergy or intolerance is currently considered valid and clinically relevant.
- `inactive`: allergy entry is no longer considered active but may remain historically visible.
- `entered-in-error`: allergy entry should not be treated as valid.
- `unknown`: status is not known reliably.

###### `criticality`
Allowed values:
- `low`
- `high`
- `unable-to-assess`
- `unknown`

Definition notes:
- `low`: exposure is expected to carry lower clinical risk.
- `high`: exposure is expected to carry high clinical risk.
- `unable-to-assess`: criticality could not be assessed reliably.
- `unknown`: criticality is not known reliably.

#### `procedures`

##### Purpose
Carries major clinical procedures or interventions that remain relevant for understanding the maternal pathway, current status and follow-up needs across transitions.

##### Subfields
- `procedureId`
- `code`
- `display`
- `status`
- `performedDate`
- `outcome`
- `stageOfRelevance`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `code`
- `display`
- `status`
- `performedDate`

###### Recommended
- `outcome`
- `stageOfRelevance`
- `recordedDate`

###### Optional
- `procedureId`
- `recorder`
- `note`

##### Subfield definitions
- `procedureId`: Local identifier for the procedure entry when available.
- `code`: Structured code representing the procedure or intervention.
- `display`: Human-readable name of the procedure or intervention.
- `status`: Current completion status of the procedure entry.
- `performedDate`: Date or date-time when the procedure was performed.
- `outcome`: Outcome or relevant result of the procedure when available.
- `stageOfRelevance`: Maternal care stage in which the procedure is especially relevant.
- `recordedDate`: Date when the procedure was recorded in the source system.
- `recorder`: Role or organization that recorded the procedure.
- `note`: Short explanatory note when structured entries do not fully capture the procedure context.

##### Allowed values

###### `status`
Allowed values:
- `completed`
- `in-progress`
- `stopped`
- `not-done`
- `unknown`

Definition notes:
- `completed`: procedure was carried out and completed.
- `in-progress`: procedure had started but was not yet complete at the time of recording.
- `stopped`: procedure was started but interrupted or discontinued.
- `not-done`: procedure was planned or considered but not performed.
- `unknown`: procedure status is not known reliably.

#### `documents`

##### Purpose
Carries references to clinically relevant supporting documents when structured fields alone are insufficient to preserve continuity across maternal care transitions.

##### Subfields
- `documentId`
- `documentType`
- `title`
- `status`
- `authorOrganization`
- `creationDate`
- `stageOfRelevance`
- `language`
- `linkReference`
- `note`

##### Field priority

###### Mandatory
- `documentType`
- `title`
- `status`
- `creationDate`

###### Recommended
- `authorOrganization`
- `stageOfRelevance`
- `linkReference`

###### Optional
- `documentId`
- `language`
- `note`

##### Subfield definitions
- `documentId`: Local identifier for the document entry when available.
- `documentType`: Category of supporting clinical document.
- `title`: Human-readable document title.
- `status`: Current availability or validity status of the document reference.
- `authorOrganization`: Organization that authored or issued the document.
- `creationDate`: Date when the document was created.
- `stageOfRelevance`: Maternal care stage in which the document is especially relevant.
- `language`: Language of the document when relevant.
- `linkReference`: Pointer, URI or local reference to the document in the source environment when available.
- `note`: Short explanatory note when structured metadata do not fully capture the document context.

##### Allowed values

###### `documentType`
Allowed values:
- `discharge-summary`
- `referral-letter`
- `clinical-note`
- `diagnostic-report`
- `care-plan-document`
- `other`
- `unknown`

Definition notes:
- `discharge-summary`: summary generated at discharge from a care setting.
- `referral-letter`: document supporting referral or handoff to another provider or setting.
- `clinical-note`: narrative clinical note relevant to continuity.
- `diagnostic-report`: report containing diagnostic findings or interpretations.
- `care-plan-document`: document describing a plan of care or follow-up actions.
- `other`: document type does not fit the main categories above.
- `unknown`: document type is not known reliably.

###### `status`
Allowed values:
- `current`
- `superseded`
- `entered-in-error`
- `unavailable`
- `unknown`

Definition notes:
- `current`: document reference is valid and current.
- `superseded`: document has been replaced by a newer version.
- `entered-in-error`: document reference should not be treated as valid.
- `unavailable`: document is known but not currently accessible through the reference.
- `unknown`: document status is not known reliably.

## 5. Risk interpretation

### Purpose
Captures continuity-relevant maternal risk in an explicitly interpreted form so that receiving settings can understand not only what has been recorded, but why it matters for ongoing care, surveillance and follow-up.

### Fields
- `riskItems`
- `riskSummary`

### Notes
This section goes beyond raw clinical facts.
It expresses the interpreted significance of maternal history, conditions, observations or results when those create continuity-relevant risk across transitions.

### Field definitions

- `riskItems`: Structured risk entries that describe clinically relevant maternal risk requiring awareness, follow-up or escalation across care settings.
- `riskSummary`: Short narrative summary of the most important interpreted risks when structured entries alone do not fully capture the continuity implications.

#### `riskItems`

##### Purpose
Carries interpreted maternal risk entries that help the receiving setting understand why specific information matters, how urgent the issue is and what kind of continuity response may be needed.

##### Subfields
- `riskId`
- `riskType`
- `display`
- `riskLevel`
- `basis`
- `timeHorizon`
- `clinicalMeaning`
- `recommendedAttention`
- `dueBy`
- `stageOfRelevance`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `riskType`
- `display`
- `riskLevel`
- `clinicalMeaning`

###### Recommended
- `basis`
- `timeHorizon`
- `recommendedAttention`
- `stageOfRelevance`
- `recordedDate`

###### Optional
- `riskId`
- `dueBy`
- `recorder`
- `note`

##### Subfield definitions
- `riskId`: Local identifier for the interpreted risk entry when available.
- `riskType`: Category of maternal risk being represented.
- `display`: Human-readable name of the risk entry.
- `riskLevel`: Degree of clinical concern or continuity importance associated with the risk.
- `basis`: Main clinical basis for the interpreted risk, such as diagnosis, result, history or combined evidence.
- `timeHorizon`: Period over which the risk is expected to remain relevant.
- `clinicalMeaning`: Short explanation of why the risk matters clinically for continuity, follow-up or management.
- `recommendedAttention`: Suggested level of response or follow-up attention.
- `dueBy`: Date or time by which the next relevant attention or review should occur, when known.
- `stageOfRelevance`: Maternal care stage in which the risk is especially relevant.
- `recordedDate`: Date when the interpreted risk was recorded in the source system.
- `recorder`: Role or organization that recorded the interpreted risk.
- `note`: Short explanatory note when structured entries do not fully capture the risk context.

##### Allowed values

###### `riskType`
Allowed values:
- `obstetric`
- `metabolic`
- `hypertensive`
- `postpartum`
- `medication-related`
- `behavioural`
- `social`
- `other`
- `unknown`

Definition notes:
- `obstetric`: risk primarily related to pregnancy or birth history, maternal complications or obstetric trajectory.
- `metabolic`: risk primarily related to glucose regulation, diabetes or broader metabolic follow-up.
- `hypertensive`: risk primarily related to blood pressure disorders or their consequences.
- `postpartum`: risk primarily related to the post-birth period, including recovery and follow-up vulnerabilities.
- `medication-related`: risk related to medication use, discontinuity or treatment safety.
- `behavioural`: risk related to health behaviours relevant to continuity and outcomes.
- `social`: risk related to social or contextual barriers affecting continuity or follow-up.
- `other`: risk type does not fit the main categories above.
- `unknown`: risk type is not known reliably.

###### `riskLevel`
Allowed values:
- `low`
- `moderate`
- `high`
- `critical`
- `unknown`

Definition notes:
- `low`: risk is present but currently of lower clinical concern.
- `moderate`: risk is clinically relevant and should remain visible across transitions.
- `high`: risk is significant and should clearly inform follow-up or management.
- `critical`: risk is urgent or potentially severe and requires immediate attention.
- `unknown`: risk level is not known reliably.

###### `timeHorizon`
Allowed values:
- `current-episode`
- `short-term`
- `postpartum-window`
- `long-term`
- `unknown`

Definition notes:
- `current-episode`: risk is mainly relevant within the active maternal episode.
- `short-term`: risk remains relevant over the near-term transition period.
- `postpartum-window`: risk is especially relevant during the postpartum follow-up period.
- `long-term`: risk remains relevant beyond the immediate maternal episode and may require longer-term surveillance.
- `unknown`: time horizon is not known reliably.

###### `recommendedAttention`
Allowed values:
- `routine-awareness`
- `planned-follow-up`
- `enhanced-monitoring`
- `urgent-review`
- `unknown`

Definition notes:
- `routine-awareness`: risk should remain visible but does not currently require intensified action.
- `planned-follow-up`: risk requires scheduled follow-up or review.
- `enhanced-monitoring`: risk requires closer observation or more active surveillance.
- `urgent-review`: risk requires prompt clinical review or escalation.
- `unknown`: recommended level of attention is not known reliably.

#### `riskSummary`

##### Purpose
Provides a short narrative synthesis of the most important interpreted maternal risks when structured risk items alone do not fully communicate the continuity implications.

##### Field priority

###### Optional
- `riskSummary`

##### Field definition
- `riskSummary`: Brief human-readable summary of the most important interpreted risks, especially when multiple risk items need to be understood together in a handoff or follow-up context.

##### Usage note
This field should complement structured `riskItems`, not replace them.
It is especially useful when the receiving setting needs a concise explanation of why certain risks matter across the transition.

## 6. Handoff context

### Purpose
Captures the reason, direction and immediate context of the maternal care transition so that the receiving setting can understand why the package was created and what kind of handoff is taking place.

### Fields
- `handoffReason`
- `sendingContext`
- `receivingContext`
- `transitionType`
- `urgency`
- `handoffSummary`

### Notes
This section focuses on the transition itself rather than the full clinical record.
It helps preserve the immediate meaning of the handoff, especially when responsibility or care setting is changing.

### Field definitions

- `handoffReason`: Main reason for the transition or transfer of continuity information.
- `sendingContext`: The care setting, team or organizational context from which the handoff is initiated.
- `receivingContext`: The intended care setting, team or organizational context for the handoff.
- `transitionType`: Category describing the type of maternal care transition taking place.
- `urgency`: Degree of immediacy associated with the handoff.
- `handoffSummary`: Short narrative explanation of the handoff context when the structured fields alone do not fully explain the transition.

#### `handoffReason`

##### Purpose
Specifies why the maternal continuity package is being handed over at this point in care.

##### Field priority

###### Mandatory
- `handoffReason`

##### Field definition
- `handoffReason`: Brief human-readable statement of the primary reason for the handoff or transition.

#### `sendingContext`

##### Purpose
Identifies the clinical or organizational setting from which the handoff originates.

##### Field priority

###### Mandatory
- `sendingContext`

##### Field definition
- `sendingContext`: Name or description of the sending care setting, service or team initiating the handoff.

#### `receivingContext`

##### Purpose
Identifies the intended clinical or organizational setting receiving the handoff.

##### Field priority

###### Mandatory
- `receivingContext`

##### Field definition
- `receivingContext`: Name or description of the receiving care setting, service or team expected to continue care.

#### `transitionType`

##### Purpose
Classifies the maternal transition so that the receiving side understands the handoff pattern in workflow terms.

##### Field priority

###### Mandatory
- `transitionType`

##### Allowed values

- `preconception-to-antenatal`
- `routine-to-specialist`
- `antenatal-to-intrapartum`
- `intrapartum-to-postpartum`
- `obstetric-to-primary-care`
- `cross-organizational-transfer`
- `cross-regional-transfer`
- `other`
- `unknown`

##### Definition notes
- `preconception-to-antenatal`: transition from preconception-related care into pregnancy care.
- `routine-to-specialist`: transition from routine maternity care to specialist review or monitoring.
- `antenatal-to-intrapartum`: transition into labour and birth-related care.
- `intrapartum-to-postpartum`: transition from birth-related care into postpartum follow-up.
- `obstetric-to-primary-care`: transition from obstetric services to primary care or longer-term follow-up.
- `cross-organizational-transfer`: transfer between organizations not fully captured by stage labels alone.
- `cross-regional-transfer`: transfer across geographic or jurisdictional boundaries.
- `other`: transition type does not fit the main categories above.
- `unknown`: transition type is not known reliably.

#### `urgency`

##### Purpose
Indicates how quickly the receiving setting should attend to the handoff.

##### Field priority

###### Recommended
- `urgency`

##### Allowed values

- `routine`
- `priority`
- `urgent`
- `unknown`

##### Definition notes
- `routine`: handoff can proceed within normal workflow timelines.
- `priority`: handoff should receive timely attention beyond routine handling.
- `urgent`: handoff requires prompt review or action.
- `unknown`: urgency is not known reliably.

#### `handoffSummary`

##### Purpose
Provides a concise narrative explanation of the handoff when structured fields alone do not fully communicate the transition context.

##### Field priority

###### Optional
- `handoffSummary`

##### Field definition
- `handoffSummary`: Brief human-readable summary of the handoff, especially useful when multiple contextual factors shape the transition.

## 7. Follow-up plan and responsibility

### Purpose
Captures the planned next actions, expected follow-up and responsibility for ongoing care so that continuity does not depend on inference alone after a maternal transition.

### Fields
- `followUpItems`
- `responsibilitySummary`

### Notes
This section makes the next step in care explicit.
It is especially important where maternal risk, postpartum surveillance or transfer back to primary care could otherwise become unclear after discharge or handoff.

### Field definitions

- `followUpItems`: Structured follow-up entries describing what should happen next, when it should happen and who is expected to take responsibility.
- `responsibilitySummary`: Short narrative summary explaining the overall follow-up expectation and responsibility arrangement when structured entries alone do not fully capture it.

#### `followUpItems`

##### Purpose
Carries explicit follow-up actions so that the receiving setting can understand what is expected next, within what timeframe and under whose responsibility.

##### Subfields
- `followUpId`
- `actionType`
- `display`
- `status`
- `dueBy`
- `responsibleActor`
- `responsibleOrganization`
- `triggerReason`
- `linkedRisk`
- `stageOfRelevance`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `actionType`
- `display`
- `status`
- `responsibleActor`

###### Recommended
- `dueBy`
- `responsibleOrganization`
- `triggerReason`
- `stageOfRelevance`
- `recordedDate`

###### Optional
- `followUpId`
- `linkedRisk`
- `recorder`
- `note`

##### Subfield definitions
- `followUpId`: Local identifier for the follow-up entry when available.
- `actionType`: Category of follow-up action being requested or expected.
- `display`: Human-readable name of the follow-up action.
- `status`: Current status of the follow-up item.
- `dueBy`: Date or time by which the follow-up action should occur, when known.
- `responsibleActor`: Role primarily expected to carry out or coordinate the follow-up action.
- `responsibleOrganization`: Organization expected to host, coordinate or receive the follow-up action, when known.
- `triggerReason`: Main reason the follow-up is required, such as diagnosis, risk or abnormal result.
- `linkedRisk`: Reference to the interpreted risk or continuity concern that this follow-up addresses, when applicable.
- `stageOfRelevance`: Maternal care stage in which the follow-up is especially relevant.
- `recordedDate`: Date when the follow-up entry was recorded in the source system.
- `recorder`: Role or organization that recorded the follow-up entry.
- `note`: Short explanatory note when structured entries do not fully capture the follow-up context.

##### Allowed values

###### `actionType`
Allowed values:
- `clinical-review`
- `laboratory-test`
- `screening`
- `medication-review`
- `specialist-referral`
- `primary-care-follow-up`
- `postpartum-follow-up`
- `patient-education`
- `other`
- `unknown`

Definition notes:
- `clinical-review`: follow-up requires a clinical review or reassessment.
- `laboratory-test`: follow-up requires a laboratory investigation.
- `screening`: follow-up requires a structured screening activity.
- `medication-review`: follow-up requires medication review or treatment reassessment.
- `specialist-referral`: follow-up requires referral to a specialist service.
- `primary-care-follow-up`: follow-up is expected to occur in primary care.
- `postpartum-follow-up`: follow-up is specifically tied to postpartum surveillance or review.
- `patient-education`: follow-up includes education, counselling or self-management support.
- `other`: action type does not fit the main categories above.
- `unknown`: action type is not known reliably.

###### `status`
Allowed values:
- `planned`
- `requested`
- `scheduled`
- `completed`
- `missed`
- `cancelled`
- `unknown`

Definition notes:
- `planned`: follow-up is expected but not yet formally arranged.
- `requested`: follow-up has been requested or communicated.
- `scheduled`: follow-up has been arranged for a specific date or time.
- `completed`: follow-up action has already occurred.
- `missed`: follow-up was due but did not occur as expected.
- `cancelled`: follow-up was cancelled and is no longer expected in its original form.
- `unknown`: follow-up status is not known reliably.

###### `responsibleActor`
Allowed values:
- `obstetric-team`
- `midwife`
- `primary-care-physician`
- `specialist-physician`
- `nursing-team`
- `patient`
- `shared-responsibility`
- `other`
- `unknown`

Definition notes:
- `obstetric-team`: responsibility primarily sits with the obstetric service or team.
- `midwife`: responsibility primarily sits with a midwife or midwifery-led service.
- `primary-care-physician`: responsibility primarily sits with primary care.
- `specialist-physician`: responsibility primarily sits with a specialist physician outside routine primary care.
- `nursing-team`: responsibility primarily sits with nursing staff or coordinated nursing follow-up.
- `patient`: the woman herself is expected to initiate or complete the action.
- `shared-responsibility`: responsibility is explicitly shared across more than one actor.
- `other`: responsible actor does not fit the main categories above.
- `unknown`: responsible actor is not known reliably.

#### `responsibilitySummary`

##### Purpose
Provides a short narrative explanation of who is expected to do what next when structured follow-up items alone do not fully communicate the continuity responsibility.

##### Field priority

###### Optional
- `responsibilitySummary`

##### Field definition
- `responsibilitySummary`: Brief human-readable summary of the overall follow-up responsibility arrangement, especially useful when more than one actor or setting is involved.

##### Usage note
This field should complement structured `followUpItems`, not replace them.
It is especially useful when continuity depends on a clear explanation of responsibility transfer or shared responsibility across settings.

## 8. Related records

### Purpose
Captures clinically meaningful relationships between the maternal record and other relevant records when continuity depends on preserving related-record context across care transitions.

### Fields
- `relatedRecordItems`
- `relatedRecordSummary`

### Notes
This section is especially relevant when maternal continuity depends on linkage beyond a single person record, such as maternal-infant continuity after birth.
It is not required in every exchange package, but should be available when related-record context is clinically important.

### Field definitions

- `relatedRecordItems`: Structured entries describing clinically relevant links between the maternal record and another person or record.
- `relatedRecordSummary`: Short narrative summary of the most important related-record context when structured entries alone do not fully explain the linkage.

#### `relatedRecordItems`

##### Purpose
Carries explicit related-record links so that continuity across connected records can be preserved when care involves more than one person or record context.

##### Subfields
- `relatedRecordId`
- `relationType`
- `targetRecordId`
- `targetRecordType`
- `linkageStatus`
- `clinicalRelevance`
- `stageOfRelevance`
- `recordedDate`
- `recorder`
- `note`

##### Field priority

###### Mandatory
- `relationType`
- `targetRecordType`
- `linkageStatus`

###### Recommended
- `targetRecordId`
- `clinicalRelevance`
- `stageOfRelevance`
- `recordedDate`

###### Optional
- `relatedRecordId`
- `recorder`
- `note`

##### Subfield definitions
- `relatedRecordId`: Local identifier for the related-record entry when available.
- `relationType`: Type of relationship between the maternal record and the linked record.
- `targetRecordId`: Identifier of the linked record when available.
- `targetRecordType`: Type of linked record being referenced.
- `linkageStatus`: Degree of certainty or status of the record linkage.
- `clinicalRelevance`: Short explanation of why the related record matters for continuity.
- `stageOfRelevance`: Maternal care stage in which the linked record is especially relevant.
- `recordedDate`: Date when the related-record entry was recorded in the source system.
- `recorder`: Role or organization that recorded the related-record linkage.
- `note`: Short explanatory note when structured entries do not fully capture the related-record context.

##### Allowed values

###### `relationType`
Allowed values:
- `infant`
- `newborn`
- `fetus`
- `prior-pregnancy-record`
- `other-related-record`
- `unknown`

Definition notes:
- `infant`: linked record belongs to the infant relevant to maternal continuity.
- `newborn`: linked record belongs to the newborn in the immediate post-birth context.
- `fetus`: linked record relates to the fetus where separate representation is used.
- `prior-pregnancy-record`: linked record refers to a prior pregnancy-related record relevant to continuity.
- `other-related-record`: linked record does not fit the main categories above.
- `unknown`: relation type is not known reliably.

###### `targetRecordType`
Allowed values:
- `patient-record`
- `episode-record`
- `document-record`
- `other`
- `unknown`

Definition notes:
- `patient-record`: linked item is another person-level patient record.
- `episode-record`: linked item is another episode-level record.
- `document-record`: linked item is a document-level record relevant to continuity.
- `other`: target record type does not fit the main categories above.
- `unknown`: target record type is not known reliably.

###### `linkageStatus`
Allowed values:
- `confirmed`
- `probable`
- `unverified`
- `broken`
- `unknown`

Definition notes:
- `confirmed`: linkage is considered valid and verified.
- `probable`: linkage is likely but not fully verified.
- `unverified`: linkage is recorded but not yet confirmed.
- `broken`: expected linkage is known to be unavailable or invalid.
- `unknown`: linkage status is not known reliably.

#### `relatedRecordSummary`

##### Purpose
Provides a short narrative explanation of the most important related-record context when structured related-record entries alone do not fully communicate the linkage.

##### Field priority

###### Optional
- `relatedRecordSummary`

##### Field definition
- `relatedRecordSummary`: Brief human-readable summary of the clinically important related-record context, especially useful when maternal and infant continuity must be understood together.

##### Usage note
This field should complement structured `relatedRecordItems`, not replace them.
It is especially useful when continuity depends on explaining why a related record matters, even if linkage is incomplete or partially verified.

## 9. Provenance

### Purpose
Captures authorship, timing and source context so that exchanged maternal continuity information remains traceable, trustworthy and interpretable across care settings.

### Fields
- `authorRole`
- `authorOrganization`
- `sourceEncounterId`
- `sourceSystemReference`
- `verificationNote`

### Notes
This section supports traceability and trust.
It helps the receiving setting understand where the package came from, who authored it and how closely it reflects the source context.

`authorOrganization` refers to responsibility for the content of the package.
It is distinct from `sendingOrganization` in Envelope, which refers to the organization transmitting the package in the exchange workflow.

`sourceSystemReference` refers to an internal source-level identifier or pointer.
It is distinct from `sourceSystem` in Envelope, which identifies the source environment at system level.

### Field definitions

- `authorRole`: Role primarily responsible for creating or authorizing the package content.
- `authorOrganization`: Organization primarily responsible for issuing the package content.
- `sourceEncounterId`: Identifier of the source encounter or originating clinical event when available.
- `sourceSystemReference`: Local system-level reference to the originating source when available.
- `verificationNote`: Short note explaining any relevant verification, review or reliability context for the package.

#### `authorRole`

##### Purpose
Identifies the role most directly responsible for the authorship or authorization of the maternal continuity package.

##### Field priority

###### Mandatory
- `authorRole`

##### Allowed values
- `obstetric-team`
- `midwife`
- `primary-care-physician`
- `specialist-physician`
- `nursing-team`
- `multidisciplinary-team`
- `system-generated-with-review`
- `other`
- `unknown`

##### Definition notes
- `obstetric-team`: package was authored or authorized by an obstetric service or team.
- `midwife`: package was authored or authorized by a midwife or midwifery-led service.
- `primary-care-physician`: package was authored or authorized by primary care.
- `specialist-physician`: package was authored or authorized by a specialist physician.
- `nursing-team`: package was authored or authorized by nursing staff or a nursing-led service.
- `multidisciplinary-team`: package was authored or authorized jointly across more than one clinical role.
- `system-generated-with-review`: package was system-generated but reviewed by an accountable human actor.
- `other`: author role does not fit the main categories above.
- `unknown`: author role is not known reliably.

#### `authorOrganization`

##### Purpose
Identifies the organization primarily responsible for issuing the package.

##### Field priority

###### Mandatory
- `authorOrganization`

##### Field definition
- `authorOrganization`: Name or identifier of the organization primarily responsible for the package.

#### `sourceEncounterId`

##### Purpose
Links the package back to the originating encounter or clinical event when such a source context is available.

##### Field priority

###### Recommended
- `sourceEncounterId`

##### Field definition
- `sourceEncounterId`: Identifier of the source encounter or clinical event from which the package was generated.

#### `sourceSystemReference`

##### Purpose
Identifies the originating system reference for traceability when available.

##### Field priority

###### Recommended
- `sourceSystemReference`

##### Field definition
- `sourceSystemReference`: Local or system-level reference to the originating source environment.

#### `verificationNote`

##### Purpose
Provides any relevant note on review, verification or reliability context that may affect interpretation of the package.

##### Field priority

###### Optional
- `verificationNote`

##### Field definition
- `verificationNote`: Brief explanatory note about package verification, review or reliability when needed.

## 10. Narrative summary

### Purpose
Provides a concise human-readable synthesis of the most important continuity information in the package so that receiving settings can rapidly understand the maternal situation without relying only on structured fields.

### Fields
- `summaryText`
- `summaryFocus`
- `summaryDate`

### Notes
This section should complement the structured package, not replace it.
It is intended to support rapid clinical understanding, especially at transitions where time, context or responsibility may otherwise be unclear.
This section should synthesize, not introduce new facts absent from the structured fields

### Field definitions

- `summaryText`: Brief narrative synthesis of the most important continuity-relevant information in the package.
- `summaryFocus`: Main emphasis of the narrative summary.
- `summaryDate`: Date or date-time when the summary text was created.

#### `summaryText`

##### Purpose
Provides the main human-readable summary of the maternal continuity package.

##### Field priority

###### Mandatory
- `summaryText`

##### Field definition
- `summaryText`: Brief clinical narrative summarizing the most important maternal context, current issues, interpreted risks, handoff meaning and follow-up expectations.

#### `summaryFocus`

##### Purpose
Indicates the primary focus of the summary so the receiving setting understands what the summary is mainly trying to communicate.

##### Field priority

###### Recommended
- `summaryFocus`

##### Allowed values
- `clinical-status`
- `risk-and-follow-up`
- `handoff-context`
- `postpartum-continuity`
- `cross-stage-summary`
- `other`
- `unknown`

##### Definition notes
- `clinical-status`: summary primarily emphasizes the woman’s current maternal clinical situation.
- `risk-and-follow-up`: summary primarily emphasizes interpreted risk and next-step follow-up.
- `handoff-context`: summary primarily emphasizes why the transition is occurring and what the receiving side should understand.
- `postpartum-continuity`: summary primarily emphasizes postpartum needs, surveillance or continuity risks.
- `cross-stage-summary`: summary intentionally synthesizes more than one maternal stage.
- `other`: summary focus does not fit the main categories above.
- `unknown`: summary focus is not known reliably.

#### `summaryDate`

##### Purpose
Records when the narrative summary was written or generated.

##### Field priority

###### Recommended
- `summaryDate`

##### Field definition
- `summaryDate`: Date or date-time when the narrative summary was created.
