---
title: Azure Service Bus Transport
tags:
 - Azure
 - Cloud
component: ASB
redirects:
 - nservicebus/using-azure-servicebus-as-transport-in-nservicebus
 - nservicebus/azure/azure-servicebus-transport
related:
 - samples/azure/azure-service-bus
reviewed: 2016-09-21
---

[Azure Service Bus (ASB)](https://azure.microsoft.com/en-us/services/service-bus/) is a messaging service hosted on the Azure platform, that allows for exchanging messages between various applications in a loosely coupled fashion. ASB Queues offer <a href="https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics)">"First In, First Out" (FIFO)</a> guaranteed message delivery, and support a range of standard protocols (REST, AMQP, WS*) and APIs (to put messages on and pull messages off the queue). ASB Topics deliver messages to multiple subscribers and facilitate use of the fan-out pattern to deliver messages to downstream systems.

NServiceBus is an abstraction over ASB. It takes advantage of ASB's built-in features, such as message batching and deferred messages. It also provides a higher-level, convenient API for programmers on top of ASB.

Note: Publish/Subscribe and Timeouts (including message deferral) are supported natively by the ASB transport and do not require NServiceBus persistence.

 * The main advantage of ASB is that it offers a highly reliable and low latency remote messaging infrastructure. A single message can be up to 256 KB in size (1 MB for Premium), and a queue can store many messages at once, up to 5 GB size in total. Furthermore, it is capable of emulating local transactions using its queue [peek-lock mechanism](https://msdn.microsoft.com/en-us/library/azure/hh780722.aspx).
 * The main disadvantage of ASB is its dependency on TCP (for low latency), which may require opening outbound ports on the firewall. Additionally, in some systems the price for the service (at the per message level) may be significant.

NOTE: NServiceBus provides two transports in its platform: Azure Storage Queues and Azure Service Bus. Each option has its own features, capabilities, and usage characteristics. A detailed comparison and discussion of when to select each one is beyond the scope of this document. To help decide which option best suits the application's needs, review the Azure article [Azure Queues and Azure Service Bus Queues - Compared and Contrasted](https://azure.microsoft.com/en-us/documentation/articles/service-bus-azure-and-service-bus-queues-compared-contrasted/).


## Enabling the Transport

When creating the namespace at the Azure portal, choose Standard or Premium Messaging Tier for Azure Service Bus.

Then at configuration time set ASB as the transport:

snippet:AzureServiceBusTransportWithAzure


## Setting the Connection String

For more details on setting up connection strings and securing them, refer to the [Configuration Connection Strings](https://azure.microsoft.com/en-us/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/#set-up-a-service-bus-connection-string) and the [Securing Credentials](/nservicebus/azure-service-bus/securing-connection-strings.md) articles.

To set the connection string use the following:

partial: code-connection


### Via App.Config

snippet:AzureServiceBusConnectionStringFromAppConfig
