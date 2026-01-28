# Allowed Document Types Implementation Guide

This Implementation Guide defines a FHIR operation for retrieving the list of clinical document types a user is authorized to create in a given context.

## Use Case

When a SMART on FHIR application launches from an EHR, it often needs to know which document types the current user can create. This varies by:

- User role/credentials
- Location (clinic, ward, facility)
- Encounter context
- EHR-specific configuration

This IG defines the `$allowed-document-types` operation to provide a standardized, FHIR-native way to query this information.

## Target EHR Platforms

This operation is designed to be implementable as a facade over:

- **VistA** - Wrapping TIU (Text Integration Utilities) RPCs
- **Cerner/Oracle Health** - Wrapping Document Ontology APIs

## Approaches

This IG supports two approaches for retrieving allowed document types:

| Approach | Description |
|----------|-------------|
| **[$allowed-document-types](operations.html)** | FHIR operation for direct API queries |
| **[CDS Hooks](hooks.html)** | Proactive suggestions during clinical workflows |

## Quick Start

```
GET /$allowed-document-types
```

Returns a `Parameters` resource containing the document types available to the current user (derived from the access token context).

See [Operations](operations.html) for the FHIR operation details, or [CDS Hooks](hooks.html) for workflow integration.
