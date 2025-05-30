# How to Handle Non-matching Resources
    
This example demonstrates how you can define a sequence to be invoked if the Micro Integrator is unable to find a matching resource definition for a specific API invocation. This sequence generates a response indicating an error when no matching resource definition is found.
    
## Synapse configurations

Following is a sample REST API configuration and Sequence configuration that we can used to implement this scenario. See the instructions on how to [build and run](#build-and-run) this example.

=== "REST API"        
    ```xml
    <api xmlns="http://ws.apache.org/ns/synapse" name="jaxrs" context="/jaxrs">
       <resource methods="GET" uri-template="/customers/{id}">
          <inSequence>
             <call>
                <endpoint key="CustomerService" />
             </call>
             <respond />
          </inSequence>
       </resource>
    </api> 
    ```
=== "Sequence"       
    ```xml
    <sequence xmlns="http://ws.apache.org/ns/synapse" name="_resource_mismatch_handler_">
       <payloadFactory media-type="xml">
          <format>
             <tp:fault xmlns:tp="http://test.com">
                <tp:code>404</tp:code>
                <tp:type>Status report</tp:type>
                <tp:message>Not Found</tp:message>
                <tp:description>The requested resource (/$1) is not available.</tp:description>
             </tp:fault>
          </format>
          <args>
             <arg xmlns:ns="http://org.apache.synapse/xsd" expression="$axis2:REST_URL_POSTFIX"/>
          </args>
       </payloadFactory>
       <property name="RESPONSE" value="true" scope="default"/>
       <property name="NO_ENTITY_BODY" action="remove" scope="axis2"/>
       <property name="HTTP_SC" value="404" scope="axis2"/>
       <header name="To" action="remove"/>
       <send/>
    </sequence>
    ```
=== "Endpoint"
    ```xml
    <endpoint name="CustomerService" xmlns="http://ws.apache.org/ns/synapse">
       <address uri="http://localhost:8290/jaxrs_basic/services/customers/customerservice"/>
    </endpoint>
    ```

## Build and run

Create the artifacts:

{!includes/build-and-run.md!}.
3. Create the [rest API]({{base_path}}/develop/creating-artifacts/creating-an-api) and [mediation sequence]({{base_path}}/develop/creating-artifacts/creating-reusable-sequences) with the configurations given above.
4. [Deploy the artifacts]({{base_path}}/develop/deploy-artifacts) in your Micro Integrator.

Set up the back-end service:

1. Download the [back-end service](https://github.com/wso2-docs/WSO2_EI/blob/master/Back-End-Service/axis2Server.zip).
2. Extract the downloaded zip file.
3. Open a terminal, navigate to the `axis2Server/bin/` directory inside the extracted folder.
4. Execute the following command to start the axis2server with the SimpleStockQuote back-end service:

    === "On MacOS/Linux"   
        ```bash
        sh axis2server.sh
        ```
    === "On Windows"              
        ```bash
        axis2server.bat
        ```

Send an invalid request to the back end as follows:
    
```bash
curl -X GET http://localhost:8290/jaxrs/customers-wrong/123
```
    
You will get the following response:
    
```bash
<tp:fault xmlns:tp="http://test.com">
<tp:code>404</tp:code>
<tp:type>Status report</tp:type>
<tp:message>Not Found</tp:message>
<tp:description>The requested resource (//customers-wrong/123) is not available.</tp:description>
</tp:fault>
```
