# Build-a-Fintech-App-on-AWS-Using-the-Plaid-API
In this project, I will show you how to build and deploy a basic fintech app on Amazon Web Services (AWS) in under an hour by using the Plaid Link API. This app allows users to sign up, log in, select their bank from a list, connect to that bank, and display the latest transactions.

## Introduction
Open Finance initiatives have been gaining momentum across the world. These initiatives require that banks provide access to customer data through a common, open API for third-party applications, which are referred to as fintech apps.

The fintech app providers are generally not banks, but they offer users a variety of convenient payment and finance features on smartphone apps. These apps enhance the customer experience and foster greater choice and innovation. Users simply need to link the app to their bank and brokerage accounts, and grant the necessary permissions.

Fintech apps offer users benefits such as:

- Viewing balances across multiple bank accounts.
- Initiating payments to friends.
- Applying for loans without gathering and scanning bank and income statements.
- Paying for things online using a “Buy Now Pay Later” plan.
- Showing monthly income and expense categories to help set budgets.
- Displaying overall investment performance across multiple brokerage accounts.
- Buying crypto-assets.

## What is Plaid?

![plaid](https://user-images.githubusercontent.com/91766546/161947363-878a5759-9dd4-4962-9d31-fcece095a5b5.png)

[Plaid](https://plaid.com/) is a San Francisco-based financial services company and [AWS Partner](https://partners.amazonaws.com/partners/0010h00001cBFNCAA4/Plaid) that helps fintech providers connect users safely to their bank accounts.

The [Plaid Link](https://plaid.com/docs/link/) acts as a secure proxy between a fintech app and a bank. With Plaid, application developers no longer need to worry about implementing scores of different ways to access data in myriad financial institutions.

Plaid is currently able to connect to more than 12,000 banks and financial institutions throughout the world. It provides a single API to connect to them. Currently, about 5,500 fintech apps use Plaid’s API to enable their users to access their bank accounts.
![1](https://user-images.githubusercontent.com/91766546/161949673-561e847a-8ecb-4f3e-8344-37d052916c12.png)

## Project Plan

In this project, I will build a demo fintech app on AWS using the [AWS Amplify](https://aws.amazon.com/amplify/) framework and Plaid Link. AWS Amplify helps us quickly build a serverless web app with a React frontend, user sign-up and sign-in using [Amazon Cognito](https://aws.amazon.com/cognito/), an [Amazon API Gateway](https://aws.amazon.com/api-gateway/)-based REST API, and an [Amazon DynamoDB](https://aws.amazon.com/dynamodb/) database for storage.
![Plaid-Fintech-API-1](https://user-images.githubusercontent.com/91766546/161953215-0c46c46e-4142-431d-8d7c-71830c4e655e.png)

AWS Amplify generates the code for signing up and authenticating users who are then stored in a [Cognito user pool](https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-user-identity-pools.html). It also helps create a REST API invoked by the React frontend and implemented by an [AWS Lambda](https://aws.amazon.com/lambda/) function behind Amazon API Gateway. The backend Lambda function sets up the Plaid Link which allows the end user to interact with a selected bank.

AWS Amplify also helps store the Plaid API key securely in [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/) so that it never needs to appear in the code or in a file. Plaid access tokens (described in the next section) are stored in the DynamoDB database.

This is a completely scalable and secure architecture which does not require the user to manage any server instances.

## How Plaid Link Works

To build an app using Plaid Link, you first need to go to [Plaid.com](https://plaid.com/), click on the **Get API Keys** button, and create an account. You can create a free sandbox account to start.
![2](https://user-images.githubusercontent.com/91766546/161954882-e5e77370-9883-44a7-a257-1686d5023b20.png)
![3](https://user-images.githubusercontent.com/91766546/161955364-3cf72011-a807-4fb8-b221-c60f59be0bc4.png)

You can then log into your dashboard and find your sandbox API key under the menu for **[Team Settings – Keys](https://dashboard.plaid.com/team/keys)**.

The following diagram shows what our demo Web app needs to implement.
![Plaid-Fintech-API-2](https://user-images.githubusercontent.com/91766546/161954007-68c58a90-cec9-4d7a-b3ef-392cffe6bd50.png)

All API calls are made through a Plaid client object. The message flow is as follows:

1. The app first creates a Plaid client object by passing in the Plaid API key and Plaid client ID. It then calls the client’s **createLinkToken** method to obtain a temporary link token.
2. When the user selects a bank, the app uses the link token to open a Plaid Link to the bank and obtain a temporary public token.
3. The app then calls the client object’s **exchangePublicToken** method to exchange the public token for a permanent access token and an item ID that represents the bank.
4. The app stores the access token in DynamoDB for subsequent requests pertaining to that item. For example, the app can pass the access token to the client object’s **getTransactions** method to obtain a list of transactions within a specific date range.

## Building and Deploying the App
### Prerequisites

- Make sure you have created a sandbox account at Plaid as described above, and obtained your API keys.
- You also need to [install AWS Amplify](https://docs.amplify.aws/cli/start/install/).
![5](https://user-images.githubusercontent.com/91766546/161970758-0f821d71-b566-497e-8a2a-31b71040a4dd.png)
- If you have not already done so, [create a default AWS configuration](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config) profile by running the **aws configure** command.
![4](https://user-images.githubusercontent.com/91766546/161970698-5ec7a9bc-b043-483e-994f-9aef515ecc9b.png)

### Building the App
Clone the repo and run *npm install*:

![2022-04-11_16-58](https://user-images.githubusercontent.com/91766546/162852046-8ae572cf-217b-48a4-8c05-64fd1ed9dd17.png)
![2022-04-11_17-00](https://user-images.githubusercontent.com/91766546/162852164-787e4000-c213-414c-b2a3-4440021f71a3.png)

Initialize a new Amplify project. Hit Return to accept the defaults.
![2022-04-11_18-08](https://user-images.githubusercontent.com/91766546/162858343-c9347fa8-9b1f-43aa-a523-409d480bfad6.png)
![2022-04-11_18-09](https://user-images.githubusercontent.com/91766546/162858441-21148cb9-a5e1-4179-acc9-4d2866d491ef.png)

Add authentication:
![2022-04-11_20-03](https://user-images.githubusercontent.com/91766546/162871008-df9d10d1-3f0d-4409-a7e0-6a7ffa835c98.png)

Add the API:
![2022-04-12_01-46](https://user-images.githubusercontent.com/91766546/162920294-1451fa04-3644-42dc-a162-3675c9d749ba.png)
![2022-04-12_01-47](https://user-images.githubusercontent.com/91766546/162920613-6090f755-e199-47c7-a1f4-c50c816b1157.png)
The Plaid client ID can be located on the Plaid website. Navigate to your account dashboard and click Team Settings on the menu bar. In the drop down menu, click Keys.
![2022-04-12_02-34](https://user-images.githubusercontent.com/91766546/162929805-db246bf7-4378-4b6a-9188-86e2be4b2bf4.png)
On the next page, locate the CLIENT_ID.
![2022-04-12_02-35](https://user-images.githubusercontent.com/91766546/162930071-431c5e26-9cf1-43b0-bb09-3f789095135b.png)

Copy the Lambda source file, install dependencies, and push:
![2022-04-12_01-48](https://user-images.githubusercontent.com/91766546/162920823-ef754738-c4f1-4496-b1de-881ea8a53eb7.png)
Now push:
![2022-04-12_01-50](https://user-images.githubusercontent.com/91766546/162921153-54509b6e-2351-4c37-a004-579af456bd7d.png)
![2022-04-12_01-51](https://user-images.githubusercontent.com/91766546/162921371-efebe06a-ce57-473d-be31-9ace0a9ea1b0.png)

Add database
![2022-04-12_01-53](https://user-images.githubusercontent.com/91766546/162921621-508e421c-23da-42d1-83e6-76dad47e50f8.png)

Update the Lambda function to add permissions for the database:
![2022-04-12_01-55](https://user-images.githubusercontent.com/91766546/162922067-bab67492-1b83-4f5b-afa7-d86938d2fc7f.png)

### Deploying the App
Add hosting for the app:
![2022-04-12_01-57](https://user-images.githubusercontent.com/91766546/162922436-eb3137e2-d6fa-482c-8deb-d4dfff2bc652.png)

Deploy the app:
![2022-04-12_01-58](https://user-images.githubusercontent.com/91766546/162922713-eb19773a-49c3-4642-ab52-94bbe2477b47.png)
![2022-04-12_02-00](https://user-images.githubusercontent.com/91766546/162922945-0d574543-3759-46a5-8f84-aaafe340e3ee.png)

### Testing the App

Go to the URL displayed by the *amplify publish* command, and sign up as a new user. After logging in, select a bank from the list displayed.
If you are using the sandbox environment, use the credentials **user_good / pass_good** to access the bank and display the transactions.

![Screenshot from 2022-04-11 16-54-35](https://user-images.githubusercontent.com/91766546/162923748-921e09c7-b707-4126-84b0-3be50aab131c.png)

Select Continue with plaid and then select Continue.

![111](https://user-images.githubusercontent.com/91766546/162923944-73576ce7-57b8-401b-9379-03d6f80bafd7.png)
![22](https://user-images.githubusercontent.com/91766546/162924210-92a870e3-a7ca-4cf4-94f7-217ebf208ba4.png)

After logging in, select a bank from the list displayed.
![33](https://user-images.githubusercontent.com/91766546/162924291-ffef6208-5fde-4f37-840d-2e3208dc8281.png)
If you are using the sandbox environment, use the credentials user_good and password pass_good to access the bank and display the transactions.
![44](https://user-images.githubusercontent.com/91766546/162924390-f5a0f5e0-aa81-4d8b-b53e-6e4eb3467d3d.png)
![55](https://user-images.githubusercontent.com/91766546/162924478-225d5012-2a4a-4917-8404-29bf419c8b99.png)

## Conclusion

The walkthrough in this project demonstrates how easy it is to use AWS Amplify to create a secure, scalable, and completely serverless fintech app on AWS that allows users to sign up, select from among the 10,000 banks that Plaid Link connects to, and obtain the transaction history for a particular account.

From here, you can add features such as making payments to friends or vendors, displaying balances across multiple accounts, sending low balance alerts and helping set a budget.

### Clean Up and delete the resources used

Navigate to your AWS account. Search and select AWS Amplify.
![2022-04-12_02-21](https://user-images.githubusercontent.com/91766546/162926987-9cef8a10-d5db-44f2-b266-ef149ceb0c44.png)

Click on the awsplaiddemoapp app.
![66](https://user-images.githubusercontent.com/91766546/162927278-6c50196a-cecf-4f3d-ac28-6ebc5e74a86d.png)
![77](https://user-images.githubusercontent.com/91766546/162927929-76c6d85c-aeab-4602-ac18-e3cb6c1d82c3.png)

When prompted, confirm that you want to delete the app:
![2022-04-12_02-29](https://user-images.githubusercontent.com/91766546/162928624-2b41cec4-431f-4cf9-b7a6-b2e1c4d719a8.png)
It will take a few minutes for the app to be completely deleted.

## Congratulations!!! You have successfully completed this project. 
