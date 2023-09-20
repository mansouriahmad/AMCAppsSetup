# 1.	ServiceNow CRM configuration

- Go To ServiceNow store and install [DaVinci App](https://store.servicenow.com/sn_appstore_store.do#!/store/application/7acb241fdb3df300bc276cf3ca9619ad/1.0.5?referer=%2Fstore%2Fsearch%3Fq%3DDaVinci
) (Its dependencies are OpenFrame and CustomerService plugins). 


- After the completion of the ServiceNow DaVinci app installation it will create records in Openframe, Application Registry, CORS Rules.


## 1.1.	Making the integration compatible with platform UI of ServiceNow: 
- Type `sys_properties.list`in search bar on left on ServiceNow and hit Enter.
- From the displayed page search for the System Property named: `glide.ui.concourse.onmessage_enforce_same_origin_whitelist`
- Once you click on that property add `https://agent.contactcanvas.com` to the Value of the property. (This is a value list separated by comma, space or new line)


# 2. ServiceNow app configuration in Davinci

## 2.1. InstanceURL:
- Select the configuration tab in DaVinci Creators Studio for the ServiceNow app.
- Set it to agent’s current ServiceNow instance. This will allow the app to utilize the ServiceNow OpenFrame API.

## 2.2. ClientID:
- On your ServiceNow instance, go to the application registry.
- Copy the clientId of the application registry with the name DaVinci.

## 2.3. ClientSecret:
- On your ServiceNow instance, go to the application registry.
- Copy the client secret of the application registry and paste it here.

## 2.4. PhoneNumberFormat:
PhoneNumberFormat can be set to allow users to convert phone formats coming from CTI to phone number formats specified by the CRM they are using.

### 2.4.1. Example 1: 
**Number coming from CTI**: 12 345 6789  
**Desired format to be passed to CRM**: +41 12 345 6789  
**Key of Configuration**: xx xxx xxxx  
**Value of Configuration**: +41 xx xxx xxxx  

**Note:** Use lowercase ‘x’ to denote numerical characters that you want to have arranged into the new format (indicated by that key’s value).

### 2.4.2. Example 2:
**Number coming from CTI**: +1 (425) 555-1212  
**Desired format to be passed to CRM**: (425) 555-1212  
**Key of Configuration**: ?? (xxx) xxx-xxxx  
**Value of Configuration**: (xxx) xxx-xxxx  

**Note**: To denote characters that you wish to have removed from the phone number before the number is passed to CRM, denote them with a ? in the position you will encounter them.

## 2.5.	ClickToDialPhoneReformatMap:
Similar to PhoneNumberFormat, you can use this configuration to convert a phone format saved in the CRM to a phone format required by your Channel.

## 2.6.	QuickComments:
This configuration allows agents to select frequently used comments to be displayed in the activity section of the softphone. Agents then can click these comment buttons and have the text quickly added to their call notes.

## 2.7.	Search Layout

### 2.7.1. ScreenpopOnInternal:
This configuration is Boolean. This configuration determines the behavior of ScreenPop on internal calls. When set to true, ScreenPop will be performed for internal calls, while setting it to false will disable ScreenPop for internal calls resulting in no activity being saved within ServiceNow for those calls.

### 2.7.2. screenpopOnOutbound:
This configuration is Boolean. This configuration determines the behavior of ScreenPop on outbound calls. When set to true, ScreenPop will be performed for outbound calls, while setting it to false will disable ScreenPop for outbound calls.

### 2.7.3. ScreenpopOnAlertingOrInitiated:
This configuration is Boolean. This configuration determines the behavior of ScreenPop on inbound/outbound calls. When set to true, ScreenPop will be performed on alerting(/ringing) and an activity will be generated inside ServiceNow; while setting it to false will disable ScreenPop for inbound calls when on ringing state resulting in performing ScreenPop for other states (usually connected).

