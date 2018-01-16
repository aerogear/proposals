# Versioning of Components and their APIs

## Introduction

Introduction to problem

## Problem Description

The initial discussions which led to this proposal were around how to handle versions of APIs of the different components. The discussions quickly followed into an overall discusion on how the different components will be versioned. There are a number of problems which need to be addressed with this proposal;
* At what level are versions kept?
* When changes are made to differnt components, what is the impact on dependent components?
* Do we have rules which define compatibility? 

## Use Cases to be Considered

1. There is a change to the backend of the Mobile Service
 * an API breaking change
 * a non API breaking change

 What happens to the versions of the SDKs?
2. There is a change to one of the Mobile Client Service SDKs
 * a SDK client API breaking change
 * a non API breaking change

 What happens with the backend?
 What happens with the other non changing SDKs 

## Expectations

- Expectation 1

## Terms

Template - template for proposal
  
## Proposed Solution

### Mobile Service Components

* Mobile Service Name and Overall Version (Major.Minor only, no patch)
  * Backend Component and its API (Major.Minor.Patch)
  * Mobile Client Service SDK (Android) and its API (Major.Minor.Patch)
  * Mobile Client Service SDK (iOS) and its API (Major.Minor.Patch)
  * Mobile Client Service SDK (Cordova) and its API (Major.Minor.Patch)

### Proposed Rules to be applied
1. **Lockstep between Mobile Client Service SDKs and the overall Mobile Service Version**

   Major and Minor versions only.
   If any one of the Mobile Client SDKs (Android, iOS, Cordova) step Major or Minor version, the other Mobile Clinet SDK versions step also and the overall Mobile Service Version also steps

   **Example**; 

   Mobile Service v1.2 (MC-A v1.2, MC-I v1.2, MC-C v1.2, Backend v1.2)
   There is a change in the 'Mobile Client Service SDK - iOS' which results in its version stepping from 1.2 to 1.3
   The knock on impact of this rule is that all components step in unison, from 1.2 to 1.3, even though there were no changes in the other componets.

   **Value provided by this rule**

   _I think its important that we try to fill this out_ 

   Open Questions
   - What happens if the Service iOS SDK had to step major versions? Is this a valid use case?
   - Unsure if we need to step every Mobile Client SDK if only one changes. We could just step the overall Mobile Service version when a single SDK changes. But on further thought, this may not be good, it could lead to the following set of versions; MS v1.5, iOS-SDK v1.4, And-SDK v1.2, Cor-SDK v1.0, Backend v1.3.

2. **Mobile Client Service SDK compatibility**
   
   For the Client Service SDK to be compatiable with the backend service;

   * Client SDK Major Version **==** Backend Major Version **&&** Client SDK Minor Version **<=** Backend Minor Version
   * Patch version can be ignored

   **Example** 

   Open Questions
   * A presumption is that the  mobile Device will support many versions of the same SDK, e.g. like I can have various JAVA versions. If so then when a Mobile App Developer is writing their app, they will need to import a specific version of the SDK

3. **Sem Ver Rules**

   [Semantic Versioning Rules](https://semver.org/) 

### Apply the Rules to the Use Cases

| Use Case      | Backend Service | Client iOS SDK | Client Android SDK | Client Cordova SDK | Overall Mobile Service Version |
| :------------|:---------------:| :-------------:| :-----------------:| :-----------------:| :-----------------------------:|
| Starting Versions before UCs                                          | v1.4     | v1.4       | v1.4      |  v1.4 |  v1.4 |
| | | | |  | |
| UC-1.1 - All SDKs will have to change                                 | **v1.5** | v1.5       | v1.5      |  v1.5 |  v1.5 |
| UC-1.2 - No SDK needs changes                                         | **v1.5** | v1.4       | v1.4      |  v1.4 |  v1.5 |
| UC-2.1 - iOS SDK change - step all other SDKs - backend doesn't change| v1.4     | **v1.5**   | v1.5      |  v1.5 |  v1.5 |
| UC-2.2 - Android SDK change - is this just a patch?                   | v1.4     | v1.4       | **v1.4.1**|  v1.4 |  v1.4 |





| Client SDK    | Backend       | Compatibility  |
| :------------:|:-------------:| :-------------:|
| v1.3          | v1.4          | Compatiable    |
| v1.3.2        | v1.3          | Compatiable    |
| v2.1          | v1.4          | InCompatible   |
| v1.6          | v1.4          | InCompatible   |

## General Open Questions
- launch.io missions and the proposed stories. Are they the same thing or different?
-  

### Benetits of Solution

### Limitations of Solution