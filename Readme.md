# AWS Elastic Disaster Recovery (DRS) Setup Guide

## Overview
This guide outlines the steps to set up and use AWS Elastic Disaster Recovery (DRS) for your on-premises or cloud-based servers.

## Prerequisites
- AWS account with permissions for DRS, EC2, VPC, and IAM
- Source servers: 
  - x86-64 architecture
  - Supported OS (e.g., Windows Server 2012 R2+, Amazon Linux 2, Ubuntu 16.04+, RHEL 7.4+)
  - Minimum 1GB RAM, 2 vCPUs
- Network: 
  - Consistent minimum 10 Mbps bandwidth
  - Ability to reach AWS public endpoints or configured VPN/Direct Connect

## Steps


### 1. Set up AWS Environment
1. Log in to AWS Console
2. Navigate to Elastic Disaster Recovery service
3. Select your target AWS Region
4. Create or select a VPC and subnet for replication servers
5. Configure security groups to allow necessary traffic
6. Create an IAM role for DRS with required permissions

### 2. Install and Configure AWS Replication Agent
### 2. Install and Configure AWS Replication Agent
1. Download AWS Replication Agent from AWS website
2. For Windows: Run MSI installer
   For Linux: Use package manager (yum, apt-get) or run installation script
3. During installation, provide:
   - AWS access key and secret key (or use IAM role for EC2)
   - AWS region
   - Proxy settings (if applicable)
4. Verify agent installation with AWS CLI:

## aws drs describe-source-servers --region <your-region>

### 3. Define Replication Settings
1. In AWS DRS console, go to 'Replication settings'
2. Select volumes to replicate (all or specific volumes)
3. Choose replication server instance type (e.g., t3.small, m5.large)
4. Configure network settings:
- VPC and subnet for replication servers
- Security groups
- Enable public IP (if required)
5. Set up replication staging area disk:
- Choose EBS volume type (gp2, gp3, io1)
- Set volume size (min 30GB recommended)

### 4. Initiate and Monitor Replication
1. Start replication process from DRS console
2. Monitor initial sync progress:
- Check 'Data replication status' in source server details
- Use CloudWatch metrics for detailed monitoring
3. Verify continuous data replication:
- Check RPO (Recovery Point Objective) status
- Monitor 'Replication lag' metric

### 5. Set Up Launch and Recovery Settings
1. Configure launch settings:
- Instance type for recovered instances
- VPC, subnet, and security group
- EBS volume type and size
2. Set up Recovery Plans:
- Group servers for coordinated recovery
- Define boot order and delays
- Configure automation scripts (optional)
3. Define recovery instance settings:
- IP address configuration (static IP, Elastic IP)
- Instance tags
- IAM instance profile

### 6. Test Failover
1. Initiate a drill from DRS console:
- Select source servers
- Choose 'Perform recovery drill'
2. Verify application functionality:
- Connect to recovered instances
- Test application-specific functionality
- Verify data consistency
3. Terminate test instances:
- Use 'Drill cleanup' in DRS console
- Verify all test resources are removed

### 7. Implement Failover Process
1. Document failover procedure:
- Activation criteria
- Step-by-step process
- Verification checks
2. Assign roles and responsibilities:
- Decision makers for failover initiation
- Technical team for execution
- Application owners for verification
3. Conduct regular drills:
- Schedule quarterly or bi-annual drills
- Rotate team members involved in drills
- Update documentation based on drill outcomes

### 8. Failback (When Ready)
1. Reverse replication:
- Ensure source environment is operational
- Configure reverse replication in DRS console
- Monitor data synchronization
2. Verify data integrity:
- Use data verification tools
- Perform application-specific checks
3. Switch back to original environment:
- Schedule a maintenance window
- Update DNS or load balancer settings
- Verify all traffic is routing correctly

## Maintenance
- Update AWS Replication Agent:
- Check for updates monthly
- Schedule update windows
- Test after each update
- Monitor replication health:
- Set up CloudWatch alarms for replication lag
- Review DRS dashboard weekly
- Regularly update recovery settings:
- Adjust as source environments change
- Update launch settings for new AWS instance types

## Troubleshooting
- Common issues:
- Replication lag: Check network bandwidth, volume of data changes
- Agent disconnection: Verify network connectivity, agent service status
- Launch failures: Review IAM permissions, VPC settings
- CloudWatch logs:
- Enable detailed logging for DRS
- Set up log insights queries for error pattern detection
- AWS Support:
- Prepare system information before contacting support
- Use AWS Support Center for case management

## Additional Resources
- [AWS Elastic Disaster Recovery Documentation](https://docs.aws.amazon.com/drs/)
- [AWS DRS Best Practices](https://docs.aws.amazon.com/drs/latest/userguide/best-practices-drs.html)
- [AWS DRS API Reference](https://docs.aws.amazon.com/drs/latest/APIReference/)
- [AWS DRS Pricing](https://aws.amazon.com/disaster-recovery/pricing/)

## Features of Disaster Recovery
a. Resize: By abstracting cluster management, resizing the cluster becomes straightforward as the underlying auto-scaling group can simply be scaled as desired.

b. Snapshots: Periodically are done everyday and are deleted every 7 days, snapshots of the entire key-value space are captured, from each of the source server and uploaded to an encrypted external storage, allowing the etcd (or human) operator to restore the store at a later time, in any  instance.

c. Failure recovery: Upon failure of a minority of the etcd members, the managed members automatically restarts and rejoins the cluster without breaking quorum or causing visible downtime - First by simply trying to rejoin with their existing data set, otherwise trying to join as a new member with a clean state, or by replacing the entire instance if necessary.

d. Disaster recovery: In the event of a quorum loss, consequence of the simultaneous failure of a majority of the members, the operator coordinates to snapshot any live members and cleanly stop then, before seeding a new cluster from the latest data revision available once the expected amount of instances are ready to start again.
