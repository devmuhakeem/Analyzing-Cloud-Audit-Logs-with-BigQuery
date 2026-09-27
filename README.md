# Analyzing Cloud Audit Logs with BigQuery

A hands-on Google Cloud lab where I generated real audit log activity, exported it to BigQuery, and wrote SQL queries to reconstruct who did what to which resources — the core workflow behind investigating a security incident.

## Scenario
A fictional bank had migrated to a hybrid cloud environment and was hit with a high-severity alert involving unauthorized access to cloud resources. As someone shadowing the incident response team, my job was to recreate the underlying activity, export the resulting logs, and analyze them to understand exactly what happened.

## What I did

### 1. Generated account activity
Used Cloud Shell to create and delete a mix of resources — a storage bucket, a file upload, a VPC network, and a Compute Engine VM — all of which get recorded as Cloud Audit Logs automatically.

### 2. Exported the audit logs to BigQuery
Built a Logs Explorer query filtered to Cloud Audit activity logs, then created a log sink (`AuditLogsExport`) routing matching logs into a new BigQuery dataset (`auditlogs_dataset`) for ongoing analysis. Confirmed the export was correctly permissioned by checking that Logging's service account had Data Editor access on the dataset.

### 3. Generated more activity to analyze
Created two more buckets, uploaded another file, deleted a VM, then deleted both buckets — giving the exported dataset a richer set of events to query.

### 4. Investigated in Logs Explorer first
Switched to a second user account (mirroring how the person who caused activity and the person investigating it are usually different people) and used Logs Explorer's "Show matching entries" feature to progressively narrow a log entry down into a reusable filter — first by service name, then by method name — to isolate all bucket-deletion events and identify which account performed them.

### 5. Queried the exported logs in BigQuery
Wrote SQL against the exported audit log table to answer two specific questions:
- Which users deleted Compute Engine VMs in the last 7 days
- Which users deleted Cloud Storage buckets in the last 7 days

Both queries correctly surfaced the activity generated earlier in the lab, including the responsible account and exact resource names.

## Key takeaways
- Logs Explorer is great for interactively narrowing down to a specific event, but BigQuery is what makes it possible to ask structured, repeatable questions across a large volume of historical log data
- "Show matching entries" is a fast way to build a precise filter without knowing the exact field names in advance — useful when you're not sure exactly what you're looking for yet
- Exporting audit logs isn't retroactive — only activity generated after the sink is created gets captured, which is why export configuration needs to happen before an incident, not during one

## Tools
Google Cloud Audit Logs, Logs Explorer, Log Router, BigQuery, Cloud Shell

---
*Completed as a Google Cloud Skills Boost lab.*
