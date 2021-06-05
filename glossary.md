**Definitions**

**Business processes:** 
Represents what the business does.

**Business rules:** Decisions or Actions that the business takes.

**RuleFlowFact:** Facts are the data that is available in drools context before executing rules. This custom annotation exposes data objects only for defined RuleFlow.

**RuleFlowConfiguration:** This custom annotation associates a Business Process to RuleFlows. You can't configure more than one BP for the same rule flow, though vice versa is possible. The businessProcess name you map here is the ID attribute in the business process asset.

**BusinessDomain:** This is domain name,  available domains are listed in this link 
https://github.intuit.com/SBG-PCS-Risk/rt-rules-engine/blob/master/rt-rules-engine-api/src/main/java/com/intuit/risk/request/BusinessDomain.java

**RuleFlowContext:** This custom object provides context about the RuleFlow. This context along with the domain helps to pick and trigger RuleFlow and  Business processes to run.

**RuleFlow:** Set of rules to be executed as part of flow. 
https://github.intuit.com/SBG-PCS-Risk/rt-rules-engine/blob/master/rt-Rules/src/main/java/com/intuit/sbg/payments/rules/engine/bomData/RuleFlow.java

**How BPM is associated with Rules?**
The Business Rule Task(middle box) in BPM has an option to set the ruleflow-group property  to define which rule flow to be triggered as part of this bpm. This is the same rule flow-group we created when we create a rule in DRL file.

**CallType**
CallType is the way we map drools objects to external data sources. Use appropriate CallTypes for the respective commands.

**Command**
If you want to assess the data through an external source, you must add a command in your service to make an external call to the data source. 

**RuleDataElement:**  This is fact available for drool. This custom field level annotation exposes property to drools. 

**ExternalCallConfiguration:** Custom annotation that denotes that this data object contains one or many external calls in it.  

**ExternalCall:**  Custom annotation, Call we make to fetch data from external datasources like DB, Sagemaker, Nighthawk, Restful web service call. 


