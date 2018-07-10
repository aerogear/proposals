# Coolstore MSA with AeroGear
[Coolstore MSA (MicroServices Architecture)](https://github.com/jbossdemocentral/coolstore-microservice) is a well established demo of using microservices implemented by various frameworks (not limited to Red Hat portfolio) running on OpenShift together to provide a complex solution, an e-shop in this particular case.

AeroGear project might benefit from building on top of the Coolstore MSA to demonstrate the capabilities it provides. This might be very helpful due to following:

- Coolstore MSA is a widely used demo for OpenShift and Middleware. Becoming part of it will promote AeroGear project to a broader audience both within and outside Red Hat.
- Even though implemented by Red Hatters mostly it is the very real world scenario. It is desirable to know how much effort is required to integrate AeroGear capabilities into existing solution. AeroGear project is mostly being used for creating the backend from scratch at the moment, not much attention is paid to integration with existing solutions.
- The upskilling is another benefit. Mobile applications for various platforms (Android, iOS, Xamarin, Cordova) can be created and it is a good opportunity for people upskilling in these areas by implementing them.
- Demonstrates the ability of AeroGear project to create real world solutions to real world problems

## Minimal Viable Product (MVP)
To keep things simple - it is always possible to extend later - the following minimal set of steps must be addressed
- Start with the standard OCP instance
- Deploy minimal installation of Coolstore MSA there
- Enable Keycloak (SSO) so it is possible to log in to the e-shop
- Turn on the AeroGear capabilities for the OCP instance
- Create a Mobile Client
- Integrate the Mobile Client with the existing Keycloak instance
- Implement mobile application using configuration provided by the Mobile Client and the appropriate AeroGear SDK

The mobile application itself should be able to
- Log in and log out, using Keycloak as the authentication broker
- Browse the products
- Display the product detail
- Add products to cart
- Check out an order

It is probably better to start with Angular/Ionic mobile app since it is consumable by most people (North America is iOS mostly, while other geographical locations are mostly Android), and allows us to tell a Progressive Web App story via a single codebase - it also avoids iOS certificate issues. Android might be preferred for native since the initial setup is easier (also avoids certificate issues, etc.) and Java language is widely adopted within Red Hat.

## Outcomes
Verification that integration with existing solution is as seamless as possible. Various bugs and enhancement might be created along the way. If the automation is completed as well, this become nice end-to-end test scenario for AeroGear project and also a nice demo for marketing.

# Possible Extensions of MVP
The MVP might be too minimal and we can aim for more even for MVP. There are endless possibilities what features to add. To list some of them (feel free to extend or add more)
- Integration with UPS - sending notification when a new product is offered for sale.
- CI/CD Mobile Service - building the mobile application, it might be possible to leverage a shared service running on community cluster. If not, Circle CI might be an option too.
- Metrics Mobile Service - metrics are always good to have, moreover, this does not require any additional coding for mobile applications.
- Full Automation (assuming having an OCP instance) - the easier is to have the demo running, the more probable it is that the people will try and explore. It would also be easier for QE to use this as a test case scenario and for marketing to promote AeroGear project.
