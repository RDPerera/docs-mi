# Email Connector Reference

The following operations allow you to work with the Email Connector. Click an operation name to see parameter details and samples on how to use it.

??? note "init"
    The init operation configures the connection parameters used to establish a connection to the mail server.
    <table>
        <tr>
            <th>Parameter Name</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
        <tr>
            <td>host</td>
            <td>Host name of the mail server.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>port</td>
            <td>The port number of the mail server.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>name</td>
            <td>Unique name the connection is identified by.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>username</td>
            <td>Username used to connect with the mail server.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>password</td>
            <td>Password to connect with the mail server.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>connectionType</td>
            <td>Email connection type (protocol) that should be used to establish the connection with the server. (IMAP/IMAPS/POP3/POP3S/SMTP/SMTPS).</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>readTimeout</td>
            <td>The socket read timeout value. E.g., 100000.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>connectionTimeout</td>
            <td>The socket connection timeout value. E.g., 100000.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>writeTimeout</td>
            <td>The socket write timeout value. E.g., 100000.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>requireTLS</td>
            <td>Whether the connection should be established using TLS. The default value is <code>false</code>. Therefore, for secured protocols SSL will be used by default.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>checkServerIdentity</td>
            <td>Whether server identity should be checked.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>trustedHosts</td>
            <td>Comma separated string of trust host names.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>sslProtocols</td>
            <td>Comma separated string of SSL protocols.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>cipherSuites</td>
            <td>Comma separated string of Cipher Suites.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>maxActiveConnections</td>
            <td>Maximum number of active connections in the pool. When negative, there is no limit to the number of objects that can be managed by the pool at one time. Default is <code>8</code>.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>maxIdleConnections</td>
            <td>Maximum number of idle connections in the pool. When negative, there is no limit to the number of objects that may be idle at one time. Default is <code>8</code>.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>maxWaitTime</td>
            <td>Specifies the number of milliseconds to wait for a pooled component to become available when the pool is exhausted and the exhaustedAction is set to <code>WHEN_EXHAUSTED_WAIT</code>. If <code>maxWait</code> is negative, it will be blocked indefinitely. Default is <code>-1</code>.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>evictionCheckInterval</td>
            <td>The number of milliseconds between runs of the object evictor. When non-positive, no eviction thread will be launched. The default setting for this parameter is -1</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>minEvictionTime</td>
            <td>The minimum amount of time an object may sit idle in the pool before it is eligible for eviction. When non-positive, no object will be dropped from the pool due to idle time alone. This setting has no effect unless <code>timeBetweenEvictionRunsMillis</code> > 0. The default setting for this parameter is 30 minutes.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>exhaustedAction</td>
            <td>The behavior of the pool when the pool is exhausted. (<code>WHEN_EXHAUSTED_FAIL</code>/<code>WHEN_EXHAUSTED_BLOCK</code>/<code>WHEN_EXHAUSTED_GROW</code>) Default is <code>WHEN_EXHAUSTED_FAIL</code>.</td>
            <td>Optional</td>
        </tr>
    </table>

    **Sample configuration**

    ```xml
    <email.connection>
        <host>127.0.0.1</host>
        <port>465</port>
        <connectionType>SMTPS</connectionType>
        <name>smtpconnection</name>
        <username>user1</username>
        <password>user1</password>
    </email.connection>
    ```
    
    
