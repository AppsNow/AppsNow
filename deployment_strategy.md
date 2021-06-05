# Deployment Strategy and rollout of new policies

## Polling of new deployments
After the service is onboarded and the rules are deployed using the Artifact Manager, and if there is a requirement to change the rules. The polling service will automatically deploy the rules
at a  regular time interval (configurable value, ex: 20 seconds). Inside the service itself, it will bootstrap the rules engine, hence there is no need to restart the service when you deploy rules using Artifact Manager.

Deployment happens in one of the following scenarios:
1. When new Commands or CallTypes or RuleFlow or anything new is added, then Service is deployed first and then the Rules.   
2. If you are deleting anything, then the rules must be deployed first and then the service.

Configuration needed for polling service:

    pegasus:
      artifact-manager:
        host: https://riskruleartifactmanager-aws-e2e.api.intuit.com
        artifact-path: /drools/e2e/“your-rules”
        polling-rate: 60000
