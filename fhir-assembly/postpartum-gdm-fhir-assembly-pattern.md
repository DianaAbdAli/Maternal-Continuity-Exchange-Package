# FHIR assembly pattern for postpartum GDM continuity

## Purpose

This file shows how a postpartum GDM continuity package could be assembled as a FHIR R4-aligned exchange package at resource level.

## Proposed package structure

- Bundle
- Composition
- Patient
- EpisodeOfCare
- Condition
- Observation (BMI)
- Observation (pregnancy glucose result)
- MedicationStatement
- DocumentReference
- RiskAssessment
- Task (postpartum glucose reassessment)
- Task (primary care follow-up)
- Provenance
- Organization
- Encounter

## Bundle

### Role in the package
The Bundle is the outer exchange container for the postpartum GDM continuity package It groups the FHIR resources that together represent the continuity package and allows them to be exchanged as one coherent artifact rather than as disconnected records.

### Why it is included
MCEP is not a single native FHIR resource. It is a continuity package composed of multiple resources that together preserve subject identity, maternal episode context, clinical summary, interpreted risk, handoff meaning, follow-up responsibility, provenance, and narrative synthesis. The Bundle is therefore needed as the package-level wrapper.

### Candidate FHIR role
- Primary resource: `Bundle`

### Best-fit bundle function
- Best-fit role: package container for the full MCEP exchange instance

### Candidate bundle type
- Preferred starting option: `collection`
- Design note: `collection` is the safest first design choice because the package is being assembled as a grouped exchange artifact. A `document` bundle could become defensible later if Composition is explicitly treated as the first entry and the package is framed as a document-style continuity handoff.

### Main contents of the Bundle
The Bundle should contain the resources needed for a postpartum GDM continuity package, including:

- Composition
- Patient
- EpisodeOfCare
- Condition
- Observation for postpartum BMI
- Observation for pregnancy glucose result
- MedicationStatement
- DocumentReference
- RiskAssessment
- Task for postpartum glucose reassessment
- Task for primary care follow-up
- Provenance
- Organization resources
- Encounter, if included as source context support

### MCEP sections represented through the Bundle
The Bundle does not correspond to one single MCEP section. Instead, it acts as the package-level wrapper for the full MCEP artifact.

### Key design logic
The Bundle preserves exchange coherence. Without it, the MCEP-aligned FHIR representation would be only a loose set of separate resources. With it, the package can be treated as one continuity-oriented exchange unit for the postpartum GDM handoff scenario.

### Working design decision
For the current thesis-stage design, the postpartum GDM continuity package should be modeled first as a Bundle of type collection. This is the most conservative and defendable assembly choice at this stage.

### Open design question
A later refinement could test whether the same package should instead be represented as a `document` bundle anchored by Composition, but that decision should follow only after the internal resource roles are fully defined.

## Composition

### Role in the package
Composition is the clinical anchor of the postpartum GDM continuity package. It provides the package with a coherent human-readable structure and is the best candidate resource for organizing the continuity narrative across the included FHIR resources.

### Why it is included
A postpartum GDM continuity package is not only a set of structured data elements. It also carries handoff meaning, risk synthesis, follow-up expectations, and responsibility transfer. Composition is therefore needed because it can frame the package as one readable clinical handoff artifact rather than only a set of disconnected technical resources.

### Candidate FHIR role
- Primary resource: `Composition`

### Best-fit function in the package
- Clinical wrapper for the package
- Narrative anchor for human-readable interpretation
- Organizing structure for the main continuity sections represented in the package

### Likely high-level Composition elements
The Composition would likely carry or support:
- `subject`
- `date`
- `author`
- `title`
- `type`
- `section`

### Example package title pattern
A working title pattern could be:

`Postpartum continuity handoff after gestational diabetes mellitus`

### MCEP sections best represented or supported through Composition
Composition is the strongest candidate resource for organizing or narratively expressing:

- Narrative summary
- Risk summary
- Responsibility summary
- Handoff summary
- parts of obstetric history when retained as narrative
- package-level clinical interpretation that is not reducible to one structured resource alone

### Relation to the Bundle
If the Bundle is the outer container, Composition is the best candidate for the internal clinical anchor. If the package is later refined into a document-style exchange artifact, Composition could become the first resource in a `document` Bundle.

### Relation to other included resources
Composition should not replace the structured resources. Instead, it should reference or summarize them. In a postpartum GDM continuity package, it would sit alongside Patient, EpisodeOfCare, Condition, Observations, MedicationStatement, DocumentReference, RiskAssessment, Task, Provenance, and supporting context resources.

### Key design logic
The package needs both structure and interpretability. Structured resources preserve computable continuity content, while Composition preserves the readable handoff logic. This is especially important in postpartum GDM because continuity depends not only on isolated facts, but also on a clear explanation of why follow-up remains necessary after delivery.

### Working design decision
For the current thesis-stage design, Composition should be treated as the best-fit clinical anchor of the MCEP-aligned postpartum GDM package, even if the Bundle remains modeled initially as a `collection` rather than a `document`.

### Open design question
A later refinement could decide how explicitly the internal MCEP sections should be mirrored as Composition sections, and whether every summary element should appear in Composition or only the most clinically important narrative syntheses.


## Patient

### Role in the package
Patient is the core subject-identification resource in the postpartum GDM continuity package. It represents the woman whose continuity information is being exchanged across settings.

