---
title: Bully algorithm
date: 2018-06-07 23:55:25
tags:
categories:
- Elasticsearch
- Master Election
- Bully algorithm
---
## Bully algorithm
Here is the introduction of Bully algorithm from [wiki](https://en.wikipedia.org/wiki/Bully_algorithm)
 
>In distributed computing, the bully algorithm is a method for dynamically electing a coordinator or leader from a group of distributed computer processes. The process with the highest process ID number from amongst the non-failed processes is selected as the coordinator.

and the detail is :

The algorithm uses the following message types:

- Election Message: Sent to announce election.
- Answer (Alive) Message: Responds to the Election message.
- Coordinator (Victory) Message: Sent by winner of the election to announce victory.

When a process P recovers from failure, or the failure detector indicates that the current coordinator has failed, P performs the following actions:

1. If P has the highest process id, it sends a Victory message to all other processes and becomes the new Coordinator. Otherwise, P broadcasts an Election message to all other processes with higher process IDs than itself.
2. If P receives no Answer after sending an Election message, then it broadcasts a Victory message to all other processes and becomes the Coordinator.
3. If P receives an Answer from a process with a higher ID, it sends no further messages for this election and waits for a Victory message. (If there is no Victory message after a period of time, it restarts the process at the beginning.)
4. If P receives an Election message from another process with a lower ID it sends an Answer message back and starts the election process at the beginning, by sending an Election message to higher-numbered processes.
5. If P receives a Coordinator message, it treats the sender as the coordinator.

## Bully algorithm In Elasticsearch
Here is the master election of version 5.6 elasticsearch.

1. Every node will ping the ip in the config file discovery.zen.ping.unicast.hosts and find the survival node 
2. Then find the active master but not itself
	List<DiscoveryNode> activeMasters = new ArrayList<>();
    for (ZenPing.PingResponse pingResponse : pingResponses) {
        // We can't include the local node in pingMasters list, otherwise we may up electing ourselves without
        // any check / verifications from other nodes in ZenDiscover#innerJoinCluster()
        if (pingResponse.master() != null && !localNode.equals(pingResponse.master())) {
            activeMasters.add(pingResponse.master());
        }
    }
3. Find the map masterCandidates that it has all the node that could be the master include itself
	// nodes discovered during pinging
    List<ElectMasterService.MasterCandidate> masterCandidates = new ArrayList<>();
    for (ZenPing.PingResponse pingResponse : pingResponses) {
        if (pingResponse.node().isMasterNode()) {
            masterCandidates.add(new ElectMasterService.MasterCandidate(pingResponse.node(), pingResponse.getClusterStateVersion()));
        }
    }
4. If activeMasters is null, it means there is no survival master node. If the current survival number is more than discovery.zen.minimum_master_nodes, then we pick one whose ip is smallest from masterCandidates and let it to be the master. If activeMasters is not null then choose the one with the smallest ip.
	if (activeMasters.isEmpty()) {
	        if (electMaster.hasEnoughCandidates(masterCandidates)) {
	            final ElectMasterService.MasterCandidate winner = electMaster.electMaster(masterCandidates);
	            logger.trace("candidate {} won election", winner);
	            return winner.getNode();
	        } else {
	            // if we don't have enough master nodes, we bail, because there are not enough master to elect from
	            logger.warn("not enough master nodes discovered during pinging (found [{}], but needed [{}]), pinging again",
	                        masterCandidates, electMaster.minimumMasterNodes());
	            return null;
	        }
	    } else {
	        assert !activeMasters.contains(localNode) : "local node should never be elected as master when other nodes indicate an active master";
	        // lets tie break between discovered nodes
	        return electMaster.tieBreakActiveMasters(activeMasters);
	    }

bost electMaster.electMaster method and electMaster.tieBreakActiveMasters method will get the smallest on from the collection.

	public MasterCandidate electMaster(Collection<MasterCandidate> candidates) {
	    assert hasEnoughCandidates(candidates);
	    List<MasterCandidate> sortedCandidates = new ArrayList<>(candidates);
	    sortedCandidates.sort(MasterCandidate::compare);
	    return sortedCandidates.get(0);
	}
	public DiscoveryNode tieBreakActiveMasters(Collection<DiscoveryNode> activeMasters) {
	    return activeMasters.stream().min(ElectMasterService::compareNodes).get();
	}

