# Post onboarding steps

## Add a new Rule Flow Context and Rule Flow

### Rule Flow Context
Rule Flow Context is the way of mapping particular Rule flow to the business process. This custom object provides context about the RuleFlow. This context along with the domain helps to pick and trigger RuleFlow and the Business process to run.

### Rule Flow
Rule Flow is a set of rules to be executed as part of flow.
https://github.intuit.com/SBG-PCS-Risk/rt-rules-engine/blob/master/rt-Rules/src/main/java/com/intuit/sbg/payments/rules/engine/bomData/RuleFlow.java

Use the following example RuleFlowContext  for testing your command:

Context name: PEGASUS_ASSESSMENT_TEST

Context name: PEGASUS_DECISION_TEST

Domain: PAYMENT

To create a Rule Flow you need to create a Pull Request(PR) to the Decision Engine. There are three entities to create a PR. They are:

1. CallType
2. Command
3. RuleFlowContextMapper

**CALLTYPE**
CallType is the way we map drools objects to external data sources. Use appropriate CallTypes for the respective commands.

Example: 
    
    public enum CallType implements CallTypeInterface {
        SRG_Stored_Procedure,
        SRG_Query,
        Realtime_ACH,
        Realtime_ACH_Count,
        ….
        Pricing_Offer,
        FDP;

        @Override
        public String getCallTypeName() {
            return this.name();
        }
    }


**COMMAND**
If you want to assess the data through an external source, you must add a command in your service to make an external call to the data source. 

**COMMAND LOOKUP MAP**

CommandLookupMap is a set of commands collected into a Map. Create a CommandLookupMap bean in your service which should be a spring component. The CommandLookupMap bean is where you link the predefined CallType with your predefined Command, allowing Drools to fetch external data on demand.

Here is an example of the CommandLookupMap bean:

Single Command Bean:

    @Bean
        public CommandLookupMap commandLookupMap() {
            CommandLookupMap map = new CommandLookupMap();
            map.put(CallType.Nighthawk, appContext.getBean(NighthawkCommand.class));
            return map;
        }

Multiple Commands Bean:

Example: code

### Use predefined commands provided by Pegasus
Pegasus provides many predefined commands that you can use for making external calls, customized  to the environment(like prod or pre-prod_.Please refer to the below document to get the configuration for the list of predefined commands:.
https://docs.google.com/spreadsheets/d/1yTtSSTh0hoocZqtueUF2aHFK_WczgjMu-xkhKQrczbs/edit#gid=0 

### Create your own Command or CallType
If you don’t find a suitable command within the pre-defined commands provided by Pegasus you can create your own command.

Create a new command by implementing this interface. Command execution returns a Map<String,RuleDataModel> which can have more than one entries in it. Each entry here is the fact that is available for drool for run time. For e.g. In DB external call you might have more than one property in you select statement, in this case you would add all the properties in SQL response to return map. Decision Engine caches this data internally and optimizes calls for subsequent uses in same session. 

Important Notes: 

* Command must be a spring component
* Command must implement CallType getCommandCallTypeLookup() api to provide the CallType which should be unique for each Command.
* If contributing back to library, make sure you have proper @Conditional with required config, so that Spring will creates bean only when needed. 

Example: code

Ensure that you follow these guidelines while creating your own command to make an external call.

Create Pull Request(PR) to Decision Engine.

To create a Pull Request, 

1. create a new CallType in [CallType.java](https://github.intuit.com/SBG-PCS-Risk/rt-rules-engine/blob/master/rt-Rules/src/main/java/com/intuit/sbg/payments/rules/dynamicDataAccess/configuration/CallType.java)
2. Add a new RuleFlow in [RuleFlow.java](https://github.intuit.com/SBG-PCS-Risk/rt-rules-engine/blob/master/rt-Rules/src/main/java/com/intuit/sbg/payments/rules/engine/bomData/RuleFlow.java)
3. Add a RuleFlowContext mapper in [RuleFlowContextMapper.java](https://github.intuit.com/SBG-PCS-Risk/rt-rules-engine/blob/master/rt-Rules/src/main/%5B%E2%80%A6%5Dsbg/payments/rules/engine/bomData/RuleFlowContextMapper.java) 
4. Add a new command in the Command.java . Make sure it returns the connected CallType value instead of ‘null’.

Example: code

default CallType getCommandCallTypeLookup(){}

* Implement the interface provided in the following file:
https://github.intuit.com/SBG-PCS-Risk/rt-rules-engine/blob/master/rt-rules-engine-service/src/main/java/com/intuit/risk/core/service/common/rule/command/Command.java

**Note**: After creating PR to the decision engine, make sure it gets merged.