### 2.7.4. InteractionSearchFields:
The purpose of this field is to search for entities within the CRM system. Entries should be added and separated by semicolons. If an attribute value is empty or not provided, no search will be performed for that attribute. The search process follows the configured order of search entries, and it terminates as soon as a matching result is found.

An entity’s format is as 

```
<InteractionAttributeName>,<ServiceNow Entity name>,<ServiceNow property name>
```
For example, if you set this configuration as:
Email,csm_consumer,email;CutomField,csm_consumer,customFieldInsideCsmConsumer

The app follows a specific workflow: Firstly, it queries the csm_consumer table to find records where the email property matches the Email value received from the channel. If the search result is empty, it further queries the csm_consumer table to find records where the customFieldInsideCsmConsumer property matches the CustomField value received from the channel.

### 2.7.5. Entities:
This configuration is to define ServiceNow entities. To do this:

- Under Search Layout, expand Entities section. When you click on each entity you will have DefaultSearchFields and APIName. Default Search Fields are the fields against which standard ScreenPop happens. APIName corresponds to the API Name of the entity in the CRM.

Under each entity, there will be DisplayFields child node where the fields related to the entity will be displayed on the softphone. Here Name corresponds to the string to be displayed on CRM app and Value represents the name of the property inside that entity. For example, if you configure a an entry with name=Phone and value=primary_phone, if an entity is a match and it has property called primary_phone as 8045551234, inside CRM "Phone 8045551234" will be shown.
Notes:
•	Multiple entries are configurable.
•	In case of multi-match, only the first entry here will be shown on the softphone.

MatchTypes:

NoMatch
In case of a no-match, where no records are found in the CRM, the DaVinci App for ServiceNow can ScreenPop to a new "entity" form to be filled out by the agent (Ex. new Contact, new Account, etc.):
•	Under MatchType make new node as NoMatch (if not present there) and add a new variable called screenPopData with type of string and put the name of desired entity inside the value i.e., csm_consumer.

autoPopulateScreenPop:
In case of NoMatch, when ScreenPop is performed to a new entity, you can use this configuration to populate call data to the entity:
<Key of CAD From Channel App>|<ServiceNow Entity Name>|<ServiceNow Field Where the CAD should be passed>;....(Multiple entries allowed separated by ;).
An example of the config is as follows:
Phone|csm_consumer|business_phone;...(Add additional rules)
For this config, in case of NoMatch, if the current interaction has a Call Attached Data with key "Phone", then the value of that CAD will be passed to the field "business_phone" on the csm_consumer form. 

MultiMatch
In case of a multi-match, it is possible to define a configuration which when enabled, all the found entities will be opened inside ServiceNow instance. Otherwise, those entities will not be opened.
•	Under MatchType make new node as MultiMatch (if not present there) and add a new variable called OpenEntitiesOnMultimatch with type of Boolean. Assign a value based on your requirements.

QuickCreate:
This configuration can be used to screenPop new entities inside ServiceNow instance.

ShowQuickCreateUI
This configuration is Boolean. When set to true, the QuickCreateActivity will be displayed; otherwise, it will be hidden.

QuickCreateKeyList
This configuration dictates which entities are available to pop to a new form via the Quick Create section where the format is as follows:
Key Value
where Key = <ServiceNow Table to Create Entity> and value = <Name to Display for Entity>|<Image to Show in Quick Create Section>.
<ServiceNow Table to Create Entity> - The API name of the entity to be created.
<Name to Display for Entity> - When the agent hovers over the image to quick-create a particular entity, this part of the configuration controls what text will be shown to the agent.
<Image to Show in Quick Create Section> - This is a link to an image representing that entity. It will be shown in the quick create section for the agent to click on. 

QuickCreateCAD
Similar to autoPopulateScreenPop, QuickCreateCAD is a configuration that allows agents to automatically populate a quick create form with call attached data (CAD), if present. The format of the configuration is as follows:
<Key of CAD From Channel App>|<ServiceNow Quick Create Entity API name>|<ServiceNow Field Where the CAD should be passed>;....(Multiple entries allowed separated by ;).

