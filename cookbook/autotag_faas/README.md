---
description: Use Cloudinary and Auth0 Webtask to Auto tag with multiple add-ons.
---

# Auto Tagging Images with FaaS

## Setup Cloudinary

First things, first,  sign up for [Cloudinary](https://cloudinary.com/signup?utm_source=Emerge_Americas&utm_medium=Gitbook&utm_campaign=Evangelism&utm_term=Hackathon&utm_content=Signup_EA)

{% hint style="info" %}
TIP: During the sign up process, make sure you create a memerable cloud name for your cloudinary account.   Hacking Teams will want to create one account and name their cloud name after their team.
{% endhint %}

## Sign up for Auth0 Webtask

Next [sign up](https://webtask.io/make) for webtask and follow these steps to create an empty function:

### Install the Cli

The recommended way of using webtask is through the `wt `command line interface. 

#### Install wt command line interface

`wt` is an [open source ](https://github.com/auth0/wt-cli)Node.js CLI to interact with the webtask API.

```text
npm install wt-cli -g
```

#### Initialize wt

`wt` will ask for an e-mail or phone number to send you an activation code.

```text
wt init name@company.com
```

#### Create a simple webtask

Now let's make our first webtask, simply enter wt edit and create empty webtask:

```text
wt edit
```

{% hint style="info" %}
This launches the wt editor and code runner. Test your webtask FaaS by hitting the play icon. The code runner will show the results. You can also copy the task URL from the bottom of the editor and open that in a new browser to view the results.
{% endhint %}

## Using the Cloudinary API: an Auto Tagging code example

**Code Signature:**

```text
cloudinary.v2.uploader.upload(id,options, callback);
```

```javascript
const public_id = "car.jpg";
const options = { categorization: "google_tagging,imagga_tagging,aws_rek_tagging", auto_tagging: 0.6 };
cloudinary.v2.uploader.upload(public_id, options, function(error, result) { 
console.log(result); 
});
```

{% hint style="warning" %}
**Note**:  First param can be a public\_id of an asset on cloudinary or a publically accessable URL. 
{% endhint %}

Once you set it up, create an empty task called auto-tag-example:

### NPM Modules

Click on the wrench Icon, npm modules, search and add the cloudinary SDK. The add the require statement at the top of your task.

```text
    var cloudinary = require('cloudinary');
```

### Secrets

Click on the wrench Icon,  Secrets and enter a key and value for **cloud\_name**, **api\_key**, and **api\_secret - **these should match those values found in the Cloudinary dashboard.

Now, let's move the code example into a new webtask, replace the hello world example with this code:

{% code-tabs %}
{% code-tabs-item title="JavaScript" %}
```javascript
    var cloudinary = require('cloudinary');
function labelImage(context, cb){
  const public_id = context.query.url ||  "http://res.cloudinary.com/de-demo/image/upload/q_auto:best/v1524008113/flooded_road_source.jpg";  
  const options = { categorization: "google_tagging,imagga_tagging,aws_rek_tagging", 
auto_tagging: 0.6 };

  cloudinary.uploader.upload(public_id, options, function(error, result) { 
      if(error){
        cb(error);
      }
      console.log(result); 
        cb(null, result)
  });
}

module.exports = function(context, cb) {
  
   cloudinary.config({
      "cloud_name":  context.secrets.cloud_name,
      "api_key": context.secrets.api_key,
      "api_secret":  context.secrets.api_secret
    });
    
    labelImage(context, cb);
};
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="warning" %}
**Note**:  We created a seperate function passing in the [context](https://webtask.io/docs/context) and callback as params. This allows us to keep the code concise and complete the callback \(cb\) when Cloudinary's api returns either an error or result.
{% endhint %}

{% page-ref page="json-results.md" %}

{% hint style="info" %}
The context object maps the query parameter **url **to the context object which we then set the public\_id variable.

**Usage Example:  **  
https://evangelism.cloudinary.auth0-extend.com/auto-tag-example?url=&lt;url of photo&gt;
{% endhint %}


