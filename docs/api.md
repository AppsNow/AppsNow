# Implementing and Consuming the Decision Engine SDK

You must call the following API from your application in order to consume the decision engine.

## Risk Analysis API

The create assessment API sends data to the decision making engine for performing an assessment. The API sends the parameters you want to evaluate against the pre-defined business rules, performs an assessment to determine if the data sent matches with the business rules, and then responds with the result of the assessment.

| HTTP Method      | POST           |
| ------------- |:-------------|
| API Endpoint      | v3/riskAnalysis |


### Request Headers

| Header      | Description|
| ------------- |:-------------|
| Authorization | The Authorization to be used is as per the authorization method defined in the service that you onboarded to the Pegasus Platform. |


### Request Body

| Name | Required | Type | Description |
| ------------- |:-------------|:-------------|:-------------|
| name | yes | string | Specify the context name that you have selected while creating the RuleFlowConfiguration. |
| domain | yes | string | Specify the domain name. Possible values are: PAYROLL, LENDING, PAYMENT, IDENTITY, RISK, ACCOUNTING, WALLET, TAX, GPU, PRODUCT_TRUST, CORE_SECURITY, INSURANCE |
| entityId | yes | string | The field that you have created in the Data Object. See Add Fields to the Data Object. |
| debitCardNumber | yes | integer | The debit card number for which you are doing the assessment. |
| metaData |  |  | [Optional] Metadata is common across multiple entities you may include in the API request. For example, multiple debit card numbers. |
	

**Note**: Contact Pegasus if you want a new context name or domain which is not available in the list.

## Example 1: API request for Debit Card Validation
In this simple example, we will check if a debit card number (provided via an API request) is on a negative list for a high loss rate. If it’s found to be on the negative list then the debit card number is Declined. If it's not found on the negative list then the debit card number is Approved. We have created the business rules for this assessment in "Example 1: Rules for Debit Card Validation".

### Sample API request and response for debit card approved

This API call will send a debit card number which is not on the negative list for a high loss. Hence the debit card number is approved. 

**Request**

    {
    "context": {
	"name": "VALIDATION_DEBIT_CARD",
	"domain": "RISK"
    },
    "source": {"name":"test", "requestId":"test","requestTimestamp":"test"},
    "metaData": {
	    "backOfficeId": "1234",
	    "realmId": "9130365793288436",
	    "offeringId": "Intuit.ems.iop",
	    "countryCode": "US",
	    "fundingModel": 0,
	    "debitDate": "2021-02-02",
	    "echoRequest": "[true|false]"
    },
    "entities": [
	{
      "entityInfo" : {
        "entityId": "debitCardNumber"
 
      },
 	"inputs": {
        "debitCardNumber": "1234_valid_sha"
        }
 
    	}
      ]
    }


**Response** 

The decisions and the results section of the response indicates that the debit card number is approved.

    {
	"source": {
        "name": "test",
        "requestId": "test",
        "requestTimestamp": "test"
	},
	"metaData": {
        "backOfficeId": "1234",
        "realmId": "9130365793288436",
        "offeringId": "Intuit.ems.iop",
        "countryCode": "US",
        "fundingModel": "0",
        "debitDate": "2021-02-02",
        "echoRequest": "[true|false]"
	},
	"decisions": [
        {
            "result": "APPROVE",
            "properties": {},
            "entityInfo": {
                "entityVersion": null,
                "entityId": "debitCardNumber",
                "entityIdType": null
            },
            "reasons": [
                {
                    "type": "REPORT",
                    "name": "DEBIT CARD NOT ON NEGATIVE LISTS",
                    "group": "debit_card_validation",
                    "description": "Debit Card was not found on                    
                     negative lists.",
                    "priority": 0,
                    "priorityLevel": null,
                    "dataPoints": {},
                    "referenceId": null,
                    "referenceIdType": null,
                    "silent": false,
                    "resultCode": null
                }
            ],
            "priority": 0,
            "description": null
        }
	  ]
    }

### Sample API request and response for debit card declined
This API call will send a debit card number which is on the negative list for a high loss. Hence the debit card number is declined. 

**Request**

    {
    "context": {
	    "name": "VALIDATION_DEBIT_CARD",
	    "domain": "RISK"
    },
    "source": {"name":"test", "requestId":"test","requestTimestamp":"test"},
      "metaData": {
	    "backOfficeId": "1234",
	    "realmId": "9130365793288436",
	    "offeringId": "Intuit.ems.iop",
	    "countryCode": "US",
	    "fundingModel": 0,
	    "debitDate": "2021-02-02",
	    "echoRequest": "[true|false]"
    },
    "entities": [
	{
      "entityInfo" : {
        "entityId": "debitCardNumber"
 
      },
 	"inputs": {
        "debitCardNumber": "testInvalid_1234"
          }
 
    	}
      ]
    }


**Response** 

The decisions and the results section of the response indicates that the debit card number is approved.

    {
	"source": {
        "name": "test",
        "requestId": "test",
        "requestTimestamp": "test"
	},
	"metaData": {
        "backOfficeId": "1234",
        "realmId": "9130365793288436",
        "offeringId": "Intuit.ems.iop",
        "countryCode": "US",
        "fundingModel": "0",
        "debitDate": "2021-02-02",
        "echoRequest": "[true|false]"
	},
	"decisions": [
        {
            "result": "DECLINED",
            "properties": {},
            "entityInfo": {
                "entityVersion": null,
                "entityId": "debitCardNumber",
                "entityIdType": null
            },
            "reasons": [
                {
                    "type": "REPORT",
                    "name": "SAMPLE RULE",
                    "group": "debit_card_validation",
                    "description": "Debit card found on negative list 
                     for high loss rate.",
                    "priority": 0,
                    "priorityLevel": null,
                    "dataPoints": {},
                    "referenceId": null,
                    "referenceIdType": null,
                    "silent": false,
                    "resultCode": null
                }
            ],
            "priority": 0,
            "description": null
        }
  	  ] 
    }