1.9	CallActivity:

ShowActivity:
This configuration is Boolean. When set to true, the activity menu will be displayed; otherwise, it will be hidden.

AutoCloseInteractionRecord:
This is a Boolean configuration. If true, the app will set the interaction record state to Close Completed after delayActivitySaveInSecond (below). Otherwise, it does not set the interaction state.

ActivityVerificationFrequencyInSecond:
This configuration is to control the frequency in which the application checks for expired Activities to remove them.
 
delayActivitySaveInSecond:
This configuration is to control the delay on saving an activity when a call ends. By default, it is 0 seconds.
 
ActivityFields:
It can be used to save CAD values inside the activities. For example, if an interaction has value named IW_CaseUid which you need to save inside your CRM activity, add an entry which maps IW_CaseUid to the name of the property field you defined inside CRM.
Important:
To link activities with various entities in your ServiceNow instance, add records for each entity type in the following format:

key: AMC_RelatedTo_<entity APIName>
value: Entity's property name in the interaction table.

For example, add one records with key=AMC_RelatedTo_csm_consumer, value: consumer.

1.10	PhoneFormat:
Utilize the following configuration to transform incoming phone numbers from CTI and perform entity searches inside CRM based on the transformed formats.
The configuration format resembles PhoneNumberFormat, and for illustration purposes, let's consider an incoming phone call in the format: 8044567890. The goal is to search for this phone number and its variations in CRM, including (804) 456-7890 and +18044567890. To accomplish this: 
•	Add a new entry inside PhoneFormat with the type set as Array.
•	Assign the key as xxxxxxxxxx, and
•	Populate the array with the following two entries: (xxx) xxx-xxxx and +1xxxxxxxxxx."
By following this configuration, you'll be able to efficiently search and match phone numbers in different formats in your CRM system.

3.	Versioning

URL - String
This URL determines which version of AMC's Five9 app is loaded.


•	The URL is of the form: https://servicenow.contactcanvas.com/?x-ms-routing-name=Vxxxx
•	For example, https://servicenow.contactcanvas.com/?x-ms-routing-name=V7001 loads app version 7001. At the bottom of this setup document, you can find the release notes for all versions.
•	If you wish to always stay on the latest release, you may use this URL: https://servicenow.contactcanvas.com
•	It is important to note that if the given version is an invalid one, the latest version is loaded and is equivalent to having the URL set as https://servicenow.contactcanvas.com

1.11	Release Notes

1) V7001 - 12/10/2021
•	URL: https://servicenow.contactcanvas.com?x-ms-routing-name=V7001
•	Updated to use .NET 6.0.
2) V7002 - 06/02/2023
•	URL: https://servicenow.contactcanvas.com?x-ms-routing-name=V7002
•	Enhanced multi scenario workflow
•	Added Phone number format.
3) V7003 - 06/09/2023
•	URL: https://servicenow.contactcanvas.com?x-ms-routing-name=V7003
•	Minor improvements
4) V7004 - 06/28/2023
•	URL: https://servicenow.contactcanvas.com?x-ms-routing-name=V7004
•	Added ability to refresh ServiceNow authentication token in the background.
5) V7005 - 06/29/2023
•	URL: https://servicenow.contactcanvas.com/?x-ms-routing-name=V7005
•	Added configuration to enable/disable activity autoClose.
•	Added functionality to populate call information into a new entity record in case of no match.
6) V7006 – TBD
•	URL: https://servicenow.contactcanvas.com/?x-ms-routing-name=V7006
•	Multi-tabbed activity has been implemented.
•	Activity associations with entities are now configurable.
•	Important: PhoneFormat has been updated to provide more flexibility. Please refer to the setup and update your configuration accordingly.
