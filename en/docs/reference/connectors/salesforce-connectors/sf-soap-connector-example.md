# Salesforce SOAP Connector Example

The Salesforce SOAP Connector allows you to work with records in Salesforce, a web-based service that allows organizations to manage contact relationship management (CRM) data. You can use the Salesforce connector to create, query, retrieve, update, and delete records in your organization's Salesforce data. The connector uses the [Salesforce SOAP API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_quickstart_intro.htm?search_text=SOAP%20API%20Developer%20Guide) to interact with Salesforce.

## What you'll build

This example explains how to use the Salesforce client to connect with the Salesforce instance and perform the create sObjects operation. Then execute a SOQL query to retrieve the Account Names in all the existing accounts. All operations are handled as SOAP messages. 

* Create an sObjects in Salesforce.

     The user sends the request payload that includes sObjects (any object that can be stored in the Lightning platform database), to create a new Account object in Salesforce. This request is sent to the integration runtime by invoking the Salesforce SOAP connector API. 

* Execute a SOQL query to retrieve the Account Names in all the existing accounts.

     In this example, the [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) is used to search stored Salesforce data for specific information created under `sObjects`. 
  
All two operations are exposed via a `salesforce-soap-API` API. The API with the context `/salesforce` has two resources  

* `/createRecords`: Create a new `Account` object in Salesforce.
* `/queryRecords` : Retrieve the Account Names in all the existing accounts in Salesforce.

<img src="{{base_path}}/assets/img/integrate/connectors/salesforcesoap-connector.png" title="Using Salesforcesoap SOAP Connector" width="800" alt="Using Salesforcesoap SOAP Connector"/>

The user calls the Salesforce SOAP API. It invokes the **createRecords** resource and creates a new account in Salesforce. Then, through the **queryRecords** resource, it displays all the existing account details to the user. 

