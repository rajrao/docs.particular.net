---
title: Logging
---


## Versions 5 and above

As of Versions 5 and above logging for the host is controlled with the [core logging API](/nservicebus/logging/).

Add the logging API calls as mentioned in the above article directly in the implementation of `IConfigureThisEndoint.Customize` method.


## Versions 4 and below

To change the host's logging infrastructure, implement the `IWantCustomLogging` interface. In the `Init` method, configure the custom setup. To make NServiceBus use the logger, use the `NServiceBus.SetLoggingLibrary.Log4Net()` API, described in the [logging documentation](/nservicebus/logging) and shown below:

snippet:CustomHostLogging

In order to specify different logging levels (`DEBUG`, `WARN`, etc.) and possibly different targets `(CONSOLE`, `FILE`, etc.): The host provides a mechanism for changing these permutations with no code or configuration changes, via [profiles](/nservicebus/hosting/nservicebus-host/profiles.md).