??? note "list"
    The list operation retrieves emails matching the specified filters.
    <table>
        <tr>
            <th>Parameter Name</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
        <tr>
            <td>deleteAfterRetrieve</td>
            <td>Whether the email should be deleted after retrieving.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>receivedSince</td>
            <td>The date after which to retrieve received emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>receivedUntil</td>
            <td>The date until which to retrieve received emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>sentSince</td>
            <td>The date after which to retrieve sent emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>sentUntil</td>
            <td>The date until which to retrieve sent emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>subjectRegex</td>
            <td>Subject Regex to match with the wanted emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>fromRegex</td>
            <td>From email address to match with the wanted emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>seen</td>
            <td>Whether to retrieve 'seen' or 'not seen' emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>answered</td>
            <td>Whether to retrieve 'answered' or 'unanswered' emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>deleted</td>
            <td>Whether to retrieve 'deleted' or 'not deleted' emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>recent</td>
            <td>Whether to retrieve 'recent' or 'past' emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>offset</td>
            <td>The index from which to retrieve emails.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>limit</td>
            <td>The number of emails to be retrieved.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>folder</td>
            <td>Name of the Mailbox folder to retrieve emails from. Default is `INBOX`.</td>
            <td>Optional</td>
        </tr>
    </table>

    **Sample configuration**
    
    ```xml
    <email.list configKey="imapconnection">
        <subjectRegex>{json-eval($.subjectRegex)}</subjectRegex>
        <seen>{json-eval($.seen)}</seen>
        <answered>{json-eval($.answered)}</answered>
        <deleted>{json-eval($.deleted)}</deleted>
        <recent>{json-eval($.recent)}</recent>
        <offset>{json-eval($.offset)}</offset>
        <limit>{json-eval($.limit)}</limit>
        <folder>{json-eval($.folder)}</folder>
    </email.list>
    ```
    
    **Sample request**
    
    Following is a sample REST/JSON request that can be handled by the list operation.
    ```json
    {
    	"subjectRegex":"This is the subject",
    	"offset":"0",
    	"limit":"2",
    	"folder":"INBOX"
    }
    ```
    
    **Sample response**
    
    The response received would contain the meta data of the email as below. 
    
    ```json
    {
        "emails": {
            "email": [
                {
                    "index": 0,
                    "emailID": "<1623446944.0.152334336343@localhost>",
                    "to": "<your-email>@gmail.com",
                    "from": "<your-email>@gmail.com",
                    "replyTo": "<your-email>@gmail.com",
                    "subject": "Sample email",
                    "attachments": {
                        "index": "0",
                        "name": "contacts.csv",
                        "contentType": "TEXT/CSV"
                    }
                }
            ]
        }
    }
    ```
        
    > **Note:** The index of the email can be used to retrieve the email content and attachment content using below operations.
    
    ??? note "getEmailBody"
    
        > **Note:** 'List' operation MUST be invoked prior to invoking this operation as it will retrieve the email body of the emails retrieved by the 'list' operation.

        The getEmailBody operation retrieves the email content.
        <table>
            <tr>
                <th>Parameter Name</th>
                <th>Description</th>
                <th>Required</th>
            </tr>
            <tr>
                <td>emailIndex</td>
                <td>Index of the email as per above response of which to retrieve the email body and content.</td>
                <td>Yes</td>
            </tr>
        </table>
    
        **Sample configuration**
        
        ```xml
        <email.getEmailBody>
            <emailIndex>0</emailIndex>
        </email.getEmailBody>
        ```
        
        Following properties will be set in the message context containing email data.
        
        * EMAIL_ID: Email ID of the email.
        * TO: Recipients of the email.
        * FROM: Sender of the email.
        * SUBJECT: Subject of the email.
        * CC: CC Recipients of the email.
        * BCC: BCC Recipients of the email.
        * REPLY_TO: Reply to Recipients of the email.
        * HTML_CONTENT: HTML content of the email.
        * TEXT_CONTENT: Text content of the email.


    ??? note "getEmailAttachment"
    
        > **Note:** 'List' operation MUST be invoked prior to invoking this operation as it will retrieve the attachment of the emails retrieved by the 'list' operation.

        The getEmailAttachment operation retrieves the email content.
        <table>
            <tr>
                <th>Parameter Name</th>
                <th>Description</th>
                <th>Required</th>
            </tr>
            <tr>
                <td>emailIndex</td>
                <td>Index of the email as per above response of which to retrieve the email body and content.</td>
                <td>Yes</td>
            </tr>
            <tr>
                <td>attachmentIndex</td>
                <td>Index of the attachment as per above response of which to retrieve the attachment content.</td>
                <td>Yes</td>
            </tr>
        </table>
    
        **Sample configuration**
        
        ```xml
        <email.getEmailAttachment>
            <emailIndex>0</emailIndex>
            <attachmentIndex>0</attachmentIndex>
        </email.getEmailAttachment>
        ```
        
        Following properties will be set in the message context containing attachment data.
        
        * ATTACHMENT_TYPE: Content Type of the attachment.
        * ATTACHMENT_NAME: Name of the attachment.
        
        This operation will set the content of the attachment in the message context according to its content type. 
        
         **Sample response**
        
        ```csv
        <soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"><soapenv:Body><axis2ns3:text xmlns:axis2ns3="http://ws.apache.org/commons/ns/payload">id,firstname,surname,phone,email
        1,John1,Doe,096548763,john1.doe@texasComp.com
        2,Jane2,Doe,091558780,jane2.doe@texasComp.com
        </axis2ns3:text></soapenv:Body></soapenv:Envelope>
        
        ```
        

