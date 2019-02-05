---
layout: post
title: Automatically Verifying AWS Cognito User Email Addresses
---

In between my full time job and university I am slowly getting round to adding more functionality to my SaaS product. Previously I wrote about simplifying the sign up process by [automatically confirming Cognito users.](http://tim-thompson.co.uk/auto-confirm-cognito-users)

However, by skipping the default aws confirmation steps users are left with an unverified email address that means they won't be able to perform forgotten password rituals and other similar activities. Once I have a few users and the service is up and running I'd like to add an email verification flow, but it will not be part of my MVP.

For now I needed a way to designate all email addresses that are used to sign up as verified. The process for this is very similar to the process for automatically confirming Cognito users.

To automatically verify the email address of a user, create a new lambda function with an appropriate name and leave the trigger section of the function empty. The Pre sign-up trigger will later be set from the Cognito console. The Pre sign-up trigger activates when a new user signs up and before they are registered into Cognito. The trigger produces an event that looks like this:

```python
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

Within the lambda function it is possible to modify this event and return it so that any additional information set in the lambda function is used for user sign up.

In this case we can check for the existence of an email address and set the `autoVerifyEmail` value accordingly.

```python
def lambda_handler(event, context):
  modifiedEvent = event
  triggerSource = event['triggerSource']
  
  if (triggerSource == "PreSignUp_SignUp"):
    if 'email' in modifiedEvent['request']['userAttributes']:
        modifiedEvent['response']['autoVerifyEmail'] = True
    return modifiedEvent
```

Now with the lambda function created head to the Cognito User Pool settings and set the `Pre sign-up Trigger` to reference the new lambda function.

New users will now be automatically verified. You can check this by registering a new user and checking the user's properties in Cognito.

It is important to note that if you want to confirm and verify a user's email address this must be done within a single lambda function as only one function can be assigned to a trigger.

Example Code:

```python
def lambda_handler(event, context):
  modifiedEvent = event
  triggerSource = event['triggerSource']
  
  if (triggerSource == "PreSignUp_SignUp"):
    modifiedEvent['response']['autoConfirmUser'] = True

    if 'email' in modifiedEvent['request']['userAttributes']:
        modifiedEvent['response']['autoVerifyEmail'] = True

    return modifiedEvent
```