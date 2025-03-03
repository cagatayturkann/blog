---
title: "How to Create JWT Tokens for using App Store Connect API?"
# subtitle: "Blog post subtitle :zap:"
summary: Hello everyone! Welcome to the first article about App Store Connect API. In this first article, I am going to give a brief explanation of App Store Connect API and how to create JWT tokens for the API.
date: 2023-11-24
cardimage: /blog/en/how-to-create-jwt-token-using-app-store-connect-api/images/bg.jpg
featureimage: /blog/en/how-to-create-jwt-token-using-app-store-connect-api/images/bg.jpg
featured: true
# caption: Image caption
categories: ["Development", "iOS"]
tags: ["jwt", "app-store", "api"]
translationKey: "app-store-connect-jwt"
authors:
  - Çağatay: /images/author.jpeg
---
\
Hello everyone! Welcome to the first article about App Store Connect API. In this first article, I am going to give a brief explanation of App Store Connect API and how to create JWT tokens for the API.

### What is App Store Connect API?

The App Store Connect API is a REST API that enables the automation of actions you take in App Store Connect. This API empowers developers to seamlessly handle various tasks, from app submission and updates to managing in-app purchases, monitoring app performance and user engagement through comprehensive reports, responding to customer reviews and feedback effectively, and many more.

### Why JWT Token?

Without a token, you won't be able to get the response from the App Store Connect API but mainly API requires JSON Web Token (JWT) for authentication and authorization purposes. In the context of the API, the JWT token serves as a secure and standardized method to verify the identity of the client and to ensure that it has the necessary permissions to access the requested resources.

### How to Create a JWT Token?

Before creating JWT tokens for using the App Store Connect API we need a few steps to setup.

* Generate API Key from App Store Connect GUI
    
* Download the private key in the p8 format
    
* Copy your Issuer ID and API Key ID
    

### Generate API Key
To generate an API key, we have to log in to the App Store Connect web interface with our account and go to the Users and Access tab. On the page click Keys. To be able to see the Keys tab your account has to have permission. We can create an API key for a specific purpose or an admin API key that can access all the App Store Connect API.

{{< figure src="./images/apiKey.png" alt="api key page">}}

Click the plus icon next to the 'active' text type a name for the key choose the roles that can access to key from the modal and click 'Generate'. After that key will be created and listed.

### Download Private Key

Once the API Key is generated we will download the Private Key. The key is usually in the .p8 format. Some somethings are important to keep in mind whilst dealing with the private key.

* The private key can be downloaded only once. Make sure to keep it secure once downloaded.
    
* The private key never expires and is used to work as long as it's valid even if it's compromised so if you think that your key is not safe anymore, revoke it from App Store Connect as soon as possible and get a new key.
    

### Copy Your Issuer and API Key ID

The last step before creating the JTW token is copying your issuer ID and API key ID which you can find on the Users&Access page.

### Create JWT Token

As mentioned earlier, JWT is used to generate the token that has been used by the App Store Connect API. The process of creating a token requires the following steps:

* **IssuerID:** The ID copied from the User&Access page.
    
* **Private Key:** Key downloaded in .p8 format.
    
* **Expiration Time:** 20 min maximum, the token cannot be valid for more than 20 minutes so we have to make sure that, we will create a new token before it expires.
    
* **Audience:** This is constant with API version value usually 'applestoreconnect-v1'
    
* **Algorithm:** The JWT algorithm required to create tokens e.g ES256
    

Once we have all the necessary details, we will be able to create a JWT token using the desired language. I am going to use Node.js for this process.

Run these commands in order:

```plaintext
mkdir appStoreToken
cd appStoreToken
npm init -y
npm i jsonwebtoken
touch index.js
```

Paste this code into your index.js file, replace the necessary information with your information, and save.

```javascript
const fs = require("fs");
const jwt = require("jsonwebtoken");
const privateKey = fs.readFileSync("yourPrivateKey.p8");
const apiKeyId = "Your API Key ID";
const issuerId = "Your Issuer ID";

let now = Math.round(new Date().getTime() / 1000); // Notice the /1000
let nowPlus20 = now + 1199; // 1200 === 20 minutes

let payload = {
  iss: issuerId,
  exp: nowPlus20,
  aud: "appstoreconnect-v1",
};

let signOptions = {
  algorithm: "ES256", 
  header: {
    alg: "ES256",
    kid: apiKeyId,
    typ: "JWT",
  },
};

let token = jwt.sign(payload, privateKey, signOptions);
console.log("@token: ", token);
```

and lastly, run the command below.

```plaintext
node index.js
```

This will return a long token that we can use to access an App Store Connect API, we also need to create another token if we want to continue using API after 20 minutes.

### Conclusion

In this post, you learned how the create a JWT token for the App Store Connect API using Node.js. This is a critical step in authenticating your requests to the API. In future posts, I will try to explore the other ways you can use the App Store Connect API. Thank you for your reading.

[Read this article in Turkish](https://cagatayturkan.com/blog-tr/appstore-connect-api-ile-jwt-token-nasil-olusturulur/)