### Why it is included
The package must preserve stable subject identity across the handoff from obstetric care to primary care. Without Patient, the package could not reliably bind episode context, clinical summary, risk interpretation, follow-up actions, and narrative synthesis to the same person.

### Candidate FHIR role
- Primary resource: `Patient`

### Best-fit function in the package
- Subject identity anchor for the continuity package
- Cross-resource reference target for the woman receiving care
- Base demographic and identifier carrier for continuity-safe matching

### MCEP sections best represented through Patient
Patient is the strongest candidate resource for representing or supporting:

- Subject.personId
- Subject.localIds
- Subject.fullName
- Subject.dateOfBirth
- Subject.sex
- Subject.contactInfo
- selected identity-related continuity fields such as residence context where appropriate

### Likely high-level Patient elements
The Patient resource would likely carry or support:
- `identifier`
- `name`
- `birthDate`
- `gender`
- `telecom`
- possibly `address`
- possibly profile-level extensions for fields such as nationality or residence-region semantics if needed

### Relation to other included resources
Patient acts as the person-level reference target for the rest of the package. In a postpartum GDM continuity package, it would be referenced by resources such as:

- Composition
- EpisodeOfCare
- Condition
- Observation
- MedicationStatement
- DocumentReference, where relevant
- RiskAssessment
- Task
- Provenance, indirectly or contextually

### Key design logic
The continuity package is built around one maternal subject moving across a transition in care. Patient therefore provides the identity backbone of the package. This is especially important in postpartum GDM handoff because the continuity problem is not only about preserving facts, but about preserving the correct linkage of those facts to the woman whose pregnancy history continues to matter after delivery.

### Working design decision
For the current thesis-stage design, Patient should be treated as the required subject-level resource in every postpartum GDM continuity package.

### Open design question
Some Subject fields, especially nationality and residence-region semantics, may not map cleanly to base FHIR R4 Patient without profile-level decisions or extension use.

## EpisodeOfCare

### Role in the package
EpisodeOfCare is the best-fit longitudinal context resource in the postpartum GDM continuity package. It represents the maternal care pathway as an ongoing episode that extends beyond a single encounter and helps preserve continuity across the transition from obstetric care to primary care.

### Why it is included
A postpartum GDM continuity package needs more than person identity and isolated clinical facts. It also needs a way to preserve that those facts belong to a specific maternal episode that began in pregnancy and remains clinically relevant after delivery. EpisodeOfCare is therefore included because it is the strongest base FHIR R4 candidate for carrying episode-level continuity context across multiple encounters and over time.

### Candidate FHIR role
- Primary resource: `EpisodeOfCare`

### Best-fit function in the package
- Longitudinal episode anchor for the maternal pathway
- Context carrier for pregnancy-related continuity beyond single encounters
- Organizational responsibility frame for the maternal episode

### MCEP sections best represented through EpisodeOfCare
EpisodeOfCare is the strongest candidate resource for representing or supporting:

- Maternal episode context.episodeId
- Maternal episode context.managingOrganization
- parts of maternal episode context that require a longitudinal frame rather than an encounter-only frame
- continuity linkage between pregnancy-related clinical resources across the same pathway

### Likely high-level EpisodeOfCare elements
The EpisodeOfCare resource would likely carry or support:
- `identifier`
- `status`
- `patient`
- `managingOrganization`
- `period`
- `careManager`, if explicitly modeled
- possibly profile-level extensions for maternal care stage or pregnancy-status semantics if needed

### Relation to other included resources
EpisodeOfCare would provide episode-level context for other resources in the package. In a postpartum GDM continuity package, it would relate especially to:

- Patient as the subject of the episode
- Condition as pregnancy-related GDM history within the episode
- Observations and results as episode-relevant findings
- MedicationStatement as treatment history within the episode
- RiskAssessment as interpreted postpartum risk arising from the episode
- Task as follow-up actions linked to continuity after the episode’s obstetric phase
- Composition as the narrative wrapper that explains the meaning of the episode in transition

### Key design logic
Postpartum GDM continuity depends on preserving that the woman’s metabolic follow-up needs are not isolated post-birth events, but consequences of a pregnancy-specific episode whose meaning continues after delivery. EpisodeOfCare is therefore important because it allows the package to express a longitudinal maternal pathway rather than reducing the exchange to disconnected encounter fragments.

### Working design decision
For the current thesis-stage design, EpisodeOfCare should be treated as the best-fit base FHIR R4 resource for carrying maternal episode continuity context in the postpartum GDM package.

### Important limitation
EpisodeOfCare is a best-fit resource, not a perfect maternity-specific model. Some MCEP fields such as pregnancy status, care stage, and pregnancy outcome do not map cleanly to core EpisodeOfCare elements and may still require supporting resources, narrative synthesis, or extension-level treatment.

### Open design question
A later refinement could define whether maternal episode context should be represented mainly through EpisodeOfCare plus supporting Observation and Composition content, or whether a more explicitly profiled maternity-specific episode model is needed.

## Condition

### Role in the package
Condition represents the pregnancy-related diagnosis or problem that remains clinically relevant to continuity after delivery. In a postpartum GDM continuity package, its main role is to preserve the gestational diabetes history as a clinically meaningful part of the maternal pathway rather than letting it disappear once the birth episode ends.

