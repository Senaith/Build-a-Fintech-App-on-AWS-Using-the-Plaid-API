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

