# Zookeeper

## Leader Elections

Leader is needed to order client requests into a transaction. 

The leader elected is the node with the smallest **zxid**.

Each server starts in the LOOKING state, where it must either elect a new leader or find the existing one. If a leader already exists, other servers inform the new one which server is the leader. At this point, the new server connects to the leader and makes sure that its own state is consistent with the state of the leader.
If an ensemble of servers, however, are all in the LOOKING state, they must communicate to elect a leader. They exchange messages to converge on a common choice for the leader. The server that wins this election enters the LEADING state, while the other servers in the ensemble enter the FOLLOWING state.

The leader election messages are called leader election notifications, or simply notifica‐ tions. The protocol is extremely simple. When a server enters the LOOKING state, it sends a batch of notification messages, one to each of the other servers in the ensemble. The message contains its current vote, which consists of the server’s identifier (sid) and the zxid (zxid) of the most recent transaction it executed. Thus, (1,5) is a vote sent by the server with a sid of 1 and a most recent zxid of 5. 

Server changes vote if it receives a vote that has a higher zxid and a higher server number (in that order)




## ZAB (Zookeeper atomic broadcast protcol)

ZAB does a two phase commit

![two phase](https://pasteboard.co/IUk3pIT.png)

1. The leader sends a PROPOSAL message, p, to all followers.
2. Upon receiving p, a follower responds to the leader with an ACK, informing the
leader that it has accepted the proposal.
3. Upon receiving acknowledgments from a quorum (the quorum includes the leader
itself ), the leader sends a message informing the followers to COMMIT it.

ZAB Guarantees:

1. Elected leader has committed all transactions that will ever be committed from previous epochs before it starts broadcasting new transactions.

2. At no point in time, two servers will have quorom.

Note that it is possible to have a quorom to commit at the same time when a new leader has quorum

## Zookeper sessions

They have buckets (imagine a linkedlist, with random access). 

They 
