# AWS RDS vs Amazon Aurora

AWS RDS and Amazon Aurora are both relational database services provided by Amazon Web Services (AWS). While they share many similarities, Aurora is designed to offer enhanced performance and scalability compared to traditional RDS database engines.

## Key Differences

| **Feature**              | **AWS RDS**                                | **Amazon Aurora**                          |
|--------------------------|--------------------------------------------|--------------------------------------------|
| **Database Engines**     | Supports multiple engines:                | Built specifically for:                    |
|                          | - MySQL                                   | - Aurora MySQL                             |
|                          | - PostgreSQL                              | - Aurora PostgreSQL                        |
|                          | - MariaDB, Oracle, and SQL Server         |                                            |
| **Performance**          | Standard performance based on selected engine. | Up to 5x faster than MySQL and 3x faster than PostgreSQL. |
| **Scalability**          | Manual scaling for compute and storage.   | Auto-scales storage up to 128 TB.          |
| **Read Replicas**        | Up to 5 replicas for MySQL and PostgreSQL. | Up to 15 low-latency replicas.             |
| **High Availability**    | Multi-AZ available for most engines.       | Built-in Multi-AZ with automatic failover. |
| **Cost**                 | Lower cost for standard workloads.         | Slightly higher cost but optimized for performance. |
| **Backup and Recovery**  | Automated backups and snapshots.           | Continuous backups to S3 with point-in-time recovery. |
| **Use Cases**            | General-purpose relational database needs. | High-performance, scalable applications requiring low latency. |

## When to Choose AWS RDS
- If you need compatibility with engines like Oracle, SQL Server, or MariaDB.
- For standard workloads that don’t require extreme performance or scalability.
- For cost-sensitive projects with moderate database needs.

## When to Choose Amazon Aurora
- For applications requiring high throughput and low latency.
- When auto-scaling storage and high read performance are priorities.
- For workloads that benefit from Aurora’s performance enhancements over MySQL/PostgreSQL.

## Summary
AWS RDS is ideal for standard use cases with a variety of database engines, while Amazon Aurora is optimized for high-performance applications requiring scalability and reliability. Choose based on your specific workload and performance requirements.