### Why it is included
A postpartum GDM continuity package must preserve the fact that the woman had gestational diabetes mellitus during the index pregnancy, because that history directly shapes postpartum metabolic follow-up and longer-term diabetes risk. Condition is therefore included as the strongest base FHIR R4 resource for representing the clinically established diagnosis within the continuity package.

### Candidate FHIR role
- Primary resource: `Condition`

### Best-fit function in the package
- Diagnosis/problem representation for GDM in the maternal episode
- Clinical continuity anchor for postpartum metabolic follow-up logic
- Structured carrier of diagnosis status, verification, onset, and explanatory note

### MCEP sections best represented through Condition
Condition is the strongest candidate resource for representing or supporting:

- Clinical summary.conditions.conditionId
- Clinical summary.conditions.code
- Clinical summary.conditions.display
- Clinical summary.conditions.clinicalStatus
- Clinical summary.conditions.verificationStatus
- Clinical summary.conditions.onsetDate
- Clinical summary.conditions.recordedDate
- Clinical summary.conditions.note

It may also support continuity interpretation when linked to RiskAssessment and follow-up Task resources.

### Likely high-level Condition elements
The Condition resource would likely carry or support:
- `identifier`
- `code`
- `clinicalStatus`
- `verificationStatus`
- `subject`
- `onsetDateTime`
- `recordedDate`
- `recorder`
- `note`

It may additionally require extension-level support for continuity-oriented maternal stage relevance.

### Relation to other included resources
Condition would sit in close relation to:
- Patient as the subject of the diagnosis
- EpisodeOfCare as the maternal episode context in which the diagnosis occurred
- Observation resources carrying relevant pregnancy and postpartum findings
- MedicationStatement if pregnancy treatment was used
- RiskAssessment as the interpreted postpartum risk arising from the GDM history
- Task resources for follow-up actions triggered by that history
- Composition as the narrative wrapper that explains why the diagnosis still matters after delivery

### Key design logic
The continuity problem in postpartum GDM is not that the diagnosis is unknown during pregnancy, but that its meaning may be lost after delivery if it is treated as resolved and no longer visible in ongoing care. Condition is therefore important because it preserves the GDM diagnosis as a traceable and clinically meaningful part of the continuity package even after the pregnancy itself has ended.

### Working design decision
For the current thesis-stage design, Condition should be treated as the primary structured resource for carrying the GDM diagnosis within the postpartum continuity package.

### Important limitation
Condition can represent the diagnosis itself, but it does not fully represent all continuity meaning attached to that diagnosis. Maternal stage relevance, future-oriented postpartum risk, and transfer of responsibility for follow-up still require additional resources such as RiskAssessment, Task, Composition, and possibly extension-level support.

### Open design question
A later refinement could define whether pregnancy-specific diagnoses such as GDM should always remain active in a continuity package after delivery, or whether the diagnosis status should be represented as resolved while continuity relevance is maintained elsewhere through risk and follow-up resources.

## Observation for postpartum BMI

### Role in the package
This Observation represents a continuity-relevant postpartum measurement that helps contextualize ongoing metabolic follow-up after gestational diabetes mellitus. In the postpartum GDM package, BMI is not included as a generic vital sign alone, but as a clinically relevant observation that contributes to metabolic interpretation after delivery.

### Why it is included
A postpartum GDM continuity package should preserve selected postpartum observations that remain relevant to follow-up planning. Postpartum BMI is included because it can contribute to the receiving clinician’s interpretation of metabolic risk and supports continuity-aware follow-up after obstetric discharge.

### Candidate FHIR role
- Primary resource: `Observation`

### Best-fit function in the package
- Structured carrier of postpartum BMI
- Supportive clinical context for interpreted postpartum metabolic risk
- Linkable observation contributing to continuity after delivery

### MCEP sections best represented through Observation
This Observation is the strongest candidate resource for representing or supporting:

- Clinical summary.keyObservations.observationId
- Clinical summary.keyObservations.code
- Clinical summary.keyObservations.display
- Clinical summary.keyObservations.value
- Clinical summary.keyObservations.unit
- Clinical summary.keyObservations.effectiveDate
- Clinical summary.keyObservations.interpretation
- Clinical summary.keyObservations.recordedDate
- Clinical summary.keyObservations.note

It may also require extension-level support for stage-of-relevance semantics.

### Likely high-level Observation elements
The Observation resource would likely carry or support:
- `identifier`
- `code`
- `subject`
- `valueQuantity`
- `effectiveDateTime`
- `interpretation`
- `issued`
- `performer`
- `note`

It may additionally require extension-level support for maternal stage relevance.

### Relation to other included resources
This Observation would relate closely to:
- Patient as the subject of measurement
- EpisodeOfCare as the maternal continuity context
- Condition representing prior GDM
- RiskAssessment as interpreted postpartum metabolic risk
- Task resources if the observation helps justify follow-up attention
- Composition as the narrative wrapper that explains why the observation matters in handoff

### Key design logic
Not every postpartum observation belongs in a continuity package. Postpartum BMI is included here because it supports interpretation of ongoing metabolic follow-up needs in the postpartum GDM context. Its value in the package is therefore continuity-oriented rather than merely descriptive.

### Working design decision
For the current thesis-stage design, postpartum BMI should be represented as an Observation resource included in the package when it contributes meaningfully to postpartum metabolic interpretation or follow-up planning.

