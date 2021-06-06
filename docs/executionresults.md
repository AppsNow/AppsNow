# Decision Engine execution results
Once  the rules are executed, depending upon the scenario, there can be one of the three decisions that can be made:

*	React
*	Recommendation
*	Review

All the above three are part of the rules. 

## Recommendation
In this scenario, once the rule is executed the output result is a choice to be made between two values. Depending upon the data that we feed, a recommendation is made.

For example: Whether the email is valid or not?

## React

In this scenario once the rule is executed, there will  be an action that is performed automatically.
For example: Decision to be made, for closing a bank account 

## Review

As part of review, cases are created and sent to the IBOSS team for manual review.

For example: Can this transaction be approved?

React and Review are handled by Event Handler. To publish Actions for Review/React , please add this configuration in DecisionEngine:

    jms:
      risk.management:
      brokerURL: failover:(ssl://qal.message-preprod.a.intuit.com:61617)?jms.redeliveryPolicy.maximumRedeliveries=10&jms.redeliveryPolicy.initialRedeliveryDelay=30000&jms.redeliveryPolicy.redeliveryDelay=30000
    event:
     destination: Subscriber.global.Prm.Intuit.globalvirtual.qal.payments.data.RiskNotification.Virtual
     isTopic: false

For Action management, code changes should be made in below service:

https://github.intuit.com/money-risk/event-management
