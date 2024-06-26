<a name="readme-top"></a>

<!-- ABOUT THE PROJECT -->
# Using Fabric and Megapatito AI to combat Food Waste

<p align="center">
  <img src="https://i.imgur.com/f4G04lV.png">
</p>

## Intro to the Megapatito Food Waste Bootcamp

* Use Case
* Architecture overview
* Setting up fabric
* Setting up the Power Platform Solution
* How to test

<!-- GETTING STARTED -->
## Use Case

Introducing the Food Fighters solution, aimed at combatting food waste: a seamless integration of Azure Open AI, Power Apps, Fabric Lakehouses, and Notebooks and much more. The solution empowers users to effortlessly manage their food inventory, track expiration dates, and receive personalized recipe suggestions via email, all with the scan of a product.

Imagine the convenience of scanning each item you purchase and instantly generating a comprehensive fridge inventory. The solution, keeping track of expiration dates becomes a breeze, eliminating the frustration of discovering forgotten items wasting away at the back of the fridge.

The application goes beyond mere inventory management. Leveraging the power of AI, it analyzes your inventory and proactively suggests delicious recipes tailored to the items nearing their expiration dates. Say goodbye to wasted food and hello to inspired culinary adventures.

## Architecture Overview

<img src="images/Fabric_AI_Food_Waste_Architecture.jpg" width="1000"/>

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## End to end Walkthrough

[![Foodwaste Hack walk through](https://img.youtube.com/vi/I6MvPD-cXrI/0.jpg)](https://www.youtube.com/watch?v=I6MvPD-cXrI)

## Setting up the Power Platform Solution

Download and Import Solution:

Begin by downloading the [Megapatito FoodFightersSLN solution file](https://github.com/OzDoll/Megapatito-Foodwaste-App/blob/main/PowerApp/FoodFightersSLN_1_0_0_14.zip) provided.
Navigate to Power Apps and log in to your account.
Locate the option to import a solution and upload the downloaded FoodFightersSLN file.
Follow the on-screen prompts to complete the import process.

Set Up Connections:

After importing the solution, set up connections and sure that credentials are correctly configured (Keyvault, Outlook, Reflex Trigger).  
<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Setting up Fabric
Create Fabric Enabled Workspace:

Navigate to https://app.powerbi.com and create a new fabric enabled workspace.

![image](https://github.com/AllgeierSchweiz/aihackers/assets/110177392/d339a1c5-4516-4519-8d4a-bd1538c11e58)


Set Up A Lakehouse:

Within the fabric enabled workspace, create a lakehouse to serve as a storage repository for imported data.

![image](https://github.com/AllgeierSchweiz/aihackers/assets/110177392/e16ed2f2-9967-4c64-bb27-f8a953f3d835)

Run Initial Load:

Add the provided py_loadproducts notebook to the newly created lakehouse.
Execute the notebook to start the initial data load process from Open Food Facts.
This process populates the database with essential product information.

![image](https://github.com/AllgeierSchweiz/aihackers/assets/110177392/09ceb4b0-9c66-40d0-8e0f-076936a49123)

Handle Fridge Inventory Data:

Incorporate the py_addtofridge notebook into the lakehouse environment.
This notebook facilitates the processing and integration of incoming data from the fridge inventory application.

Scan and Send Products:

Begin utilizing the fridge inventory application to scan products and transmit relevant information to the designated lakehouse.

![image](https://github.com/AllgeierSchweiz/aihackers/assets/110177392/0d12325a-eba3-4424-86f8-5f3ca21ef0b5)

Build Semantic Model:

With the data successfully imported into the lakehouse, proceed to construct a semantic model.

![image](https://github.com/AllgeierSchweiz/aihackers/assets/110177392/7a97dbdb-4744-4da9-abb4-0e699fdebf34)

Calculate Expiry Dates:

Within the semantic model, add a DAX expression to calculate the number of days remaining before each product's expiration.
For example:Expires in x Days = DATEDIFF(TODAY(),Min(fridgecontents[expirydate]),DAY)

![image](https://github.com/AllgeierSchweiz/aihackers/assets/110177392/622b3ce5-ba22-4e1f-a80d-8deb1329732a)

Create Fridge Inventory Report:

Set up a report within Power BI showcasing the current inventory of products along with their respective expiry dates.

Activate Data Reflex:

Set up Data Activator reflex to automate responses based on predefined conditions within the inventory report. Select the measure you created earlier in the reflex select section, set the amount of days you want to be informed before product expiration in the detect section and within the act section set the custom action that you will configure in the next step. 

![image](https://github.com/AllgeierSchweiz/aihackers/assets/110177392/d3ee00ef-2ccb-4c99-a382-d8705e7743a5)

Create custom action:

Define a custom action within the reflex to initiate the GetRecipeOpenAI_Flow when your products are getting closer to their expiration date. Copy paste the connection string for the action and add replace the current value for the Trigger action within that flow. 

![image](https://github.com/AllgeierSchweiz/aihackers/assets/110177392/c4c63202-c114-4be4-8b2e-4a4c1cae06d9)

Add Connection Details:

If you haven't configured the connection reference details for the Food Fighter SLN yet you should now add keyvault credentials to the keyvault that holds your OpenAI api-key (if you have no keyvault configured yet you should set it up and add the api-key to it) and the connection to your outlook to send the recipe mail. 

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## How to Test

After setting up the credentials as mentioned above the FoodFighter solution will be ready to use. Download the PowerApps App to your phone and open the FoodFighters App within PowerApps. Scan the barcode some food that you would like to add to your fridge. Select Log Product and wait a couple of minutes. You can now refresh Fridge Inventory lakehouse you created before and check the inventory table for the product you scanned.

If the product is added to the inventory you can move to the reflex you created earlier and send a test alert. If configured correctly this will trigger the GetRecipeOpenAI_Flow that will now send 3 recipes to your email adress using the products you added to the Fridge Inventory lakehouse. 

You are now ready to use the solution. Add all your products to your Fridge Invetory and let Food Fighters keep track of your products and supply you with great recipes you can enjoy!

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- REFERENCES -->
## References

The following documentation was used to source the information contained in this workshop.

* [Azure Architectures](https://learn.microsoft.com/en-us/azure/architecture/browse/)
* [Azure Open AI Service Proxy](https://github.com/microsoft/azure-openai-service-proxy)
* [Fabric Global AI Hack](https://github.com/microsoft/Hack-Together-Fabric-AI)
* [Fabric Tutorials](https://learn.microsoft.com/en-us/fabric/get-started/end-to-end-tutorials)
* [Fabric Open AI How To Guide](https://learn.microsoft.com/en-us/fabric/data-science/ai-services/how-to-use-openai-sdk-synapse?tabs=python)
* Application based upon [AllgeierSchweiz Power App](https://github.com/AllgeierSchweiz/aihackers)

<p align="right">(<a href="#readme-top">back to top</a>)</p>