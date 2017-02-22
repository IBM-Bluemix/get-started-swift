---

copyright:
  years: 2017
lastupdated: "2017-02-06"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:aside: .aside}


# Getting started with Swift on Bluemix
To get started, we'll take you through a sample *Swift hello world* application that takes only few minutes to deploy.

You'll need a [{{site.data.keyword.Bluemix}} account](https://console.ng.bluemix.net/registration/) and the following tools.  If you do not have the tools follow the links to download them.

## Prerequisites
{: #prereqs}
* [Git ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://git-scm.com/downloads){: new_window}
* [Cloud Foundry CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://github.com/cloudfoundry/cli#downloads){: new_window}
* [Swift compiler ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://swift.org/download/) for your platform.

## 1. Clone the sample app
{: #clone}

Now you're ready to start working with the simple Swift app. Clone the repository and change to the directory to where the sample app is located.
  ```
git clone https://github.com/IBM-Bluemix/get-started-swift
  ```
  {: pre}

  ```
cd get-started-swift
  ```
  {: pre}

  Peruse the files in the *get-started-swift* directory to familiarize yourself with the contents.

## 2. Run the app locally
{: #run_locally}

Once you have installed the Swift compiler and cloned this Git repository, you can now compile and run the application. Go to the root folder of this repository on your system and issue the following command:
```
swift build
```
{: pre}

This command might take a few minutes to run.

Once the application is successfully compiled, you can run the executable that was generated by the Swift compiler:
```
.build/debug/kitura-helloworld
```
{: pre}

You should see an output similar to the following:

```
Server is listening on port: 8080
```
{: screen}

View your app at: http://localhost:8080

## 3. Prepare the app for deployment
{: #prepare}

To deploy to {{site.data.keyword.Bluemix_notm}}, it can be helpful to set up a manifest.yml file. One is provided for you with the sample. Take a moment to look at it.

The manifest.yml includes basic information about your app, such as the name, how much memory to allocate for each instance and the route. In this manifest.yml **random-route: true** generates a random route for your app to prevent your route from colliding with others.  You can replace **random-route: true** with **host: myChosenHostName**, supplying a host name of your choice. [Learn more...](/docs/manageapps/depapps.html#appmanifest)
 ```
 applications:
 - name: GetStartedSwift
   random-route: true
   memory: 256M
 ```
 {: codeblock}

## 4. Deploy the app
{: #deploy}

You can use the Cloud Foundry CLI to deploy apps.

Choose your API endpoint
  ```
cf api <API-endpoint>
  ```
  {: pre}

Replace the *API-endpoint* in the command with an API endpoint from the following list.

|URL                             |Region          |
|:-------------------------------|:---------------|
| https://api.ng.bluemix.net     | US South       |
| https://api.eu-gb.bluemix.net  | United Kingdom |
| https://api.au-syd.bluemix.net | Sydney         |

Login to your {{site.data.keyword.Bluemix_notm}} account

   ```
 cf login
   ```
   {: pre}

From within the *get-started-swift* directory push your app to {{site.data.keyword.Bluemix_notm}}
   ```
 cf push
   ```
   {: pre}

This can take a minute. If there is an error in the deployment process you can use the command `cf logs <Your-App-Name> --recent` to troubleshoot.

When deployment completes you should see a message indicating that your app is running.  View your app at the URL listed in the output of the push command.  You can also issue the `cf apps` command to view your apps status and see the URL.

## 5. Add a database
{: #add_database}

Next, we'll add a NoSQL database to this application and set up the application so that it can run locally and on {{site.data.keyword.Bluemix_notm}}.

1. Log in to {{site.data.keyword.Bluemix_notm}} in your Browser. Browse to the `Dashboard`. Select your application by clicking on its name in the `Name` column.
2. Click on `Connections` then `Connect new`.
3. In the `Data & Analytics` section, select `Cloudant NoSQL DB`
4. Select a pricing plan. Bluemix offers free `Lite` plans for a select collection of its cloud services with enough capacity to get you started
5. Select `Restage` when prompted. {{site.data.keyword.Bluemix_notm}} will restart your application and provide the database credentials to your application using the `VCAP_SERVICES` environment variable. This environment variable is only available to the application when it is running on {{site.data.keyword.Bluemix_notm}}.

Environment variables enable you to separate deployment settings from your source code. For example, instead of hardcoding a database password, you can store this in an environment variable which you reference in your source code. [Learn more...](/docs/manageapps/depapps.html#app_env)
{: tip}

## 6. Use the database
{: #use_database}

We're now going to update your local code to point to this database. Create a json file that will store the credentials for the services the application will use. This file will get used ONLY when the application is running locally. When running in {{site.data.keyword.Bluemix_notm}}, the credentials will be read from the VCAP_SERVICES environment variable.

1. Create a file called `config.json` in the `Sources` directory with the following content (see config.json.example):
 ```
 {
    "vcap":{
       "services":{
          "cloudantNoSQLDB":[
             {
                "credentials":{
                   "host":"<host>",
                   "password":"<password>",
                   "port":443,
                   "url":"<url>",
                   "username":"<username>"
                },
                "label":"cloudantNoSQLDB",
                "name":"cloudantService"
             }
          ]
       }
    }
 }
 ```
 This sample application uses the Swift-cfenv package to interact with Bluemix to parse environment variables. [Learn more...](https://packagecatalog.com/package/IBM-Swift/Swift-cfenv)

 {: pre}

2. Back in the {{site.data.keyword.Bluemix_notm}} UI, select your App -> Connections -> Cloudant -> View Credentials

3. Copy and paste just the credentials to fields in your local config.json file.

4. Run your application locally.
 ```
swift build  
 ```
 {: pre}

 View your app at: http://localhost:8080. Any names you enter into the app will now get added to the database.

 This sample application uses the Kitura-CouchDB package to interact with Cloudant. [Learn more...](https://packagecatalog.com/package/IBM-Swift/Kitura-CouchDB)

5. Make any changes you want and re-deploy to {{site.data.keyword.Bluemix_notm}}!
 ```
cf push
 ```
 {: pre}

 View your app at the URL listed in the output of the push command, for example, *myUrl.mybluemix.net*.


Remember if you don't need your app live, stop it so you don't incur any unexpected charges.
{: tip}
