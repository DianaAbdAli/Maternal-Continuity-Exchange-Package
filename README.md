# Maternal Continuity Exchange Package

This repository contains supplementary design artifacts developed for a master’s thesis on workflow-informed semantic interoperability for improving continuity of care across the maternal care continuum.

The repository focuses on the Maternal Continuity Exchange Package (MCEP), a lightweight, FHIR R4-aligned design artifact intended to preserve continuity-critical information across maternal care transitions. The included tracer scenario is a postpartum handoff from obstetric care to primary care after gestational diabetes mellitus (GDM).

## Repository structure

| Folder | Content | Purpose |
|---|---|---|
| `case-frame/` | Postpartum GDM case frame | Defines the tracer scenario used to test the design artifact. |
| `mcep/` | MCEP specification | Describes the conceptual structure of the Maternal Continuity Exchange Package. |
| `examples/` | Postpartum GDM MCEP example instance | Shows how the MCEP can be populated for the tracer scenario. |
| `mapping/` | MCEP to FHIR R4 mapping | Maps MCEP sections to relevant FHIR R4 resources and identifies areas requiring profiling or interpretation. |
| `fhir-assembly/` | FHIR assembly pattern | Describes how the postpartum GDM continuity package can be represented at FHIR resource level. |

## Scope

This repository is not a national implementation guide and does not define a production-ready FHIR profile. It is a thesis-level supplementary repository that documents the design logic, example artifacts and mapping approach used in the accompanying thesis.

## Thesis context

The artifacts support the thesis design chapter by separating detailed technical specifications from the main body of the thesis. The main thesis text summarizes and analyzes the design, while this repository preserves the supplementary artifacts in a transparent and version-controlled format.
