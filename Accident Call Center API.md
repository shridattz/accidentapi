# Accident.com Call Center API Documentation

Accident.com’s APIs (Application Programming Interfaces) power its platform for lead creation. Behind these APIs is a software layer enabling the lead creation and this document discusses the terminologies and rules involved in greater detail.

Table of Content

 * Endpoints
 * Authentication
 * Type of Leads
 * Request Object for `lead` Type
     * List of Accepted `case_type` values
     * Anatomy of `fields` array
     * The `ref` guide
     * Response Object
     * Understanding Responses
     * References for `case_type` - Auto Accident
          * Sample Request
     * References for `case_type` - Medical Malpractice
          * Sample Request
     * References for `case_type` - Personal Injury
          * Sample Request
     * References for `case_type` - Slip And Fall
          * Sample Request
     * References for `case_type` - Workers Compensation
          * Sample Request
     * References for `case_type` - Wrongful Death
          * Sample Request
      * References for `case_type` - SSDI
          * Sample Request
     * References for `case_type` - Other Case Types
          * Sample Request
	     * Sample Request with `MM/DD/YYYY` in `incident_date_option_b`
     * Pipes IVR - Requalification
	     * Sample Request with `pipes` param - Fully Qualified
	     * Sample Request with `pipes` param - Not a Lead / Refused Intake 
 * Request Object for `attorney` Type
     * Sample Request & Response
 * Request Object for `inquiry` Type
     * Sample Request & Response
 * Web Leads

## Endpoints
|Environment   	|Link   	|Description   	|
|---	|---	|---	|
|Production	|https://api.accident.com/api/lead	| Production environment should be used once the integration is tested perfectly on staging.	|

## Authentication

The `api-key` and `api-secret` header should be set in order to make requests to our API.
These credentials will be provided to you for both production and staging servers.


```
#!curl

$ curl --location --request POST '<ENDPOINT>' \
--header 'api-key: XXXXXXXXX' \
--header 'api-secret: XXXXXXXXX'
```
## Type of Leads

These are the 3 types of leads that can be created using the Accident API


|Type   	|Description   	|
|---	|---	|---	|
|lead	| To be used to create leads who are searching for personal injury consultation	|
|attorney	| To be used to create an attorney lead who is trying to learn or follow up about Accident.com Technology	|
|inquiry	| This type should be used to address calls that are generic in nature|


## Request for `lead` Type

The request body for `type` - `lead` should comply with these rules.

|Name   	|Required   	|Type   	|Description   	|
|---	|---	|---	|---	|
| type   	| True  	| Text 	| Should be set as `lead`.  	|
| arrived_at   	| True  	| Timestamp 	| Should be in `UTC`. We use this timestamp to identify the call record on our end, this call record data is used to send a conversion to Google Ads  	|
| converted_at  	| True  |  Timestamp 	| Should be in `UTC`. will be used to update the call record on our end, this call record data is used to send a conversion to Google Ads    	|
| test_mode  	|   false	|  Boolean	|  `Default : false`. Should be set as true if the API call is used to send test data. 	|
| caller_first_name  	|  True 	| Text  	| First name of the caller
| caller_last_name  	|  True 	| Text  	| Last name of the caller
| lead_first_name  	|  True 	| Text  	| First name of the lead. This can be same as `caller_first_name` if the lead themselves have called in.
| lead_last_name  	|  True 	| Text  	| Last name of the lead. This can be same as `caller_first_name` if the lead themselves have called in.
| lead_email  	| True  	| Email   	| Email Address of the Lead
| lead_phone  	| True  	| Phone  	| Phone number of the lead. This can be either in US Domestic format ( Eg: `(541) 754-3010` ) or else in E.164 format ( Eg: `+14155552671` )
| caller_ID  	| True  	| Phone  	| Phone number of the lead. This can be either in US Domestic format ( Eg: `(541) 754-3010` ) or else in E.164 format ( Eg: `+14155552671` )
| case_type  	| True  	| Text  	| The injury type identified in the phone conversation. The list of values is provided later in the document. Should there be a case type beyond the values that we accept
| zip_code  	| False  	|  US Zip Code	| Location of the Lead where the injury occured
| city  	| False  	|  Text	| Location of the Lead where the injury occured. `city` should be in text and used in combination with `state`. 
| state  	| False  	|  Text	| Location of the Lead where the injury occured. `state` should be in text and used in combination with `city`. 
| disqualify  	| False  	|  Boolean	| If there are problems understanding location of the lead or anything else, `disqualify` can be set as true. This will prevent us from auto assigning this lead to our partners. 
| disqualify_reason  	| False  	|  Text	| explanation on why the lead was disqualified. 
| DNC  	| False  	|  Boolean	| If the lead expressed they do not want to be called back
| customer_id  	| False  	|  Identifier for Lead	| This is a hashed version of lead identifier in our system. We will post this field on alerts web lead api and it should be returned if team alert believes that this is an update to an existing lead in our system.
| source  	| False  	|  Source for this lead	| use from a list of values provided - `google`, `facebook`, `5654 AdMediary`, `zeeto`. This is currently passed in  comments field.
| unresponsive  	| False  	|  Boolean	| If the lead is not responding to our calls, please set this to `True`
| number_of_calls  	| False  	|  Text	| How many call attempts were made for this lead. Please populate this field if it's a web lead.
| status  	| False  	|  Text	| what ended up happening with the call. Accepts string, should be a value of status matrix that `Alert` works with.
| pipes  	| False  	|  Boolean	| If the lead is requalified after an inbound call on the pipes DID, please set this as `True`
| fields  	| True  	|  Array 	| The request body for each case type differs as it has a specific set of references for each case type. Each element in the `fields` array is detailed below.