### Important limitation
Observation can represent the BMI measurement itself, but not the full continuity meaning attached to it. Why it matters for postpartum follow-up may still need support from RiskAssessment, Composition, or explanatory note content.

### Open design question
A later refinement could define more explicitly which postpartum observations should qualify as continuity-relevant inclusions in the package, rather than allowing all potentially available postpartum measurements to enter the exchange artifact.
## Observation for pregnancy glucose result

### Role in the package
This Observation represents the pregnancy glucose result that supports the diagnosis of gestational diabetes mellitus and remains clinically relevant after delivery because it explains why postpartum metabolic follow-up is necessary.

### Why it is included
A postpartum GDM continuity package should preserve the key diagnostic result that underpins the pregnancy diagnosis. The pregnancy glucose result is included because postpartum continuity depends not only on knowing that GDM occurred, but also on preserving the diagnostic evidence that gave rise to that diagnosis.

### Candidate FHIR role
- Primary resource: `Observation`

### Best-fit function in the package
- Structured carrier of the pregnancy glucose result
- Diagnostic support for the GDM Condition
- Clinical evidence supporting postpartum RiskAssessment and follow-up Tasks

### MCEP sections best represented through Observation
This Observation is the strongest candidate resource for representing or supporting:

- Clinical summary.keyResults.resultId
- Clinical summary.keyResults.code
- Clinical summary.keyResults.display
- Clinical summary.keyResults.value
- Clinical summary.keyResults.effectiveDate
- Clinical summary.keyResults.resultType
- Clinical summary.keyResults.interpretation
- Clinical summary.keyResults.referenceRange
- Clinical summary.keyResults.recordedDate
- Clinical summary.keyResults.note

It may also require extension-level support for stage-of-relevance semantics.

### Likely high-level Observation elements
The Observation resource would likely carry or support:
- `identifier`
- `code`
- `subject`
- `value[x]`
- `effectiveDateTime`
- `category`
- `interpretation`
- `referenceRange`
- `issued`
- `performer`
- `note`

It may additionally require extension-level support for maternal stage relevance.

### Relation to other included resources
This Observation would relate closely to:
- Patient as the subject of the result
- EpisodeOfCare as the maternal episode context in which the result arose
- Condition representing GDM
- RiskAssessment as interpreted postpartum and future diabetes risk
- Task resources for postpartum reassessment triggered by this history
- Composition as narrative support for why the result remains important after delivery

### Key design logic
The postpartum continuity problem is not solved by carrying a diagnosis label alone. The package should also preserve the key result that made the diagnosis clinically meaningful. This is especially important when the receiving setting needs to understand why postpartum glucose reassessment is warranted even though the pregnancy itself has ended.

### Working design decision
For the current thesis-stage design, the key pregnancy glucose result should be represented as an Observation resource that remains visible in the postpartum continuity package as supporting evidence for the GDM history.

### Important limitation
In practice, pregnancy glucose results may be represented numerically, qualitatively, or through multiple related observations. A design-level package may therefore carry a simplified continuity-relevant representation rather than the full laboratory detail set in every case.

### Open design question
A later refinement could define whether the package should carry the original structured laboratory values in full, or only the minimum diagnostic result summary required to preserve postpartum continuity logic.

## MedicationStatement

### Role in the package
MedicationStatement represents pregnancy-related medication use that remains relevant to continuity after delivery. In the postpartum GDM continuity package, its main role is to preserve treatment history, especially when medication use during pregnancy helps explain postpartum risk interpretation and follow-up needs.

### Why it is included
A postpartum GDM continuity package should preserve not only the diagnosis, but also whether treatment was required during pregnancy. MedicationStatement is included because prior insulin use or other pregnancy-related treatment may remain clinically meaningful after delivery, especially when responsibility shifts from obstetric care to primary care.

### Candidate FHIR role
- Primary resource: `MedicationStatement`

### Best-fit function in the package
- Structured carrier of pregnancy-related medication use history
- Continuity support for understanding treatment intensity during pregnancy
- Linkable medication history contributing to postpartum risk interpretation and follow-up planning

### MCEP sections best represented through MedicationStatement
MedicationStatement is the strongest candidate resource for representing or supporting:

- Clinical summary.medications.medicationId
- Clinical summary.medications.code
- Clinical summary.medications.display
- Clinical summary.medications.status
- Clinical summary.medications.dose
- Clinical summary.medications.route
- Clinical summary.medications.frequency
- Clinical summary.medications.startDate
- Clinical summary.medications.endDate
- Clinical summary.medications.recordedDate
- Clinical summary.medications.note

It may also require extension-level support for stage-of-relevance semantics.

### Likely high-level MedicationStatement elements
The MedicationStatement resource would likely carry or support:
- `identifier`
- `medicationCodeableConcept`
- `subject`
- `status`
- `effectivePeriod`
- `dateAsserted`
- `informationSource`
- `dosage`
- `note`

It may additionally require extension-level support for maternal stage relevance.

### Relation to other included resources
MedicationStatement would relate closely to:
- Patient as the subject of medication use
- EpisodeOfCare as the maternal continuity context
- Condition representing GDM
- Observation resources that help contextualize treatment need
- RiskAssessment if treatment history contributes to postpartum risk interpretation
- Task resources if follow-up planning depends partly on prior treatment intensity
- Composition as narrative support for why treatment history still matters after delivery

