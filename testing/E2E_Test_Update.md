# Update E2E Test Proposal

## Document Purpose

The purpose document and the supporting documents that are created along with it 
are to: 

* outline possible approaches to writing tests and documentation on how to implement 
 them
* provide an update on current state of e2e testing
* provide documentation on Appium and tools for help in creation of e2e tests
* provide an estimation on the time required for developing e2e tests

## Supporting Documentation

[Previous Test Proposal](./supporting_docs/previous_test_proposal.md)

[Appium Background Information](./supporting_docs/appium_information.md) - Contains 
information on Appium, its clients and some important considerations when selecting 
an Appium client library. 

[UI Selectors And Their Importance](./supporting_docs/ui_selectors_and_their_importance.md) - 
Contains information on UI selectors, thier impact on a UI test suite, and examples 
of how to implement some prefered selectors in mobile applications.  

[Tools for Finding UI Selectors](./supporting_docs/tools_to_find_selectors.md) - 
Contains information on tools that can be used to identify selectors and generate
unrefined e2e test code.

## The Previous Test Proposal

The previous test prosal sections on test suites and test infrastructure can be
found at the following links:

[Proposal on Test Suite](./e2e_drafts/)

[Proposal on Test Infrastructure](./e2e_drafts/E2E_Test_infrastructure.md)

### Test Suite Proposal Summary
The main criteria that were used to create the proposal for the test suite 
for end to end UI tests, were:

* Tests must be readable to enable incoming developers to get involved 
quickly in the maintenance of the test suite and production of new tests.

* The test suite should be easily maintainable to reduce time spent on 
fixing bugs and the amount of developers required to maintain it.

* In order to fulfill the required test cases, the framework used to create 
the test suite needs to be able to handle interactions from applications 
than redirects it to the browser for signing into the application.

During the research phase, various test frameworks for android apps were 
investigated including UIAutomator, Espresso and Appium multiple appium clients 
(some information in Appium Clients section).  It was determined, from this 
research, that the only framework capable of performing application and 
browser redirect actions was Appium and should be used for end to end UI testing. 

In order to facilitate the other two expectations of the test suite, it was 
proposed that best practices such as page objects should be used to avoid 
code repetition and split the presentation logic from the test logic, and 
that while the Appium Java-client was the preferred option to create the tests, 
the proposal called for using Kotlin to create a domain specific language (DSL) 
to write the tests, as it would provide easier readability and understanding 
for new developers.
 
### Test Infrastructure Proposal Summarry
The test proposal looked at possible ways of running tests in a continuous 
integration manner.   Tools such as Circle CI, Travis and Jenkins CI were 
evaluated to determine their viability in running the appium UI tests.  It was 
determined, due to problems running on Circle CI, Travis and docker images on 
Jenkins CI, a macOS slave should be attached to Jenkins CI.  On this device 
tools such as Appium, Android Emulators and iOS simulators can be installed and
 created to provide the necessary environment for running the the Continuous 
Integration end to end UI tests.  Devices could also be attached to the new 
slave in order to to provide the capability of running tests on real devices.

### Updates on Test Infrastructure
Regarding the test infrastructure, a new macOS slave has been attached to the 
Jenkins CI infrastructure.  The require software has been installed on the 
device and emulators have been created.  As of yet no real devices have been 
attached to the slave to provide the the ability to run the tests on real 
devices. The only thing that needs to be checked is the compatability of the
Chromedriver and the emulators installed on the macOS slave.

## Making a Solid Test Suite

Where possible, the use of preferred selectors such as AccessibilityId, Id, name etc., 
placed inside the applications on relevant UI elements would be preferable, as it would 
eliminate the risks of minor changes in the development of the application, such as moving 
the order or position of elements on a page or changing text values, requiring an update 
in the UI tests. 

The use of common design in application development coupled with the development of the 
suite of tests in a single repository that contains smart abstractions can be major factors 
in the reduction of the amount of code that will be written and in turn that needs to be 
maintained.  Common design of application means that only a single test needs to be written 
to cover all platforms that need to be covered if abstractions such as page objects and 
data objects are used to remove selectors and expected data from the the tests themselves.  
These abstractions can then be passed into the tests for multiple platforms.  Indeed, the 
Java-client has support for cross-platform page objects through the use of special 
annotations.

## Updated Proposals

A decision should first be made if prefferred selectors are to be implmented in the mobile
showcase applications.  The effects of this decision are shown below:

#### 1). Using Prefferred Selectors in Applications

* Tests are likely to be less brittle, therefore less time should be required on 
maintainence
* Any client can be used provided it has the ability to handle redirects to the browser


#### 2). Not Using Prefferred Selectors in Applications

* Tests are likely to be more brittle, therefore more time required to maintain them
* The Java-client should be the preferred choice of client as it has more stragtegies to
target locators than other libraries

### Possible Test Suite Implementations

1. A nodejs implmentation with webdriverio - This should be considered if there is a 
decision to use preferrd selecotrs is to use them in the showcase applications as there 
is a lot of experience using nodejs within the teams.

1. A Java-client implementation or the Akow library - Should be considered if the decision
on using prefered selectors is not to use them. This will allow for more locator strategies 
thatcan be used to create the tests.  The downside of this approach could be the upkeep of 
the Akow library. 

## Test Writing Time Estimation

### Writing New tests

A new test should take approximately 3-4 hours to complete regardless of the language of 
implementation.  Appium Inspector can be used to find the selectors and also generate an 
initial version of the code that is unrefined.  The person developing the test will need 
to extract the selectors and generate page objects and test case files based on the 
unrefined code.  

Through the use of best practices such as page objects, areas of a test that have been 
previously covered by code inside the page object can be quickly reused in the new test 
cases and the only selectors and code to be created will belong to pages that have yet 
to be covered by a test case.

However, in cases where preferred selector strategies cannot be used or in cases where 
there may be some problems with the application some extra time may be required.


### Maintaining tests

The majority of time spent to make changes to maintain the test suite should be minimal 
(1 – 2 hours).  

If the changes are made to the UI that affect the UI selectors in the application the 
selectors in the page objects should be updated to reflect them and this should take the 
shortest amount of time.  

The only time a test file should be changed is when there is a change in the logic flow 
inside an application.  This will likely take longer than a simple change to a UI selector 
in the application, but still be much shorter.
