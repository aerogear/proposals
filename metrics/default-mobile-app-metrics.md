# Abstract

This document contains the proposal for gathering some default metrics of mobile apps and making them available to developers.

## Goals

### Goals

- Provide mobile app developers a service that can receive some basic default metrics, namely sdk version and app version

- Provide developers an interface to query and visualize the gathered data in order to make informed decisions

### Non Goals

- Identify individual devices or users in metrics events (bonus)

- Registering of user-defined metrics and logs (not prioritized; just keep in mind)

- Provide mobile app developers with an easy to use SDK to connect easily and securely to the metrics service (separate epic/proposal)

## Terms

For a list of terms used in this document, see [Glossary](./glossary.md).

## Problem Description

As mobile apps are developed, improved and iterated on, different versions of those apps can be used on many different devices. 
Metrics data becomes invaluable for developers to observe the landscape of application installations and make sure further 
development and deployment of backend services will not affect users.

## Proposed Solution

A Metrics Service will be available from the Service Catalog, which can be provisioned by developers for use with their mobile applications.

The following diagram illustrates the components:

![service diagram](./default-mobile-metrics-system-landscape.png)

### Mobile App Client

A native, platform-specific SDK for targeting the Metrics Service will be available to developers.

Upon installation into a project and providing configuration for targeting the Metrics Service the SDK will automatically 
send the default metrics information for the application with a unique ID to the metrics service.
This information will be sent to the service every time the application starts.  

Please note that, the client side is not part of this proposal.

### Mobile Metrics Service

A thin HTTP-based service will be present to receive metrics data from the mobile applications and then to forward to the storage service.

At a PoC level, the endpoints available from the Mobile Metrics Service should be:

`POST /default-metrics`

Receives `Content-Type: application/json` containing JSON with following schema:
```
{
  "clientId": "deadbeef123"
  "appId": "com.example.someApp"
  "sdkVersion": "2.4.6"
  "appVersion": "256"
}
```

It is not part of this proposal what client id is, but it must be something unique per device/client.

### Storage

Prometheus will be used for time series data. However, for long term storage and better scaling, Prometheus will use Postgres as the target long term storage.

For non-time series data, metrics service will directly talk to Postgres.

### Visualisation

The visualization service will be supplied by Grafana, which can utilize the storage service implementation directly as a data source.

As shared with the backend metrics service, Grafana's service will be behind OpenShift's [OAuth Proxy](https://github.com/openshift/oauth-proxy) 
that will pick up the permissions of the user's OpenShift cluster credentials.

### Authentication

Authentication of the service is not part of this proposal.

# Technical details

## Alternative architectures

We have investigated a number of alternative architectures. They're not going to be discussed here in detail. 

See <https://issues.jboss.org/browse/AEROGEAR-1894> for that.

Mainly, as we already use Prometheus and Grafana currently in mobile backend metrics, we wanted to go that way. 

## Time series data vs non-time series data and 

Before starting the discussion, here is a good summary of what time series data and regular data means:

| Time series data                                          | Regular data                           |
|-----------------------------------------------------------|----------------------------------------|
| You normally do inserts                                   | You do inserts, but updates more often |
| Getting some stats about what happened in the last N days | Knowledge base                         |

Typically, what is tried to achieve here is done with inserting time series data in a time series database.
This answers the question "what's the number of SDK init events for SDK version 1.2.3 in last N days?"

However, in order to answer the question "what's the usage of SDK version 1.2.3 among clients?" we need to eliminate the duplicate events per client.
This is a knowledge base and not a stream of events.

Trying to build a knowledge base from a stream of events is not natural thing to do on a time series database. Aggregations would be super-complex 
(if even possible) and slow (due to very long time range and too many matching documents).

See <https://stackoverflow.com/questions/48378449/> for more information about this.

Because of the reasons above:
- Our storage system must support both time series data and regular data.
- We need to maintain the knowledge base outside Prometheus.


## Storage

The Storage service will store metrics data forwarded from the Mobile Metrics Service.

The requirements and preferences for the selected implementation are as follows:

- Support for inserts and updates
- Serve as a long-term storage for the back-end metrics Prometheus instance
- Be able to become a Data Source for Grafana in order to avoid a separate visualisation layer
- Low enough resource usage in order to run in low-powered "evaluation" OpenShift clusters.
- Flexibility in metrics types for future-proofing

We will use Postgres as the storage. TimescaleDB is installed in Postgres as an extension to provide time series database capabilities.

Other options:
- ElasticSearch: high memory usage
- InfluxDB: time series data only
- MongoDB: no support on Prometheus

## Prometheus <-> Postgres

Prometheus cannot directly talk to Postgres. It needs an adapter to send and receive data to/from Postgres. This is called "Prometheus Postgres Storage Adapter".
An extension is needed on Postgres side that talks to that adapter. This extension, "Postgres Prometheus Extension", is installed in the Postgres instance.

While the extension is installed into Postgres instance, the adapter is a separate web application.  

## Grafana vs others

User interface familiarity is an important factor for developers that use the overall metrics service, so addition of other visualisation 
tools like Kibana that would increase the learning curve of the solution should be avoided.

We choose Grafana as we already use it in other places.


## This architecture vs ElasticSearch+Kibana

One of the POCs done for this proposal was about using ElasticSearch and Kibana. While we can satisfy the requirements of this epic with those, we avoided them because
- ElasticSearch requires much more memory
- In backend metrics, we use most of the tools in the proposed stack already (Prometheus and Grafana) or we are going to have them (long term storage for Prometheus)