??? note "send"
    The send operation sends an email to specified recipients with the specified content.
    <table>
        <tr>
            <th>Parameter Name</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
        <tr>
            <td>from</td>
            <td>The 'From' address of the message sender.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>to</td>
            <td>The recipient addresses of 'To' (primary) type.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>personalName</td>
            <td>The personal name of the message sender. This is available from Email Connector version 1.1.2 onwards.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>cc</td>
            <td>The recipient addresses of 'CC' (carbon copy) type.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>bcc</td>
            <td>The recipient addresses of 'BCC' (blind carbon copy) type.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>replyTo</td>
            <td>The email addresses to which to reply to this email.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>subject</td>
            <td>The subject of the email.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>content</td>
            <td>Body of the message in any format.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>contentType</td>
            <td>Content Type of the body text.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>encoding</td>
            <td>The character encoding of the body.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>attachments</td>
            <td>The attachments that are sent along with the email body.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>contentTransferEncoding</td>
            <td>Encoding used to indicate the type of transformation that is used to represent the body in an acceptable manner for transport.</td>
            <td>Optional</td>
        </tr>
        <tr>
            <td>inlineImages</td>
            <td>
            A JSONArray that enables the insertion of inline image details. This can be used to specify the image properties, such as the image URL, size, and alignment, among others. By including inline images in the JSONArray, developers can create more visually appealing and engaging content within their application. Note that this feature is available from Email connector version 1.1.1 onwards.
            <div class="admonition note">
            <p class="admonition-title">Note</p>
            There are 2 methods you can follow to add images, as listed below.<br>
            **1. Providing file path**
            ```json
            {
                "from": "abc@wso2.com",
                "to": "xyz@wso2.com",
                "subject": "Sample email subject",
                "content": "<H1>Image1</H1><img src=\"cid:image1\" alt=\"this is image of image1\"><br/><H1>Image2</H1><img src=\"cid:image2\" alt=\"this is image of image2\">",
                "inlineImages": [
                    {
                        "contentID": "image1",
                        "filePath": "/Users/user/Documents/images/image1.jpeg"
                    },
                    {
                        "contentID": "image2",
                        "filePath": "/Users/user/Documents/images/image2.jpeg"
                    }
                ],
                "contentType": "text/html"
            }   
            ``` 
            **2. Base64Content**
            ```json
            {
                "from": "abc@wso2com",
                "to": "xyz@wso2.com",
                "subject": "Sample email subject",
                "content": "<H1>Image1</H1><img src=\"cid:image1\" alt=\"this is image of a image1\"><br/><H1>Image2</H1><img src=\"cid:image2\" alt=\"this is a image2\">",
                "inlineImages": [
                    {
                        "contentID": "image1",
                        "fileName": "image1.jpeg",
                        "base64Content": "/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAY......"
                    },
                    {
                        "contentID": "image2",
                        "fileName": "image2.jpeg",
                        "base64Content": "/9j/4AAQSkZJRgABAQEBLAEsAAD/4QBbRXhp...."
                    }
                ],
                "contentType": "text/html"
            }
            ```
            </div>  
            </td>
            <td>Optional</td>
        </tr>
    </table>
    
    > NOTE: If there are any custom headers to be added to the email they can be set as Axis2 properties in the context with the prefix "EMAIL-HEADER:" as the property name similar to below.
    ```
    <property name="EMAIL-HEADER:myProperty" value="testValue"/>
    ```

    **Sample configuration**

    ```xml
    <email.send configKey="smtpconnection">
        <from>{json-eval($.from)}</from>
        <to>{json-eval($.to)}</to>
        <subject>{json-eval($.subject)}</subject>
        <content>{json-eval($.content)}</content>
        <attachments>{json-eval($.attachments)}</attachments>
    </email.send>
    ```
    
    **Sample request**

    ```json
    {
    	"from": "user1@gmail.com",
    	"to": "user2@gmail.com",
    	"subject": "This is the subject",
    	"content": "This is the body",
    	"attachments": "/Users/user1/Desktop/contacts.csv"
    }
    ```

    > NOTE: The latest Email connector (v1.0.2 onwards) supports the attachments from JSON request payload. The connector is tested with .txt, .pdf and images (.png and .jpg).
    ```json
    {
    	"attachments": [{"name": "sampleimagefile.png", "content": "iVBORw0KGgoAAAANSUhEUgAAAAUAAAAFCAYAAACNbyblAAAAHElEQVQI12P4//8/w38GIAXDIBKE0DHxgljNBAAO9TXL0Y4OHwAAAABJRU5ErkJggg=="}]
    }
    ```

