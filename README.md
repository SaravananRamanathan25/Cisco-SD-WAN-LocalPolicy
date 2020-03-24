# Cisco SD-WAN - LocalPolicy (REST API)
The Cisco SD-WAN Solution (a.k.a Viptela) is a cloud-delivered overlay SD-WAN architecture that facilitates digital and cloud transformation for enterprises and communication service providers (CSP). It significantly reduces WAN costs and time to deploy new services.

Cisco SD-WAN builds a API based architecture that's crucial for enterprises and CSPs to do the configuration automation through their internal tools.

Once below templates are successfully created,  these can be grouped under one device template which can then attached to an edge device

* Feature Templates
* Local Policy Template
* Security Policy Template

# LocalPolicy - POSTMAN Collection
This postman collection provides some example of various APIs dealing with Local Policies.

This POSTMAN environment and collection that can be used to interact with the Cisco SD-WAN powered by Viptela vManage REST API. You can edit the variables in the environment to point to your own vManage instance. The collection contains REST API calls to create list, policy component and Local policy. Please note that the username should have write permission for "Policy Configuration" Feature for the usergroup that it is associated with.

# Steps to execute APIs in the Postman Collection
* Clone or Download the JSON files "CiscoSD-WAN-LocalPolicy.postman_collection.json" and "Cisco-SD-WAN-Environment.postman_environment.json"  
* Import above files to the POSTMAN  
* In the POSTMAN, make sure you set the environment as "Cisco-SD-WAN-Environment" in the top right corner![SelectEnvDetails](https://github.com/SaravananRamanathan25/Cisco-SD-WAN-LocalPolicy/blob/master/Images/SelectEnvDetails-Postman.png)
* Go to Environment options and edit the vmanage, j_username, j_password and port details as per your own vmanage environment. User account should have write permission for the feature "Policy Configuration" Feature.![EditEnvDetails](https://github.com/SaravananRamanathan25/Cisco-SD-WAN-LocalPolicy/blob/master/Images/UpdateEnvDetails_Postman.png)
* First execute the API under "Authentication\Authentication". This will make sure that you have logged into the vManage for that session.
* Next execute the API under "LocalPolicy\1.To Create List\To Create Class Map List".
* Next execute the API under "LocalPolicy\2.To Get List Reference\To Get Class Map List.
  * In the response payload, search with the list name and find its corresponding queue value as shown below ![Get_Class](https://github.com/SaravananRamanathan25/Cisco-SD-WAN-LocalPolicy/blob/master/Images/Get_Class.png)
* Next execute the third API under "LocalPolicy\3.To Create Policy Component\To Create QoS Map Policy Component". If you wish, you can change value for below parameters in the request body 
  * bandwidthPercent
  * bufferPercent
* Next execute the API under "LocalPolicy\4.To Get Definition Id of Policy Component\To Get Definition Id of QoS Map Policy Component".
  * In the response payload, search with the Policy Component name and find its definitionId
* In order to execute the fifth API under "DeviceTemplate\5.To Create Local Policy\To Create Local Policy", replace value for below parameter in the request body 
  * definitionId : templateId fetched from the fourth API
* We have successfully created a Local Policy.

# Local Policy Components' Cardinality with Local Policy
| Local Policy Components | Cardinality for one instance of Local policy | Lists that can be used | Important Aspects |
| ----------------------- | -------------------------------------------- | ---------------------- | ----------------- |
| QoS Map | 0..many | Class Map (Queue) |     |
| Rewrite Rule | 0..many | Class Map (Class)      |      | 
| ACL | 0..many | <p>Class Map (Match/Actions: Class)<br>DataPrefix (Match: Source/Destination IP Prefix)<br>Policer (Actions: Policer)<br>Mirror (Actions: Mirror)</p> | <ul><li>Variables are enabled only for the fields, Source IP Prefix and Destination IP Prefix</li><li>Single ACL Policy can have 0..many ACL Sequence</li><li>Single ACL Sequence can have 1..many Sequence Rules</li></ul> |
| Route Policy | 0..many | <p>AS Path (Match: AS Path)<br>Community (Match: Community)<br>Extended Community (Match: Extended Community)<br>Prefix (Match: Address)</p> | <ul><li>Single Route Policy can have 0..many Sequence Types</li><li>Single Sequence Type can have 1..many Sequence Rules</li></ul> |	

# Local Policy and its related list/component - Delete Operations Analysis

| Level | Analysis |
| ----------------------- | -------------------------------------------- |
| List (3rd level) | 1. Cannot be deleted if it is referenced in any 2nd level policy.<br> 2. Will not be auto deleted if the 2nd level parent policy is deleted. It has to be deleted manually.|
| Individual Local Policy Lists (2nd Level) | 1. Cannot be deleted if it is referenced in any 1st  level policy. <br>2. Will not be auto deleted if the1st level parent policy is deleted. It has to be deleted manually.
| Higher Level Local Policies (1st Level) |1. Deletion of 1st level policy will not delete any 2nd level policies referred under it.<br>2. If we have created new 2nd Level policy while creating new 1st level policy and cancelling the 1st level policy without saving, then the 2nd level policy will also get deleted.<br>3. If we have created new list while creating new 1st level policy and cancelling the 1st level policy without saving,  then it will not delete the list automatically. It has to be deleted manually.
