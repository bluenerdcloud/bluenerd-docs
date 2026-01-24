# Compliance Artifact (source of truth)

The canonical JSON schema, example payload, and PDF template live in **bluenerd-api**:

- `contracts/compliance/compliance-artifact.schema.json`
- `contracts/compliance/examples/compliance-submission.sample.json`
- `templates/compliance-summary.hbs`

> Engineering: validate wizard output against the schema, store JSON to S3, render PDF from the template.
