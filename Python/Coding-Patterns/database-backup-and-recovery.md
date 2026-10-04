
# Database Backup and Recovery

## 1. What Is a Database Backup?

A database backup is a copy of database data and, depending on the backup method, the information needed to restore it later.

Backups help recover from:
- Accidental deletion
- Application bugs that modify data incorrectly
- Hardware failures
- Database corruption
- Security incidents
- Infrastructure failures

A backup is useful only if it can be restored successfully.

## 2. Types of Database Backups

### Full Backup

Copies the complete selected database or database objects.

Advantages:
- Straightforward restoration.
- Provides a complete baseline.

Disadvantages:
- Can require substantial storage.
- May take longer for large databases.

### Incremental Backup

Captures changes since the previous backup in the relevant backup chain.

Advantages:
- Often uses less storage per backup.
- Can reduce backup duration.

Disadvantages:
- Restoration may require the full backup and several incremental backups.

### Differential Backup

Captures changes since the last full backup.

Advantages:
- Restoration generally requires the full backup and the latest applicable differential backup.

Disadvantages:
- Differential backups can grow as more changes accumulate.

The exact terminology and behavior depend on the database and backup tool.

## 3. Full vs. Incremental vs. Differential

| Feature | Full | Incremental | Differential |
|---|---|---|---|
| Captured changes | All selected data | Changes since the previous backup | Changes since the last full backup |
| Storage per backup | Often larger | Often smaller | Grows between full backups |
| Restore complexity | Usually simpler | May require several backup sets | Usually requires fewer backup sets than a long incremental chain |
| Common use | Baseline backup | Frequent change capture | Balance between backup size and restoration complexity |

## 4. Logical and Physical Backups

### Logical Backup

Exports database objects and data as SQL statements or another logical representation.

Useful for:
- Moving selected tables.
- Inspecting exported data.
- Migrating between compatible database environments.

Example with PostgreSQL:

```bash
pg_dump -U myuser -d mydb -f backup.sql
```

Restore a plain SQL dump with:

```bash
psql -U myuser -d mydb -f backup.sql
```

The target database must exist or be created as required. Authentication, privileges, extensions, roles, and database compatibility may also need attention.

### Physical Backup

Copies database files or uses a database-specific physical backup mechanism.

Useful for:
- Large databases.
- Recovery workflows supported by the database engine.
- Preserving database files and structures for compatible restoration.

Physical backups must follow the database engine's consistency and recovery requirements. Copying live database files arbitrarily is not a reliable backup strategy.

## 5. PostgreSQL Backup Examples

Create a custom-format backup:

```bash
pg_dump -U myuser -d mydb -Fc -f backup.dump
```

Restore it into an existing target database:

```bash
pg_restore -U myuser -d restored_db backup.dump
```

These commands assume the PostgreSQL client utilities are installed and the user has suitable privileges.

For important environments, consider schema objects, ownership, privileges, extensions, and the possibility that the target database contains existing data.

Never commit real database backups or credentials to a public GitHub repository.

## 6. What Is Database Recovery?

Database recovery restores a database to a usable and consistent state after a failure or unwanted change.

Recovery may involve:
1. Identifying the incident and desired recovery point.
2. Selecting a valid backup.
3. Restoring the backup.
4. Applying additional logs when supported.
5. Validating data and database consistency.
6. Reconnecting applications safely.

Recovery time depends on database size, backup type, infrastructure, and the required recovery point.

## 7. Point-in-Time Recovery (PITR)

**Point-in-time recovery** restores a database to a specific time or transaction position, when supported by the database's backup and log-retention configuration.

For example, suppose someone accidentally deletes important records at 14:30.

If the system has a suitable base backup and the required transaction logs, PITR may allow recovery to a point just before the deletion.

PITR generally requires more than a normal SQL dump. It depends on database-specific backup procedures and continuous log archiving or equivalent mechanisms.

