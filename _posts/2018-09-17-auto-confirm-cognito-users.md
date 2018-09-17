---
layout: post
title: Automatically Confirming AWS Cognito Users
---

the SaaS product I am creating uses AWS Cognito for user and identity management. As the product is very niche I wanted to make the sign up process as simple as possible. This meant once the registration form is complete, immediately log the user in and place them on the dashboard to begin onboarding and try to hook them straight away. The default setup for AWS Cognito requires users to verify their email address which confirms their account in the process. Clearly this doesn't fit my desired flow but luckily there is a simple workaround.

AWS Cognito provides a series of triggers that can be linked to AWS Lambda functions. Each trigger executes the associated Lambda function at a point in the sign up/confirmation process, providing event detail that allows for additional verification, automated welcome emails etc.

So create a Lambda function with an appropriate name. Annoyingly, the signup Cognito triggers do not currently appear in the list of triggers that can be configured from the Lambda designer. So leave the trigger section empty.

The Pre sign-up Cognito trigger that we will be using produces an event that looks like this.

```json
{
   'version':'1',
   'region':'eu-west-2',
   'userPoolId':'eu-west-2_xxxxxxxxx',
   'userName':'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx',
   'callerContext':{
      'awsSdkVersion':'aws-sdk-unknown-unknown',
      'clientId':'xxxxxxxxxxxxxxxxxxxxxxxxxx'
   },
   'triggerSource':'PreSignUp_SignUp',
   'request':{
      'userAttributes':{
         'email':'me@tim-thompson.co.uk'
      },
      'validationData':None
   },
   'response':{
      'autoConfirmUser':False,
      'autoVerifyEmail':False,
      'autoVerifyPhone':False
   }
}
```

We can modify this event data and return it as the output of the lambda function and Cognito will then take the modified event data through the sign up process.

The field we are interested in is `autoConfirmUser`. The following code checks that the correct trigger has been called, modifies `autoConfirmUser` to be True and returns the modified event.

```python
def lambda_handler(event, context):
  print("starting auto-confirm handler")
  
  modifiedEvent = event
  
  if (event['triggerSource'] == "PreSignUp_SignUp"):
    modifiedEvent['response']['autoConfirmUser'] = True
    return modifiedEvent
  
  print("error with cognito trigger")
```

You will now be able to sign up users and see that they have been automatically confirmed and can log in straight away.