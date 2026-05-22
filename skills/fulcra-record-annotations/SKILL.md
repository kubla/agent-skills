---
name: fulcra-record-annotations
description: "Record data for custom Annotations within the Fulcra environment during user onboarding."
---

# Fulcra Record Annotations

Use this skill to record (ingest) data entries for existing custom Annotations in a user's Fulcra account. 

## Authentication

Always retrieve a fresh access token using the Fulcra CLI before making requests:
```bash
TOKEN=$(uv tool run fulcra-api auth print-access-token)
```

## Recording Data

Data is recorded by sending a `POST` request to the Fulcra Ingest API.

```bash
curl -i -X POST \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{ ... json payload ... }' \
  https://api.fulcradynamics.com/ingest/v1/record
```

### Payload Structure Rules
1. **`metadata.source`**: Must include an array with the annotation's specific identifier: `["com.fulcradynamics.annotation.<ANNOTATION_ID>"]`. This maps the recording to the exact schema created earlier.
2. **`metadata.data_type`**: Must match the annotation type in CamelCase (e.g., `ScaleAnnotation`, `MomentAnnotation`, `NumericAnnotation`, etc.).
3. **`metadata.recorded_at`**: Must be a valid ISO 8601 timestamp in UTC (e.g., `2026-05-22T20:15:57Z`).
4. **`data`**: Must be a **stringified JSON string** containing the `value` (if the annotation type requires one) and an optional `note`.

### Examples

#### 1. Scale Annotation
Used for logging a value on a defined scale (e.g., 1-5).
```json
{
  "metadata": {
    "data_type": "ScaleAnnotation",
    "tags": [],
    "recorded_at": "2026-05-22T20:15:57Z",
    "content_type": "application/json",
    "source": [
      "com.fulcradynamics.annotation.94dbf87b-3c0d-43d5-a501-ad61a8cc3226"
    ]
  },
  "data": "{\"note\":\"This is an example of a note being attached to a recording.\",\"value\":4}"
}
```