### Key design logic
In postpartum GDM continuity, prior medication use can signal that the pregnancy required more active glycaemic management. The package therefore benefits from preserving medication history as part of continuity meaning, rather than allowing treatment information to disappear once the pregnancy medication is stopped.

### Working design decision
For the current thesis-stage design, pregnancy-related treatment history should be represented through MedicationStatement when the goal is to preserve clinically meaningful medication-use context across the transition to postpartum and primary care follow-up.

### Important limitation
MedicationStatement preserves medication use history, but it does not itself express why that history matters for continuity. Its meaning may still depend on linked Condition, Observation, RiskAssessment, Task, and narrative summary content.

### Open design question
A later refinement could define when medication history is important enough to include in the package, and when minimal continuity exchange should rely instead on diagnosis, risk, and follow-up planning alone.

## DocumentReference

### Role in the package
DocumentReference represents supporting clinical documents that remain important to continuity across the postpartum GDM handoff. In this package, its main role is to preserve traceable access to authored records such as discharge summaries that contain clinically meaningful context not fully reducible to structured elements alone.

### Why it is included
A postpartum GDM continuity package should not depend only on structured resources. Some transition-critical information, especially discharge recommendations, follow-up instructions, and contextual clinical summaries, may still be carried in authored documents. DocumentReference is therefore included because it provides a standard way to point to those supporting records without requiring the package to reproduce the full document content itself.

### Candidate FHIR role
- Primary resource: `DocumentReference`

### Best-fit function in the package
- Traceable reference to supporting clinical documents
- Continuity support for discharge and handoff documentation
- Linkable source record that complements structured continuity content

### MCEP sections best represented through DocumentReference
DocumentReference is the strongest candidate resource for representing or supporting:

- Clinical summary.documents.documentId
- Clinical summary.documents.documentType
- Clinical summary.documents.title
- Clinical summary.documents.status
- Clinical summary.documents.authorOrganization
- Clinical summary.documents.creationDate
- Clinical summary.documents.language
- Clinical summary.documents.linkReference
- Clinical summary.documents.note

It may also require extension-level support for stage-of-relevance semantics.

### Likely high-level DocumentReference elements
The DocumentReference resource would likely carry or support:
- `identifier`
- `status`
- `type`
- `subject`
- `author`
- `custodian`
- `content.attachment.title`
- `content.attachment.creation`
- `content.attachment.language`
- `content.attachment.url`
- `description`

It may additionally require extension-level support for maternal stage relevance.

### Relation to other included resources
DocumentReference would relate closely to:
- Patient as the subject of the referenced document
- EpisodeOfCare as the maternal continuity context in which the document was created
- Composition as the package-level narrative wrapper
- Task resources when the referenced document contains follow-up instructions
- Provenance when document origin or traceability needs to be clarified
- Organization resources representing the authoring or custodial institution

### Key design logic
Structured continuity content is essential, but not always sufficient. In postpartum GDM handoff, discharge summaries and related authored documents may still contain clinically meaningful transition information that supports safe follow-up. DocumentReference therefore helps preserve continuity without forcing the design to rely only on free text or only on structured fields.

### Working design decision
For the current thesis-stage design, supporting authored records such as discharge summaries should be represented through DocumentReference when they contribute to continuity across the transition from obstetric care to primary care.

### Important limitation
DocumentReference preserves access to supporting documents, but it does not itself guarantee that the document content is semantically structured or computable. Its main role in the package is traceable documentary support rather than full semantic representation.

### Open design question
A later refinement could define which documents are important enough to include in a minimal continuity package, and whether some document-derived information should instead be promoted into structured resources when continuity depends on it operationally.

## RiskAssessment

### Role in the package
RiskAssessment represents the interpreted postpartum risk that remains clinically relevant after a pregnancy complicated by gestational diabetes mellitus. In the postpartum GDM continuity package, its main role is to preserve future-oriented metabolic risk meaning across the handoff from obstetric care to primary care.

### Why it is included
A postpartum GDM continuity package should preserve more than diagnosis and raw findings. It should also preserve the interpreted significance of those findings for ongoing care. RiskAssessment is therefore included because it is the strongest base FHIR R4 candidate for representing clinically reasoned future-oriented risk, including why postpartum follow-up remains necessary after delivery.

### Candidate FHIR role
- Primary resource: `RiskAssessment`

### Best-fit function in the package
- Structured carrier of interpreted postpartum metabolic risk
- Link between pregnancy history and future-oriented follow-up need
- Clinical continuity resource that explains why the handoff matters beyond the birth episode

### MCEP sections best represented through RiskAssessment
RiskAssessment is the strongest candidate resource for representing or supporting:

- Risk interpretation.riskItems.riskId
- Risk interpretation.riskItems.display
- Risk interpretation.riskItems.riskLevel
- Risk interpretation.riskItems.basis
- Risk interpretation.riskItems.timeHorizon
- Risk interpretation.riskItems.clinicalMeaning
- Risk interpretation.riskItems.recommendedAttention
- Risk interpretation.riskItems.recordedDate
- Risk interpretation.riskItems.recorder
- Risk interpretation.riskItems.note

It may also require extension-level support for risk category, maternal stage relevance, and follow-up-oriented timing semantics.

