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
ms.openlocfilehash: 88b0ac838dfa8e097a30cc6cef591377e4a95ad8
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079185"
---
<span data-ttu-id="51895-103">V předchozím modulu jste viděli, jak funkce bez serveru může usnadnit zabezpečené nahrávání obrázků do úložiště objektů blob z webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="51895-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="51895-104">V tomto modulu vytvoříte další funkci bez serveru, která bude sledovat nahrané obrázky a vytvářet pro ně miniatury.</span><span class="sxs-lookup"><span data-stu-id="51895-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="51895-105">Vytvoření kontejneru úložiště objektů blob pro miniatury</span><span class="sxs-lookup"><span data-stu-id="51895-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="51895-106">Obrázky v plné velikosti se ukládají v kontejneru **images**.</span><span class="sxs-lookup"><span data-stu-id="51895-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="51895-107">Potřebujete další kontejner pro ukládání miniatur těchto obrázků.</span><span class="sxs-lookup"><span data-stu-id="51895-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="51895-108">Ujistěte se, že jste stále přihlášeni ve službě Cloud Shell (Bash).</span><span class="sxs-lookup"><span data-stu-id="51895-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="51895-109">Pokud ne, výběrem možnosti **Vstoupit do detailního režimu** otevřete okno služby Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="51895-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="51895-110">Vytvořte ve svém účtu úložiště nový kontejner **thumbnails** s veřejným přístupem ke všem objektům blob.</span><span class="sxs-lookup"><span data-stu-id="51895-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="51895-111">Vytvoření funkce bez serveru aktivované objektem blob</span><span class="sxs-lookup"><span data-stu-id="51895-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="51895-112">Trigger definuje, jak se funkce vyvolá.</span><span class="sxs-lookup"><span data-stu-id="51895-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="51895-113">Funkce, kterou v dalších krocích vytvoříte, používá trigger objektu blob: funkce se automaticky vyvolá při nahrání objektu blob (soubor obrázku) do kontejneru **images**.</span><span class="sxs-lookup"><span data-stu-id="51895-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="51895-114">Funkce musí mít jeden trigger.</span><span class="sxs-lookup"><span data-stu-id="51895-114">A function must have one trigger.</span></span> <span data-ttu-id="51895-115">Triggery obsahují související data, což je obvykle datová část, která funkci aktivovala.</span><span class="sxs-lookup"><span data-stu-id="51895-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="51895-116">Vazby definují, jak funkce čte nebo zapisuje data ve službách Azure nebo službách třetích stran.</span><span class="sxs-lookup"><span data-stu-id="51895-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="51895-117">Tato funkce vytvoří miniaturu obrázku, který ji aktivuje, a uloží miniaturu v kontejneru *thumbnails*.</span><span class="sxs-lookup"><span data-stu-id="51895-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="51895-118">Otevřete svou aplikaci funkcí na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="51895-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="51895-119">V levém navigačním panelu okna aplikace funkcí najeďte myší na **Funkce** a kliknutím na **+** začněte vytvářet novou funkci bez serveru.</span><span class="sxs-lookup"><span data-stu-id="51895-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="51895-120">Pokud se zobrazí stránka Rychlý start, kliknutím na **Vlastní funkce** zobrazte seznam šablon funkcí.</span><span class="sxs-lookup"><span data-stu-id="51895-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="51895-121">Vyhledejte šablonu **BlobTrigger** a vyberte ji.</span><span class="sxs-lookup"><span data-stu-id="51895-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="51895-122">K vytvoření funkce, která při nahrávání obrázků vytváří miniatury, použijte tyto hodnoty.</span><span class="sxs-lookup"><span data-stu-id="51895-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="51895-123">Nastavení</span><span class="sxs-lookup"><span data-stu-id="51895-123">Setting</span></span>      |  <span data-ttu-id="51895-124">Navrhovaná hodnota</span><span class="sxs-lookup"><span data-stu-id="51895-124">Suggested value</span></span>   | <span data-ttu-id="51895-125">Popis</span><span class="sxs-lookup"><span data-stu-id="51895-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="51895-126">**Jazyk**</span><span class="sxs-lookup"><span data-stu-id="51895-126">**Language**</span></span> | <span data-ttu-id="51895-127">C# nebo JavaScript</span><span class="sxs-lookup"><span data-stu-id="51895-127">C# or JavaScript</span></span> | <span data-ttu-id="51895-128">Zvolte preferovaný jazyk.</span><span class="sxs-lookup"><span data-stu-id="51895-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="51895-129">**Pojmenujte svoji funkci**</span><span class="sxs-lookup"><span data-stu-id="51895-129">**Name your function**</span></span> | <span data-ttu-id="51895-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="51895-130">ResizeImage</span></span> | <span data-ttu-id="51895-131">Zadejte tento název přesně tak, jak je uvedený, aby aplikace funkci zjistila.</span><span class="sxs-lookup"><span data-stu-id="51895-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="51895-132">**Cesta**</span><span class="sxs-lookup"><span data-stu-id="51895-132">**Path**</span></span> | <span data-ttu-id="51895-133">images/{name}</span><span class="sxs-lookup"><span data-stu-id="51895-133">images/{name}</span></span> | <span data-ttu-id="51895-134">Funkce se spustí, když se v kontejneru **images** objeví nový soubor.</span><span class="sxs-lookup"><span data-stu-id="51895-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="51895-135">**Informace o účtu úložiště**</span><span class="sxs-lookup"><span data-stu-id="51895-135">**Storage account information**</span></span> | <span data-ttu-id="51895-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="51895-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="51895-137">Použijte proměnnou prostředí s připojovacím řetězcem, kterou jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="51895-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Zadání nastavení nové funkce](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="51895-139">Funkci vytvoříte kliknutím na **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="51895-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="51895-140">Po vytvoření funkce kliknutím na **Integrovat** zobrazte její trigger a vstupní a výstupní vazby.</span><span class="sxs-lookup"><span data-stu-id="51895-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="51895-141">Kliknutím na **Nový výstup** vytvořte novou výstupní vazbu.</span><span class="sxs-lookup"><span data-stu-id="51895-141">Click **New output** to create a new output binding.</span></span>

    ![Výběr možnosti Nový výstup na kartě Integrovat](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="51895-143">Vyberte **Azure Blob Storage** a klikněte na **Vybrat**.</span><span class="sxs-lookup"><span data-stu-id="51895-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="51895-144">Možná se budete muset posunout dolů, aby se tlačítko **Vybrat** zobrazilo.</span><span class="sxs-lookup"><span data-stu-id="51895-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Výběr služby Azure Blob Storage](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="51895-146">Zadejte následující hodnoty.</span><span class="sxs-lookup"><span data-stu-id="51895-146">Enter the following values.</span></span>

    | <span data-ttu-id="51895-147">Nastavení</span><span class="sxs-lookup"><span data-stu-id="51895-147">Setting</span></span>      |  <span data-ttu-id="51895-148">Navrhovaná hodnota</span><span class="sxs-lookup"><span data-stu-id="51895-148">Suggested value</span></span>   | <span data-ttu-id="51895-149">Popis</span><span class="sxs-lookup"><span data-stu-id="51895-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="51895-150">**Název parametru objektu blob**</span><span class="sxs-lookup"><span data-stu-id="51895-150">**Blob parameter name**</span></span> | <span data-ttu-id="51895-151">thumbnail</span><span class="sxs-lookup"><span data-stu-id="51895-151">thumbnail</span></span> | <span data-ttu-id="51895-152">Funkce používá parametr s tímto názvem k zápisu miniatury.</span><span class="sxs-lookup"><span data-stu-id="51895-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="51895-153">**Používat návratovou hodnotu funkce**</span><span class="sxs-lookup"><span data-stu-id="51895-153">**Use function return value**</span></span> | <span data-ttu-id="51895-154">Ne</span><span class="sxs-lookup"><span data-stu-id="51895-154">No</span></span> |  |
    | <span data-ttu-id="51895-155">**Cesta**</span><span class="sxs-lookup"><span data-stu-id="51895-155">**Path**</span></span> | <span data-ttu-id="51895-156">thumbnails/{name}</span><span class="sxs-lookup"><span data-stu-id="51895-156">thumbnails/{name}</span></span> | <span data-ttu-id="51895-157">Miniatury se zapisují do kontejneru **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="51895-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="51895-158">**Připojení k účtu úložiště**</span><span class="sxs-lookup"><span data-stu-id="51895-158">**Storage account connection**</span></span> | <span data-ttu-id="51895-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="51895-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="51895-160">Použijte proměnnou prostředí s připojovacím řetězcem, kterou jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="51895-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Zadání nastavení pro výstupní vazbu objektu blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="51895-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="51895-162">**JavaScript**</span></span>

    1. <span data-ttu-id="51895-163">(JavaScript) Kliknutím na **Rozšířený editor** v pravém horním rohu okna zobrazte JSON představující vazby.</span><span class="sxs-lookup"><span data-stu-id="51895-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="51895-164">(JavaScript) Do vazby `blobTrigger` přidejte vlastnost `dataType` s hodnotou `binary`.</span><span class="sxs-lookup"><span data-stu-id="51895-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="51895-165">Tím se vazba nakonfiguruje tak, aby do funkce předávala obsah objektu blob jako binární data.</span><span class="sxs-lookup"><span data-stu-id="51895-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. <span data-ttu-id="51895-166">Kliknutím na **Uložit** vytvořte novou vazbu.</span><span class="sxs-lookup"><span data-stu-id="51895-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="51895-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="51895-167">**C#**</span></span>

    1. <span data-ttu-id="51895-168">(C#) Výběrem názvu funkce **ResizeImage** na levém navigačním panelu otevřete zdrojový kód funkce.</span><span class="sxs-lookup"><span data-stu-id="51895-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="51895-169">(C#) Funkce ke generování miniatur vyžaduje balíček NuGet **ImageResizer**.</span><span class="sxs-lookup"><span data-stu-id="51895-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="51895-170">Balíčky NuGet se do funkcí jazyka C# přidávají pomocí souboru **project.json**.</span><span class="sxs-lookup"><span data-stu-id="51895-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="51895-171">Pokud chcete tento soubor vytvořit, kliknutím na **Zobrazit soubory** na pravé straně zobrazte soubory, ze kterých se funkce skládá.</span><span class="sxs-lookup"><span data-stu-id="51895-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="51895-172">(C#) Klikněte na **Přidat** a přidejte nový soubor **project.json**.</span><span class="sxs-lookup"><span data-stu-id="51895-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="51895-173">(C#) Zkopírujte do nově vytvořeného souboru obsah souboru [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json).</span><span class="sxs-lookup"><span data-stu-id="51895-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="51895-174">Uložte soubor.</span><span class="sxs-lookup"><span data-stu-id="51895-174">Save the file.</span></span> <span data-ttu-id="51895-175">Balíčky se automaticky obnoví při aktualizaci souboru.</span><span class="sxs-lookup"><span data-stu-id="51895-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![Soubor project.json s balíčkem ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="51895-177">(C#) V části **Zobrazit soubory** vyberte soubor **run.csx** a nahraďte jeho obsah obsahem souboru [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="51895-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="51895-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="51895-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="51895-179">(JavaScript) Tato funkce ke změně velikosti fotografie vyžaduje balíček `jimp` z npm.</span><span class="sxs-lookup"><span data-stu-id="51895-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="51895-180">Pokud chcete tento balíček npm nainstalovat, na levém navigačním panelu klikněte na název aplikace Function App a pak klikněte na **Funkce platformy**.</span><span class="sxs-lookup"><span data-stu-id="51895-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="51895-181">(JavaScript) Kliknutím na **Konzola** zobrazte okno konzoly.</span><span class="sxs-lookup"><span data-stu-id="51895-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="51895-182">(JavaScript) V konzole spusťte příkaz `npm install jimp`.</span><span class="sxs-lookup"><span data-stu-id="51895-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="51895-183">Dokončení operace může pár minut trvat.</span><span class="sxs-lookup"><span data-stu-id="51895-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="51895-184">(JavaScript) Kliknutím na název funkce **ResizeImage** v levém navigačním panelu zobrazte funkci a pak nahraďte veškerý obsah souboru **index.js** obsahem souboru [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="51895-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="51895-185">Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.</span><span class="sxs-lookup"><span data-stu-id="51895-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="51895-186">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="51895-186">Click **Save**.</span></span> <span data-ttu-id="51895-187">Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně uložila a nedošlo k žádným chybám.</span><span class="sxs-lookup"><span data-stu-id="51895-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="51895-188">Test funkce bez serveru</span><span class="sxs-lookup"><span data-stu-id="51895-188">Test the serverless function</span></span>

1. <span data-ttu-id="51895-189">Otevřete aplikaci v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="51895-189">Open the application in a browser.</span></span> <span data-ttu-id="51895-190">Vyberte soubor obrázku a nahrajte ho.</span><span class="sxs-lookup"><span data-stu-id="51895-190">Select an image file and upload it.</span></span> <span data-ttu-id="51895-191">Nahrávání se dokončí, ale protože jsme ještě nepřidali schopnost zobrazit obrázky, v aplikaci se nahraná fotografie nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="51895-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="51895-192">Ve službě Cloud Shell ověřte nahrání obrázku do kontejneru **images**.</span><span class="sxs-lookup"><span data-stu-id="51895-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="51895-193">Ověřte vytvoření miniatury v kontejneru **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="51895-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="51895-194">Získejte adresu URL miniatury.</span><span class="sxs-lookup"><span data-stu-id="51895-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="51895-195">Otevřete tuto adresu URL v prohlížeči a ověřte správné vytvoření miniatury.</span><span class="sxs-lookup"><span data-stu-id="51895-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="51895-196">Než budete pokračovat k dalšímu kurzu, odstraňte všechny soubory v kontejnerech **images** a **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="51895-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="51895-197">Souhrn</span><span class="sxs-lookup"><span data-stu-id="51895-197">Summary</span></span>

<span data-ttu-id="51895-198">V tomto modulu jste vytvořili funkci bez serveru, která při každém nahrání obrázku do kontejneru úložiště objektů blob vytvoří jeho miniaturu.</span><span class="sxs-lookup"><span data-stu-id="51895-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="51895-199">Dále se dozvíte, jak pomocí služby Azure Cosmos DB ukládat a vypisovat metadata obrázků.</span><span class="sxs-lookup"><span data-stu-id="51895-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
