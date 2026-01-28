# CDS Hooks

This Implementation Guide also supports a CDS Hooks approach for proactively presenting available document types during clinical workflows.

## document-select Hook

The `document-select` hook fires when an application (such as an AI Scribe) has prepared a clinical document and the user needs to select the appropriate document type before saving.

### Workflow

1. AI Scribe generates clinical documentation from an encounter
2. Application invokes `document-select` hook
3. CDS Service returns available document types as selectable suggestions
4. User selects the appropriate document type
5. Application creates the document with the selected type

### Hook Definition

| Property | Value |
|----------|-------|
| **hook** | `document-select` |
| **description** | Invoked when a user needs to select a document type for a newly created document |
| **workflow** | Documentation / Note Creation |

### Context

| Field | Optionality | Prefetch Token | Type | Description |
|-------|-------------|----------------|------|-------------|
| `userId` | REQUIRED | Yes | string | Practitioner FHIR resource id (e.g., `Practitioner/123`) |
| `patientId` | REQUIRED | Yes | string | Patient FHIR resource id |
| `encounterId` | OPTIONAL | Yes | string | Encounter FHIR resource id if applicable |
| `locationId` | OPTIONAL | Yes | string | Location FHIR resource id (clinic, ward) |
| `category` | OPTIONAL | No | array | Array of Coding objects to filter by category |

### Example Request

```json
{
  "hookInstance": "d1577c69-dfbe-44ad-ba6d-3e05e953b2ea",
  "hook": "document-select",
  "context": {
    "userId": "Practitioner/dr-smith",
    "patientId": "Patient/123",
    "encounterId": "Encounter/456",
    "locationId": "Location/ward-3b"
  },
  "prefetch": {
    "user": {
      "resourceType": "Practitioner",
      "id": "dr-smith",
      "name": [{ "text": "Dr. Jane Smith" }]
    }
  }
}
```

### Example Response

The CDS Service returns a card with selectable suggestions for each available document type:

```json
{
  "cards": [
    {
      "uuid": "allowed-doc-types-card",
      "summary": "Select document type",
      "detail": "Choose the appropriate document type for this clinical note.",
      "indicator": "info",
      "source": {
        "label": "Document Authorization Service",
        "url": "https://example.org/document-authz"
      },
      "selectionBehavior": "at-most-one",
      "suggestions": [
        {
          "label": "Progress Note",
          "uuid": "suggestion-progress-note",
          "isRecommended": true,
          "actions": [
            {
              "type": "create",
              "description": "Create Progress Note",
              "resource": {
                "resourceType": "DocumentReference",
                "status": "current",
                "type": {
                  "coding": [
                    {
                      "system": "http://loinc.org",
                      "code": "11506-3",
                      "display": "Progress note"
                    }
                  ]
                }
              }
            }
          ]
        },
        {
          "label": "Discharge Summary",
          "uuid": "suggestion-discharge-summary",
          "actions": [
            {
              "type": "create",
              "description": "Create Discharge Summary",
              "resource": {
                "resourceType": "DocumentReference",
                "status": "current",
                "type": {
                  "coding": [
                    {
                      "system": "http://loinc.org",
                      "code": "18842-5",
                      "display": "Discharge summary"
                    }
                  ]
                }
              }
            }
          ]
        },
        {
          "label": "Consultation Note",
          "uuid": "suggestion-consult-note",
          "actions": [
            {
              "type": "create",
              "description": "Create Consultation Note",
              "resource": {
                "resourceType": "DocumentReference",
                "status": "current",
                "type": {
                  "coding": [
                    {
                      "system": "http://loinc.org",
                      "code": "11488-4",
                      "display": "Consultation note"
                    }
                  ]
                }
              }
            }
          ]
        }
      ]
    }
  ]
}
```

### Integration with $allowed-document-types

The CDS Service implementing this hook may call the [$allowed-document-types](operations.html) operation internally to determine available document types, then transform the response into CDS Hooks cards.

```
┌─────────────┐    document-select    ┌─────────────┐    $allowed-document-types    ┌──────────┐
│  AI Scribe  │ ───────────────────►  │ CDS Service │ ────────────────────────────► │   EHR    │
│             │ ◄─────────────────    │             │ ◄────────────────────────────  │   FHIR   │
└─────────────┘    cards/suggestions  └─────────────┘       Parameters              └──────────┘
```

## Comparison: CDS Hooks vs Operation

| Approach | Best For |
|----------|----------|
| **CDS Hooks** | Integrated EHR workflows where the hook is fired automatically |
| **$allowed-document-types** | Direct API queries, non-EHR clients, or when CDS Hooks infrastructure unavailable |

Both approaches return the same underlying authorization data; CDS Hooks simply wraps it in a user-facing presentation format.
