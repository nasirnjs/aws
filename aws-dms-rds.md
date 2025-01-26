Here’s a hands-on lab outline for AWS Data Migration Service (DMS) with Amazon Aurora MySQL as the target database.

# AWS DMS: Migrating RDS MySQL to Amazon Aurora MySQL

---

## **Step 1: Create Source (RDS MySQL) and Target (Aurora MySQL) Databases**

### **Source Database: Amazon RDS MySQL**
1. Go to **RDS Console** → **Create Database**.
2. Select:
   - **Engine**: MySQL.
   - **Database creation method**: Standard Create.
   - **Version**: Latest MySQL version.
   - **DB Instance Class**: `db.t4g.micro` (or larger for production workloads).
   - **Allocated Storage**: 20GB (minimum).
   - **DB Instance Identifier**: `source-mysql`.
   - **Username/Password**: Define credentials for the `admin` user.
3. Under **Connectivity**, configure:
   - **VPC**: Same VPC as the target database.
   - **Public Access**: Enable (for this lab).
   - Configure **security group** to allow access from DMS and your local machine (port 3306).
4. Wait for the instance to be created and note the **endpoint URL**.

5. Populate the source database:
   - Connect using a MySQL client:
     ```bash
     mysql -h <source-endpoint> -u admin -p
     ```
   - Create a sample schema and table:
     ```sql
     CREATE DATABASE source_db;
     USE source_db;
     CREATE TABLE employees (
         id INT AUTO_INCREMENT PRIMARY KEY,
         name VARCHAR(100) NOT NULL,
         department VARCHAR(100),
         salary DECIMAL(10,2)
     );
     INSERT INTO employees (name, department, salary)
     VALUES
         ('Alice', 'Engineering', 85000.00),
         ('Bob', 'Sales', 60000.00),
         ('Charlie', 'HR', 70000.00);
     ```

### **Target Database: Amazon Aurora MySQL**
1. Go to the **RDS Console** → **Create Database**.
2. Select:
   - **Engine**: Amazon Aurora.
   - **Edition**: MySQL-compatible.
   - **DB Instance Class**: `db.t4g.medium` (or larger for production).
   - **DB Cluster Identifier**: `aurora-cluster`.
   - **Master Username/Password**: Define credentials for the `admin` user.
3. Under **Connectivity**, configure:
   - **VPC**: Same as the RDS MySQL database.
   - **Public Access**: Enable (for this lab).
   - Configure **security group** to allow access from DMS and your local machine (port 3306).
4. Wait for the Aurora cluster to be created and note the **endpoint URL**.

5. Create the target database:
   - Connect using a MySQL client:
     ```bash
     mysql -h <aurora-endpoint> -u admin -p
     ```
   - Create the database:
     ```sql
     CREATE DATABASE source_db;
     ```

---

## **Step 2: Create a DMS Replication Instance**



1. Navigate to **AWS DMS Console** → **Replication Instances**.
2. Click **Create replication instance**.
3. Configure:
   - **Name**: `dms-replication-instance`.
   - **Instance Class**: `dms.t3.medium`.
   - **VPC**: Same VPC as your databases.
   - **Public Access**: Enable (for this lab).
4. Launch the replication instance and wait until it becomes **Available**.

---

## **Step 3: Create Source and Target Endpoints**

1. Go to **Endpoints** → **Create Endpoint**.
2. **Source Endpoint**:
   - **Endpoint Type**: Source.
   - **Engine**: MySQL.
   - Provide:
     - **Endpoint Identifier**: `source-mysql-endpoint`.
     - **Server Name**: Source RDS MySQL endpoint.
     - **Port**: `3306`.
     - **Username/Password**: RDS MySQL admin credentials.
   - Test endpoint connection (skip for now if databases are not reachable).

3. **Target Endpoint**:
   - **Endpoint Type**: Target.
   - **Engine**: Aurora MySQL.
   - Provide:
     - **Endpoint Identifier**: `target-aurora-endpoint`.
     - **Server Name**: Aurora MySQL cluster endpoint.
     - **Port**: `3306`.
     - **Username/Password**: Aurora admin credentials.

---

## **Step 4: Test Endpoints' Connection**

1. Navigate to **Endpoints** in the DMS Console.
2. Select an endpoint → Click **Test Connection**.
   - Ensure the DMS replication instance can reach both the source and target databases.
   - If the test fails:
     - Verify **security group rules** for both databases.
     - Ensure the RDS instances are in the same **subnet group**.

---

## **Step 5: Create a Data Migration Task**

1. Go to **Tasks** → **Create Task**.
2. Configure:
   - **Name**: `rds-to-aurora-task`.
   - **Replication Instance**: Select the DMS instance created earlier.
   - **Source Endpoint**: `source-mysql-endpoint`.
   - **Target Endpoint**: `target-aurora-endpoint`.
   - **Migration Type**: 
     - **Full Load**: Migrate all existing data.
     - **CDC**: Capture ongoing changes (optional for real-time replication).
3. Specify **Table Mappings**:
   - Include all tables: `%`.
   - Optionally, apply schema transformations.
4. Start the task:
   - Choose **Start task on create**.

---

## **Step 6: Monitor the Data Migration Task**

1. Go to the **Tasks** section and monitor the status.
   - **Status**: Should move from **Starting** → **Running** → **Load Completed**.
   - Check **Logs** for troubleshooting if there are errors.
2. Validate data on the target:
   - Connect to Aurora and query:
     ```sql
     SELECT * FROM employees;
     ```
3. If CDC is enabled, make changes on the source database and verify the updates replicate to the target.

---

## **Conclusion**

You’ve successfully migrated data from an Amazon RDS MySQL database to an Amazon Aurora MySQL database using AWS DMS.