### Likely high-level RiskAssessment elements
The RiskAssessment resource would likely carry or support:
- `identifier`
- `subject`
- `occurrenceDateTime`
- `performer`
- `basis`
- `prediction.outcome`
- `prediction.qualitativeRisk`
- `prediction.when[x]`
- `mitigation`
- `note`

It may additionally require extension-level support for risk type and maternal stage relevance.

### Relation to other included resources
RiskAssessment would relate closely to:
- Patient as the subject of the assessed risk
- EpisodeOfCare as the maternal pathway from which the risk arises
- Condition representing prior GDM
- Observation resources carrying supportive pregnancy or postpartum evidence
- MedicationStatement where treatment history contributes to risk interpretation
- Task resources representing the follow-up actions triggered by the interpreted risk
- Composition as the narrative wrapper that explains the continuity meaning of the risk

### Key design logic
The postpartum GDM continuity problem is not solved by carrying diagnosis history alone. The receiving setting must also understand why that history still matters after delivery. RiskAssessment is therefore important because it preserves the interpreted future-oriented meaning of prior GDM, especially the need for postpartum glucose reassessment and longer-term metabolic awareness.

### Working design decision
For the current thesis-stage design, RiskAssessment should be treated as the primary structured resource for expressing interpreted postpartum metabolic risk in the continuity package.

### Important limitation
RiskAssessment is a best-fit resource, not a perfect match for every MCEP risk field. Some elements, especially risk category, maternal stage relevance, and due-by logic linked to follow-up planning, may still require extension-level treatment or explicit linkage to Task resources.

### Open design question
A later refinement could define more explicitly how much future-oriented risk meaning should be preserved inside RiskAssessment itself, and how much should instead be distributed across linked Condition, Observation, Task, and Composition content.

## RiskAssessment

### Role in the package
RiskAssessment represents the interpreted postpartum risk that remains clinically relevant after a pregnancy complicated by gestational diabetes mellitus. In the postpartum GDM continuity package, its main role is to preserve future-oriented metabolic risk meaning across the handoff from obstetric care to primary care.

### Why it is included
A postpartum GDM continuity package should preserve more than diagnosis and raw findings. It should also preserve the interpreted significance of those findings for ongoing care. RiskAssessment is therefore included because it is the strongest base FHIR R4 candidate for representing clinically reasoned future-oriented risk, including why postpartum follow-up remains necessary after delivery.

### Candidate FHIR role
- Primary resource: `RiskAssessment`

### Best-fit function in the package
- Structured carrier of interpreted postpartum metabolic risk
- Link between pregnancy history and future-oriented follow-up need
- Clinical continuity resource that explains why the handoff matters beyond the birth episode

### MCEP sections best represented through RiskAssessment
RiskAssessment is the strongest candidate resource for representing or supporting:

- Risk interpretation.riskItems.riskId
- Risk interpretation.riskItems.display
- Risk interpretation.riskItems.riskLevel
- Risk interpretation.riskItems.basis
- Risk interpretation.riskItems.timeHorizon
- Risk interpretation.riskItems.clinicalMeaning
- Risk interpretation.riskItems.recommendedAttention
- Risk interpretation.riskItems.recordedDate
- Risk interpretation.riskItems.recorder
- Risk interpretation.riskItems.note

It may also require extension-level support for risk category, maternal stage relevance, and follow-up-oriented timing semantics.

### Likely high-level RiskAssessment elements
The RiskAssessment resource would likely carry or support:
- `identifier`
- `subject`
- `occurrenceDateTime`
- `performer`
- `basis`
- `prediction.outcome`
- `prediction.qualitativeRisk`
- `prediction.when[x]`
- `mitigation`
- `note`

It may additionally require extension-level support for risk type and maternal stage relevance.

### Relation to other included resources
RiskAssessment would relate closely to:
- Patient as the subject of the assessed risk
- EpisodeOfCare as the maternal pathway from which the risk arises
- Condition representing prior GDM
- Observation resources carrying supportive pregnancy or postpartum evidence
- MedicationStatement where treatment history contributes to risk interpretation
- Task resources representing the follow-up actions triggered by the interpreted risk
- Composition as the narrative wrapper that explains the continuity meaning of the risk

### Key design logic
The postpartum GDM continuity problem is not solved by carrying diagnosis history alone. The receiving setting must also understand why that history still matters after delivery. RiskAssessment is therefore important because it preserves the interpreted future-oriented meaning of prior GDM, especially the need for postpartum glucose reassessment and longer-term metabolic awareness.

### Working design decision
For the current thesis-stage design, RiskAssessment should be treated as the primary structured resource for expressing interpreted postpartum metabolic risk in the continuity package.

### Important limitation
RiskAssessment is a best-fit resource, not a perfect match for every MCEP risk field. Some elements, especially risk category, maternal stage relevance, and due-by logic linked to follow-up planning, may still require extension-level treatment or explicit linkage to Task resources.

### Open design question
A later refinement could define more explicitly how much future-oriented risk meaning should be preserved inside RiskAssessment itself, and how much should instead be distributed across linked Condition, Observation, Task, and Composition content.

## Task for primary care follow-up

### Role in the package
This Task represents the broader transfer of follow-up responsibility to primary care after obstetric discharge. Its role is to preserve that postpartum metabolic continuity does not end with birth care, but continues in another setting with an expected receiving actor.

