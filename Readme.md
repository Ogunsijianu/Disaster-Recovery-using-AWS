Features of Disaster Recovery
Resize: By abstracting cluster management, resizing the cluster becomes straightforward as the underlying auto-scaling group can simply be scaled as desired.

Snapshots: Periodically are done everyday and are deleted every 7 days, snapshots of the entire key-value space are captured, from each of the source server and uploaded to an encrypted external storage, allowing the etcd (or human) operator to restore the store at a later time, in any  instance.

Failure recovery: Upon failure of a minority of the etcd members, the managed members automatically restarts and rejoins the cluster without breaking quorum or causing visible downtime - First by simply trying to rejoin with their existing data set, otherwise trying to join as a new member with a clean state, or by replacing the entire instance if necessary.

Disaster recovery: In the event of a quorum loss, consequence of the simultaneous failure of a majority of the members, the operator coordinates to snapshot any live members and cleanly stop then, before seeding a new cluster from the latest data revision available once the expected amount of instances are ready to start again.
