---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
{{% notice warning %}}
⚠️ **Note:** The information below is for reference purposes only. Please **do not copy verbatim** for your report, including this warning.
{{% /notice %}}

# Logical Replication in Amazon RDS for PostgreSQL 18 - Notable Improvements

**Logical replication** is a well-known feature in **PostgreSQL** that allows copying data from a source database to a target database at the logical level. Instead of copying all data like **physical replication**, you can choose specific tables, publications, and subscriptions for more flexible synchronization.

In practice, logical replication is often used to synchronize data between primary and read-replica systems, migrate databases, or push data to analytics systems. However, in earlier PostgreSQL versions, running production workloads had some annoying issues: **generated columns couldn't be replicated**, **conflicts were hard to debug**, or **replication slots could be forgotten, holding WAL too long**.

**PostgreSQL 18** has significantly improved many of these issues. On **Amazon RDS for PostgreSQL 18**, operating logical replication becomes easier to monitor and more stable.

---

## How Logical Replication Works

In this model, the source database is called a **publisher**, and the database receiving data is a **subscriber**.

The publisher creates a **publication** to define what data will be sent. The subscriber creates a **subscription** to connect and receive data changes through the **WAL stream**.

This model is very suitable when you need to synchronize data between different PostgreSQL instances, especially in **RDS** or **Aurora** environments.

---

## Previous Limitations

Before PostgreSQL 18, logical replication had several limitations:

- **Generated columns couldn't be replicated** → easy to cause missing data on the subscriber.
- **Conflicts were hard to track** → had to read logs and deduce causes manually.
- **Forgotten replication slots** → held WAL long, increasing storage volume.
- **Two-phase commit was hard to change** → sometimes had to drop subscription and resync from scratch.

PostgreSQL 18 resolves nearly all of these issues.

---

## Replicate STORED Generated Columns

A notable improvement is the ability to now replicate **STORED generated columns**.

For example, you have a column `final_price` calculated from `base_price`, `discount_rate`, `tax_rate`. Previously this column wasn't sent to the subscriber, but now you can enable it:

```
WITH (publish_generated_columns = stored)
```

When enabled, generated columns will be replicated like regular columns.

Small note: the subscriber side should define this column as a **regular column**, not a generated column, otherwise there will be errors when applying data.

---

## Better Conflict Tracking

PostgreSQL 18 adds **conflict counters** in the `pg_stat_subscription_stats` view.

You can now clearly see error types like:

- `INSERT` with duplicate key.
- `UPDATE`/`DELETE` on non-existent rows.
- Conflicts due to replication origin.
- Violations of multiple unique constraints.

For example, if `confl_insert_exists` increases, it means the subscriber already has data conflicting with what the publisher sent.

This greatly helps debugging compared to just reading logs like before.

---

## Parallel Streaming by Default

Previously, large transactions had to buffer completely before sending → easy to cause lag.

Now PostgreSQL 18 defaults to:

```
streaming = parallel
```

This allows streaming data earlier and parallel processing with multiple workers.

The result is reduced **replication lag**, especially with workloads having large transactions.

You can also tune further:

```
max_parallel_apply_workers_per_subscription
max_logical_replication_workers
```

---

## More Flexible Two-Phase Commit Changes

Previously, enabling/disabling **two-phase commit** usually required dropping the subscription.

Now you can change it directly:

```
ALTER SUBSCRIPTION sub_orders DISABLE;
ALTER SUBSCRIPTION sub_orders SET (two_phase = true);
ALTER SUBSCRIPTION sub_orders ENABLE;
```

The nice part is that the replication slot remains unchanged, so no need to resync data from scratch.

Note: need to enable `max_prepared_transactions > 0` on both publisher and subscriber. On RDS, this operation requires a reboot.

---

## Automatic Idle Replication Slot Handling

Forgotten replication slots are a common cause of uncontrolled WAL growth.

PostgreSQL 18 adds the parameter:

```
idle_replication_slot_timeout
```

If a slot is idle too long, PostgreSQL will automatically invalidate it to free up WAL.

Some points to note:

- Default = `0`, meaning not enabled.
- Can be changed without rebooting.
- Applied when checkpoint runs.

This helps reduce the risk of disk full due to forgotten slots.

---

## A Few Notes

When using the new features, remember:

- Generated columns replicate only if they are **STORED**.
- Subscriber should use regular columns, not generated columns.
- Some conflict counters related to replication origin require enabling `track_commit_timestamp` on the subscriber.
- Other counters like `INSERT` with duplicate key or `UPDATE`/`DELETE` on non-existent rows can still be tracked without enabling this parameter.
- Logical replication doesn't sync sequences → need to check if promoting subscriber.
- Should use the same PostgreSQL 18 version to fully leverage the features.

---

## When to Use These Improvements

These improvements are especially useful when:

- Synchronizing data to read replicas or reporting systems.
- Migrating databases.
- Separating read/write workloads.
- Running multiple subscribers for different purposes.
- Wanting clearer conflict control.
- Avoiding WAL growth from forgotten slots.

In **RDS** or **Aurora** environments, these improvements make logical replication significantly easier to operate.

---

## Conclusion

PostgreSQL 18 brings many practical upgrades for logical replication: support for generated columns, better conflict tracking, parallel streaming by default, flexible two-phase commit changes, and automatic idle replication slot handling.

The most valuable aspect is that it makes production operations less troublesome, requires less manual debugging, has fewer risks, and is easier to control.

If you're using **Amazon RDS for PostgreSQL** or **Aurora PostgreSQL-Compatible Edition**, this is definitely a version worth considering upgrading to.

---

## Reference Links

[https://aws.amazon.com/blogs/database/logical-replication-improvements-in-amazon-rds-for-postgresql-18/](https://aws.amazon.com/blogs/database/logical-replication-improvements-in-amazon-rds-for-postgresql-18/)

---

![Blog 2](/images/Blog2.png)