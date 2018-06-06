## The Previous Test Proposal

### Test Suite
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
 
### Test Infrastructure
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
devices. 