# Decision Engine

Decision Engine enables policy authors to automate policy decisions by leveraging machine learning (ML) and business rules to produce a decision. The decision engine provides capabilities such as managing the policy rules, policy rule life cycle management, and preserves decisions for audit and analytics. The platform has out-of-the-box integration with internal and external services, feature stores, and model execution environments. 

Decision engine outcome would be to:

* Recommend a product behavior
* Create a manual review for operations
* React using product controls in extreme cases

## Capabilities

The Decision Engine platform provides common APIs to enable services to integrate with rule based engine (e.g. Drools) to make risk assessment decisions. The platform provides capabilities such as rule artifact management, storing decisions for data analysis, managing events emitted by the rule engine, etc.

* Out of the box integration with Drools workbench. A popular workbench for writing business rules.
* Rule artifact manager to manage the deployment of rule artifacts for all rule based services
* Managed CI/CD Pipelines to automate and text new rule changes
* Manage events emitted by rule engines for creating decisions (create case, close account, etc.)
* Eager and lazy rule data fetching
* Integration and Invocation of ML models in real-time hosted on Sagemaker and MXS
* Integration & feedback from external vendors (GreenDot, Kount, Ethoca, BioCatch, Giact, Whitepages, Experian, ThreatMetrix, Quickbase, etc.)
* Integration with Data Analytics (RDM)
* Reusable commands to be used across multiple services
* Support of silent mode and/or champion/challenger testing of new models/rules
* Support sync/async risk assessment modes
* Hot deployment of rules without restarting applications
* Common automation test framework 
* SOX compliance controls & preserving test evidence out of the box
* Agent tool(s) integration (IBOSS, ASC, Sentinel, Kount, etc).

## Benefits
Decision engine automates the work of executing business rules to produce a decision. Business rules execution for risk and fraud detection when done manually is expensive and susceptible to human errors. Having the ability to automate rule based workflows enables the business to quickly implement fraud and risk controls as the decision is made quickly. The Pegasus Decision Engine implements a hybrid approach of combining machine learning models and workflow specific rules. This allows for a more flexible system, which is less expensive, and produces smarter decisions.

Decision engines can be used in many work streams today:

* Account onboarding
* Post underwriting
* Transaction authorization
* Transaction clearing
* Deposit funding
* Email verification
* Identity management
* And any other user case where decision making is required

## Architecture

![](images/arch.png)