### List of Accepted `case_type` values

Mentioned below are `case_type` values that we support. For any `case_type` that doesn't match with items from this list, should be entered as meaningful text. For all such new `case_type` the request body should have references of `Person Injury`. The sample for the request is provided later in this document.

|  Case Type 	|   Value	| Description
|---	|---	|---	|
| Auto Accident  	| `Auto Accident`  	|  If the lead case is an auto accident. 	|
| Medical Malpractice  	| `Medical Malpractice`  	| If the lead case is medical malpractice.  	|
| Personal Injury  	| `Personal Injury`  	| If the lead case is personal injury.  	|
| Slip and Fall  	|  `Slip and Fall` 	| If the lead case is slip and fall.  	|
| Workers Compensation  	|  `Workers Compensation` 	| If the lead case is workers compensation.  	|
| Wrongful Death  	|  `Wrongful Death` 	| If the lead case is wrongful death.  	|


### Anatomy of fields array

The fields array in the request contains objects. Each of these objects has three properties


|  Property  	|   Required	| Description
|---	|---	|---	|
| `ref`  	| True  	|  Key to identifying questions to our internal 	|
| `title` 	| False  	| If you believe that you want to share the question that was used, it should be passed in this field.  	|
| `answer`  	| True  	| The answer comes from a set of choices or adheres to a type. The " `ref` guide " section explains the `ref` and their rules. 	|

Example : 


```
#!json

 {
           "ref":"have_attorney",
           "title": "Do you have an attorney representing you for this matter?",
           "answer" : "No"
  }

```

### The `ref` guide

Below are all of the `ref` values that can be used in the `fields` array. Each `ref` has a type and can be a choice of options, a boolean or text. We have included the default question that will get considered should the ref object have a missing `title` property. The explanation of which `ref` objects to be used for each `case_type` is mentioned later in the document.


