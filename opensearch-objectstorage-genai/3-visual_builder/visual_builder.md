
# Create a search user interface and run searches

## Introduction
In this lab you will create a user interface in Visual Builder that can be used to execute searches against the content that has been processed by the AI services and loaded into OpenSearch. A Visual Builder project has been prepared and you will upload this project, connect it to your environment, then use it. 

Estimated time: 20 min

### Objectives

- Create a user interface with Visual Builder and use it to query content that has been processed by the solution

### Prerequisites
- You've completed the previous labs

## Task 1: User and Tenancy details

First, we need data about your tenancy and user.
- On the top, right, click *Tenancy: name*
- Copy the tenancy OCID *##TENANCY\_OCID##*

![Tenancy](images/opensearch-tenancy.png)

Then, we need data about the user
- On the top, right, click *Your username*
- Copy the username *##USERNAME##* (without oracleidentitycloudservice )
- Copy the user OCID *##USER\_OCID##*
- Click on *API Keys*

![User](images/opensearch-user.png)

- Click *Add API Key*
- Generate one
- Download it *##PRIVATE_KEY##*
- Copy the *##FINGERPRINT##*

![User API Keys](images/opensearch-user2.png)

In your computer (NOT in cloud shell), you need to convert the PEM key to RSA format:
- Run the below command 
- And keep the ##PRIVATE\_KEY\_RSA\_FORMAT##

