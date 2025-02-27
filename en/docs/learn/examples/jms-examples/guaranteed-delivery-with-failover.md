# How to Implement a Guaranteed Delivery with Failover

WSO2 Micro Integrator ensures guaranteed delivery with the failover message store and scheduled failover message forwarding processor. The topics in the following section describe how you can set up guaranteed message delivery with failover configurations.

The following diagram illustrates a scenario where a failover message
store and a scheduled failover message forwarding processor is used
to ensure guaranteed delivery:

![guaranteed delivery]({{base_path}}/assets/img/integrate/tutorials/guaranteed-delivery-failover/guaranteed-delivery.png)

In this scenario, the original message store fails due to, either network
failure, message store crash, or system shutdown for maintenance. The
failover message store is the solution for the original message
store failure. So now the store mediator sends messages to the failover
message store. Then, when the original message store is available again,
the messages sent to the failover message store need to be
forwarded to the original message store. The **scheduled failover message forwarding processor**
is used for this purpose. The scheduled failover message
forwarding processor is almost the same as the scheduled message
forwarding processor. The only difference is that the scheduled message
forwarding processor forwards messages to a defined endpoint, whereas
the scheduled failover message forwarding processor forwards messages to
the original message store where message was supposed to be
temporarily stored.

## Synapse Configurations

Given below are the synapse configurations that are required for mediating the above use case.