|  Ref Key	|   Type	|   Choices	| Description | Default Question |
|---	|---	|---	|---	|---	|
| `incident_date_option_b`  	|  Enum / Date 	| `Less than 1 year` , `Less than 2 years` , `Less than 3 years`, `MM/DD/YYYY` or `MM-DD-YYYY`,`MM/YYYY` or `MM-YYYY`, `YYYY`	| Single Choice. Should be used for date questions. The actual date if any can be specified too. We will automatically convert it to our set of options internally. 	| `How long ago did the injury happen?` |
| `have_attorney`  	|  Yes/No 	| `Yes` , `No`	| Single Choice. Whether lead already has an attorney. In case of unsure, the answer to choose should be `No`  	| `Do you already have a lawyer representing you?` |
| `primary_injury`  	|  Enum 	| `Back or Neck Pain`,`Broken Bones` , `Cuts and Bruises`, `Headaches` , `Memory Loss` , `Loss of Limb` , `Other`	| Single Choice. Please use the closest possible injury type, If the answer from the caller doesn't match the list or there is something additional to add for a particular choice, `comments` ref can be used to furnish those details.  	| `What is the primary type of injury?` |
| `doctor_treatment`  	|  Yes/No 	| `Yes` , `No`	| Single Choice. If the lead has undergone any treatment, hospitalization etc. In case of unsure, the answer to choose should be `No`   	| `Did the injury require hospitalization, medical treatment, or surgery?` |
| `charges_filed`  	|  Yes/No 	| `Yes` , `No`	| Single Choice. If the lead has lodged a police complaint. In case of unsure, the answer to choose should be `No`  	| `Was a police report filed?` |
| `action_taken `  	|  Yes/No 	| `Yes` , `No`	| Single Choice. If the lead has filed a case etc. In case of unsure, the answer to choose should be `No`  	| `Have you taken any action regarding your claim?` |
| `were_you_at_fault`  	|  Yes/No 	| `Yes` , `No`	| Single Choice. Lead at fault. In Auto Accident, if only one vehicle is involved in the accident and the lead is the driver,  Pass `were_you_at_fault` as **true**.  In case of unsure, the answer to choose should be `No` 	| `Were you at fault for the accident?` |
| `were_you_injured `  	|  Yes/No 	| `Yes` , `No`	| Single Choice. Did the lead have any injury?. In case of unsure, the answer to choose should be `Yes`, this field is optional and should be passed only when the lead mentions that they aren't injured. 	| `Were you injured?` |
| `cause_of_injury`  	|  Yes/No 	| `Yes` , `No`	| Single Choice. What the cause of the injury is.  	| `What is the cause of your injury?` |
| `missed_work`  	|  Yes/No 	| `Yes`, `No`	| Single Choice. Lead missed work because of the injury. In case of unsure, the answer to choose should be `No`  	| `Did the injury require you to miss work?` |
| `injuries_medical_malpractice`  	|  Enum 	| `Loss of Physical Ability` , `Death Of Patient` , `Birth Injury` , `Misdiagnosis` , `Other`	| Single Choice. What are the injuries that resulted from the medical malpractice?  	| `What injuries resulted from Medical Malpractice?` |
| `cause_of_death`  	|  Enum	| `Vehicle Accident` , `Reckless Act` , `Negligence/Careless Act`, `Other`	| Single Choice. Cause of Victim's death.  	| `What was the primary cause of the victim's death?` |
| `relation_with_victim`  	|  Enum 	| `Parent` , `Spouse` , `Sibling` , `Friend`	| Single Choice. Relationship to the victim.  	| `What is your relationship to the victim?` |
| `terms`  	|  Yes/No 	| `I agree`, `I do not Agree`	| Single Choice. If the user accepts our Terms of Service  	| `Please read and accept our Terms of Service` |
| `comments`  	|  Text 	| NA	| Text. Details about the case  	| `Describe your case` |
| `settled_insurance`  	|  Yes/No 	| `Yes`, `No`	| Single Choice. If the lead has already settled with the insurance company. In case of unsure, the answer to choose should be `No`  	| `Have you settled with the insurance company regarding your injuries?` |
| `signed_retainer`  	|  Yes/No 	| `Yes`, `No`	| Single Choice. If the lead has already signed a retainer with a lawfirm. In case of unsure, the answer to choose should be `No`  	| `Have you ever signed a retainer with another law firm regarding this case?` |
| `role_in_accident`  	|  Enum 	| `passenger`, `driver`, `pedestrian`	| Single Choice. Used in MVA cases, to relate the lead's role in the accident. 	| `Were you the driver, passenger or pedestrian?` |
| `cash_advance_discussion`  	|  Yes/No 	| `Yes`, `No`	| Single Choice. If the lead has discussed with the lawfirm to get a cash advance for your pending settlement. In case of unsure, the answer to choose should be `No`  	| `Have you spoken to your attorney about this cash advance?` |
| `lawfirm_approved_funding`  	|  Yes/No 	| `Yes`, `No`	| Single Choice. If the lead's lawfirm has approved them of the funding they wish to receive. In case of unsure, the answer to choose should be `No`  	| `Did your attorney approve of you receiving a cash advance for your pending settlement?` |
| `property_damage`  	|  Yes/No 	| `Yes`, `No`	| Single Choice. If the lead has already signed a retainer with a lawfirm. In case of unsure, the answer to choose should be `No`  	| `Did the accident cause at least $1500 in damage to your car or property?` |
| `police_report`  	|  Yes/No 	| `Yes`, `No`	| Single Choice. If the lead has filed a police report that indicates that they weren't at fault. In case of unsure, the answer to choose should be `No`  | `Is there are police report to support that you were not at fault?` |
| `driver_insured`  	|  Yes/No 	| `Yes`, `No`	| Single Choice. Used in MVA cases indicating if the other driver involved in the accident has a valid auto insurance. 	| `Did the other driver, who was at fault, have auto insurance?` |
| `accident_vehicle_count`  	|  Text 	| NA	| Used in MVA cases indicating the number of vehicles that were involved in the accident 	| `How many cars were involved in the accident?` |
|`challenges_at_work`|Yes/No|`Yes`, `No`|The purpose of this question is to ensure that the disability is catastrophic, and prevents the lead from being able to do tasks for a prolonged period of time. If "yes", disqualify. |`Can you stand, sit, walk, or lift objects for long periods of time?`
|`doctor_work_recommendation`|Yes/No|`Yes`,`No`|The lead must be unable to work for at least 12 months when applying for SSDI. If "No", disqualify. |`Has your doctor told you that you can’t work for at least 1 year due to your disability?`
|`age` |Enum / Text|`18 - 39`,`40 - 49`,`50 - 64`,`65+`|We accept age range. For date use `dob` field |`What is your age?(select your age range)`
|`dob` | Date / Month & Year / Year |`MM-DD-YY` , `MM-DD-YYYY`, `MM-YYYY` ,`YYYY`| Valid date. |`What is your age?(select your age range)`
|`years_employed`|Enum / Text|`Less than 1 year`,`1 - 4 years`,`5 - 9 years`,`10+ years`,|Applicant must have been working at least 5 out of the last 10 years. If `years_employed` is less than 5, then disqualify. |`Out of the last 10 years, how many years did you work?`
|`received_legal_advise`|Yes/No|`Yes`,`No`|If the applicant has received legal assistance |`Have you already spoken to a lawyer about your case?`
|`household_income`|`Text`|NA|If the applicant has an income source. This field is optional|`Do you have any household income?`
|`applied_ssdi`|Yes/No|`Yes`,`No`|If the applicant has an income source. This field is optional |Have you applied for disability yet?
|`stopping_work`|Yes/No|`Yes`,`No`|If the applicant is ready to stop work in the next 2 months. This field is optional |Will you stop working within the next 2 months because of your disability?
|`received_benefits`|Yes/No|`Yes`,`No`|If the applicant is already receiving SSDI benefits. This field is optional |Are you receiving, or have you been approved for disability benefits?

##### Note : 
When either `settled_insurance`  or `signed_retainer` is sent as yes, we will consider that the lead already has an attorney and will default `have_attorney` ref to **Yes**, even if the requalification request sends it as **No**


### Response Object

Mentioned below are the properties that will be present in the response object.

|  Key	|   Seen In	| Description 
|---	|---	|---	|
|`status`	| All Responses (`lead`, `attorney`, `inquiry`)	| `created` - to acknowledge we received the details. `failed` - We received the details but the request was poorly formulated. `error` - Something has gone wrong.  	|
|`result`	| Only in `lead`	| `matched` - to acknowledge that lead qualified and is assigned to an attorney. `qualified` - to acknowledge that lead qualified but we didn't find a attorney. `disqualified` - the lead has no injury and is rejected.  	|
|`problems`	| All Responses	| This array will contain multiple messages which will indicate the issues with the request  	|
|`attorney`	| Only in `lead`	| This is an object and it will contain `name`, `law_firm_name`, `phone` and `email` ( see skeleton below )  	|
|`lead_score`	| Only in `lead`	| score for the `lead`. Our proprietary decides these scores depending on the answers	|
|`script`	| All Responses	| will contain a message that should be communicated over the phone.	|
|`call_ref`	| All Responses	| This will be a HashString which will refer to the call on our end.	|


Response Skeleton


```
#!json

