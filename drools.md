# Drools

Once you have onboarded your own service to the Pegasus Platform you can create business rules. As part of the onboarding, Pegasus will create some sample test rules for you. You can modify the existing rules or add new rules.

## How to create a sample rule
We will now create a sample rule to demonstrate how you can create  business rules and later for the same rules, we will be creating an API request for data assessment.

But first, we need to add the assets required for creating the business rules.

1. Go to the [Drools WorkBench](https://drools-wb-e2e.app.intuit.com/kiewb/kie-wb.jsp), import your project. Select risk-analyses-rules and click **Import**. 
![](images/onboard20.png)
2. Once the project gets imported, go to the **risk-analysis-rules** space, and add the following assets:
    1. Business Process
    2. Data Object
    3. DRL File
![](images/rules1.png)
3.	While adding a **Business Process**, provide a name for example "Debit Card Validation", and select the package for example, Java package.
4.	While adding a **Data Object**, provide a name for example "Debit Card Validation", and select the same package you have selected while creating the business process. Optionally, you can select the **Persistable** check box if you want all necessary configuration to be stored in the database.
5.	While adding a **DRL file**, provide a name for example "Debit Card Validation", and select the same package you have selected while creating the business process.

## Example 1: Rules for Debit Card Validation

In this simple example, we will check if a debit card number (provided via an API request) is on a negative list for a high loss rate. If it’s found to be on the negative list then the debit card number is Declined. If it's not found on the negative list then the debit card number is Approved.

Perform the following steps to create the business rules

1. Create a Business Process
2. Add Fields to the Data Object
3. Add Rules to the DRL File

### Create a Business Process
Once you have created the DRL file, you need to create a business process. 

1. Open the business process that you created in Step 3 of "How to create a sample rule".
2. On the **Model** tab, Create a business process diagram by using the elements provided on the left hand side, and then from the Properties panel select your rule from the **Rule Flow Group**. 
![](images/rules2.png)
### Add Fields to the Data Object
Once you have created the Data Object, you need to add fields that will be used for creating the business rules against which you will be assessing the API input.

1. Open the data object that you created in Step 4 of "How to create a sample rule".
2. Add a new field with the name "debitCardNumber", and add the **RuleDataElement**, a **field level annotation** that determines the behaviour of the rule created for the field. RuleDataElement is how a field is exposed to Drools.


| Field Name | Descripton |
| ------------- |:-------------|
| externalCall | Get data from an external source (not user provided data). |
| resultKey | Specify a location to store the rule assessment result. |
| defaultValue | The default value to be used if the system fails to get data from an external source. |
| optional | Whether the field is mandatory or not. |
| name | The name of the field. |


![](images/rules3.png)

3. Create another data object for getting data from external source. Annotate with ExternalCallConfiguration.in that object there should be a field which says which call type to be used to fetch the data.
4. Provide the following **class level annotations**:
    1. **RuleFlowFact**: Under **availableForRuleFlows**, specify the rule flow for which the data object is available. For example, "Debit Card Validation".
    2. **RuleFlowConfiguration**: Under **triggeredByRuleFlow**, specify the rule flow that will trigger the data object. These are context names that you can select for the rule flow. For example, "Debit Card Validation". Contact Pegasus if you want a new context name which is not available in the list.
    Under **businessProcess**, select the business process that you have created for the debit card validation. (the id under the business process properties)


### Add Rules to the DRL File
Update the DRL file for the external call
Once you have created the Data Objects, you can now add the actual rules that will define the assessment logic.

1. Open the DRL file that you created in Step 5 of "How to create a sample rule".

2. Paste the following code:

        package sbg.risk.rulesets.debitcardvalidation;
 	
        import com.intuit.sbg.payments.rules.engine.bomData.RuleEngineNode;
        import com.intuit.sbg.payments.rules.engine.bomData.RuleEngineOutput;
        import com.intuit.sbg.payments.rules.engine.bomData.RuleGroupStrategy;
        import com.intuit.sbg.payments.rules.engine.bomData.Reason;
        import com.intuit.sbg.payments.rules.engine.bomData.PolicyType;
        import sbg.risk.rulesets.debitcardvalidation.DebitCardValidation;
        import com.intuit.sbg.payments.rules.engine.bomData.ReasonType;
 
 
        rule "SAMPLE RULE"
                enabled true
                ruleflow-group "debit_card_validation"
                dialect "mvel"
                salience 1
                when
                output : RuleEngineOutput( )
                debitCardValidation : DebitCardValidation ( debitCardNumber 
                == "testInvalid_1234")
 
          then
                    Reason reason = new Reason();
                    reason.setType( ReasonType.REPORT );
                    reason.setDescription( "Debit card found on negative list 
                    for a high loss rate." );
 
                    insert( reason );
                    RuleEngineNode node = new RuleEngineNode();
                                
        node.setStrategy(RuleGroupStrategy.TEST);
                  node.setPercentage("100");
                  node.setPolicyVersion("1");
                  node.setPolicyType(PolicyType.FRAUD);
 
                  insert ( node )
                  output.setResult( "DECLINED" )
        end
 
          rule "DEBIT CARD VALID"
                enabled true
                ruleflow-group "debit_card_validation"
                dialect "mvel"
                salience 0
                when
               	output : RuleEngineOutput( )
                                not Reason()
 
                then
                                Reason reason = new Reason();
                                reason.setType( ReasonType.REPORT );
                                reason.setDescription( "Debit Card was not 
                                found on negative lists." );
 
                                insert( reason );
 
                                RuleEngineNode node = new RuleEngineNode();
                                node.setStrategy(RuleGroupStrategy.TEST);
                                node.setPercentage("100");
                                node.setPolicyVersion("1");
                                node.setPolicyType(PolicyType.FINANCIAL);
 
                                insert ( node );
 
                                output.setResult( "APPROVE" )
 
          end

The above code contains rules to assess the incoming debit card number. If the debit card number is on a negative list for a high loss rate the debit card number is Declined. If it's not found on the negative list then the debit card number is Approved.


# Rules Integration

Business Rules integration with Pegasus happens in the following workflow.
![](images/integration.png)
First you import the business rules project the to drools workbench/rules fork. Once you make any artifact changes on the User Fork, it creates a Pull Request on the Master repo, for example, SBG-PCS-Risk. Once the Pull Request is merged, it triggers a Jenkins Job on IBP Jenkins. The Jenkins Job will build the rules artifact and upload the artifact to the PreProd (pre-production bucket). TEP Jenkins then triggers the regression job, which is run as the Rules Service. The Rules Services pulls the artifact from the PreProd, and runs the regression tests. If the regression tests pass, the artifact is uploaded to the Prod (production bucket). The artifact is then put into the Artifact Manager via AWS Lambda. 

## How to generate the rules artifact and deploy to Pre-pod
1. Login to https://pegasus-e2e.app.intuit.com/app/ares/artifacts and click **Generate Artifact**.
![](images/s3prod1.png)
2. Select the **Space**, **Project**, **Branch** of the rules repository, add a **description**, and then click **Generate**.
3. A new artifact is generated with an ID. Click the deploy icon to deploy the artifact to Pre-Prod.
![](images/s3prod4.png)
4. Select the **Environment**, add a **description**, specify a **Snow ticket ID**, and then click **Deploy artifact**.
5. Once the artifact is deployed, you’ll see a confirmation message and the environment name is displayed as shown below.
![](images/s3prod3.png)
 
## How to deploy the rules artifact to Prod
Perform the following steps to deploy the rules artifact to Prod:
1. Login to https://pegasus.app.intuit.com/app/ares/artifacts.
2. Find the artifact you want to deploy and then follow the Steps 3 to 5 in "How to generate the rules artifact and deploy to Pre-pod". 