For PostgreSQL, PITR commonly uses a base backup together with archived WAL files.

## 8. Recovery Point Objective (RPO)

**RPO** describes how much data loss an organization can tolerate, measured in time.

Example:

An RPO of 15 minutes means the organization aims to limit lost transactions to approximately the most recent 15 minutes of data.

A backup schedule alone does not guarantee this target. Log archiving, replication, and other recovery mechanisms may be needed.

## 9. Recovery Time Objective (RTO)

**RTO** describes the target time within which a service should be restored after a disruption.

Example:

An RTO of one hour means the organization aims to restore the service within one hour.

A backup may exist, but a slow restoration process can still violate the RTO.

Both RPO and RTO should be tested against realistic recovery scenarios.

## 10. Backup vs. Replication

| Feature | Backup | Replication |
|---|---|---|
| Main purpose | Recovery from data loss or corruption | Maintain copies for availability, scaling, or failover |
| Historical recovery | Often possible with retained backups | Not necessarily available |
| Accidental deletion | A previous backup may help recover data | Deletion may propagate to replicas |
| Typical role | Disaster recovery | High availability or read scaling |

Replication is not a substitute for backups. Corrupted or accidentally deleted data can be replicated to other database instances.

## 11. Backup Security

Database backups may contain sensitive information.

Protect them with:
- Encryption in transit and at rest.
- Restricted access and least privilege.
- Secure credential management.
- Separate storage or accounts where appropriate.
- Retention and deletion policies.
- Monitoring and audit logs.
- Tested recovery procedures.

A backup that is accessible to the same compromised account as the production database may not provide sufficient protection against ransomware or account compromise.

## 12. The 3-2-1 Backup Principle

A commonly used backup guideline is:

- Maintain three copies of important data.
- Store copies on two different types of storage or failure domains.
- Keep one copy off-site or otherwise isolated.

The precise implementation should match the organization's threat model. Immutable backups and isolated credentials can further improve resilience.

## 13. Backup Verification and Restore Testing

A successful backup command does not prove that recovery will work.

A practical process includes:

1. Verify that the backup completed successfully.
2. Check backup size and integrity using supported tools.
3. Store it securely.
4. Restore it into an isolated test environment.
5. Validate tables, constraints, and important records.
6. Measure recovery duration.
7. Document failures and improve the process.

Schedule restore drills rather than testing only after an incident.

## 14. Common Mistakes

- Assuming replication replaces backups.
- Never testing restoration.
- Storing backups only on the production server.
- Keeping backup credentials accessible to compromised systems.
- Retaining backups without a clear policy.
- Assuming a logical dump automatically supports point-in-time recovery.
- Ignoring database versions and extension compatibility.
- Failing to measure actual recovery time.

## 15. Interview Questions

1. What is a database backup?
2. Compare full, incremental, and differential backups.
3. What is the difference between logical and physical backups?
4. What is point-in-time recovery?
5. Explain RPO and RTO.
6. Why is replication not a complete backup strategy?
7. Why should restore operations be tested regularly?
8. What is the 3-2-1 backup principle?
9. How would you recover from an accidental deletion?
10. How can backup systems be protected against ransomware?

## 16. Practice Tasks

1. Explain which backup type you would choose for a small application.
2. Write down the steps required to restore a PostgreSQL logical backup.
3. Design a backup schedule for a database with a strict RPO.
4. Create a recovery plan for accidental deletion.
5. Explain how replication and backups complement each other.
6. Create a restore-testing checklist.
7. Estimate whether a proposed backup and recovery process meets a given RPO and RTO.

## Key Takeaways

- Backups support recovery from accidental changes, failures, and data loss.
- Incremental and differential backups have different restoration requirements.
- PITR depends on suitable backups and retained database logs.
- RPO measures acceptable data loss; RTO measures the target restoration time.
- Replication improves availability but does not replace backups.
- Regular restore testing is essential for a dependable recovery strategy.