```
openssl rsa -in ##PRIVATE_KEY## -out ##PRIVATE_KEY_RSA_FORMAT##
ex: openssl rsa -in private_key.pem -out private_key_rsa_format.pem
````

Double-check that the private\_key\_rsa_format.pem is really in RSA format like this:

```
-----BEGIN RSA PRIVATE KEY-----
...
-----END RSA PRIVATE KEY-----
```

## Task 2: Import the project
1. You will need the VB application archive *opensearch-1.0.x.zip* for the next task. If you cloned the Git repo to your local computer in a previous lab, you will already have this file. This file is located in the *visual_builder* folder of the cloned repo. You can continue to the next task.

1. Go to the Oracle Integration Cloud (OIC) service console home page. If this is not already open in one of your browser tabs, you can get back to it by following these steps.
  
    1. Go the Cloud console 3-bar/hamburger menu and select the following    
        1. Developer Services
        1. Integration
  
    1. Check that you are in the intended compartment. (*oci-starter* was the recommended compartment name.)

    1. Click **oic** in the Integration instances list

    1. Click the **Service Console** button. It will open the OCI service console in a new tab.


1. In the OIC service console menu, choose **Visual Builder**.
![Visual Builder link](images/opensearch-vb-link-oic.png)

1. Import the project file that you obtained in **Task 1**.
    1. Click *Import*
        ![Visual Builder Import](images/opensearch-vb.png)
        
    1. Click **Application from file**
        ![Visual Builder file](images/opensearch-vb-file.png)
    1. Choose the file *vb/opensearch-1..x.zip*
    1. Application Name: *opensearch*
    1. Application ID: *opensearch*
    1. Click **Import**

        ![Visual Builder Import](images/opensearch-vb-import.png)
    1. Wait for the import to be confirmed

## Task 3: Edit the connections

1. From the Visual Applications list, open the imported application by clicking on the application name, **opensearch**
![Visual Builder Import](images/opensearch-vb-applications.png)

1. To edit the connection to connect to the OpenSearch server, click on the **Service icon**

### 1. Opensearch connection
1. Click the **opensearch** connection 

1. Click on **Servers** tab

1. Click the **Edit icon** 
![Connection OpenSearch](images/opensearch-vb-connection-opensearch.png)

1. Use the ***APIGW_URL*** value you saved to your text file to fill in the Connection details:
    - (If you need to get the value, run this command in OCI Console Cloud Shell: oci-searchlab/starter/src/search_env.sh)

1. Configure the server as follows:
    - Replace Instance URL: *APIGW_URL*
      - Ex: https://xxxxxxxx.apigateway.eu-frankfurt-1.oci.customer-oci.com
    - Authentication: None
![Edit Connection OpenSearch](images/opensearch-vb-connection-opensearch2.png)

1. Click *Save*

1. Test the connection:
    1. Select tab *Endpoints*
    1. Click **Get - Get Many**
    1. Select tab *Test*
    1. Click **Send Request**

    ![Test Connection OpenSearch](images/opensearch-vb-connection-opensearch3.png)
    1. The test succeeded if you see *"successful": 1,* in the response

### 2. Generative AI connection
1. Click on the again on *Service icon*
2. Click on the *Backend Tab* 
3. Choose the *GenerativeAI* backend
4. Click on *Servers* tab 
5. Then *Edit icon* 
6. First, we need to get the Generative rest API. *##AI\_GENAI\_URL##*. You got it in the previous lab too.
    You can find it here [https://docs.oracle.com/en-us/iaas/api/#/en/generative-ai/20231130/](https://docs.oracle.com/en-us/iaas/api/#/en/generative-ai/20231130/)
7. Fill the details:
    - Instance URL: ##AI\_GENAI\_URL##
    - Edit the authentication
        - KeyId format = ##TENANCY_OCID##/##USER_OCID##/##FINGERPRINT##
        - Ex: ocid1.tenancy.oc1..xxxxx/ocid1.user.oc1..xxxx/11:22:33:44:55:66:77
    - Private KEY =  *##PRIVATE_KEY_RSA_FORMAT##*
8. *Save*
9. *Save*

## Task 4: Test the application

1. Click the **run** button on the top right of the Visual Builder page. The Visual Builder application user interface will appear in a new browser tab.
![Run the application](images/opensearch-vb-test.png)

1. In the search user interface, enter *candle* and click **Search**. This will return a record for the file that was processed by OCI Document Understanding AI service to extract text from the image file so that it could be made searchable.
![Test Result](images/opensearch-vb-test-result.png)

1. Notice the search results contain the file name, an excerpt of the text extracted from the file (although the full text is searchable), the file type, and the topic classification provided by the OCI Language AI service. You can click the file name to display it.
    
    **Note - since most web browsers don't natively display .tif files, if you attempt to open a .tif in the search results, you will be prompted to download it. You can then view it with an application on your local computer, if you have an app that can view .tif files.**

1. If you performed the optional task at the end of the previous lab and processed the additional files, you can query for those now. Here's some hints on words you can search for each of the provided documents. You can also search on *"\*"* to see all indexed documents.

    | File | Search Terms |
    | ------------------------------------- | --------------------------------------- |
    | Image\_Map\_Brazil.png | brasil, rio |
    | Image\_Workshop\_architecture.png | streaming, "object storage" |
    | Invoice\_6879875\_scanned.tif | 6879875, bijou, 111.31 |
    | Invoice\_DS67076\_scanned.pdf | ds67076, champaign, pcanywhere, 709.63 |
    | Text\_DocU\_blog\_HTML\_to\_PDF.pdf | blogs.oracle.com, datascience |
    | Text\_DocU\_Overview\_PPTX\_to\_PDF.pdf | harbor, "product direction" |
    | Voice\_SupportRobot.mp3 | headphones, bluetooth, "consumer electronics", yesterday |
    | Shakespeare\_macbeth.tif | shakespeare, macbeth, candle, shadow |
    | Penguins.jpg | animal, beak, bird, penguin |
    | HOL\_summary.txt | workshop, oic, "document understanding" |




**Congratulations! You have completed this workshop.**

Here's what you accomplished. You provisioned multiple services into a compartment in your OCI tenancy. These included Oracle Integration Cloud (OIC), several AI services (such as OCI Document Understanding and OCI Language), and Oracle Search with OpenSearch. You imported a project into OIC that ingests document files from Object Storage, sends them to AI services based on the file type, and then loads extracted data into an OpenSearch index. You configured all of the service connections used by that OIC project and then you ran the integration project. Finally, you imported an application project into Visual Builder, you configured the connection to OpenSearch, and then you ran the project to display a search user interface. You used that search tool to query the content added to the OpenSearch index. This workshop has illustrated how different OCI services can be integrated together to use AI to make many types of content more searchable.

## Acknowledgements
- **Author**
    - Marc Gueury, Master Principal Account Cloud Engineer
    - Badr Aissaoui, Principal Account Cloud Engineer
    - Marek Krátký, Cloud Storage Specialist 
