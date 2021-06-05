# Post onboarding steps

After onboarding the service, follow the below steps:
1. Depending on business context, check if any of the existing RuleFlow listed here suits your need. If not, you can create a new RuleFlow and RuleFlowContextMapper in rt-rules. 
2. Command Usage: Skip this step if your rules don't need data from external sources like DB, Webservice call, etc. Command (name originated from command design pattern) is the pattern we use to fetch data from various external data sources. Listed here are the commands that are available out of the box from multiple flavors of SDK (rt-rules, DecisionEngine-Commons, Payments-Common). Pick the command that suits your need. The detailed information about available commands is here. If you do not find the command that suits you, you can create a new command following the steps here.  
3. In case you had to create a new RuleFlow or Command in previous steps, raise a PR for your changes to the DE team. Upon successful merge, update RulesRepo and Service to use the newly created SDK version.

## Add a new Rule Flow Context and Rule Flow

Check if you can use any of Business Domain, Context Name, EventType and Subtype that matches with your context. 
* Create Rule Flow Context using the Business Domain and Context name . Add the below code in RuleFlowContext.java. 

For Example:

    PROFILE_VALIDATION(new HashMap(){{
            put(BusinessDomain.IDENTITY, "PROFILE_VALIDATION");
        }})

* Create Rule Flow using the Event.Type, EventSubType and Rule Flow Context Mapper(Collection of ). Add the below code in RuleFlow.java

For Example:

    PROFILE_VALIDATION(EventType.ASSESSMENT, EventSubType.PROFILE_VALIDATION, RuleFlowContextMapper.PROFILE_VALIDATION)

### Use predefined commands provided by Pegasus
Pegasus provides many predefined commands that you can use for making external calls, customized  to the environment(like prod or pre-prod).The excel sheet consisting of pre-defined commands provides additional information like description of the command, implementation notes, call type, DataSource Type, where(location of the command) and Required Configuration
 Please refer to the below document to get the details:
    https://docs.google.com/spreadsheets/d/1yTtSSTh0hoocZqtueUF2aHFK_WczgjMu-xkhKQrczbs/edit#gid=0 

The below screenshot shows detailed information provided in the excel sheet :
![](images/post_onboard_1.png)

### Creating Custom Command 
* Identify the location where you want to place the Command. If you feel this new Command is too specific for your needs, create the Command in your service.

**Tip:**
       If other teams can use this command, you can contribute back to one of our SDK libraries. 
* Commands are spread across multiple flavors of SDKs on purpose.  The most common and core commands are placed in rt-rules. Commands that are specific to decision-engine are located in DE-Commons, and ones that are specific to the Payments domain are placed in Payment-common. 
* CallType uniquely identifies the Command and associates an externalCall to Command. You can use any of the existing callType that are listed here or create a new one. Make sure the callType you create is unique and return the same in your command implementation. 
* Create a new command by implementing the Command interface. Command execution returns a Map<String, RuleDataModel> which can have more than one entry. Each entry here is the fact that it is available for drools for run time. For e.g., In the restful external call, you might have more than one property in your response. Add all the properties that you require in response to the map so that those properties are available in drools. DecisionEngine caches this data internally and optimizes calls for subsequent use in the same session.  

Sample CustomCommand

    @Component
    public class CustomCommand implements Command<RuleDataCommandRequestEntity, Map<String, RuleDataModel>> {
        @Autowired
        private Environment environment;
        @Override
        public Map<String, RuleDataModel> execute(RuleDataCommandRequestEntity entity) {
            Map<String, RuleDataModel> map = new HashMap<>();
        //  Make external call like DB or Restful web call, parse response and place attributes in map and return it.
            return map;
        }
        @Override
        public CallType getCommandCallTypeLookup() {
            return CallType.CustomCallType;
        }
    }

In case if you have a need to pass any custom properties from Rules to Command(during runtime), you can do that by creating new custom Annotation.  propertyToBePassedFromRules is sample property you can access in command during runtime. There is second way to configure the data objects which can be found here()

SampleCustomAnnotation

    @Target( { ElementType.FIELD })
    @Retention(RetentionPolicy.RUNTIME)
    @RuleDataComponent
    public @interface CustomAnnotationData {    
        String UNPROVIDED = "UNPROVIDED";    
        String name();    
        String propertyToBePassedFromRules();    
        String defaultValue() default UNPROVIDED;    
        LoadingType loadingType() default LoadingType.LAZY;
    }

If you are using CustomAnnotation, we need to implement an Interface RuleDataElementAdapter, please find the sample Adapter implementation below 

    @Component
    public class SampleCustomAnnotationRDEAdapter implements RuleDataElementAdapter<SampleCustomAnnotation>{    
        public final static String SOURCE_TYPE = "source_type";
        @Override
        public RuleDataElement adapt(CustomAnnotationData customAnnotationData, Field field) {
            RuleDataElement ruleData = new RuleDataElement();
            ruleData.setName(customAnnotationData.name());
            ruleData.setType(RuleDataType.getRuleDataType(field.getType()));
            ruleData.setResultKey(cacheData.name());        
            ExternalCall externalCall = new ExternalCall();
            externalCall.setName(CallType.CustomCallType.name());
            externalCall.setLoading(customAnnotationData.loadingType());        
            Map<String, String> parametersMap = new HashMap<>();
            parametersMap.put("PROPERTY", propertyToBePassedFromRules);
            // Can add more than one.         
            ruleData.setExternalCall(externalCall);
            ruleData.setParameters(parametersMap);
            return ruleData;
        }}

Important Notes: 
* Command must be a spring component
* Command must implement CallType getCommandCallTypeLookup() api to provide the CallType which should be unique for each Command.
* If contributing back to the library, make sure you have proper @Conditional with required config, so that Spring creates bean only when needed. So this way, it will not break other services even without config related to this new command.   

Examples: 


**Call Type**
 
    public enum CallType implements CallTypeInterface {
        SRG_Stored_Procedure,
        SRG_Query,
        Realtime_ACH,
        Realtime_ACH_Count,
        …
        Pricing_Offer,
        FDP;

        @Override
        public String getCallTypeName() {
            return this.name();
        }
    }
**Command**

Example link 

**Rule Flow Context Mapper**

    Use the following example RuleFlowContext  for testing your command:
    Context name: PEGASUS_ASSESSMENT_TEST
    Context name: PEGASUS_DECISION_TEST
    Domain: PAYMENT

**Rule Flow**

    public enum RuleFlow {
            ...
        // DEBIT CARD VALIDATION
            VALIDATION_DEBIT_CARD(EventType.VALIDATION, EventSubType.DEBIT_CARD, RuleFlowContextMapper.VALIDATION_DEBIT_CARD),
            ...
    }

Ensure that you follow these guidelines while creating your own command to make an external call.

* Implement the interface provided in the following file:
https://github.intuit.com/SBG-PCS-Risk/rt-rules-engine/blob/master/rt-rules-engine-service/src/main/java/com/intuit/risk/core/service/common/rule/command/Command.java
* After creating PR to the decision engine, make sure it gets merged.
