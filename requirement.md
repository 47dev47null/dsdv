Assignment #2 - Implementing the Destination Sequenced Distance Vector (DSDV) Routing Protocol
==============================================================================================

**Note:** the link cost change can be either a positive number or a negative one. E.g., the cost between A-B can be changed from 2.0 to 4.0, or changed from 2.0 to -1. A negative one such as -1 means that the link is broken/down.

Assignment requirements
-----------------------

In this assignment you are asked to implement the destination sequenced distance vector (DSDV) routing protocol discussed in class. Your program will be running at the provided Linux testbed. At each host, the input to your program is the set of directly attached links and their costs. **Note that the program at each host doesn't know the complete network topology.** Your routing program at each host should report the cost and the next hop for the shortest paths to all other hosts in the network. This is an individual assignment, so each person should turn in his/her own program.

In DSDV, each host sends out the routing information to its neighbors at a certain frequency (e.g., once every 10 seconds), no matter if the information has changed since the last announcement. This strategy improves the robustness of the protocol. For instance, a lost message will be automatically recovered by later messages. In this strategy, typically a host re-computes its distance vector and routing table right before sending out the routing information to its neighbors.

Since we don't have real network routers for you to play with, your programs will run on machines in the testbed. As specified in the distance vector protocols, your routing program at each host will exchange the routing information with directly connected neighbors. Real routing protocols use UDP for such exchanges. Under such a scheme, each host should listen at a UDP port for incoming routing messages.

### Input format ###

Your program at host *n1* should take a text file as input, which describes the set of directly attached links and their costs. The first line of the text file is a single number and a string, where the number specify the total number of directly attached links and the string specify the name of current node. All subsequent lines in the input file are in the format of "*n2 machine cost port\_number*", which stands for a link between node *n1* and node *n2* with cost *cost*, and the program of node *n2* actually is running on *machine* and listening at port *port\_number*. Note that *cost* is a floating point number. The four fields are separated by spaces in each line of the input file.

Let's look at an example with the network topology shown in the following figure.

![network](https://raw.github.com/47dev47null/dsdv/master/network.jpg "Network Topology")

The input files at all hosts will look like the following:

**a.dat**

	3 a
	b node1 2.0 3033
	c node2 5.0 3033
	d node3 1.0 3033

**b.dat**

	3 b
	a head 2.0 3033
	c node2 3.0 3033
	d node3 2.0 3033

**c.dat**

	5 c
	a head 5.0 3033
	b node1 3.0 3033
	d node3 3.0 3033
	e node4 1.0 3033
	f head 5.0 3034

**d.dat**

	4 d
	a head 1.0  3033
	b node1 2.0 3033
	c node2 3.0 3033
	e node4 1.0 3033

**e.dat**

	3 e
	c node2 1.0 3033
	d node3 1.0 3033
	f head 2.0 3034

**f.dat**

	2 f
	c node2 5.0 3033
	e node4 2.0 3033

Note that in the above example, *a*, *b*, *c*, *d*, *e*, and *f* stand for real machine *head*, *node1*, and *node2*, *node3*, *node4*, and *head* in the testbed, respectively. *a* and *f* will use different UDP port numbers.

Be aware that we will use **DIFFERENT** network topologies in our testing.

### Output format ###

Your program should produce a terminal output each time it sends out the routing information to its neighbors (i.e., once every 10 seconds). Each such output should include an incremental print-out number (1 for the first output, 2 for the second output, etc.). Each output should also include the cost and the next hop for the shortest paths to all other network nodes. For instance, one of the terminal outputs at node *a* may look like the following.

	## print-out number 10
	shortest path to node b (seq# 10): the next hop is b and the cost is 2.0
	shortest path to node c (seq# 12): the next hop is d and the cost is 3.0
	shortest path to node d (seq# 10): the next hop is d and the cost is 1.0
	shortest path to node e (seq# 12): the next hop is d and the cost is 2.0
	shortest path to node f (seq# 8): the next hop is d and the cost is 4.0

In the above, "node b (seq# 10)" means the sequence number from node b is 10. You must produce the terminal output from the first time the host sends out its routing information to its neighbors. Yes, the first a few outputs often contain immature routing information.

### Startup and termination ###

It is possible that some hosts may start earlier than their neighbors. As a result, you might send the routing information to a neighbor which has not run yet. You should not worry about this since your routing program at each host will repeatedly send the routing information to its neighbors and a slow-starting neighbor will eventually get the information.

You program does not need to terminate. It should keep running and outputing the routing information until being killed (e.g., though Ctr-C).

### Link cost change ###

Your implementation should also be able to handle link cost changes. In our test platform, link cost changes manifest as value updates in the neighbor link input files. More specifically, to emulate the cost change of a link, the input files for the two nodes attached to the link should be updated. During your testing, you might not be able to update the two files simultaneously. But this is fine --- in practice the two attached nodes may not simultaneously detect a link cost change either. Due to potential link cost change, your program at each host should re-read the link input file each time it is about to re-compute its distance vector and routing table (typically right before sending out the routing information to its neighbors). Be aware of the potential file caching at the OS --- make sure you do read the new file content each time you re-read the file.

Note that you implementation only needs to support link cost changes, not network topology changes.

### Invocation ###

We invoke your program like:

	$ ./dsdv 3033 a.dat

