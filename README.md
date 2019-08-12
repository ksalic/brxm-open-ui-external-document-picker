
# Documentation BrXM External Document Picker (Open UI)  
  
Welcome to the documentation page of the External Document Picker (Re-created with Open UI)  
    
Previously we have had great success with our long lasting [External Document Picker Plugin (Original)](https://github.com/bloomreach-forge/external-document-picker)  
  
Now we feel there is a time for a new and refurbished UI to the features the external document picker offers us.  
  
**Short Demo:**  
  
![Open UI DEMO](https://github.com/ksalic/brxm-open-ui-external-document-picker/blob/master/resources/open-ui-demo.gif?raw=true)  
  
 ## OOTB Integrations

In this project we have included 2 examples with 3rd party integrations: [Unsplash.com]([https://unsplash.com/](https://unsplash.com/)) and [Giphy](https://giphy.com/)
  
## Build & RUN  - Dependencies and Demo
  
### Build External Document Picker Dependencies  

     mvn clean install  

### Running External Document Picker UI frontend  

     cd frontend 
     npm install 
     npm run start

### Run Demo Project   

     cd demo 
     mvn clean verify 
     mvn -P cargo.run  
     
## Install to your project  

In the cms pom.xml add the following dependencies

API dependency (required):

    <dependency>  
     <groupId>com.bloomreach.openui</groupId>  
     <artifactId>openui-external-document-picker-api</artifactId>  
     <version>${edp.version}</version>  
    </dependency>

 Unsplash.com dependency (optional):

    <dependency>  
     <groupId>com.bloomreach.openui</groupId>  
     <artifactId>openui-external-document-picker-unsplashed-cms</artifactId>  
     <version>${edp.version}</version>  
    </dependency>

 Giphy dependency (optional):

	   <dependency>  
         <groupId>com.bloomreach.openui</groupId>  
         <artifactId>openui-external-document-picker-giphy-cms</artifactId>  
         <version>${edp.version}</version>  
       </dependency>

 In the site/components pom.xml add the following (optional) dependencies:
 

    <dependency>  
     <groupId>com.bloomreach.openui</groupId>  
     <artifactId>openui-external-document-picker-unsplashed-site</artifactId>  
     <version>${edp.version}</version>  
    </dependency> 
     
    <dependency>  
     <groupId>com.bloomreach.openui</groupId>  
     <artifactId>openui-external-document-picker-giphy-site</artifactId>  
     <version>${edp.version}</version>  
    </dependency>

 ## Configure

Example: add Unsplash Picker to a News item:

**Prerequisites**: Installed the cms and site dependencies for unslash.com (see above)

 1. Log in to the console and find the unsplash CRISP configuration

![enter image description here](https://github.com/ksalic/brxm-open-ui-external-document-picker/blob/master/resources/configure-console.png?raw=true)

*/hippo:configuration/hippo:modules/crispregistry/hippo:moduleconfig/crisp:resourceresolvercontainer/unsplashed*

 2. Edit the client.id (abc123) to match the **Acces Key** supplied by your unsplash [developer account](https://unsplash.com/developers)
 3. Login to the CMS, navigate to the document type section and find a document which you want to add the picker to and go into edit mode.
 4. Select **unsplashed** from the right sidebar in the compound section

![enter image description here](https://github.com/ksalic/brxm-open-ui-external-document-picker/blob/master/resources/configure.png?raw=true)

5. Done and Commit. The picker should now be available to each News item in the BrXM instance.

For Gipy is the same approach.

## Create Your Own Integration

The following section will go to how to create your own integration with a 3rd Party. Before we go into creating your own external document picker extension we will explain on how it works.

### Resources

before we start going into the details on how to create you own integration with the Open UI External Document Picker make sure to read through the official documentation:

[https://documentation.bloomreach.com/library/concepts/open-ui/introduction.html](https://documentation.bloomreach.com/library/concepts/open-ui/introduction.html)



### Register the Open UI Extension in the CMS

The first thing that is required is to register the Open UI extension point in the CMS.

1. Go to the console and navigate to:

*/hippo:configuration/hippo:frontend/cms/ui-extensions*

2. Import the following snippet and replace all the parts with `<application>` with the name of the integration you wish to create:

`/<application>:`
	  `jcr:primaryType: frontend:uiExtension`
	  `jcr:mixinTypes: ['hippostd:relaxed']`
	  `frontend:clientid: abc123`
	  `frontend:config: '{"dataMode":"multiple", "title":"<application> Image Picker","size":"large",
   "application": "<application>", "query": "hippo", "pageSize" :"16", "clientId": "abc123"}'`
	 `frontend:displayName: External Document Picker`
	 `frontend:extensionPoint: document.field`
	 `frontend:initialHeightInPixels: 200`
	 `frontend:url: http://<frontend-url>`

In the above snippet we are registering an Open UI extension to be used in the CMS.

in the **frontend:config** we are specifying the JSON string which the frontend application (external document picker frontend ui) is going to receive during initialisation of the UI. 

| property | value | comment |
|--|--|--|
|dataMode  | single or multiple | Depending if you would like to select a single or multiple items in the external document picker. |
|title | X Picker | The title of the dialog
|size | small, medium or large| Size of the Dialog
|application | foo | Name of the 3rd party connector developed. E.g. unsplash: {... ,application: unsplash}. This corresponds with the value of @Path annotation in the Rest Resource (see below)
| query | hippo | Default query executed when dialog instantiated 
| pageSize | 16 | Default limit  used for pagination in the infinite scroll feature
|cliendId | foobar123 | the same value as the  `frontend:clientid: abc123` property. Used for (simple authentication purposes)

The **frontend:url** is the property which is pointing towards the frontend application. If you are running the frontend application locally on http://localhost:3002 then the value should be `frontend:url: http://localhost:3002`. Check the release notes (below) which frontend application is already deployed and you can use the link directly e.g.: [http://brxm-extdoc-pickerv0.2.s3-website-eu-west-1.amazonaws.com](http://brxm-extdoc-pickerv0.2.s3-website-eu-west-1.amazonaws.com/)


### How it works

```mermaid
sequenceDiagram
CMS User ->> CMS UI: 
CMS UI ->> Frontend: application: unsplash
Note right of CMS UI: Open UI extension <br/> Embed as iframe.
Frontend ->> CMS EDP Endpoint: retrieve results
Note right of Frontend: /edp/<application>/search?..
CMS EDP Endpoint ->> 3rd Party Integration: query
3rd Party Integration -->> CMS EDP Endpoint: results 
CMS EDP Endpoint -->> Frontend: results 
Note right of Frontend: transformed results




```

 
## Release Notes

| CMS Version| External Document Picker Version| Frontend URL | Release Notes
|--|--|--|--|
| 13.3.x | 0.1.0 | [http://brxm-extdoc-pickerv0.2.s3-website-eu-west-1.amazonaws.com](http://brxm-extdoc-pickerv0.2.s3-website-eu-west-1.amazonaws.com/) | Initial Version: Included Unsplash.com + Giphy |

## Feature Requests:

1. Re-ordering the selected items in the edit mode
2. CKeditor Support
3. Custom Dialog result view (i.e. video support)