If you do not want to configure this yourself, you can simply [get the project](#get-the-project) and run it.

## Set up the integration project

Follow the steps in [create integration project]({{base_path}}/develop/create-integration-project/) guide to set up the integration project. 

### Add integration logic

First create an API to configure the integration logic. Select Micro Integrator and click on `+` in APIs to create a REST API. Specify the API name as `salesforce-soap-API` and API context as `/salesforce`.

<img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/adding-an-api.png" title="Adding a Rest API" alt="Adding a Rest API"/>

#### Configure the createRecords resource

Now follow the steps below to add configurations to the resource.

1. Set up the salesforce.create operation.

    1. Navigate into the **Connectors** pane and select the `create` operation under the **Salesforce Connector** section.

        <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforcesoap-create.png" title="Drag and drop init operation" width="80%" alt="Drag and drop init operation"/> 

    2. Click on the `Add new connection` button and replace the `username`, `password`, `loginUrl` and `blocking` fields in the opened form with your values.
            
        - **username**: The username to access the Salesforce account.
        - **password**: The password provided here is a concatenation of the user password and the security token provided by Salesforce.
        - **loginUrl** : The login URL to access the Salesforce account.
        - **blocking** : Indicates whether the connector needs to perform blocking invocations to Salesforce. (Supported in WSO2 ESB 4.9.0 and later.)

        <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/new-connection.png" title="Drag and drop init operation" width="80%" alt="Drag and drop init operation"/> 

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/new-connection-form.png" title="Drag and drop init operation" width="80%" alt="Drag and drop init operation"/> 

    3. Setup the `create` configurations. 

         In this operation we are going to create a `sObjects` in the Salesforce account. An `SObject` represents a specific table in the database that you can discretely query. It describes the individual metadata for the specified object. Please find the `create` operation parameters listed here.
       
         - **sObjectName** : XML representation of the records to add.
         - **allowFieldTruncate** : Whether to truncate strings that exceed the field length (see Common Parameters).
         - **allOrNone** : Whether to rollback changes if an object fails (see Common Parameters).
        
         While invoking the API, the above three parameter values come as a user input.

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforcesoap-drag-and-drop-create.png" title="Drag and drop create operation" width="80%" alt="Drag and drop create operations"/>
    
    3. To get the input values in to the API we can use the [property mediator]({{base_path}}/reference/mediators/property-mediator). Navigate into the **Mediators** pane and select the `Property` mediators .

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforce-api-drag-and-drop-property-mediator.png" title="Add property mediators" width="70%" alt="Add property mediators"/>

         The parameters available for configuring the Property mediator are as follows:
    
         > **Note**: The properties should be added to the pallet before creating the operation.
    
    4. Add the property mediator to capture the sObject `Name` value. In this example we are going to create a new Account object using the POST method.

         - **name** : `Name`
         - **expression** : `//Name/text()`
         - **type** : `STRING`

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforcesoap-api-property-mediator-property1-value1.png" title="Add values to capture sObjectName value" width="80%" alt="Add values to capture sObjectName value"/>

    5. Add the [Payload mediator]({{base_path}}/reference/mediators/payloadfactory-mediator) mediator to capture the sObject content.

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforcesoap-payloadfactory-mediator-property1-value1.png" title="Add values to capture sObject value" width="80%" alt="Add values to capture sObject value"/>
    
    6. Forward the backend response to the API caller.
        
         When you are invoking the created resource, the request of the message is going through the `/createRecords` resource. Finally, it is passed to the [Respond mediator]({{base_path}}/reference/mediators/respond-mediator/). The Respond Mediator stops the processing on the current message and sends the message back to the client as a response.            
        
         Select the **respond mediator**. 

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforcesoap-respond-mediator.png" title="Add Respond mediator" width="800" alt="Add Respond mediator"/> 

#### Configure the queryRecords resource


1. Set up the salesforce.query operation.

    1. Setup the `query` configurations. 
       
         In this operation we are going to retrieve data from an object, use `salesforce.query` and specify the following properties. If you already know the record IDs, you can use retrieve instead.

         - **batchSize** : The number of records to return. If more records are available than the batch size, you can use the queryMore operation to get additional results.
         - **queryString** : The SQL query to use to search for records.

         While invoking the API, the above two parameter values come as a user input.
        
    2. Navigate into the **Connectors** pane and select `query` operation listed under **Salesforce Connector** section.

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforcesoap-drag-and-drop-query.png" title="Drag and drop create operation" width="80%" alt="Drag and drop query operations"/>
        
    3. To get the input values in to the API we can use the [property mediator]({{base_path}}/reference/mediators/property-mediator). Navigate into the **Mediators** pane and select the `Property` mediator.
        
    4. Add the property mediator to capture the sObject `queryString` value. In this example we are going to create a new Account object using the POST method.

         - **name** : `queryString`
         - **expression** : `//queryString/text()`
         - **type** : `STRING`

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforcesoap-api-property-querystring-mediator-property1-value1.png" title="Add values to capture queryString value" width="80%" alt="Add values to capture queryString value"/>

    5. Forward the backend response to the API caller.

         When you are invoking the created resource, the request of the message is going through the `/createRecords` resource. Finally, it is passed to the [Respond mediator]({{base_path}}/reference/mediators/respond-mediator/). The Respond Mediator stops the processing on the current message and sends the message back to the client as a response.

         Select the **respond mediator**.

         <img src="{{base_path}}/assets/img/integrate/connectors/sf-soap/salesforcesoap-respond-mediator.png" title="Add Respond mediator" width="800" alt="Add Respond mediator"/> 

     Now you can switch into the Source view and check the XML configuration files of the created API and sequences. 

    ??? note "create.xml"
        ```
        <?xml version="1.0" encoding="UTF-8"?>
        <api context="/salesforce" name="salesforce-soap-API" xmlns="http://ws.apache.org/ns/synapse">
            <resource methods="POST" url-mapping="/createRecords">
                <inSequence>
                    <property expression="//Name/text()" name="Name" scope="default" type="STRING"/>
                    <salesforce.init>
                        <username>kasunXXX@wso2.com</username>
                        <password>eiconnectortestXXXnO9Nz4Qpiz5Us4N7ijj9zyA</password>
                        <loginUrl>https://login.salesforce.com/services/Soap/u/42.0</loginUrl>
                        <blocking>false</blocking>
                    </salesforce.init>
                    <payloadFactory media-type="xml">
                        <format>
                            <sfdc:sObjects type="Account" xmlns:sfdc="sfdc">
                                <sfdc:sObject>
                                    <sfdc:Name>{$ctx:Name}</sfdc:Name>
                                </sfdc:sObject>
                            </sfdc:sObjects>
                        </format>
                        <args/>
                    </payloadFactory>
                    <salesforce.create>
                        <allOrNone>0</allOrNone>
                        <allowFieldTruncate>0</allowFieldTruncate>
                        <sobjects xmlns:sfdc="sfdc">{//sfdc:sObjects}</sobjects>
                    </salesforce.create>
                    <respond/>
                </inSequence>
                <faultSequence/>
            </resource>
            <resource methods="POST" url-mapping="/queryRecords">
                <inSequence>
                    <property expression="//queryString/text()" name="queryString" scope="default" type="STRING"/>
                    <salesforce.init>
                        <username>kasunXXX@wso2.com</username>
                        <password>eiconnectortestXXXnO9Nz4Qpiz5Us4N7ijj9zyA</password>
                        <loginUrl>https://login.salesforce.com/services/Soap/u/42.0</loginUrl>
                        <blocking>false</blocking>
                    </salesforce.init>
                    <salesforce.query>
                        <batchSize>200</batchSize>
                        <queryString>{$ctx:queryString}</queryString>
                    </salesforce.query>
                    <respond/>
                </inSequence>
                <faultSequence/>
            </resource>
        </api>
        ```
## Get the project

You can download the ZIP file and extract the contents to get the project code.

<a href="{{base_path}}/assets/attachments/connectors/salesforcesoapconnector.zip">
    <img src="{{base_path}}/assets/img/integrate/connectors/download-zip.png" width="200" alt="Download ZIP">
</a>

!!! tip
    You may need to update the value of the access token and make other such changes before deploying and running this project.

## Deployment

In order to deploy and run the project, refer to the [build and run]({{base_path}}/develop/deploy-artifacts/#build-and-run) guide.

## Test

Invoke the API as shown below using the curl command. Curl application can be downloaded from [here](https://curl.haxx.se/download.html).

1. Create a new `Account` object in Salesforce.
 
     **Sample request**

    ```
     curl -v POST -d '<Name>Engineers</Name>' "http://172.17.0.1:8290/salesforce/createRecords" -H "Content-Type:text/xml"    
    ```

     **Expected Response**
    
     ```xml
     <?xml version='1.0' encoding='utf-8'?>
     <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns="urn:partner.soap.sforce.com">
         <soapenv:Header>
             <LimitInfoHeader>
                 <limitInfo>
                     <current>55</current>
                     <limit>15000</limit>
                     <type>API REQUESTS</type>
                 </limitInfo>
             </LimitInfoHeader>
         </soapenv:Header>
         <soapenv:Body>
             <createResponse>
                 <result>
                     <id>0012x00000Am4kXAAR</id>
                     <success>true</success>
                 </result>
             </createResponse>
         </soapenv:Body>
     </soapenv:Envelope>
     ```

2. Retrieve the Account Names in all the existing accounts in Salesforce. 

   **Sample request**
    
   ```
    curl -v POST -d '<queryString>select id,name from Account</queryString>' "http://172.17.0.1:8290/salesforce/queryRecords" -H "Content-Type:text/xml"    
   ```    
   **Expected Response**    

    You will get a set of account names and the respective IDs as the output.

## What's next

* To customize this example for your own scenario, see [Salesforce SOAP Connector Configuration]({{base_path}}/reference/connectors/salesforce-connectors/sf-soap-connector-config/) documentation for all operation details of the connector.