See the instructions on how to [build and run](#build-and-run) this example.

- **Message Stores**

    === "Failover message store"
        ```xml
        <messageStore name="failover"/>  
        ```
    === "Message Store"        
        ```xml
        <messageStore  
            class="org.apache.synapse.message.store.impl.jms.JmsStore" name="Original">  
            <parameter name="store.failover.message.store.name">failover</parameter>  
            <parameter name="store.producer.guaranteed.delivery.enable">true</parameter>  
            <parameter name="java.naming.factory.initial">org.apache.activemq.jndi.ActiveMQInitialContextFactory</parameter>  
            <parameter name="java.naming.provider.url">tcp://localhost:61616</parameter>  
            <parameter name="store.jms.JMSSpecVersion">1.1</parameter>  
        </messageStore>
        ```

- **Message Processors**

    === "Scheduled message forwarding processor"
        ```xml
        <messageProcessor name="ForwardMessageProcessor" class="org.apache.synapse.message.processor.impl.forwarder.ScheduledMessageForwardingProcessor" targetEndpoint="SimpleStockQuoteService" messageStore="Original" xmlns="http://ws.apache.org/ns/synapse">
               <parameter name="interval">1000</parameter>
               <parameter name="client.retry.interval">1000</parameter>
               <parameter name="max.delivery.attempts">4</parameter>
               <parameter name="is.active">true</parameter>
               <parameter name="max.delivery.drop">Disabled</parameter>
               <parameter name="member.count">1</parameter>
        </messageProcessor>
        ```
    === "Scheduled failover message forwarding processor"
        ```xml
        <messageProcessor name="FailoverMessageProcessor" class="org.apache.synapse.message.processor.impl.failover.FailoverScheduledMessageForwardingProcessor" messageStore="failover" xmlns="http://ws.apache.org/ns/synapse">
               <parameter name="interval">1000</parameter>
               <parameter name="client.retry.interval">60000</parameter>
               <parameter name="max.delivery.attempts">1000</parameter>
               <parameter name="is.active">true</parameter>
               <parameter name="max.delivery.drop">Disabled</parameter>
               <parameter name="member.count">1</parameter>
               <parameter name="message.target.store.name">Original</parameter>
        </messageProcessor> 
        ```

- **Proxy configurations**

    === "Proxy Service"
        ```xml
        <proxy name="Proxy1" transports="https http" startOnLoad="true" trace="disable" xmlns="http://ws.apache.org/ns/synapse">    
              <target>  
                <inSequence>  
                 <property name="SOAPAction" value="urn:getQuote" scope="transport"/>
                 <property name="messageType" value="text/xml" scope="axis2"/>
                 <property name="FORCE_SC_ACCEPTED" value="true" scope="axis2"/>  
                 <property name="OUT_ONLY" value="true"/>  
                 <log level="full"/>  
                 <store messageStore="Original"/>  
                </inSequence>  
              </target>  
        </proxy>   
        ```
    === "Endpoint"
        ```xml
        <endpoint name="SimpleStockQuoteService" xmlns="http://ws.apache.org/ns/synapse">  
          <address uri="http://127.0.0.1:9000/services/SimpleStockQuoteService"/>  
        </endpoint>
        ```

The synapse configurations used above are as follows:

- **Failover message store**
  
    In this example, an in-memory message store is used to create the failover message store. This step does not involve any special configuration.

- **Original message store**
  
    In this example, a JMS message store is used to create the original message store.  When creating the original message store, you need to enable guaranteed delivery on the producer side. To do this, set the following parameters in the message store configuration:</br>
  `<parameter name="store.failover.message.store.name">failover</parameter>`  
  `<parameter name="store.producer.guaranteed.delivery.enable">true</parameter>`

- **Endpoint for the scheduled message forwarding processor**

    In this example, the `SimpleStockquote` service is used as the back-end service.

- **Scheduled failover message forwarding processor**

    When creating the scheduled failover message forwarding processor, you need to specify the following two mandatory parameters that are important in the failover scenario.
        
    * Source Message Store
    * Target Message Store

    The scheduled failover message forwarding processor sends messages from the failover store to the original store when it is available in the failover scenario. In this configuration, the source message store should be the failover message store and the target message store should be the original message store.

- **Proxy service**

    A proxy service is used to send messages to the original message store via the store mediator.

## Build and run

Create the artifacts:

{!includes/build-and-run.md!}
3. Create the [proxy service]({{base_path}}/develop/creating-artifacts/creating-a-proxy-service), [message stores]({{base_path}}/develop/creating-artifacts/creating-a-message-store), and [message processors]({{base_path}}/develop/creating-artifacts/creating-a-message-processor) with the configurations given above.
4. [Deploy the artifacts]({{base_path}}/develop/deploy-artifacts) in your Micro Integrator.

Set up the broker:

1.  [Configure a broker]({{base_path}}/install-and-setup/setup/transport-configurations/configuring-transports/#configuring-the-jms-transport) with your Micro Integrator instance. Let's use Active MQ for this example.
2.  Start the broker.
3.  Start the Micro Integrator (after starting the broker).

Set up the back-end service:

1. Download the [back-end service](https://github.com/wso2-docs/WSO2_EI/blob/master/Back-End-Service/axis2Server.zip).
2. Extract the downloaded zip file.
3. Open a terminal and navigate to the `axis2Server/bin/` directory inside the extracted folder.
4. Execute the following command to start the axis2server with the SimpleStockQuote back-end service:

    === "On MacOS/Linux"   
          ```bash
          sh axis2server.sh
          ```
    === "On Windows"         
          ```bash
          axis2server.bat
          ```

Invoke the proxy service (`http://localhost:8290/services/Proxy1`) with the following payload:

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:ser="http://services.samples" xmlns:xsd="http://services.samples/xsd">
   <soapenv:Header/>
   <soapenv:Body>
      <ser:getQuote>
         <ser:request>
            <xsd:symbol>IBM</xsd:symbol>
         </ser:request>
      </ser:getQuote>
   </soapenv:Body>
</soapenv:Envelope>
```

You will see the following response in the back-end service console:

```bash
INFO  [wso2/stockquote_service] - Stock quote service invoked.
INFO  [wso2/stockquote_service] - Generating getQuote response for IBM
INFO  [wso2/stockquote_service] - Stock quote generated.
```

To test the failover scenario, shut down the JMS broker (i.e., the original message store) 
and send a few messages to the proxy service.

You will see that the messages are not sent to the backend since the
original message store is not available. You will also see that the
messages are stored in the failover message store.

If you analyze the Console log, you will see the failover
message processor trying to forward messages to the original message
store periodically. Once the original message store is available, you
will see that the scheduled failover message forwarding processor sends
the messages to the original store and that the scheduled message
forwarding processor then forwards the messages to the back-end service.