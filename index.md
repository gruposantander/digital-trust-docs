# Welcome to Digital Trust Protocol Documentation Site

You will be able to find here references to the different specifications we are publishing as part of the protocol.

## Introduction

When defining Digital Trust Protocol, the first major decision we took was to built on top of OpenID.
However, we thought that extending existing OpenID specs would be needed to fulfil our requirements, with end-user privacy control, and security best practices as core principles.


### Security Profile
The core specification for OpenID includes the main elements that will help fully adoption by the industry, however and by design, in order to support many use cases it's a very open specification subject to individual implementations.
That led us to the conclusion that we should create our own profile restricting less secure elements and flows within OpenID Core, but also to produce a set of extensions to cover specific features for identity and verified data sharing.

- Read specifications [here](https://gruposantander.github.io/digital-trust-docs/dtp-auth-code-00.html)

### Assertion Claims
In order to provide the information that is needed by the RP, and the same time avoid unnecessary leak of information, the answer to some claims may be only a boolean verifying the claim instead of returning the actual claim. By providing a rich syntax for creating such assertions, it will be possible to accommodate a variety of use cases, as far as we also provide sufficient explanation to end user when providing the consent to share the assertion.

- Read specifications [here](https://gruposantander.github.io/digital-trust-docs/claim-assertions-00.html)

### Level of Assurance
Within current OpenID specifications, when returning claims to the RP, with the exception of email and telephone, there is no a way to declare and differentiate those claims that have been validated by the OP following their current customer due diligence or onboarding processes.
That is the concept around level of assurance, which has associated some degree of liability based on contractual conditions of the service and the relevant legislation the OP is attached too. For instance, banks currently perform KYC and AML checks as part of onboarding process. In that case, some of the claims provided by the bank, could be tight to a particular level of assurance and trust framework.
We believe that in the majority of situations, the level of assurance will be enough, and it will not be necessary to disclose any other attribute or evidence document back to the RP.
With this extension proposal, requested claims by the RP can refer to a desired level of assurance. If the OP can meet that LoA for the claim and the user consents, the data will be included in the response, otherwise the claim will not be returned.

- Read specifications (TBD)

## SDKs
TBD

## Sample Application
TBD

## Sandbox
Check our Sandbox where you will be able to review further documentation to help you getting started. Please visit [Santanderlabs.io](https://www.santanderlabs.io/en/api/iamid) for more details.

## Support or Contact

Interested in the specifications and want to know more?. Contact us by email at [digital.trust@santander.co.uk](mailto:digital.trust@santander.co.uk) and we’ll help you!
