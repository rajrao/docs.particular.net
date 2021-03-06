---
title: Fair load distribution
summary: Implementing fair load distribution for heterogeneous scaled-out endpoints
component: Core
reviewed: 2016-06-21
tags:
- Routing
- Distribution
- DistributionStrategy
---

The sample demonstrates how NServiceBus routing model can be extended with a custom distribution strategy. Distribution strategies replace the Distributor feature as a scale out mechanism for MSMQ. The default built-in distribution strategy uses a simple round-robin approach. This sample shows a more sophisticated distribution strategy that keeps the queue length of all load-balanced instances equal, allowing for effective usage of non-heterogeneous worker clusters.


## Prerequisites

Make sure MSMQ is set up as described in the [MSMQ Transport - NServiceBus Configuration](/nservicebus/msmq/) section.


## Running the project

 1. Start all the projects by hitting F5.
 1. The text `Press <enter> to send a message` should be displayed in the Client's console window.
 1. Hold down `<enter>` for a few seconds to send many messages.


### Verifying that the sample works correctly

 1. Notice more messages are being send to Server.1 compared to Server.2
 1. Use a [MSMQ viewing tool](/nservicebus/msmq/viewing-message-content-in-msmq.md) to view the queue contents.
 1. Keep hitting `<enter>` and observer number of messages in Server.1 and Server.2 queues.
 1. Notice that although Server.2 processes messages 50% slower than Server.1, numbers of messages in both queues are almost equal.


## Code walk-through

This sample contains four projects:

 * Shared - A class library containing common routing code including the message definitions.
 * Client - A console application responsible for sending the initial `PlaceOrder` message.
 * Server - A console application responsible for processing the `PlaceOrder` command.
 * Server2 - A console application identical to Server.


### Client

The Client does not store any data. It mimics the front-end system where orders are submitted by the users and passed via the bus to the back-end. Client routing is configured to send `PlaceOrder` commands to two instances of `Server` endpoint:

snippet:Routing

Following code enables fair load distribution (as opposed to default round-robin algorithm):

snippet:FairDistributionClient


### Server

The Server project mimics the back-end system where orders are accepted. Apart from the standard NServiceBus configuration it enables the flow control feature:

snippet:FairDistributionServer

In real-world scenarios NServiceBus is scaled out by deploying multiple physical instances of a single logical endpoint to multiple machines (e.g. Server in this sample). For simplicity in this sample the scale out is simulated by having two separate endpoints Server and Server2.


### Shared project

The shared project contains definitions for messages and the custom routing logic.


### Marking messages

All outgoing messages are marked with sequence numbers to keep track of how many message are in-flight at any given point in time. Separate sequences are maintained for each downstream queue. The number of in-flight messages is defined as the difference between the last sequence number sent and the last sequence number acknowledged.

snippet:MarkMessages


### Acknowledging message delivery

Every N messages the downstream endpoint instance sends back an acknowledgement (ACK) message containing the biggest sequence number it processed so far. The ACK messages are sent separately to each upstream endpoint instance.

snippet:ProcessMarkers


### Processing acknowledgments

When the ACK message is received, the upstream endpoint can calculate the number of messages that are currently in-flight between itself and the downstream endpoint.

snippet:ProcessACKs


### Smart routing

The calculated number of in-flight messages can be used to distribute messages in such a way that all instances of downstream scaled-out endpoint have similar number of messages in their input queues. That way the load is appropriate for the capacity of the given instance, e.g. instances running on weaker machines process less messages. As a result no instance is getting overwhelmed and no instance is underutilized when work is available.

snippet:GetLeastBusy


### Real-world scenario

For the sake of simplicity, in this sample all the endpoints run on a single machine. In real world is is usually best to run each instance on a separate virtual machine. In such case the instance mapping file would contain `machine` attributes mapping instances to their machines' host names instead of `queue` attributes used to run more than one instance on a single box.