??? note "delete"
    The delete operation deletes an email.
    <table>
        <tr>
            <th>Parameter Name</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
        <tr>
            <td>emailID</td>
            <td>Email ID of the email to delete.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>folder</td>
            <td>Name of the mailbox folder from which to delete the emails. Default is `INBOX`.</td>
            <td>Optional</td>
        </tr>
    </table>

    **Sample configuration**

    ```xml
    <email.delete configKey="imapconnection">
        <folder>{json-eval($.folder)}</folder>
        <emailID>{json-eval($.emailID)}</emailID>
    </email.delete>
    ```
    
    **Sample request**
    
    ```json
    {
    	"folder":"Inbox",
    	"emailID": "<296045440.2.15945432523040@localhost>"
    }
    ```


??? note "markAsDeleted"
    The markAsDeleted operation marks an email as deleted.
    <table>
        <tr>
            <th>Parameter Name</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
        <tr>
            <td>emailID</td>
            <td>Email ID of the email to mark as deleted.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>folder</td>
            <td>Name of the mailbox folder where the email is. Default is `INBOX`.</td>
            <td>Optional</td>
        </tr>
    </table>

    **Sample configuration**
    
    ```xml
    <email.markAsRead configKey="imapconnection">
        <folder>{json-eval($.folder)}</folder>
        <emailID>{json-eval($.emailID)}</emailID>
    </email.markAsRead>
    ```
    
    **Sample request**
    
    ```json
    {
    	"folder":"Inbox",
    	"emailID": "<296045440.2.15945432523040@localhost>"
    }
    ```


??? note "markAsRead"
    The markAsRead marks an email as read.
    <table>
        <tr>
            <th>Parameter Name</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
        <tr>
            <td>emailID</td>
            <td>Email ID of the email to mark as read.</td>
            <td>Yes</td>
        </tr>
        <tr>
            <td>folder</td>
            <td>Name of the mailbox folder where the email is. Default is `INBOX`.</td>
            <td>Optional</td>
        </tr>
    </table>

    **Sample configuration**
    
    ```xml
    <email.markAsRead configKey="imapconnection">
        <folder>{json-eval($.folder)}</folder>
        <emailID>{json-eval($.emailID)}</emailID>
    </email.markAsRead>
    ```

    **Sample request**
    
    ```json
    {
        "folder":"Inbox",
        "emailID": "<296045440.2.15945432523040@localhost>"
    }
    ```


??? note "expungeFolder"
    The expungeFolder operation permanently deletes the emails marked for deletion.
    <table>
        <tr>
            <th>Parameter Name</th>
            <th>Description</th>
            <th>Required</th>
        </tr>
        <tr>
            <td>folder</td>
            <td>Name of the mailbox folder where the email is. Default is `INBOX`.</td>
            <td>Optional</td>
        </tr>
    </table>

    **Sample configuration**
    
    ```xml
    <email.expungeFolder configKey="imapconnection">
        <folder>{json-eval($.folder)}</folder>
    </email.expungeFolder>
    ```
    
    **Sample request**
    
    ```json
    {
    	"folder":"Inbox"
    }
    ```

### Sample configuration in a scenario

The following is a sample proxy service that illustrates how to connect to the Email connector and use the send operation to send an email. You can use this sample as a template for using other operations in this category.

**Sample Proxy**
```xml
<proxy xmlns="http://ws.apache.org/ns/synapse"
       name="SendEmail"
       transports="https,http"
       statistics="disable"
       trace="disable"
       startOnLoad="true">
   <target>
      <inSequence>
         <email.send configKey="smtpsconnection">
             <from>{json-eval($.from)}</from>
             <to>{json-eval($.to)}</to>
             <subject>{json-eval($.subject)}</subject>
             <content>{json-eval($.content)}</content>
         </email.send>
         <respond/>
      </inSequence>
   </target>
   <description/>
</proxy>         
```

**Note**: For more information on how this works in an actual scenario, see [Email Connector Example]({{base_path}}/reference/connectors/email-connector/email-connector-example/).