### Why it is included
A postpartum GDM continuity package should preserve not only the testing action itself, but also the wider expectation that primary care assumes responsibility for ongoing postpartum and longer-term metabolic follow-up. Task is therefore included because it is the strongest base FHIR R4 candidate for expressing responsibility-bearing follow-up work across a care transition.

### Candidate FHIR role
- Primary resource: `Task`

### Best-fit function in the package
- Structured carrier of the requested primary care follow-up action
- Operational representation of responsibility transfer after discharge
- Continuity support for linking interpreted risk to ongoing non-obstetric care

### MCEP sections best represented through Task
This Task is the strongest candidate resource for representing or supporting:

- Follow-up plan and responsibility.followUpItems.followUpId
- Follow-up plan and responsibility.followUpItems.actionType
- Follow-up plan and responsibility.followUpItems.display
- Follow-up plan and responsibility.followUpItems.status
- Follow-up plan and responsibility.followUpItems.dueBy
- Follow-up plan and responsibility.followUpItems.responsibleActor
- Follow-up plan and responsibility.followUpItems.responsibleOrganization
- Follow-up plan and responsibility.followUpItems.triggerReason
- Follow-up plan and responsibility.followUpItems.linkedRisk
- Follow-up plan and responsibility.followUpItems.recordedDate
- Follow-up plan and responsibility.followUpItems.recorder
- Follow-up plan and responsibility.followUpItems.note

It may also require extension-level support for maternal stage relevance.

### Likely high-level Task elements
The Task resource would likely carry or support:
- `identifier`
- `code`
- `description`
- `status`
- `intent`
- `for`
- `authoredOn`
- `owner`
- `requester`
- `restriction`
- `reasonCode`
- `note`

It may additionally require extension-level support for stage relevance and explicit linkage to interpreted risk.

### Relation to other included resources
This Task would relate closely to:
- Patient as the subject of the requested follow-up
- RiskAssessment as the reason ongoing follow-up remains necessary
- Condition representing prior GDM
- EpisodeOfCare as the obstetric maternal pathway that is ending or changing phase
- Composition as the narrative explanation of responsibility transfer
- Organization resources representing the receiving primary care setting

### Key design logic
The central continuity issue after GDM is not only whether one test is ordered, but whether ongoing responsibility clearly passes from obstetric care to primary care. This Task is therefore important because it expresses the broader follow-up obligation rather than only a single discrete diagnostic action.

### Working design decision
For the current thesis-stage design, primary care follow-up after postpartum discharge should be represented through a dedicated Task resource when continuity depends on explicit responsibility transfer and continuing oversight beyond one isolated action.

### Important limitation
Task can represent the requested follow-up and its responsible owner, but it does not by itself explain the full clinical rationale. That meaning still depends on linked Condition, Observation, RiskAssessment, and narrative package content.

### Open design question
A later refinement could define whether primary care follow-up should remain a distinct Task from postpartum glucose reassessment, or whether both should be bundled within a shared follow-up structure under tighter implementation rules.

## Provenance

### Role in the package
Provenance represents the authorship, source context, and verification background of the postpartum GDM continuity package. Its role is to preserve traceability so that the receiving setting can understand where the package came from, who was responsible for it, and what source context supports its content.

### Why it is included
A postpartum GDM continuity package should not only carry clinical meaning, but also make that meaning trustworthy and interpretable across settings. Provenance is therefore included because continuity-sensitive handoff depends partly on knowing who authored or reviewed the package, from which source context it was generated, and how it relates to the originating care environment.

### Candidate FHIR role
- Primary resource: `Provenance`

### Best-fit function in the package
- Structured carrier of package-level authorship and traceability
- Source-context support for the continuity package
- Trust layer linking the package to its originating clinical environment

### MCEP sections best represented through Provenance
Provenance is the strongest candidate resource for representing or supporting:

- Provenance.authorRole
- Provenance.authorOrganization
- Provenance.sourceEncounterId
- Provenance.sourceSystemReference
- Provenance.verificationNote

It may also support authorship and traceability context for individual resources in the package when needed.

### Likely high-level Provenance elements
The Provenance resource would likely carry or support:
- `target`
- `recorded`
- `agent`
- `entity`
- `activity`
- possibly profile-level extensions for package verification notes or source-system semantics if needed

### Relation to other included resources
Provenance would relate especially to:
- Composition as the package’s clinical anchor
- Task resources when the receiving side needs confidence in who initiated the follow-up actions
- RiskAssessment when interpreted risk needs accountable authorship context
- DocumentReference when source documentation and traceability need to remain visible
- Encounter and Organization resources as part of the source environment from which the package emerged

### Key design logic
In postpartum GDM continuity, the receiving setting must be able to trust that the handoff package reflects a real obstetric discharge context and accountable clinical review. Provenance is therefore important because it supports continuity not by adding new clinical facts, but by preserving authorship, traceability, and source reliability across the transition.

### Working design decision
For the current thesis-stage design, Provenance should be treated as the primary resource for representing package-level authorship and source context in the postpartum GDM continuity package.

### Important limitation
Provenance aligns conceptually with MCEP provenance well, but some package-specific semantics, especially source-system reference logic and verification-note meaning, may still require profiling or extension-level clarification because MCEP is a continuity package rather than a single native FHIR resource.

