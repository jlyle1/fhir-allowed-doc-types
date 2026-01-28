# Operations

## $allowed-document-types

Returns the list of clinical document types the current user is authorized to create.

### Invocation

This is a system-level operation:

```
GET /$allowed-document-types
POST /$allowed-document-types
```

### Input Parameters

| Name | Cardinality | Type | Description |
|------|-------------|------|-------------|
| user | 0..1 | Reference(Practitioner) | Defaults to current user from token |
| location | 0..1 | Reference(Location) | Location context (clinic, ward) |
| encounter | 0..1 | Reference(Encounter) | Encounter context if applicable |
| category | 0..* | Coding | Filter by document category |

### Output Parameters

| Name | Cardinality | Type | Description |
|------|-------------|------|-------------|
| documentType | 0..* | (complex) | A document type the user may create |
| documentType.code | 1..1 | Coding | Document type code (typically LOINC) |
| documentType.title | 1..1 | string | Display title |
| documentType.templateId | 0..1 | string | EHR-specific template identifier |
| documentType.category | 0..* | Coding | Categories this type belongs to |

### Example Request

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "location",
      "valueReference": { "reference": "Location/ward-3b" }
    },
    {
      "name": "category",
      "valueCoding": {
        "system": "http://loinc.org",
        "code": "LP173418-7",
        "display": "Progress note"
      }
    }
  ]
}
```

### Example Response

See [Example Response](Parameters-allowed-document-types-response-example.html).
