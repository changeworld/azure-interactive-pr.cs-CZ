---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079239"
---
<span data-ttu-id="b5678-103">Azure Cosmos DB je globálně distribuovaná databáze Microsoftu bez serveru pro více modelů.</span><span class="sxs-lookup"><span data-stu-id="b5678-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="b5678-104">V tomto modulu zjistíte, jak pomocí služby Azure Functions ukládat a načítat metadata obrázků jako dokumenty JSON ve službě Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="b5678-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="b5678-105">Vytvoření účtu, databáze a kolekce Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="b5678-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="b5678-106">Účet Cosmos DB je prostředek Azure, který obsahuje databáze Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="b5678-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="b5678-107">Ujistěte se, že jste stále přihlášeni ve službě Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="b5678-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="b5678-108">Pokud ne, výběrem možnosti **Vstoupit do detailního režimu** otevřete okno služby Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="b5678-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="b5678-109">Vytvořte účet Cosmos DB s jedinečným názvem ve stejné skupině prostředků jako ostatní prostředky v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="b5678-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="b5678-110">Po vytvoření účtu Cosmos DB v tomto účtu vytvořte novou databázi **imagesdb**.</span><span class="sxs-lookup"><span data-stu-id="b5678-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="b5678-111">Po vytvoření databáze v této databázi vytvořte novou kolekci **images** s propustností 400 jednotek žádostí (RU).</span><span class="sxs-lookup"><span data-stu-id="b5678-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="b5678-112">Uložení dokumentu do služby Cosmos DB při vytvoření miniatury</span><span class="sxs-lookup"><span data-stu-id="b5678-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="b5678-113">Výstupní vazba služby Cosmos DB umožňuje vytvářet dokumenty v kolekci Cosmos DB ze služby Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="b5678-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="b5678-114">V následujících krocích nakonfigurujete výstupní vazbu služby Cosmos DB ve funkci **ResizeImage** a upravíte tuto funkci tak, aby vracela dokument (objekt), který se má uložit.</span><span class="sxs-lookup"><span data-stu-id="b5678-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="b5678-115">Otevřete aplikaci funkcí na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="b5678-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="b5678-116">V levém navigačním panelu rozbalte funkci **ResizeImage** a pak vyberte **Integrovat**.</span><span class="sxs-lookup"><span data-stu-id="b5678-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="b5678-117">V části **Výstupy** klikněte na **Nový výstup**.</span><span class="sxs-lookup"><span data-stu-id="b5678-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="b5678-118">Vyhledejte položku **Azure Cosmos DB** a vyberte ji.</span><span class="sxs-lookup"><span data-stu-id="b5678-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="b5678-119">Pak klikněte na **Vybrat**.</span><span class="sxs-lookup"><span data-stu-id="b5678-119">Then click **Select**.</span></span>

    ![Výběr možnosti Nový výstup](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="b5678-121">Do polí v části **Výstup služby Azure Cosmos DB** vyplňte následující hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b5678-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="b5678-122">Nastavení</span><span class="sxs-lookup"><span data-stu-id="b5678-122">Setting</span></span>      |  <span data-ttu-id="b5678-123">Navrhovaná hodnota</span><span class="sxs-lookup"><span data-stu-id="b5678-123">Suggested value</span></span>   | <span data-ttu-id="b5678-124">Popis</span><span class="sxs-lookup"><span data-stu-id="b5678-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="b5678-125">**Název parametru dokumentu**</span><span class="sxs-lookup"><span data-stu-id="b5678-125">**Document parameter name**</span></span> | <span data-ttu-id="b5678-126">Vyberte **Používat návratovou hodnotu funkce**.</span><span class="sxs-lookup"><span data-stu-id="b5678-126">Select **Use function return value**</span></span> | <span data-ttu-id="b5678-127">Hodnota textového pole se automaticky nastaví na **$return**.</span><span class="sxs-lookup"><span data-stu-id="b5678-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="b5678-128">**Název databáze**</span><span class="sxs-lookup"><span data-stu-id="b5678-128">**Database name**</span></span> | <span data-ttu-id="b5678-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="b5678-129">imagesdb</span></span> | <span data-ttu-id="b5678-130">Použijte název databáze, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="b5678-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="b5678-131">**Název kolekce**</span><span class="sxs-lookup"><span data-stu-id="b5678-131">**Collection name**</span></span> | <span data-ttu-id="b5678-132">images</span><span class="sxs-lookup"><span data-stu-id="b5678-132">images</span></span> | <span data-ttu-id="b5678-133">Použijte název kolekce, kterou jste vytvořili.</span><span class="sxs-lookup"><span data-stu-id="b5678-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="b5678-134">Vedle **Připojení účtu Azure Cosmos DB** klikněte na **nový**.</span><span class="sxs-lookup"><span data-stu-id="b5678-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="b5678-135">Vyberte účet Cosmos DB, který jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="b5678-135">Select the Cosmos DB account you previously created.</span></span>

    ![Zadání nastavení výstupní vazby služby Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="b5678-137">Kliknutím na **Uložit** vytvořte výstupní vazbu služby Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="b5678-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="b5678-138">Otevřete funkci kliknutím na název funkce **ResizeImage** na levé straně.</span><span class="sxs-lookup"><span data-stu-id="b5678-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="b5678-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="b5678-139">**C#**</span></span>

    1. <span data-ttu-id="b5678-140">(C#) Změňte návratový typ funkce z hodnoty **void** na **object**.</span><span class="sxs-lookup"><span data-stu-id="b5678-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="b5678-141">(C#) Na konec funkce přidejte následující blok kódu vracející dokument, který se má uložit:</span><span class="sxs-lookup"><span data-stu-id="b5678-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![Upravený soubor run.csx s funkcí ResizeImages](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="b5678-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="b5678-143">**JavaScript**</span></span>

    1. <span data-ttu-id="b5678-144">(JavaScript) Změňte příkaz `context.done()` v klauzuli `else` tak, aby vracel dokument, který se má uložit do služby Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="b5678-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. <span data-ttu-id="b5678-145">Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.</span><span class="sxs-lookup"><span data-stu-id="b5678-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="b5678-146">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="b5678-146">Click **Save**.</span></span> <span data-ttu-id="b5678-147">Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně uložila a nedošlo k žádným chybám.</span><span class="sxs-lookup"><span data-stu-id="b5678-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="b5678-148">Vytvoření funkce pro výpis obrázků ze služby Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="b5678-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="b5678-149">Webová aplikace k načtení metadat obrázků ze služby Cosmos DB vyžaduje rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b5678-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="b5678-150">V následujících krocích</span><span class="sxs-lookup"><span data-stu-id="b5678-150">In the following steps.</span></span> <span data-ttu-id="b5678-151">vytvoříte funkci aktivovanou protokolem HTTP, která používá vstupní vazbu služby Cosmos DB k dotazování kolekce databáze.</span><span class="sxs-lookup"><span data-stu-id="b5678-151">uou create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="b5678-152">Ve vaší aplikaci funkcí najeďte myší na **Funkce** na levé straně a kliknutím na **+** vytvořte novou funkci.</span><span class="sxs-lookup"><span data-stu-id="b5678-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="b5678-153">Vyhledejte šablonu **HttpTrigger** a vyberte ji.</span><span class="sxs-lookup"><span data-stu-id="b5678-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="b5678-154">Použijte následující hodnoty k vytvoření funkce, která vygeneruje adresu URL pro získání obrázků.</span><span class="sxs-lookup"><span data-stu-id="b5678-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="b5678-155">Nastavení</span><span class="sxs-lookup"><span data-stu-id="b5678-155">Setting</span></span>      |  <span data-ttu-id="b5678-156">Navrhovaná hodnota</span><span class="sxs-lookup"><span data-stu-id="b5678-156">Suggested value</span></span>   | <span data-ttu-id="b5678-157">Popis</span><span class="sxs-lookup"><span data-stu-id="b5678-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="b5678-158">**Pojmenujte svoji funkci**</span><span class="sxs-lookup"><span data-stu-id="b5678-158">**Name your function**</span></span> | <span data-ttu-id="b5678-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="b5678-159">GetImages</span></span> | <span data-ttu-id="b5678-160">Zadejte tento název přesně tak, jak je uvedený, aby aplikace funkci zjistila.</span><span class="sxs-lookup"><span data-stu-id="b5678-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="b5678-161">**Úroveň autorizace**</span><span class="sxs-lookup"><span data-stu-id="b5678-161">**Authorization level**</span></span> | <span data-ttu-id="b5678-162">Anonymní</span><span class="sxs-lookup"><span data-stu-id="b5678-162">Anonymous</span></span> | <span data-ttu-id="b5678-163">Povolte k funkci veřejný přístup.</span><span class="sxs-lookup"><span data-stu-id="b5678-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="b5678-164">Klikněte na možnost **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="b5678-164">Click **Create**.</span></span>

1. <span data-ttu-id="b5678-165">Po vytvoření nové funkce klikněte na **Integrovat** pod názvem funkce na levém navigačním panelu.</span><span class="sxs-lookup"><span data-stu-id="b5678-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="b5678-166">Klikněte na **Nový vstup** a vyberte **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="b5678-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Výběr možnosti Nový vstup](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="b5678-168">Klikněte na **Vybrat**.</span><span class="sxs-lookup"><span data-stu-id="b5678-168">Click **Select**.</span></span>

1. <span data-ttu-id="b5678-169">Vyplňte následující hodnoty:</span><span class="sxs-lookup"><span data-stu-id="b5678-169">Fill out the following values:</span></span>

    | <span data-ttu-id="b5678-170">Nastavení</span><span class="sxs-lookup"><span data-stu-id="b5678-170">Setting</span></span>      |  <span data-ttu-id="b5678-171">Navrhovaná hodnota</span><span class="sxs-lookup"><span data-stu-id="b5678-171">Suggested value</span></span>   | <span data-ttu-id="b5678-172">Popis</span><span class="sxs-lookup"><span data-stu-id="b5678-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="b5678-173">**Název parametru dokumentu**</span><span class="sxs-lookup"><span data-stu-id="b5678-173">**Document parameter name**</span></span> | <span data-ttu-id="b5678-174">documents</span><span class="sxs-lookup"><span data-stu-id="b5678-174">documents</span></span> | <span data-ttu-id="b5678-175">Tento název odpovídá názvu parametru ve funkci.</span><span class="sxs-lookup"><span data-stu-id="b5678-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="b5678-176">**Název databáze**</span><span class="sxs-lookup"><span data-stu-id="b5678-176">**Database name**</span></span> | <span data-ttu-id="b5678-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="b5678-177">imagesdb</span></span> |  |
    | <span data-ttu-id="b5678-178">**Název kolekce**</span><span class="sxs-lookup"><span data-stu-id="b5678-178">**Collection name**</span></span> | <span data-ttu-id="b5678-179">images</span><span class="sxs-lookup"><span data-stu-id="b5678-179">images</span></span> |  |
    | <span data-ttu-id="b5678-180">**Dotaz SQL**</span><span class="sxs-lookup"><span data-stu-id="b5678-180">**SQL query**</span></span> | <span data-ttu-id="b5678-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="b5678-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="b5678-182">Získání dokumentů od nejnovějšího po nejstarší.</span><span class="sxs-lookup"><span data-stu-id="b5678-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="b5678-183">**Připojení účtu Azure Cosmos DB**</span><span class="sxs-lookup"><span data-stu-id="b5678-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="b5678-184">Vyberte existující připojovací řetězec.</span><span class="sxs-lookup"><span data-stu-id="b5678-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="b5678-185">Kliknutím na **Uložit** vytvořte vstupní vazbu.</span><span class="sxs-lookup"><span data-stu-id="b5678-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="b5678-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="b5678-186">**C#**</span></span>

    1. <span data-ttu-id="b5678-187">Kliknutím na název funkce otevřete okno kódu a pak nahraďte veškerý obsah souboru **run.csx** obsahem souboru [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span><span class="sxs-lookup"><span data-stu-id="b5678-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="b5678-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="b5678-188">**JavaScript**</span></span>

    1. <span data-ttu-id="b5678-189">Kliknutím na název funkce otevřete okno kódu a pak nahraďte veškerý obsah souboru **index.js** obsahem souboru [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span><span class="sxs-lookup"><span data-stu-id="b5678-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="b5678-190">Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.</span><span class="sxs-lookup"><span data-stu-id="b5678-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="b5678-191">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="b5678-191">Click **Save**.</span></span> <span data-ttu-id="b5678-192">Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně uložila a nedošlo k žádným chybám.</span><span class="sxs-lookup"><span data-stu-id="b5678-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="b5678-193">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="b5678-193">Test the application</span></span>

1. <span data-ttu-id="b5678-194">Otevřete aplikaci v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="b5678-194">Open the application in a browser.</span></span> <span data-ttu-id="b5678-195">Vyberte soubor obrázku a nahrajte ho.</span><span class="sxs-lookup"><span data-stu-id="b5678-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="b5678-196">Po několika sekundách se na stránce zobrazí miniatura nového obrázku.</span><span class="sxs-lookup"><span data-stu-id="b5678-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="b5678-197">Na webu Azure Portal pomocí vyhledávacího pole vyhledejte váš účet Cosmos DB podle názvu.</span><span class="sxs-lookup"><span data-stu-id="b5678-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="b5678-198">Klikněte na něj a otevřete ho.</span><span class="sxs-lookup"><span data-stu-id="b5678-198">Click it to open it.</span></span>

1. <span data-ttu-id="b5678-199">Na levé straně klikněte na **Průzkumník dat**, ve kterém můžete procházet kolekce a dokumenty.</span><span class="sxs-lookup"><span data-stu-id="b5678-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="b5678-200">V databázi **imagesdb** vyberte kolekci **images**.</span><span class="sxs-lookup"><span data-stu-id="b5678-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="b5678-201">Ověřte vytvoření dokumentu pro nahraný obrázek.</span><span class="sxs-lookup"><span data-stu-id="b5678-201">Confirm that a document was created for the uploaded image.</span></span>

    ![Průzkumník dat ukazující dokument pro nahraný obrázek](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="b5678-203">Souhrn</span><span class="sxs-lookup"><span data-stu-id="b5678-203">Summary</span></span>

<span data-ttu-id="b5678-204">V tomto modulu jste zjistili, jak vytvořit účet, databázi a kolekci Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="b5678-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="b5678-205">Také jste zjistili, jak pomocí vazeb služby Cosmos DB ukládat a načítat metadata obrázků v kolekci Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="b5678-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="b5678-206">Dále se dozvíte, jak pro jednotlivé nahrané obrázky automaticky generovat titulky pomocí služeb Microsoft Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="b5678-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>