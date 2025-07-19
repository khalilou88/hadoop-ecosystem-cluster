# Hadoop Ecosystem Cluster

A Docker Compose setup for running a complete Hadoop ecosystem cluster with its components.

## Architecture

This cluster consists of the following services:

- **NameNode**: HDFS metadata management and coordination
- **DataNode**: HDFS data storage
- **ResourceManager**: YARN resource allocation and job scheduling
- **NodeManager**: YARN node-level resource management and task execution

## Prerequisites

- Docker Engine 20.10+
- Docker Compose 2.0+
- Minimum 4GB RAM allocated to Docker
- At least 10GB free disk space

## Quick Start

1. Clone this repository and navigate to the project directory
2. Start the cluster:
   ```bash
   docker-compose up -d
   ```
3. Wait for all services to be healthy (this may take 2-3 minutes)
4. Verify the cluster status:
   ```bash
   docker-compose ps
   ```

## Configuration

The cluster uses a shared configuration file `./config` that should contain Hadoop environment variables.

## Web Interfaces

Once the cluster is running, you can access the following web UIs:

- **NameNode Web UI**: http://localhost:9870
  - HDFS cluster overview, file system browser, and node status
- **ResourceManager Web UI**: http://localhost:8088
  - YARN applications, cluster metrics, and node information

## Service Details

### NameNode

- **Port**: 9870 (Web UI)
- **Purpose**: Manages HDFS metadata and namespace
- **Health Check**: `hdfs dfsadmin -report`
- **File Limits**: Increased to 65536 for high-throughput operations

### DataNode

- **Purpose**: Stores actual HDFS data blocks
- **Health Check**: `hdfs dfsadmin -report`
- **Dependencies**: Connects to NameNode for coordination

### ResourceManager

- **Port**: 8088 (Web UI)
- **Purpose**: Manages cluster resources and schedules applications
- **Health Check**: Verifies ResourceManager process is running
- **File Limits**: Increased to 65536 for managing many containers

### NodeManager

- **Purpose**: Manages containers and resources on individual nodes
- **Dependencies**: Registers with ResourceManager

## Common Operations

### Check Cluster Health

```bash
# View all service statuses
docker-compose ps

# Check specific service logs
docker-compose logs namenode
docker-compose logs datanode
docker-compose logs resourcemanager
docker-compose logs nodemanager
```

### HDFS Operations

```bash
# Access NameNode container
docker-compose exec namenode bash

# Create directories in HDFS
hdfs dfs -mkdir /user
hdfs dfs -mkdir /user/input

# Upload files to HDFS
hdfs dfs -put /opt/hadoop/etc/hadoop/*.xml /user/input

# List HDFS contents
hdfs dfs -ls /user/input
```

### YARN Operations

```bash
# Access ResourceManager container
docker-compose exec resourcemanager bash

# Run a sample MapReduce job
hadoop jar /opt/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar wordcount /user/input /user/output

# Check application status
yarn application -list
```

## Testing

The cluster includes a test script mounted at `/opt/test.sh` in the ResourceManager container. Execute it to verify the cluster functionality:

```bash
docker-compose exec resourcemanager /opt/test.sh
```

## Scaling

To scale DataNodes or NodeManagers:

```bash
# Scale DataNodes to 3 instances
docker-compose up -d --scale datanode=3

# Scale NodeManagers to 2 instances
docker-compose up -d --scale nodemanager=2
```

## Stopping the Cluster

```bash
# Stop all services
docker-compose down

# Stop and remove all data (caution: this deletes HDFS data)
docker-compose down -v
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with the provided cluster setup
5. Submit a pull request

## License

This project is open source and licensed under the MIT License.