{:codeblock: .codeblock}
{:shortdesc: .shortdesc}
{:new_window: target="_blank"}

# Getting started with the {{site.data.keyword.autoscaling}} service
{: #autoscaling}

*Last updated: 22 Feburary 2015*

In {{site.data.keyword.Bluemix_notm}}, you can automatically manage your application capacity. Use the  {{site.data.keyword.autoscalingfull}} service to automatically increase or decrease the compute capacity of your application. The number of application instances are adjusted dynamically based on the {{site.data.keyword.autoscaling}} policy that you define. There are several ways to manage service and perform tasks like define {{site.data.keyword.autoscaling}} policy and get {{site.data.keyword.autoscaling}} data,  which will be covered in the following part of this document.
{:shortdesc} 

## Contents
  * [Using the {{site.data.keyword.autoscaling}} service in {{site.data.keyword.Bluemix_notm}}](#as-service)
  * [Manage {{site.data.keyword.autoscaling}} service through RESTful API](#RESTAPI)
  * [Manage {{site.data.keyword.autoscaling}} service through {{site.data.keyword.autoscaling}} CLI](#CLI)
  * [Policy fields for the {{site.data.keyword.autoscaling}} service](#policyfields)
  * [Error messages](#errmsgs)

## Using the {{site.data.keyword.autoscaling}} service in {{site.data.keyword.Bluemix_notm}}
{: #as-service}

To use the {{site.data.keyword.autoscaling}} service, complete the following steps:

1. On {{site.data.keyword.Bluemix_notm}}  Dashboard, click *Add a service or API*, and then select the {{site.data.keyword.autoscaling}} service from the DevOps section in the service catalog. A new window is displayed to present an overview of the {{site.data.keyword.autoscaling}} service.
2. Select the application that you want to bind the {{site.data.keyword.autoscaling}} service to, and click *Create*. <br/>
*Remember:* You can bind only one {{site.data.keyword.autoscaling}} service to an application. If the application is already bound with another {{site.data.keyword.autoscaling}} service, do not select the application in this step. Otherwise, you will get the CWSCV2004E error.<br/>The Restage Application window is displayed.
3. In the Restage Application window, click *Restage* to restage your application before you use the new {{site.data.keyword.autoscaling}} service that you just added. After restaging application is completed, you can start to configure the {{site.data.keyword.autoscaling}} service for your application.
4. To configure the {{site.data.keyword.autoscaling}} for an application, in the {{site.data.keyword.Bluemix_notm}}  user interface, click your application that the {{site.data.keyword.autoscaling}} service is bound to.
5. In the services section on the Dashboard, click the *Auto-Scaling* icon.
6. If you have not done so, define the {{site.data.keyword.autoscaling}} policy for the application by clicking *Create {{site.data.keyword.autoscaling}} Policy*.

Now you can configure the {{site.data.keyword.autoscaling}} policy, see the metric statistics, or view the scaling history for the application.
<dl>
<dt>Policy Configuration</dt>
<dd>Use this section to create or edit the scaling rules to specify the conditions in which certain scaling activities are to be triggered.<ul>
<li> For Liberty for Java™ applications, you can define scaling rules for JVM Heap, Memory, and Throughput.
<li> For Node.js applications, you can define scaling rules for Memory.
<li> For Ruby applications, you can define scaling rules for Memory.</ul>
*Note:* You can define multiple scaling rules for more than one metric type. However, the {{site.data.keyword.autoscaling}} service does not detect conflicts between scaling policies. When you define the scaling policy, you must ensure that multiple scaling rules do not conflict with one another. Otherwise, you might see the total instance number fluctuates because the application scales in 1 minute and scales out the next.<br/><br/>
If the workload of your application changes dramatically during the peak time and the spare time, you can create a scaling schedule to handle the different scaling requirements for different time periods. Use the Minimum Instance Count parameter that is specified in a schedule to define the baseline of the application instance number, while dynamic scaling rules still apply to the schedule to trigger the scaling in and scaling out actions.</dd>
<dt>Metric Statistics</dt>
<dd>Displays the metric statistics for the instances of your application. You can see the average statistics and select specific instance to see its statistic.</dd>
<dt>Scaling History</dt>
<dd>Displays the scaling history of your applications.<ul>
<li> Past Week: Displays the scaling history for the past week.
<li> Past Month: Displays the scaling history for the past month.
<li> Custom Range: You can set the time period.</ul>
*Note:* You can filter the history record by selecting Scaling Status or Scaling In/Out.</dd>
</dl>

## Manage {{site.data.keyword.autoscaling}} service through RESTful API 
{: #RESTAPI}

{{site.data.keyword.autoscaling}} RESTful API provides an alternative way to manage {{site.data.keyword.autoscaling}} service beside the {{site.data.keyword.Bluemix_notm}} UI, and it also supports the scaling data retrieval and analysis. It provides similar functionalities, such as Creating a Policy and Getting the Scaling History, as in the {{site.data.keyword.Bluemix_notm}} UI with API.

There are several key components in the {{site.data.keyword.autoscaling}} RESTful API to manage the {{site.data.keyword.autoscaling}} service: Authentication, EndPoint, app_id and API . After binding the {{site.data.keyword.autoscaling}} service with your application as specified in previous section, you can use them to manage your {{site.data.keyword.autoscaling} service:

1. Authentication: for security purpose, in each request header of {{site.data.keyword.autoscaling}} RESTful API, a proper `AccessToken` obtained through CloudFoundry UAA procedure must be provided in the `Authorization` header to indicate required validation of the request. Failing to comply with this `AccessToken` results in a 401 Unauthorized response. `AccessToken`is a string credential used to access protected resources and represent an authorization issued to the client, see [User Account and Authentication - A note on Tokens](https://github.com/cloudfoundry/uaa/blob/master/docs/UAA-Tokens.md) for an introdution on `AccessToken` and Cloud Foundry UAA procedure. There are two ways you can get this `AccessToken` after you install the Cloud Foundry command line tool and logged into the {{site.data.keyword.Bluemix_notm}}:<ul><li>you can obtain this token through `cf oauth-token` command:
   ```
   > cf oauth-token
   Getting OAuth token...
   OK

     bearer eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiJhNjIzMGE1YS1mNzE3LTQ0YjItOWM3Yi1kNGJkYThhZGU0NjkiLCJzdWIiOiI4OGViNjM2My1hMjkzLTRlZTItYWQ1MS0yOGVkMTZmZjMwNzQiLCJzY29wZSI6WyJjbG91ZF9jb250cm9sbGVyLnJlYWQiLCJwYXNzd29yZC53cml0ZSIsImNsb3VkX2NvbnRyb2xsZXIud3JpdGUiLCJvcGVuaWQiXSwiY2xpZW50X2lkIjoiY2YiLCJjaWQiOiJjZiIsImF6cCI6ImNmIiwiZ3JhbnRfdHlwZSI6InBhc3N3b3JkIiwidXNlcl9pZCI6Ijg4ZWI2MzYzLWEyOTMtNGVlMi1hZDUxLTI4ZWQxNmZmMzA3NCIsIm9yaWdpbiI6InVhYSIsInVzZXJfbmFtZSI6Imtqa29uZ2tqQGNuLmlibS5jb20iLCJlbWFpbCI6Imtqa29uZ2tqQGNuLmlibS5jb20iLCJyZXZfc2lnIjoiZDhlNGY0MDIiLCJpYXQiOjE0NTU2MDc1NDUsImV4cCI6MTQ1NTY1MDc0NSwiaXNzIjoiaHR0cHM6Ly91YWEuc3RhZ2UxLm5nLmJsdWVtaXgubmV0L29hdXRoL3Rva2VuIiwiemlkIjoidWFhIiwiYXVkIjpbImNsb3VkX2NvbnRyb2xsZXIiLCJwYXNzd29yZCIsImNmIiwib3BlbmlkIl19.nrzsEAZmGXNOWjX8r5Xf7U8Hj5-CE1rtHg8_C-jZKSk
   ```
The returned long string begins with “bearer” is the `AccessToken` that can be used in the request of {{site.data.keyword.autoscaling}} RESTful API. If you encounter errors such as “Command not found”, you can update your Cloud Foundry command line tool to a newer version.</li><li>You can also find this `AccessToken` in the home directory. After you logged into {{site.data.keyword.Bluemix_notm}} from the command line interface, a `.cf` folder is created in your home folder, where you can find a JSON file named as `config.json`  that contains a list of the environment information of your current logging, such as organization, space, authentication endpoint, and version. You can locate an entry `AccessToken` in the file as the following : 
    ```
   >cat ~/.cf/config.json
 {
  "ConfigVersion": 3,
  "Target": "https://api.ng.bluemix.net",
  "ApiVersion": "2.40.0",
  "AuthorizationEndpoint": "https://login.ng.bluemix.net/UAALoginServerWAR",
  "LoggregatorEndPoint": "wss://loggregator.ng.bluemix.net:443",
  "DopplerEndPoint": "wss://doppler.ng.bluemix.net:443",
  "UaaEndpoint": "https://uaa.ng.bluemix.net",
  "RoutingApiEndpoint": "https://api.ng.bluemix.net/routing",
  "AccessToken": "bearer eyJhbGciOiJIUzI1NiJ9.eyJqdGkiOiJhNjIzMGE1YS1mNzE3LTQ0YjItOWM3Yi1kNGJkYThhZGU0NjkiLCJzdWIiOiI4OGViNjM2My1hMjkzLTRlZTItYWQ1MS0yOGVkMTZmZjMwNzQiLCJzY29wZSI6WyJjbG91ZF9jb250cm9sbGVyLnJlYWQiLCJwYXNzd29yZC53cml0ZSIsImNsb3VkX2NvbnRyb2xsZXIud3JpdGUiLCJvcGVuaWQiXSwiY2xpZW50X2lkIjoiY2YiLCJjaWQiOiJjZiIsImF6cCI6ImNmIiwiZ3JhbnRfdHlwZSI6InBhc3N3b3JkIiwidXNlcl9pZCI6Ijg4ZWI2MzYzLWEyOTMtNGVlMi1hZDUxLTI4ZWQxNmZmMzA3NCIsIm9yaWdpbiI6InVhYSIsInVzZXJfbmFtZSI6Imtqa29uZ2tqQGNuLmlibS5jb20iLCJlbWFpbCI6Imtqa29uZ2tqQGNuLmlibS5jb20iLCJyZXZfc2lnIjoiZDhlNGY0MDIiLCJpYXQiOjE0NTU2MDc1NDUsImV4cCI6MTQ1NTY1MDc0NSwiaXNzIjoiaHR0cHM6Ly91YWEuc3RhZ2UxLm5nLmJsdWVtaXgubmV0L29hdXRoL3Rva2VuIiwiemlkIjoidWFhIiwiYXVkIjpbImNsb3VkX2NvbnRyb2xsZXIiLCJwYXNzd29yZCIsImNmIiwib3BlbmlkIl19.nrzsEAZmGXNOWjX8r5Xf7U8Hj5-CE1rtHg8_C-jZKSk",
  "SSHOAuthClient": "ssh-proxy",
 .....
 }
   ```
The `AccessToken` in the `config.json` is only valid for a period of time. If you get a 401 Unauthorized response for RESTful API request, you might have to login in again from the command line interface to refresh the file and get the new `AcessToken`. </li></ul>
 
2. Endpoint: {{site.data.keyword.autoscaling}} API server serves as the endpoint of RESTful API. <ul><li>You can obtain the URL of {{site.data.keyword.autoscaling}} API server by checking the `VCAP_SERVICE` environment variable after you bind your application with the {{site.data.keyword.autoscaling}} service. The `api_url` in the `credentials` part of  {{site.data.keyword.autoscaling}} service section in the `VCAP_SERVICE`is the URL of API server that your application is bound with. Check [Binding Credentials](http://docs.cloudfoundry.org/services/binding-credentials.html) for more information about the structure and key fields used in service credentials in Cloud Foundry :
   ```
    {
      "Auto-Scaling": [
      {
         "name": "Auto-Scaling-iw",
         "label": "Auto-Scaling",
         "plan": "free",
         "credentials": {
            "agentUsername": "agent",
            "api_url": "https://ScalingAPI.ng.bluemix.net",
            "service_id": "3f42b7ff-d939-4ff2-9a55-cb09cef9ab9e",
            "app_id": "2287f442-a7f3-4799-8919-d3908c386fa3",
            "url": "https://Scaling3.ng.bluemix.net",
            "agentPassword": "0cddf80b-37e1-4cfd-b648-83a6c8wee69f"
         }
      }
     ]
   }
   ``` 
   
   </li><li>You can also get the Endpoint url through the `cf env APPNAME` command, which is more useful when you want to use a script file to manage {{site.data.keyword.autoscaling}} service through RESTful API :
   ```
   > cf env Hello
   Getting env variables for app Hello in org OE_Runtimes_SVT / space RT_SVT as Alice...
   OK

   System-Provided:
   {
     "VCAP_SERVICES": {
     "Auto-Scaling": [
     {
      "credentials": {
       "agentPassword": "b626b064-7d26-417a-b954-41c6d3cb5200",
       "agentUsername": "agent",
       "api_url": "https://ScalingAPI.ng.bluemix.net",
       "app_id": "aa8d19b6-eceb-4b6e-b034-926a87e98a51",
       "service_id": "8f482f78-58d8-493c-829b-635e4cbfd817",
       "url": "https://Scaling.ng.bluemix.net"
      },
      "label": "Auto-Scaling",
      "name": "ScalingService",
      "plan": "free",
      "tags": [
       "bluemix_extensions",
       "ibm_created",
       "dev_ops"
      ]
     }
   ...
   ```  
   </li></ul>
3. app_id: The `app_id` is used in every RESTful API. You can get the `app_id` from the `VCAP_SERVICES` enviroment variable, or just run the `cf app APPNAME --guid` command:

   ```
   > cf app Hello --guid
   aa8d19b6-eceb-4b6e-b034-926a87e98a51
   > 
   ```
4. API: Currently serveral RESTful APIs are provided with nearly the same funtionality that {{site.data.keyword.Bluemix_notm}} UI can do: Usually you create the policy for your application at first, check scaling data by querying scaling history, and then you can update the existing policy to refine the scaling behaviour or cope with new service requirement. You can also temporarily disable or enable policy and check the policy status.<ul>
<li> Create/Update Policy: Use this API to create or update the policy that guides the scaling action for your application. In this API, you need to supply a policy file in JSON format that contains the key elements in a Policy definition, such as instance count and metric type, like what you do by clicking the *Create {{site.data.keyword.autoscaling}} Policy* on the {{site.data.keyword.Bluemix_notm}} UI.
<li> Delete Policy: Use this API if you do not need the policy anymore.
<li> Enable/Disable Policy: Use this API to temporarily enable or disable the policy settings.
<li> Query Policy status: Use this API to check the policy status, which is `enabled` or `disabled`.
<li> Query Scaling History: Use this API to get the scaling data including the timing, the reason and the instance number of scaling action. You can specify the time range to narrow down the result in this API.</ul>

    You can make RESTful API request by using the RestClient add-on in browser or just through some tool like `curl`. 

    <ul><li>With REST Client Add-on, like those for Firefox or Chrome, you can trigger REST request to {{site.data.keyword.autoscaling}} API server to execute your command. You just supply these add-on with the URL of the RESTful API, method and headers that are required by this RESTful API, and the parameters in the body part.</li> 

    <li>With tools like `curl`, you can manage the {{site.data.keyword.autoscaling}} service within a script file, the Cloud Foundry command listed in the previous section can be helpfule when you write you own script.</li></ul>

5. The swagger page at [Rest API of IBM {{site.data.keyword.autoscaling}} for {{site.data.keyword.Bluemix_notm}}](https://new-console.{DomainName}/apidocs/48){:new_window} provides a more interactive and detailed description of each RESTful API. In the `Model` tab part of each API you will find detailed definition of each field that is used in this API.

## Manage {{site.data.keyword.autoscaling}} service through {{site.data.keyword.autoscaling}} CLI 
{: #CLI}

 {{site.data.keyword.autoscaling}} CLI provide similar functionality as {{site.data.keyword.autoscaling}} RESTful API but in a more friendly way to configure {{site.data.keyword.autoscaling}} service. With {{site.data.keyword.autoscaling}} CLI you do not have to care about the details in {{site.data.keyword.autoscaling}} RESTful API, such as `AccessToken` and URL of API server. All you need is just following the step by step directions that the CLI provides. For more details about how to install and use {{site.data.keyword.autoscaling}} CLI, see [{{site.data.keyword.autoscaling}} CLI](../../cli/plugins/auto-scaling/index.html){:new_window}.

## Policy fields for the {{site.data.keyword.autoscaling}} service
{: #policyfields}

| Field name  | Description |
|-------------|----------------------|
|*Allowable maixmum instance count* |	The maximum number of the application instance that can be started. If the current number of the application instances equals this value, the {{site.data.keyword.autoscaling}} service does not scale out the application any more. Default minimum instance count	The minimum number of the application instance that can be started. If the number of the instances equals this value, the {{site.data.keyword.autoscaling}} service dose not scale in the application any more. |
| *Metric Type*	| 	The supported metric types that can be monitored. For more information, see Table 2. |
| *Scale Out* | 	Specifies the threshold that triggers a scaling out action and how many instances are increased when the scaling out action is triggered. |
| *Scale In* |	Specifies a threshold that triggers a scaling in action and how many instances are decreased when the scaling in action is triggered. |
| *Statistic Window* |	The length of the past period when received metric values are recognized as valid. Metric values are valid only if the time stamps fall within this period. The unit of the Statistic Window parameter is second. |
| *Breach Duration*	| The length of the past period when a scaling action might be triggered. A scaling action is triggered when collected metric values are either above the upper threshold, or below the lower threshold longer than the time specified. The unit of the Breach Duration parameter is second. |
| *Cooldown period for scaling in* | After a scaling in action occurs, other scaling requests are ignored during the length of the period that is specified by the Cooldown period for scaling in parameter. The unit of this parameter is second. |
| *Cooldown period for scaling out*	| After a scaling out action occurs, other scaling requests are ignored during the length of the period that is specified by the Cooldown period for scaling out parameter. The unit of this parameter is second. |
| *Time Zone*	| The time zone where the schedule applies. |
| *Start Time*  |	The start time of a recurring schedule. |
| *End Time*    |	The end time of a recurring schedule.	|
| *Repeat On*	|	The day in a week when a recurring schedule applies. |
| *Minimum Instance Count* |	The minimum number of instances that can be started for the application during the specified time period in the schedule. |
| *Start Date&Time* |	The start date and time of the schedule set up on a specific date. |
| *End Date&Time* |	The end date and time of the schedule set up on a specific date.	|

Table 1. Policy fields in the scaling policy

| Metric name | Description | Supported application type |
|-------------|----------------------| ------------------- |
| *JVM heap* |	The usage percentage of the JVM heap memory.	| Liberty for Java |
| *Memory*   |	The usage percentage of the memory.	|  Liberty for Java<br/> Node.js <br/> Ruby <br/> |
| *Throughput* | The number of the processed requests per second.| Liberty for Java |
| *Response time* |	The response time of the processed requests.	| Liberty for Java |

Table 2. Supported metric names

## Error messages
{: #errmsgs}
This section lists the warning and error messages that are produced by the {{site.data.keyword.autoscaling}} service.
 
### CWSCV2004E Another {{site.data.keyword.autoscaling}} service is already bound to application.
**You can bind only one {{site.data.keyword.autoscaling}} service to one application. This error occurs when you want to bind the {{site.data.keyword.autoscaling}} service to an application that is already bound with another {{site.data.keyword.autoscaling}} service.**

Select another application without any other {{site.data.keyword.autoscaling}} service bound to and bind the target {{site.data.keyword.autoscaling}} service to this application.
If you encounter this error in all the other cases, contact IBM Support.

### CWSCV6001E The API server cannot parse the input JSON strings for API: {0}.
**Problem occurs when parsing input JSON strings.**

Check the Input JSON with API document and correct the error in it.

### CWSCV6002E The API server cannot parse the output JSON strings for API: {0}.
**Problem occurs when parsing input JSON strings.**

Contact the Cloud Administrator for more information.

### CWSCV6003E Input JSON strings format error: {0} in the input JSON for API: {1}.
**Format error found when parsing input JSON strings.**

Check the Input JSON with API document and correct the error in it.

### CWSCV6004E Output JSON strings format error: {0} in the output JSON for API: {1}.
**Format error found when parsing output JSON strings.**

Contact the Cloud Administrator for more information.

### CWSCV6005E Internal server error happened during {0}.
**Internal server error occurs when processing the request.**

Contact the Cloud Administrator for more information.

### CWSCV6006E Calling CloudFoundry APIs failed: {0}
**Error occurred when calling CloudFoundry APIs.**

Contact the Cloud Administrator for more information.

### CWSCV6007E The application is not found: {0}
**The application is not found.**

Check application information for more information.

### CWSCV6008E Following error occurred when retrieving information for application {0}: {1}
**Retrieving application information failed with certain errors.**

Check application information for more information.

### CWSCV6009E Service: {0} for App {1} is not found.
**The service for this application cannot be found.**

Check service binding for this application for more information.

### CWSCV6010E Policy for App {0} is not found
**The policy for this application cannot be found.**

Check application configuration for more information.

### CWSCV6011E Internal Authentication failed during {0}
**Internal Authentication failed.**

Contact the Cloud Administrator for more information.


# rellinks
## samples
* [Tutorial: Make your application elastic on {{site.data.keyword.Bluemix_notm}}](http://www.ibm.com/developerworks/cloud/library/cl-autoscale-app/index.html){:new_window}
* [Cloud Foundry Architecture Labs](https://developer.ibm.com/bluemix/docs/category/cloud-foundry-docs/){:new_window}

## sdk
* [Rest API of IBM {{site.data.keyword.autoscaling}} for {{site.data.keyword.Bluemix_notm}}](https://www.{DomainName}/docs/api/content/api/auto-scaling/index.html){:new_window}

## general
* [{{site.data.keyword.Bluemix_notm}}  Pricing Sheet](https://console.{DomainName}/pricing/){:new_window}
* [{{site.data.keyword.Bluemix_notm}}  Prerequisites](https://developer.ibm.com/bluemix/support/#prereqs){:new_window}
* [{{site.data.keyword.autoscaling}} CLI](../../cli/plugins/auto-scaling/index.html){:new_window}