{
    "status": "created", // failed,error
    "problems" : [
​
    ],
    "attorney":{
        "name":"Someone",
        "law_firm_name":"Somewhere",
        "phone":"+19999999999",
        "email":"someone@developer.com"
    },
    "lead_score":"90",
    "script":"This message should be communicated back to the caller.",
    "result": "matched" /* Other values : qualified, disqualified */
    "call_ref":"dvzxwece2ef22eqcqs" // for eg only
}
```

Sample Response

```
#!json

{
    "status": "created",
    "problems" : [
​        "incident_date_option_b with answer [06/27/2019] does not match list of options"
    ],
    "attorney":{
        "name":"Test Lawyer",
        "law_firm_name":"Test Lawyer Firm",
        "phone":"+19999999999",
        "email":"testlawyer@lawfirm.com"
    },
    "lead_score":"90",
    "script":"This message should be communicated back to the caller.",
    "result": "matched",
    "call_ref":"dvzxwece2ef22eqcqs"
}
```

### Understanding Responses

Here's a list of **problem** array messages

-   Missing Answer - Would appear when nothing is passed for a **ref**

     * **REF_KEY** ref has a missing answer ( Eg. doctor_treatment ref has a missing answer )

-   Answer Mismatch - Would appear when the ref answer set doesn't match with the answer that is passed

     * **REF_KEY** ref with an answer [ANSWER] does not match the list of options ( Eg. doctor_treatment ref with an answer [] does not match the list of options )

-   Missing in Request - Would appear when a ref is not passed for a request
     * **REF_KEY** ref is missing in the request body ( Eg. zip_code ref is missing in the request body )

Example Responses

1)  


	{
	    "problems": [
	        "doctor_treatment ref has a missing answer"  
	    ],
	    "lead_score": "",
	    "call_ref": "",
	    "attorney": [],
	    "status": "failed"
	}

2)

    {
	"problems": [
		"case_type ref is missing in the request body"
        ],
	"lead_score": "",
	"call_ref": "",
	"attorney": [],
	"status": "failed"
	}


3)

    {
	"problems": [
		"zip_code ref is missing in the request body", 
		"incident_date_option_b ref is missing in the request body",
		"primary_injury ref is missing in the request body",
		"have_attorney with answer [] does not match list of options",
		"zip_code ref is missing in the request body"
        ],
	"lead_score": "",
	"call_ref": "",
	"attorney": [],
	"status": "failed"
	}

### References for `case_type` - Auto Accident

Following set of references should be passed for `Auto Accident` case type.

* `incident_date_option_b`
* `doctor_treatment`
* `were_you_at_fault`
* `were_you_injured` - OPTIONAL
* `have_attorney`
* `primary_injury`
* `comments`
* `settled_insurance`
* `signed_retainer`
* `role_in_accident`
* `accident_vehicle_count`

If only one vehicle is involved in the accident and the lead is the driver,  Pass `were_you_at_fault` as **true**. 

#### Sample Request - Auto Accident


```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "Auto Accident",
    "zip_code":"10027",
    "fields":[
	    {
            "ref":"were_you_injured",
            "title": "Were you or a loved one Injured in an Accident that wasn’t your fault?",
            "answer" : "No"
        },
        {
            "ref":"were_you_at_fault",
            "title": "Were you at fault for the accident?",
            "answer" : "No"
        },
        {
            "ref":"injury_cause",
            "title": "What caused your injury?",
            "answer" : "Motorcycle Accident"
        },
        {
            "ref":"have_attorney",
            "title": "Do you currently have a lawyer representing your claim?",
            "answer" : "No"
        },
         {
            "ref":"primary_injury",
            "title": "Did you sustain any of the following?",
            "answer" : "Headaches"
        },
         {
            "ref":"doctor_treatment",
            "title": "Did the injury require hospitalization, medical treatment, surgery or cause you to miss work?",
            "answer" : "No"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Select Year of Injury",
            "answer" : "2021"
        },
        {
            "ref":"settled_insurance",
            "title": "Have you settled with the insurance company regarding your injuries?",
            "answer" : "No"
        },
        {
            "ref":"signed_retainer",
            "title": "Have you ever signed a retainer with a law firm regarding this case?",
            "answer" : "No"
        },
         {
	         ref: "role_in_accident",
	         title: "Were you the driver, passenger or pedestrian?",
	         answer: "passenger"
         },
         {
            "ref":"accident_vehicle_count",
            "title": "Including your vehicle, how many cars were involved in the accident?",
            "answer" : 1
        },
         {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'

```

### References for `case_type` - Medical Malpractice

Following set of references should be passed for `Medical Malpractice` case type.

* `incident_date_option_b`
* `doctor_treatment`
* `were_you_at_fault`
* `were_you_injured` - OPTIONAL
* `have_attorney`
* `injuries_medical_malpractice`
* `comments`
* `settled_insurance`
* `signed_retainer`

#### Sample Request - Medical Malpractice


```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "Medical Malpractice",
    "zip_code":"10027",
    "fields":[
	    {
		    "ref" : "settled_insurance",
			"answer" : "no"
		},
		{
			"ref" : "signed_retainer",
			"answer" : "no"
		},
        {
            "ref":"have_attorney",
            "title": "",
            "answer" : "No"
        },
         {
            "ref":"injuries_medical_malpractice",
            "title": "What injuries resulted from Medical Malpractice?",
            "answer" : "Minor injury"
        },
         {
            "ref":"doctor_treatment",
            "title": "Did you receive any medical treatment?",
            "answer" : " No"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Did the accident happen?",
            "answer" : "Less than 2 years"
        },
        {
            "ref":"were_you_at_fault",
            "title": "Were you at fault for the accident?",
            "answer" : "No"
        },
        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'
```

### References for `case_type` - Personal Injury

Following set of references should be passed for `Personal Injury` case type.

* `incident_date_option_b`
* `doctor_treatment`
* `have_attorney`
* `were_you_injured` - OPTIONAL
* `primary_injury`
* `comments`
* `settled_insurance`
* `signed_retainer`

#### Sample Request - Personal Injury


```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "Personal Injury",
    "zip_code":"10027",
    "fields":[
	    {
		    "ref" : "settled_insurance",
			"answer" : "no"
		},
		{
			"ref" : "signed_retainer",
			"answer" : "no"
		},    
        {
            "ref":"have_attorney",
            "title": "",
            "answer" : "No"
        },
         {
            "ref":"primary_injury",
            "title": "Did you sustain any of the following?",
            "answer" : "Headaches"
        },
         {
            "ref":"doctor_treatment",
            "title": "Did you receive any medical treatment?",
            "answer" : " No"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Did the accident happen?",
            "answer" : "Less than 2 years"
        },
        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'
```

### References for `case_type` - Slip And Fall

Following set of references should be passed for `Slip And Fall` case type.

* `incident_date_option_b`
* `doctor_treatment`
* `have_attorney`
* `were_you_injured` - OPTIONAL
* `primary_injury`
* `comments`
* `settled_insurance`
* `signed_retainer`

#### Sample Request - Slip And Fall


```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "Slip And Fall",
    "zip_code":"10027",
    "fields":[
	    {
		    "ref" : "settled_insurance",
			"answer" : "no"
		},
		{
			"ref" : "signed_retainer",
			"answer" : "no"
		},
        {
            "ref":"have_attorney",
            "title": "",
            "answer" : "No"
        },
         {
            "ref":"primary_injury",
            "title": "Did you sustain any of the following?",
            "answer" : "Headaches"
        },
         {
            "ref":"doctor_treatment",
            "title": "Did you receive any medical treatment?",
            "answer" : " No"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Did the accident happen?",
            "answer" : "Less than 2 years"
        },
        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'
```

### References for `case_type` - Workers Compensation

Following set of references should be passed for `Workers Compensation` case type.

* `incident_date_option_b`
* `doctor_treatment`
* `have_attorney`
* `were_you_injured` - OPTIONAL
* `primary_injury`
* `comments`
* `settled_insurance`
* `signed_retainer`

#### Sample Request - Workers Compensation

```
#!curl

curl --location --request POST 'https://staging.api.accident.com/api/accident-create-lead' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "Workers Compensation",
    "zip_code":"10027",
    "fields":[
	    {
		    "ref" : "settled_insurance",
			"answer" : "no"
		},
		{
			"ref" : "signed_retainer",
			"answer" : "no"
		},
        {
            "ref":"have_attorney",
            "title": "",
            "answer" : "No"
        },
         {
            "ref":"primary_injury",
            "title": "Did you sustain any of the following?",
            "answer" : "Headaches"
        },
         {
            "ref":"doctor_treatment",
            "title": "Did you receive any medical treatment?",
            "answer" : " No"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Did the accident happen?",
            "answer" : "Less than 2 years"
        },
        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'
```

### References for `case_type` - Wrongful Death

Following set of references should be passed for `Wrongful Death` case type.

* `cause_of_death`
* `charges_filed`
* `relation_with_victim`
* `incident_date_option_b`
* `were_you_injured` - OPTIONAL
* `comments`
* `settled_insurance`
* `signed_retainer`

#### Sample Request - Wrongful Death


```
#!curl

curl --location --request POST 'https://staging.api.accident.com/api/accident-create-lead' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
	"test_mode":"true",
    "type":"lead ",
    "caller_first_name":"tester",
    "caller_last_name":"caller",
    "lead_first_name":"testLeadFromapi06",
    "lead_last_name":"leasss23",
    "lead_email":"testLeadFromapi06@yopmail.com",
    "lead_phone":"6467011444",
    "case_type": "Wrongful Death",
    "zip_code":"10027",
    "fields":[
	    {
		    "ref" : "settled_insurance",
			"answer" : "no"
		},
		{
			"ref" : "signed_retainer",
			"answer" : "no"
		},
        {
            "ref":"cause_of_death",
            "title": "What was the primary cause of the victims death?",
            "answer" : "Vehicle Accident"
        },
         {
            "ref":"charges_filed",
            "title": "Was a police report filed?",
            "answer" : "Yes"
        },
         {
            "ref":"relation_with_victim",
            "title": "What is your relationship to the victim?",
            "answer" : "Parent"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Did the accident happen?",
            "answer" : "Less than 2 years"
        },
        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'
```
### References for `case_type` - SSDI

Following set of references should be passed for `SSDI` case type.

* `applied_ssdi`
* `receiving_benefits`
* `currently_working`
* `stopping_work`
* `primary_injury`
* `last_employed_on`
* `years_employed`
* `challenges_at_work`
* `doctor_treatment`
* `doctor_work_recommendation`
* `age` or `dob` - either one of these
* `years_employed`
* `household_income`
* `received_legal_advise`
* `signed_retainer`
* `comments`

#### Sample Request - SSDI

```
#!curl

curl --location --request POST 'https://api.accident.com/api/accident-create-lead' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
		"arrived_at": "2020-04-13T22:33:47Z",
		"test_mode": "false",
		"lead_first_name": "TestFirst",
		"lead_last_name": "Testlast",
		"lead_email": "test@accident.com",
		"lead_phone": "9999999999",
		"case_type": "SSDI",
		"zip_code": "10022",
		"fields": [
		{
			"ref": "applied_ssdi",
			"title": "Have you already applied for disability benefits?",
> > 			"answer": "No"
		},
		{
			"ref": "receiving_benefits",
			"title": "Are you receiving, or have you been approved for disability benefits?",
			"answer": "No"
		},
		{
			"ref": "currently_working",
			"title": "Are you currently working?",
			"answer": "Yes"
		},
		{
			"ref": "stopping_work",
			"title": "Will you stop working within the next 2 months because of your disability?",
			"answer": "Yes"
		},
		{
			"ref": "comments",
			"title": "What is your disability? Please briefly describe your injuries.",
			"answer": "I was injured and now I can not work"
		},
		{
			"ref": "last_employed_on",
			"title": "When was the last time you worked?",
			"answer": "11/07/2021"
		},
		{
			"ref": "challenges_at_work",
			"title": "Can you stand, sit, walk, or lift objects for long periods of time?",
			"answer": "No"
		},
		{
			"ref": "doctor_treatment",
			"title": "Are you seeing a doctor for these conditions?",
			"answer": "Yes"
		},
		{
			"ref": "doctor_work_recommendation",
			"title": "Has your doctor told you that you can’t work for at least 1 year due to your disability?",
			"answer": "Yes"
		},
		{
			"ref": "dob",
			"title": "What is your date of birth?",
			"answer": "08/19/1970"
		},
		{
			"ref": "age",
			"title": "May I have your age?",
			"answer": "50"
		},
		{
			"ref": "years_employed",
			"title": "Out of the last 10 years, how many years did you work?",
			"answer": "10+ years"
		},
		{
			"ref": "household_income",
			"title": "Do you have any household income?",
			"answer": "Yes"
		},
		{
			"ref": "received_legal_advise",
			"title": "Have you already been helped by an attorney or disability advocate?",
			"answer": "No"
		},
		{
			"ref": "signed_retainer",
			"title": "Did you sign a retainer with a lawyer for your case?",
			"answer": "No"
		}
	]
}
'
```

### References for `case_type` - Other Case Types

Following set of references should be passed for `Slip And Fall` case type.

* `incident_date_option_b`
* `doctor_treatment`
* `have_attorney`
* `were_you_injured` - OPTIONAL
* `primary_injury`
* `comments`
* `settled_insurance`
* `signed_retainer`

#### Sample Request - Other Case Types


```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "CASE_TYPE",
    "zip_code":"10027",
    "fields":[
	    {
		    "ref" : "settled_insurance",
			"answer" : "no"
		},
		{
			"ref" : "signed_retainer",
			"answer" : "no"
		},
        {
            "ref":"have_attorney",
            "title": "",
            "answer" : "No"
        },
         {
            "ref":"primary_injury",
            "title": "Did you sustain any of the following?",
            "answer" : "Headaches"
        },
         {
            "ref":"doctor_treatment",
            "title": "Did you receive any medical treatment?",
            "answer" : " No"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Did the accident happen?",
            "answer" : "Less than 2 years"
        },
        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'
```

### Sample Request with `MM/DD/YYYY` in `incident_date_option_b`

##### Note : Other accepted formats are`MM-DD-YYYY` , `MM/YYYY` or `MM-YYYY`  and `YYYY`

```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "CASE_TYPE",
    "zip_code":"10027",
    "fields":[
	    {
		    "ref" : "settled_insurance",
			"answer" : "no"
		},
		{
			"ref" : "signed_retainer",
			"answer" : "no"
		},
        {
            "ref":"have_attorney",
            "title": "",
            "answer" : "No"
        },
         {
            "ref":"primary_injury",
            "title": "Did you sustain any of the following?",
            "answer" : "Headaches"
        },
         {
            "ref":"doctor_treatment",
            "title": "Did you receive any medical treatment?",
            "answer" : " No"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Did the accident happen?",
            "answer" : "05/20/2021"
        },
        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'
```
## Pipes IVR - Requalification

Explained below is the lifecycle of how the lead is sent across pipes and team alert.

When a Lead is sent by an Affiliate, Accident.com sends it to Alert for requalification. Lead gets created in Alert's system and the customerID is with alert.

1.  If requalification is a success, we attempt auto-assignment / assert a response.
2.  If requalification is not a lead, refused intake, we assert a response.
3.  If requalification was unresponsive, and disputed and sent with the status - "Max Reach - Intake Required"
	- We send such leads to pipes IVR, to make additional call attempts.
	 -   If the lead chooses to speak to Accident.com ( Team Alert's Call Center Reps), we send the call directly to the PIPES DID.
			-   When Alert receives such inbound calls, we believe the system can easily lookup the customerID for this lead
			-   Pipes forwards the lead's caller ID on this call and we've checked this.
			-   For all such requalifications, the scenario can only be #1 or #2 as mentioned above. We can either successfully re-qualify a lead or they reject to speak with us.
			-   There cannot be "Max Reach - Intake Required" for these leads, neither can they be sent as "**unresponsive**" or "**disqualified**".
			- We will need the `disqualify` param for leads with blank fields array ( no requalification data ) , if they have the status **Not a Lead** or **Refused Intake**

### Sample Request with `pipes` param - Fully Qualified
```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "CASE_TYPE",
    "zip_code":"10027",
    "pipes" : true,
    "customer_id" : "vP3rmnZdba69w1lKrk8qOK1EQ4WYeR",
    "fields":[
	    {
		    "ref" : "settled_insurance",
			"answer" : "no"
		},
		{
			"ref" : "signed_retainer",
			"answer" : "no"
		},
        {
            "ref":"have_attorney",
            "title": "",
            "answer" : "No"
        },
         {
            "ref":"primary_injury",
            "title": "Did you sustain any of the following?",
            "answer" : "Headaches"
        },
         {
            "ref":"doctor_treatment",
            "title": "Did you receive any medical treatment?",
            "answer" : " No"
        },
        {
            "ref":"incident_date_option_b",
            "title": "Did the accident happen?",
            "answer" : "05/20/2021"
        },
        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "test description"
        }
    ]
}
'
```

### Sample Request with `pipes` param - Not a Lead / Refused Intake / Wrong Number
```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"lead",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "case_type": "CASE_TYPE",
    "zip_code":"10027",
    "pipes" : true,
    "disqualify" : true,
    "customer_id" : "vP3rmnZdba69w1lKrk8qOK1EQ4WYeR",
    "status" : "Not A Lead", //or "Refused Intake" or "Wrong Number"
    "fields":[
    ]
}
'
```

## Request Object for `attorney` Type

The request body for `type` - `attorney` should comply with these rules.

|Name   	|Required   	|Type   	|Description   	|
|---	|---	|---	|---	|
| type   	| True  	| Text 	| Should be set as `attorney`.  	|
| arrived_at   	| True  	| Timestamp 	| Should be in `UTC`. We use this timestamp to identify the call record on our end, this call record data is used to send a conversion to Google Ads  	|
| converted_at  	| True  |  Timestamp 	| Should be in `UTC`. will be used to update the call record on our end, this call record data is used to send a conversion to Google Ads    	|
| test_mode  	|   false	|  Boolean	|  `Default : false`. Should be set as true if the API call is used to send test data. 	|
| caller_first_name  	|  False 	| Text  	| First name of the caller
| caller_last_name  	|  False 	| Text  	| Last name of the caller
| lead_first_name  	|  False 	| Text  	| First name of the Attorney lead. This can be same as `caller_first_name` if the lead themselves have called in.
| lead_last_name  	|  False 	| Text  	| Last name of the Attorney lead. This can be same as `caller_first_name` if the lead themselves have called in.
| lead_email  	| False  	| Email   	| Email Address of the Attorney Lead
| lead_phone  	| False  	| Phone  	| Phone number of the lead. This can be either in US Domestic format ( Eg: `(541) 754-3010` ) or else in E.164 format ( Eg: `+14155552671` )
| caller_ID  	| True  	| Phone  	| Phone number of the lead. This can be either in US Domestic format ( Eg: `(541) 754-3010` ) or else in E.164 format ( Eg: `+14155552671` )
| zip_code  	| False  	|  US Zip Code	| Location of the Lead 
| fields  	| True  	|  Array 	| Fields array will contain only `comments` ref object.


Sample Request

```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"attorney",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Attorney",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "zip_code":"10027",
    "fields":[

        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "David John, NY PI Attorney, wants to understand the technology better."
        }
    ]
}
'
```

Sample Response

```
#!json

{
    "status": "created",
    "problems" : [
​
    ],
    "script":"This message should be communicated back to the caller.",
    "call_ref":"dvzxwece2ef22eqcqs"
}
```


## Request Object for `inquiry` Type

The request body for `type` - `inquiry` should comply with these rules.

|Name   	|Required   	|Type   	|Description   	|
|---	|---	|---	|---	|
| type   	| True  	| Text 	| Should be set as `inquiry`.  	|
| arrived_at   	| True  	| Timestamp 	| Should be in `UTC`. We use this timestamp to identify the call record on our end, this call record data is used to send a conversion to Google Ads  	|
| converted_at  	| True  |  Timestamp 	| Should be in `UTC`. will be used to update the call record on our end, this call record data is used to send a conversion to Google Ads    	|
| test_mode  	|   false	|  Boolean	|  `Default : false`. Should be set as true if the API call is used to send test data. 	|
| caller_first_name  	|  False 	| Text  	| First name of the caller
| caller_last_name  	|  False 	| Text  	| Last name of the caller
| lead_first_name  	|  False 	| Text  	| First name of the Attorney lead. This can be same as `caller_first_name` if the lead themselves have called in.
| lead_last_name  	|  False 	| Text  	| Last name of the Attorney lead. This can be same as `caller_first_name` if the lead themselves have called in.
| lead_email  	| False  	| Email   	| Email Address of the Attorney Lead
| lead_phone  	| False  	| Phone  	| Phone number of the lead. This can be either in US Domestic format ( Eg: `(541) 754-3010` ) or else in E.164 format ( Eg: `+14155552671` )
| caller_ID  	| True  	| Phone  	| Phone number of the lead. This can be either in US Domestic format ( Eg: `(541) 754-3010` ) or else in E.164 format ( Eg: `+14155552671` )
| zip_code  	| False  	|  US Zip Code	| Location of the Lead 
| fields  	| True  	|  Array 	| Fields array will contain only `comments` ref object.

Sample Request
```
#!curl

curl --location --request POST '<ENDPOINT>' \
--header 'api-key: some_key' \
--header 'api-secret: some_secret' \
--header 'Content-Type: application/json' \
--data-raw '{
    "arrived_at":"2020-1-01T10:26:21Z",
    "converted_at":"2020-1-01T10:26:21Z",
    "test_mode":"true",
    "type":"inquiry",
    "caller_first_name":"Test",
    "caller_last_name":"caller",
    "lead_first_name":"Test",
    "lead_last_name":"Lead",
    "lead_email":"testlead@yopmail.com",
    "lead_phone":"9999999999",
    "zip_code":"10027",
    "fields":[

        {
            "ref":"comments",
            "title": "Describe your case",
            "answer" : "XYZ called back in to ask about their case consultation"
        }
    ]
}
'
```

Sample Response

```
#!json

{
    "status": "created",
    "problems" : [
​
    ],
    "script":"This message should be communicated back to the caller.",
    "call_ref":"dvzxwece2ef22eqcqs"
}
```

## Web Leads

This section is specifically for leads that we post on Alert's API. There can be two scenarios - Alert gets in touch with the lead ( Connected with the Lead ) or the lead doesn't pick up the calls ( Unresponsive Lead ) . 

### Connected with the Lead.

If Team Alert could connect with the lead that we posted, please formulate the request in below format.
Please see that there are three new parameters that you're expected to pass for all such leads.

 - `source` - which site referred this lead.
 - `customer_id` - the hashed identifier that we posted to your API
 - `number_of_calls` - how many calls were made to this lead by Team Alert.

Please check the request sample below.

    curl --location --request POST '<ENDPOINT>' \ --header 
    'Content-Type: application/json' \ --header 
    'api-secret: secret ' \ --header 'api-key: key' \ --data-raw '{ 
    "arrived_at": "2020-03-24 9:28:29",
    "converted_at": "2020-03-24 10:50:29",
    "test_mode": true,
    "lead_first_name": "testAdmediaryFirst",
    "lead_last_name": "testAdmediaryLast",
    "lead_email":"testadmediaryemail@yopmail.com",
    "lead_phone": "+919637245120",
    "case_type":"Personal Injury",
    "zip_code": "82201",
    "customer_id":"dm3a47xp2ARGM9EZlM6DrngoyqY8zV",
    "number_of_calls":"2",
    "caller_ID":"6467011444",
    "source" : "lawsuitwinning.com",
    "type":"lead",
    "fields": [
	    {
		    "ref": "incident_date_option_b",
		    "title": "When did the accident happen?",
		    "answer":"Less than 1 year"
	    },
	    {
		    "ref": "have_attorney",
		    "answer":"Yes"
	    },
	    {
		    "ref": "doctor_treatment",
		    "answer":"No"
	    },
	    {
		    "ref": "primary_injury",
		    "answer":"Broken Bones"
	    },
	    {
		    "ref": "comments",
		    "title": "Additional Details",
		    "answer":"Test lead comments "
	    }
	  ]
	}'


###  Unresponsive Lead

If the lead doesn't pick up the call, the request on our API should contain these three additional fields.

- `unresponsive` - set this field to `true`.
- `number_of_calls` - how many calls were made to this lead by Team Alert.
- `customer_id` - the hashed identifier that we posted to your API

We will disqualify this lead directly as we have not got in touch with the lead and it's unsellable.

    curl --location --request POST '<ENDPOINT>' \ --header 
    'Content-Type: application/json' \ --header 
    'api-secret: secret ' \ --header 'api-key: key' \ --data-raw '{ 
    "arrived_at": "2020-03-24 9:28:29",
    "converted_at": "2020-03-24 10:50:29",
    "test_mode": false,
    "lead_first_name": "testAdmediaryFirst",
    "lead_last_name": "testAdmediaryLast",
    "lead_email":"testadmediaryemail@yopmail.com",
    "lead_phone": "+919637245120",
    "case_type":"Personal Injury",
    "zip_code": "82201",
    "customer_id":"dm3a47xp2ARGM9EZlM6DrngoyqY8zV",
    "number_of_calls":"3",
    "unresponsive" : "true",
    "caller_ID":"6467011444",
    "type":"lead",
    "fields": [
	  ]
	}'

# Location Field Guide

This section discusses the use of location fields and how the API would react to requests. Mentioned below are the list of location fields that Accident's API would accept.

| Name | Required | Type | Description |
|--|--|--|--|
| zip_code  	| False  	|  US Zip Code	| Location of the Lead where the injury occured
| city  	| False  	|  Text	| Location of the Lead where the injury occured. `city` should be in text and used in combination with `state`. 
| state  	| False  	|  Text	| Location of the Lead where the injury occured. `state` should be in text and used in combination with `city`. 


 **NOTE**: 
 - If `zip_code`, `city`, `state` is provided in the request body and the `zip_code` is a valid US zip code, we will use it as a single reference to fetch `city`, `county`, `state` details. 
 - `city` and `state` in the request are only considered to fetch location when the `zip_code` provided is invalid.
 - The flow is as follows
	 -	check if `zip_code` is valid, if yes fetch additional details
	- if not, check for `city` and `state` in the request body.
- If `city` and `state` is provided in the request, we use them in combination to resolve a `zip_code` for the lead. From our location provider APIs we use the first zipcode in the results.
- If only `city` or only `state` is provide, we create the lead on our end but the lead will require a follow up from Team Accident to find an attorney. 
	- All such leads won't be automatically assigned.
- If any of the callers are not able to provide no `zip_code` or no `city`/`state` details, we recommend that Alert should disqualify the lead, setting `disqualify` as true. 

### Responses

If no location fields are populated in the request body, you would see a response like the one mentioned below.

    {  
	    "problems": [  
	        "zip_code, city, ref is missing in the request body"  
	    ],  
	    "lead_score": "",  
	    "call_ref": "",  
	    "attorney": [],  
	    "status": "failed"
    }

If the request body contains `disqualify` field, we will create the lead and the status will be disqualified. We won't be able to assign an attorney for such leads as there're no location details sent. Please make sure that `disqualify` is only used when there is no location.

**IMPORTANT NOTE** : The lead will not be created if there's no location fields in the request and  `disqualify` field is not provided. `disqualify` facilitates a way to create the lead without location information.

    {  
	    "problems": [  
	        "lead has been disqualified"  
	    ],  
	    "lead_score": 40,  
	    "call_ref": "jK6qPQERWpJeMxQl3kB8OmyX7LvDb4",  
	    "attorney": [],  
	    "message": "Lead generated successfully",  
	    "status": "created",  
	    "result": "disqualified"  
    }




 
