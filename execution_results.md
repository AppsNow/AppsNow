# Decision Engine execution results
Once  the rules are executed, depending upon the scenario, there can be one of the three outputs:

*	React
*	Recommendation
*	Review

All the above three are part of the rules. 

## Recommendation
When the output is Recommendation, the result is a choice to be made between two values. Depending upon the data that we feed, a recommendation is made.

     For example: 
        Whether the email is valid or not?

## React

When the output is React, there will  be an action that is performed automatically.

    For example: 
      Decision to be made, for closing a bank account 
 

## Review

When the output is Review, cases are created and sent to the third party team for manual review.This workflow can be configured.

    For example: 
      Can this transaction be approved?

React and Review are handled by Event Handler. To publish Actions for Review/React , please add this configuration in  cloud config of service:

The below configuration is for Pre-prod:


    pegasus:
    data:
      jms:
        event-management:
          broker-url: failover:(ssl://qal.message-preprod.a.intuit.com:61617)?jms.redeliveryPolicy.maximumRedeliveries=10&jms.redeliveryPolicy.initialRedeliveryDelay=30000&jms.redeliveryPolicy.redeliveryDelay=30000
          username: Intuit.sbg.payments.prm
          password: "{secret}idps:/app/jmspasskey"
          event:
            destination: Subscriber.global.Prm.Intuit.globalvirtual.qal.payments.data.RiskNotification.Virtual
            enabled: true
            is-topic: false
            replay.success.filter.status: 2
            replay.error.filter.status: 3
            replay.count.limit: 10
            replay.date.hour.partitions: 4


  The below configuration is for Prod:

    pegasus:
    data:
      jms:
        event-management:
          broker-url: failover:(ssl://qal.message-preprod.a.intuit.com:61617)?jms.redeliveryPolicy.maximumRedeliveries=10&jms.redeliveryPolicy.initialRedeliveryDelay=30000&jms.redeliveryPolicy.redeliveryDelay=30000
          username: Intuit.sbg.payments.prm
          password: "{secret}idps:/app/jmspasskey"
          event:
            destination: Subscriber.global.Prm.Intuit.globalvirtual.qal.payments.data.RiskNotification.Virtual
            enabled: true
            is-topic: false
            replay.success.filter.status: 2
            replay.error.filter.status: 3
            replay.count.limit: 10
            replay.date.hour.partitions: 4

Once the event is published, please click on the below link to go to the events management section for the detailed configuration or handling of events:

Event Management

For Action management, code changes should be made in below service:
https://github.intuit.com/money-risk/event-management
