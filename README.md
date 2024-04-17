# DIO Challenge — Azure AI Search index (UI)
Azure AI Search index using data pulled from customer reviews of a fantasy company. The files used to complete this challenge are located in the **company data** and **results** folders, being the source files and their results, respectively.

# Step by Step:
In this challenge, I needed three resources: **Azure AI Search**, **Azure AI services** and **Storage account**.

### Creating an Azure AI Search resource:

![azure_ai_services](screenshots/Captura%20de%20tela%202024-04-17%20190037.png)

1. Sign into the [Azure portal](https://portal.azure.com);
2. Click the _**+ Create a resource**_ button and search for _Azure AI Search_; 

    
    ![create_a_resource](screenshots/Captura%20de%20tela%202024-04-17%20185616.png)

3. Create a **Azure AI Search** resource with the settings:
	
    **Subscription**: _Your Azure subscription._
    
    **Resource group**: _Select or create a resource group with a unique name._
    
    **Service name**: _A unique name._
    **Location**: _Choose any available region._
    
    **Pricing tier**: _Basic_

4. Select Review + create and, when available, **Create**;
5. Now, select **Go to resource**, on the Azure AI Search overview page, you can add indexes, import data and search created indexes.

### Creating an Azure AI services resource:

![azure_ai_services](screenshots/Captura%20de%20tela%202024-04-17%20190814.png)

1. Return to the home page of the Azure portal, go to the Create a resource button and search for _Azure AI services_. Configure with the settings:

    **Subscription**: _Your Azure subscription._
    
    **Resource group**: _The same resource group as your Azure AI Search resource._
    
    **Region**: _The same location as your Azure AI Search resource._
    
    **Name**: _A unique name._
    
    **Pricing tier**: _Standard S0_
    
    **By checking this box I acknowledge…**: _Selected_

2. Select Review + create and when possible select **Create**;
3. Wait for deployment to complete, then view the deployment details.

### Creating a storage account:

![storage_account](screenshots/Captura%20de%20tela%202024-04-17%20190854.png)

1. Return to the home page of the Azure portal and select the + Create a resource button;
2. Search for _storage account_ and create the resource with the following settings:
	
    **Subscription**: _Your Azure subscription._

    **Resource group**: _The same resource group as your Azure AI Search and Azure AI services resources._

    **Storage account name**: _A unique name._

    **Location**: _Choose any available location._

    **Performance**: _Standard_

    **Redundancy**: _Locally redundant storage (LRS)_

3. Click Review and then click Create;
4. In the Azure Storage account you created, in the left-hand menu pane, select Configuration (under Settings);
5. Change the setting for _Allow Blob anonymous access_ to Enabled and then select Save.

## Challenge:

### Upload Documents to Azure Storage:
1. In the left-hand menu pane located in the storage account resource, select Containers;

2. Select + Container. A pane on your right-hand side opens;

3. Enter the following settings, and click Create:

    **Name**: _coffee-reviews_
    
    **Public access level**: _Container (anonymous read access for containers and blobs)_
    
    **Advanced**: _no changes._

4. Download the zipped [coffee reviews](https://aka.ms/mslearn-coffee-reviews), and extract the files to the reviews folder;

5. In the Azure portal, select your coffee-reviews container. In the container, select Upload;

6. In the Upload blob pane, select _Select a file_;

7. In the Explorer window, select all the files in the reviews folder, select Open, and then select Upload;

8. After the upload is complete, you can close the Upload blob pane. Your documents are now in your coffee-reviews storage container.

### Index the documents:
1. In the Azure portal, browse to your Azure AI Search resource. On the Overview page, select Import data.
2. On the Connect to your data page, in the Data Source list, select Azure Blob Storage. Complete the data store details with the following values:
	
    **Data Source**: _Azure Blob Storage_
    
    **Data source name**: _coffee-customer-data_
    
    **Data to extract**: _Content and metadata_
    
    **Parsing mode**: _Default_
    
    **Connection string**: _Select Choose an existing connection. Select your storage account, select the coffee-reviews container, and then click Select._
    
    **Managed identity authentication**: _None_
    
    **Container name**: _this setting is auto-populated after you choose an existing connection._
    
    **Blob folder**: _Leave this blank._
    
    **Description**: _Reviews for Fourth Coffee shops._

3. Select Next: Add cognitive skills (Optional).
4. In the Attach Cognitive Services section, select your Azure AI services resource.
5. In the Add enrichments section:

    * Change the Skillset name to coffee-skillset.
    * Select the checkbox Enable OCR and merge all text into merged_content field.
    * Ensure that the Source data field is set to merged_content.
    * Change the Enrichment granularity level to Pages (5000 character chunks).
    * Don’t select Enable incremental enrichment
    * Select the following enriched fields:
    ![fields](screenshots/Captura%20de%20tela%202024-04-17%20200603.png)

6. Under Save enrichments to a knowledge store, select:
    * Image projections
    * Documents
    * Pages
    * Key phrases
    * Entities
    * Image details
    * Image references

7. Select Azure blob projections: Document. A setting for Container name with the knowledge-store container auto-populated displays. Don’t change the container name.

8. Select Next: Customize target index. Change the Index name to coffee-index.

9. Ensure that the Key is set to metadata_storage_path. Leave Suggester name blank and Search mode autopopulated.

10. Review the index fields’ default settings. Select filterable for all the fields that are already selected by default.

11. Select Next: Create an indexer.

12. Change the Indexer name to coffee-indexer.

13. Leave the Schedule set to Once.

14. Expand the Advanced options. Ensure that the Base-64 Encode Keys option is selected, as encoding keys can make the index more efficient.

15. Select Submit to create the data source, skillset, index, and indexer. The indexer is run automatically and runs the indexing pipeline, which:

    * Extracts the document metadata fields and content from the data source.
    * Runs the skillset of cognitive skills to generate more enriched fields.
    * Maps the extracted fields to the index.

16. Return to your Azure AI Search resource page. On the left pane, under Search Management, select Indexers. Select the newly created coffee-indexer. Wait a minute, and select &orarr; Refresh until the Status indicates success.

17. Select the indexer name to see more details.

### Query the index:

1. In your Search service’s Overview page, select Search explorer at the top of the screen.

2. Notice how the index selected is the coffee-index you created. Below the index selected, change the view to JSON view.

3. In the JSON query editor field, write:
        
        {
            "search": "*",
            "count": true
        }

    1. Select Search. The search query returns all the documents in the search index, including a count of all the documents in the @odata.count field. The search index should return a JSON document containing your search results.

    2. Now let’s filter by location. In the JSON query editor field, write:
            
            {
            "search": "locations:'Chicago'",
            "count": true
            }
    3. Select Search. The query searches all the documents in the index and filters for reviews with a Chicago location. You should see 3 in the @odata.count field.

    4. Now let’s filter by sentiment. In the JSON query editor field, write:

            {

            "search": "sentiment:'negative'",
            
            "count": true
            
            }

    5. Select Search. The query searches all the documents in the index and filters for reviews with a negative sentiment. You should see 1 in the @odata.count field.

The results of the searches are located in the _results_ folder.

### Review the knowledge store:

1. In the Azure portal, navigate back to your Azure storage account.

2. In the left-hand menu pane, select Containers. Select the knowledge-store container.

3. Select any of the items, and then click the **objectprojection.json** file.

4. Select Edit to see the JSON produced for one of the documents from your Azure data store.

5. Select the storage blob breadcrumb at the top left of the screen to return to the Storage account Containers.

6. In the Containers, select the container coffee-skillset-image-projection. Select any of the items.

7. Select any of the .jpg files. Select Edit to see the image stored from the document. Notice how all the images from the documents are stored in this manner.

8. Select the storage blob breadcrumb at the top left of the screen to return to the Storage account Containers.

9. Select Storage browser on the left-hand panel, and select Tables. There’s a table for each entity in the index. Select the table coffeeSkillsetKeyPhrases.