### Open design question
A later refinement could define whether one package-level Provenance resource is sufficient, or whether some implementations would need both package-level provenance and resource-level provenance for selected high-risk elements such as RiskAssessment or follow-up Tasks.

## Organization

### Role in the package
Organization represents the institutional settings involved in the postpartum GDM continuity package. Its role is to preserve which care organizations are sending, receiving, managing, authoring, or otherwise supporting the transition from obstetric care to primary care.

### Why it is included
A postpartum GDM continuity package should preserve not only clinical content, but also organizational context. Continuity across transition depends partly on knowing which institution managed the maternal episode, which institution authored the package, and which institution is expected to receive or continue follow-up care. Organization is therefore included because it provides the clearest base FHIR R4 representation of those institutional actors.

### Candidate FHIR role
- Primary resource: `Organization`

### Best-fit function in the package
- Structured carrier of sending, receiving, managing, and authoring institutional context
- Organizational reference target across the package
- Support for responsibility and source-context interpretation

### MCEP sections best represented through Organization
Organization is the strongest candidate resource for representing or supporting:

- Envelope.sendingOrganization
- Envelope.receivingOrganization
- Maternal episode context.managingOrganization
- Provenance.authorOrganization
- Clinical summary.documents.authorOrganization
- Follow-up plan and responsibility.followUpItems.responsibleOrganization

### Likely high-level Organization elements
The Organization resource would likely carry or support:
- `identifier`
- `name`
- `type`
- `telecom`, if needed
- `address`, if needed

In many cases, the package may include more than one Organization resource rather than one single organizational entry.

### Relation to other included resources
Organization would relate closely to:
- EpisodeOfCare through managing organization
- Provenance through authoring or accountable organization
- Task through receiving or responsible organization
- DocumentReference through author or custodian logic
- Composition through author references, where relevant
- Encounter through service-provider context, if included

### Key design logic
Postpartum continuity is partly organizational. The handoff from obstetric care to primary care depends on a shift in responsibility across institutions or organizational settings, not only across clinical facts. Organization is therefore important because it preserves where responsibility sat before the transition and where it is expected to sit afterward.

### Working design decision
For the current thesis-stage design, Organization should be treated as a supporting but necessary resource in the postpartum GDM continuity package, with multiple Organization instances included as needed for sending, receiving, managing, and authoring roles.

### Important limitation
Organization can represent institutional actors clearly, but it does not by itself explain the meaning of their involvement. That meaning still depends on how the Organization resources are linked through EpisodeOfCare, Task, DocumentReference, Provenance, Composition, and package-level logic.

### Open design question
A later refinement could define whether all organizational roles should be represented through distinct Organization resources, or whether some roles should instead be carried through PractitionerRole or CareTeam structures when the transition depends more strongly on service-level than institution-level accountability.

## Encounter

### Role in the package
Encounter represents the source clinical interaction or care event from which the postpartum GDM continuity package emerges. In this package, its main role is to preserve the immediate source context of the obstetric or postpartum discharge transition.

### Why it is included
A postpartum GDM continuity package may need to preserve not only the longitudinal maternal episode, but also the concrete source encounter in which discharge, handoff, or package generation occurred. Encounter is therefore included because it is the strongest base FHIR R4 candidate for representing the immediate clinical event or service contact from which the continuity package is assembled.

### Candidate FHIR role
- Primary resource: `Encounter`

### Best-fit function in the package
- Source-context resource for the discharge or handoff event
- Immediate clinical event anchor supporting provenance and transition interpretation
- Contextual link between package generation and originating care contact

### MCEP sections best represented through Encounter
Encounter is the strongest candidate resource for representing or supporting:

- Provenance.sourceEncounterId
- parts of Handoff context when the transition emerges from a specific discharge encounter
- supporting context for package generation date and source environment

Encounter may also support interpretation of where the transition occurred, but it is not the main carrier of handoff meaning.

### Likely high-level Encounter elements
The Encounter resource would likely carry or support:
- `identifier`
- `status`
- `class`
- `subject`
- `period`
- `serviceProvider`
- `reasonCode`, if needed
- `location`, if needed

### Relation to other included resources
Encounter would relate closely to:
- Patient as the subject of the clinical interaction
- EpisodeOfCare as the broader maternal pathway surrounding the encounter
- Provenance as the source encounter context of package generation
- Composition when the narrative handoff is anchored to a discharge event
- Organization through service-provider or institutional context
- Task resources when follow-up actions are initiated from the encounter

### Key design logic
The postpartum continuity package is longitudinal in design, but it still often emerges from one concrete transition point, such as discharge from obstetric or postpartum care. Encounter is therefore useful because it anchors the package to the immediate clinical event that triggered handoff, while EpisodeOfCare continues to carry the broader maternal pathway context.

### Working design decision
For the current thesis-stage design, Encounter should be treated as an optional but useful supporting resource in the postpartum GDM continuity package when the source discharge or handoff event needs to remain explicit.

### Important limitation
Encounter supports source-event context, but it should not replace EpisodeOfCare as the main longitudinal continuity anchor. Over-reliance on Encounter would risk pulling the package back toward an encounter-based view of continuity, which the MCEP is specifically designed to move beyond.

### Open design question
A later refinement could define more explicitly when Encounter should be included in the minimal package, and when provenance plus episode-level context are sufficient without a separate encounter resource.
