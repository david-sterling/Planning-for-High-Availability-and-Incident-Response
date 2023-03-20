# Infrastructure

## AWS Zones
us-east-2 as primary
us-west-1 as secondary/standby

## Servers and Clusters

### Table 1.1 Summary
| Asset      | Purpose           | Size                                                                   | Qty                                                             | DR                                                                                                           |
|------------|-------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Asset name | Brief description | AWS size eg. t3.micro (if applicable, not all assets will have a size) | Number of nodes/replicas or just how many of a particular asset | Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere | Asset                   | Purpose                                                 | Size                                                                   | Qty                                                             | DR                                                                                                           |
|-------------------------|---------------------------------------------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Asset name              | Brief description                                       | AWS size eg. t3.micro (if applicable, not all assets will have a size) | Number of nodes/replicas or just how many of a particular asset | Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere |
| EKS Control Plane       | kubernetes controller                                   | N\A                                                                    | N\A                                                             | AWS managed, deployed to many zones                                                                          |
| K8s Nodes               | container apps run here                                 | aws eks node                                                           | 2                                                               | multiple availability zones, created in disaster recovery                                                    |
| Web Application ALB     | Entry point for flask demo api instances                | N\A                                                                    | 1                                                               | Connected to all instances                                                                                   |
| Grafana ALB             | Entry point for Grafana application running in EKS pods | N\A                                                                    | 1                                                               | Connected to all targets                                                                                     |
| VPC                     | NEtwork config for our infrastructure                   | /16                                                                    | 1                                                               | Nets (public and private ones) connects all AZs                                                              |
| Ubuntu Web server image | flask api app                                           | t3.micro                                                               | 3                                                               | Replicated in the AZs                                                                                        |
| Aurora MySQL            | database for app                                        | t3.small                                                               | 2                                                               | AZ replicated with standby (secondary) and primary zone, capable to take over to secondary                   |
| Grafana app image       | Grafana server                                          | t3.medium                                                              | 1                                                               | Grafana app web application server                                                                           |


### Descriptions
More detailed descriptions of each asset identified above.

## DR Plan
### Pre-Steps:
#### Infra:
Creation and configuration of AWS tool for DNS records (Route 53). The tool should be able to failover to standby region.
#### Monitoring tools:
Zone2 EKS cluster deployment, then install prometheus and grafana to that cluster. Check metrics.
#### Application software:
Deploy ALB instance and create EC2 instances. Check if everything is working as intended.
#### Database:
Primary RDS cluster should point ARNs to standby RDS, use it as source of data. 
Specify that primary RDS should be the replication source and check dependency of primary.
 

## Steps:
You won't actually perform these steps, but write out what you would do to "fail-over" your application and database cluster to the other region. Think about all the pieces that were setup and how you would use those in the other region

1. Our virtual IP from route 53 should change their registers and point out to another instance that is up and running.
2. Check if secondary RDS cluster had taken over.
3. Verify app behavior in grafana, check if DDBB is capable of R/W access to secondary RDS