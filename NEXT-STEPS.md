# Next-step memo: from MCEP v1.0 toward the Phase 1 dyad implementation guide

_Supervision memo · 2026-06-01 · etza_

## Where v1.0 stands

The thesis submission establishes a coherent maternal continuity exchange package for one subject across a single transition — the postpartum handoff from obstetric care to primary care after gestational diabetes mellitus. The content specification, the populated example, the FHIR R4 mapping matrix and the assembly outline together carry one substantive conclusion: continuity meaning survives the mapping into base FHIR resources only through multi-resource assembly, narrative anchoring and a defined set of extensions. That conclusion is the foundation the next iteration builds on, and it was reached independently, which is the part that matters most.

The distance still to travel is the family. The package today represents the woman, whereas the engagement requires the computable mother–pregnancy–newborn relationship that the surrounding health information exchanges currently leave unrepresented. The Related Records section anticipates this relationship, yet the frozen example carries no related-record entry, so the dyad remains designed rather than demonstrated. Closing that distance is the organising purpose of the work below.

## Priority A — close the dyad gap

**NS-1. Instantiate the maternal–newborn linkage.** Populate `relatedRecords.relatedRecordItems` in a worked example so that the maternal record points to a specific newborn record together with the pregnancy episode that binds the two, carrying `relationType`, `targetRecordType`, `linkageStatus` and `clinicalRelevance` explicitly. Done when a reader can trace mother → pregnancy episode → newborn through the structured fields alone, with the narrative serving only as confirmation.

**NS-2. Produce a dyad-bearing tracer.** Design a second case in which infant data shapes maternal follow-up, or maternal history shapes infant follow-up, so the linkage earns its place through a clinical reason that would fail without it. The clinical scoping already favours postpartum depression linked across the two records as one candidate, however the choice of tracer and its justification are yours to make. Done when the example exercises the linkage under a reason that collapses if the link is absent.

**NS-3. Extend the assembly to the newborn.** Represent the newborn Patient resource and the linkage mechanism at FHIR level — through RelatedPerson, through a shared EpisodeOfCare reference, or through a profiled extension — and record the design decision alongside the continuity cost it carries. Done when the Bundle holds both subjects with the linkage resource, and the Related Records rows in the mapping matrix move from "not present in example" to instantiated with a stated mapping status.

## Priority B — internal consistency, lowest cost, do first

**NS-4.** The assembly pattern file repeats the RiskAssessment section and omits the postpartum glucose reassessment Task; restore the missing Task write-up and remove the duplicated block.

**NS-5.** The assembly instance outline lists MedicationStatement in the Bundle yet carries no resource block for it; add the block so the outline agrees with both the Bundle entry list and the MCEP example.

**NS-6.** The BMI interpretation reads `borderline` in the mapping matrix and `abnormal` in the example instance; reconcile to a single value and propagate it across the files.

## Priority C — profiling and conformance path

**NS-7.** Replace the logical placeholder codes with terminology bindings, SNOMED CT for conditions and procedures and LOINC for observations and results, at least across the tracer's core elements, and record which bindings stay open for later resolution.

**NS-8.** Draft StructureDefinition stubs for the elements the mapping flagged as requiring an extension or profile, beginning with stage-of-relevance and the maternal episode context, so the profiling burden is documented even where the full profiles remain beyond thesis scope.

**NS-9.** Specify the conformance expectations the package would impose, covering mandatory-element presence and linkage validity, with completeness measures such as the share of packages that carry an explicit follow-up responsibility. This becomes the seed of the data-quality governance the proposal calls for.

## Priority D — coverage beyond the single tracer

**NS-10.** Add or outline at least one upstream transition, antenatal-to-intrapartum or intrapartum-to-postpartum, so the package demonstrates the longitudinal episode across more than its closing handoff. Where postpartum GDM stays the single worked tracer, document the reasoning that makes it representative.

## Beyond the thesis, on the proposal path

Two items sit inside the engagement rather than the thesis, recorded here so the boundary stays visible. The first is a data-readiness assessment of the package against the published Riayati, NABIDH and Malaffi interface specifications, naming for each MCEP element where it survives the mapping cleanly, where it degrades and where it breaks. The second is an enrichment-strategy decision that follows from that assessment and settles how the missing structure is to be supplied. Both depend on the dyad work above, so they wait until Priority A holds.
