---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 51c7d3e64424d499b473f3b138ce249a9cfd0182
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460075"
---
<span data-ttu-id="c08b6-103">Aplikace, kterou vytváříte, je fotogalerie.</span><span class="sxs-lookup"><span data-stu-id="c08b6-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="c08b6-104">Pomocí JavaScriptu na straně klienta volá rozhraní API za účelem nahrání a zobrazení obrázků.</span><span class="sxs-lookup"><span data-stu-id="c08b6-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="c08b6-105">V tomto modulu vytvoříte rozhraní API s funkcí bez serveru, která vygeneruje časově omezenou adresu URL pro nahrání obrázku.</span><span class="sxs-lookup"><span data-stu-id="c08b6-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="c08b6-106">Webová aplikace použije vygenerovanou adresu URL k nahrání obrázku do úložiště objektů blob pomocí [rozhraní REST API úložiště objektů Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="c08b6-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="c08b6-107">Vytvoření kontejneru úložiště objektů blob pro obrázky</span><span class="sxs-lookup"><span data-stu-id="c08b6-107">Create a blob storage container for images</span></span>

<span data-ttu-id="c08b6-108">Aplikace k nahrávání a hostování obrázků vyžaduje samostatný kontejner úložiště.</span><span class="sxs-lookup"><span data-stu-id="c08b6-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="c08b6-109">Ujistěte se, že jste stále přihlášeni ve službě Cloud Shell (Bash).</span><span class="sxs-lookup"><span data-stu-id="c08b6-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="c08b6-110">Pokud ne, výběrem možnosti **Vstoupit do detailního režimu** otevřete okno služby Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="c08b6-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="c08b6-111">Vytvořte ve svém účtu úložiště nový kontejner **images** s veřejným přístupem ke všem objektům blob.</span><span class="sxs-lookup"><span data-stu-id="c08b6-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="c08b6-112">Vytvoření aplikace Azure Function App</span><span class="sxs-lookup"><span data-stu-id="c08b6-112">Create an Azure Function app</span></span>

<span data-ttu-id="c08b6-113">Azure Functions je služba umožňující spouštění funkcí bez serveru.</span><span class="sxs-lookup"><span data-stu-id="c08b6-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="c08b6-114">Funkci bez serveru můžou aktivovat (volat) události, jako je požadavek HTTP nebo vytvoření objektu blob v kontejneru úložiště.</span><span class="sxs-lookup"><span data-stu-id="c08b6-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="c08b6-115">Aplikace funkcí Azure je kontejner pro jednu nebo několik funkcí bez serveru.</span><span class="sxs-lookup"><span data-stu-id="c08b6-115">An Azure Function app is a container for one or more serverless functions.</span></span>

<span data-ttu-id="c08b6-116">Vytvořte novou aplikaci funkcí Azure s jedinečným názvem **first-serverless-app** ve skupině prostředků, kterou jste vytvořili dříve.</span><span class="sxs-lookup"><span data-stu-id="c08b6-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="c08b6-117">Aplikace funkcí vyžaduje účet úložiště. V tomto kurzu použijete existující účet úložiště.</span><span class="sxs-lookup"><span data-stu-id="c08b6-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a><span data-ttu-id="c08b6-118">Konfigurace aplikace Function App</span><span class="sxs-lookup"><span data-stu-id="c08b6-118">Configure the function app</span></span>

<span data-ttu-id="c08b6-119">Aplikace funkcí v tomto kurzu vyžaduje modul runtime služby Functions verze 1.x.</span><span class="sxs-lookup"><span data-stu-id="c08b6-119">The function app in this tutorial requires version 1.x of the Functions runtime.</span></span> <span data-ttu-id="c08b6-120">Nastavení aplikace `FUNCTIONS_WORKER_RUNTIME` na `~1` připne aplikaci funkcí k nejnovější verzi 1.x.</span><span class="sxs-lookup"><span data-stu-id="c08b6-120">Setting the `FUNCTIONS_WORKER_RUNTIME` application setting to `~1` pins the function app to the latest 1.x version.</span></span> <span data-ttu-id="c08b6-121">Pomocí příkazu [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) nastavte nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c08b6-121">Set application settings with the [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) command.</span></span>

<span data-ttu-id="c08b6-122">V následujícím příkazu Azure CLI je <app_name> název vaší aplikace funkcí.</span><span class="sxs-lookup"><span data-stu-id="c08b6-122">In the following Azure CLI command, \`<app_name> is the name of your function app.</span></span>

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_WORKER_RUNTIME=~1
```

## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="c08b6-123">Vytvoření funkce bez serveru aktivované protokolem HTTP</span><span class="sxs-lookup"><span data-stu-id="c08b6-123">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="c08b6-124">Webová aplikace fotogalerie do funkce bez serveru odešle požadavek HTTP na vygenerování časově omezené adresy URL pro zabezpečené nahrání obrázku do úložiště objektů blob.</span><span class="sxs-lookup"><span data-stu-id="c08b6-124">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="c08b6-125">Funkce se aktivuje požadavkem HTTP a pomocí sady SDK služby Azure Storage vygeneruje a vrátí zabezpečenou adresu URL.</span><span class="sxs-lookup"><span data-stu-id="c08b6-125">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="c08b6-126">Po vytvoření aplikace funkcí ji pomocí vyhledávacího pole vyhledejte na webu Azure Portal a kliknutím ji otevřete.</span><span class="sxs-lookup"><span data-stu-id="c08b6-126">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![Otevření aplikace funkcí](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="c08b6-128">V levém navigačním panelu okna aplikace funkcí najeďte myší na **Funkce** a kliknutím na **+** začněte vytvářet novou funkci bez serveru.</span><span class="sxs-lookup"><span data-stu-id="c08b6-128">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Vytvoření nové funkce](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="c08b6-130">Kliknutím na **Vlastní funkce** zobrazte seznam šablon funkcí.</span><span class="sxs-lookup"><span data-stu-id="c08b6-130">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="c08b6-131">Vyhledejte šablonu **HttpTrigger** a klikněte na jazyk, který chcete použít (C# nebo JavaScript).</span><span class="sxs-lookup"><span data-stu-id="c08b6-131">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="c08b6-132">Použijte následující hodnoty k vytvoření funkce, která vygeneruje adresu URL pro nahrání objektu blob.</span><span class="sxs-lookup"><span data-stu-id="c08b6-132">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="c08b6-133">Nastavení</span><span class="sxs-lookup"><span data-stu-id="c08b6-133">Setting</span></span>      |  <span data-ttu-id="c08b6-134">Navrhovaná hodnota</span><span class="sxs-lookup"><span data-stu-id="c08b6-134">Suggested value</span></span>   | <span data-ttu-id="c08b6-135">Popis</span><span class="sxs-lookup"><span data-stu-id="c08b6-135">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="c08b6-136">**Jazyk**</span><span class="sxs-lookup"><span data-stu-id="c08b6-136">**Language**</span></span> | <span data-ttu-id="c08b6-137">C# nebo JavaScript</span><span class="sxs-lookup"><span data-stu-id="c08b6-137">C# or JavaScript</span></span> | <span data-ttu-id="c08b6-138">Vyberte jazyk, který chcete použít.</span><span class="sxs-lookup"><span data-stu-id="c08b6-138">Select the language you want to use.</span></span> |
    | <span data-ttu-id="c08b6-139">**Pojmenujte svoji funkci**</span><span class="sxs-lookup"><span data-stu-id="c08b6-139">**Name your function**</span></span> | <span data-ttu-id="c08b6-140">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="c08b6-140">GetUploadUrl</span></span> | <span data-ttu-id="c08b6-141">Zadejte tento název přesně tak, jak je uvedený, aby aplikace funkci zjistila.</span><span class="sxs-lookup"><span data-stu-id="c08b6-141">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="c08b6-142">**Úroveň autorizace**</span><span class="sxs-lookup"><span data-stu-id="c08b6-142">**Authorization level**</span></span> | <span data-ttu-id="c08b6-143">Anonymní</span><span class="sxs-lookup"><span data-stu-id="c08b6-143">Anonymous</span></span> | <span data-ttu-id="c08b6-144">Povolte k funkci veřejný přístup.</span><span class="sxs-lookup"><span data-stu-id="c08b6-144">Allow the function to be accessed publicly.</span></span> |

    ![Zadání nastavení nové funkce aktivované protokolem HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="c08b6-146">Funkci vytvoříte kliknutím na **Vytvořit**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-146">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="c08b6-147">**C#**</span><span class="sxs-lookup"><span data-stu-id="c08b6-147">**C#**</span></span> 

    1. <span data-ttu-id="c08b6-148">Jakmile se zobrazí zdrojový kód funkce, nahraďte veškerý obsah souboru **run.csx** obsahem souboru [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="c08b6-148">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="c08b6-149">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="c08b6-149">**JavaScript**</span></span> 

    1. <span data-ttu-id="c08b6-150">(JavaScript) Tato funkce k vygenerování tokenu sdíleného přístupového podpisu (SAS) nezbytného k vytvoření zabezpečené adresy URL vyžaduje balíček `azure-storage` z npm.</span><span class="sxs-lookup"><span data-stu-id="c08b6-150">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="c08b6-151">Pokud chcete tento balíček npm nainstalovat, na levém navigačním panelu klikněte na název aplikace Function App a pak klikněte na **Funkce platformy**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-151">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="c08b6-152">(JavaScript) Kliknutím na **Konzola** zobrazte okno konzoly.</span><span class="sxs-lookup"><span data-stu-id="c08b6-152">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Otevření okna konzoly](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="c08b6-154">(JavaScript) Spuštěním příkazu `cd d:\home\site\wwwroot` se ujistěte, že je aktuální adresář **d:\home\site\wwwroot**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-154">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="c08b6-155">(JavaScript) Spuštěním příkazu `npm init -y` vytvořte prázdný soubor **package.json**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-155">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="c08b6-156">(JavaScript) Spuštěním příkazu `npm install --save azure-storage` v konzole nainstalujte balíček a uložte ho do souboru **package.json**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-156">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="c08b6-157">Dokončení operace může pár minut trvat.</span><span class="sxs-lookup"><span data-stu-id="c08b6-157">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="c08b6-158">(JavaScript) Kliknutím na název funkce (**GetUploadUrl**) v levém navigačním panelu zobrazte funkci a pak nahraďte veškerý obsah souboru **index.js** obsahem souboru [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="c08b6-158">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![Aktualizovaný soubor index.js](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="c08b6-160">Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.</span><span class="sxs-lookup"><span data-stu-id="c08b6-160">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="c08b6-161">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-161">Click **Save**.</span></span> <span data-ttu-id="c08b6-162">Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně zkompilovala.</span><span class="sxs-lookup"><span data-stu-id="c08b6-162">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="c08b6-163">Funkce vygeneruje tzv. adresu URL sdíleného přístupového podpisu (SAS), která slouží k nahrání souboru do úložiště objektů blob.</span><span class="sxs-lookup"><span data-stu-id="c08b6-163">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="c08b6-164">Tato adresa URL SAS má krátkodobou platnost a umožňuje nahrání pouze jednoho souboru.</span><span class="sxs-lookup"><span data-stu-id="c08b6-164">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="c08b6-165">Další informace o [používání sdílených přístupových podpisů](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) najdete v dokumentaci k úložišti objektů blob.</span><span class="sxs-lookup"><span data-stu-id="c08b6-165">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="c08b6-166">Přidání proměnné prostředí pro připojovací řetězec úložiště</span><span class="sxs-lookup"><span data-stu-id="c08b6-166">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="c08b6-167">Funkce, kterou jste právě vytvořili, vyžaduje připojovací řetězec účtu úložiště, aby mohla vygenerovat adresu URL SAS.</span><span class="sxs-lookup"><span data-stu-id="c08b6-167">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="c08b6-168">Místo pevného zakódování připojovacího řetězce do těla funkce ho můžete uložit jako nastavení aplikace.</span><span class="sxs-lookup"><span data-stu-id="c08b6-168">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="c08b6-169">Nastavení aplikace jsou přístupná jako proměnné prostředí pro všechny funkce v aplikaci funkcí.</span><span class="sxs-lookup"><span data-stu-id="c08b6-169">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="c08b6-170">Ve službě Cloud Shell odešlete dotaz na připojovací řetězec účtu úložiště a uložte ho do proměnné prostředí Bash **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-170">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="c08b6-171">Ověřte úspěšné nastavení proměnné.</span><span class="sxs-lookup"><span data-stu-id="c08b6-171">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="c08b6-172">Vytvořte nové nastavení aplikace **AZURE_STORAGE_CONNECTION_STRING** s použitím hodnoty uložené v předchozím kroku.</span><span class="sxs-lookup"><span data-stu-id="c08b6-172">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="c08b6-173">Ověřte, že výstup příkazu obsahuje nové nastavení aplikace se správnou hodnotou.</span><span class="sxs-lookup"><span data-stu-id="c08b6-173">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="c08b6-174">Test funkce bez serveru</span><span class="sxs-lookup"><span data-stu-id="c08b6-174">Test the serverless function</span></span>

<span data-ttu-id="c08b6-175">Kromě vytváření a úprav funkcí poskytuje Azure Portal také integrovaný nástroj pro testování funkcí.</span><span class="sxs-lookup"><span data-stu-id="c08b6-175">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="c08b6-176">Pokud chcete otestovat funkci bez serveru HTTP, kliknutím na kartu **Test** na pravé straně okna kódu rozbalte panel testování.</span><span class="sxs-lookup"><span data-stu-id="c08b6-176">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="c08b6-177">U položky **Metoda HTTP** změňte hodnotu na **GET**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-177">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="c08b6-178">V části **Dotaz** klikněte na *Přidat parametr*\* a přidejte následující parametr:</span><span class="sxs-lookup"><span data-stu-id="c08b6-178">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="c08b6-179">jméno</span><span class="sxs-lookup"><span data-stu-id="c08b6-179">name</span></span>      |  <span data-ttu-id="c08b6-180">hodnota</span><span class="sxs-lookup"><span data-stu-id="c08b6-180">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="c08b6-181">filename</span><span class="sxs-lookup"><span data-stu-id="c08b6-181">filename</span></span> | <span data-ttu-id="c08b6-182">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="c08b6-182">image1.jpg</span></span> |

1. <span data-ttu-id="c08b6-183">Kliknutím na **Spustit** na panelu testování odešlete do funkce požadavek HTTP.</span><span class="sxs-lookup"><span data-stu-id="c08b6-183">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="c08b6-184">Funkce ve výstupu vrátí adresu URL pro nahrání.</span><span class="sxs-lookup"><span data-stu-id="c08b6-184">The function returns an upload URL in the output.</span></span> <span data-ttu-id="c08b6-185">Spuštění funkce se zobrazí na panelu protokolů.</span><span class="sxs-lookup"><span data-stu-id="c08b6-185">The function execution appears in the logs panel.</span></span>

    ![Protokoly ukazující úspěšné spuštění funkce](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="c08b6-187">Konfigurace CORS v aplikaci funkcí</span><span class="sxs-lookup"><span data-stu-id="c08b6-187">Configure CORS in the function app</span></span>

<span data-ttu-id="c08b6-188">Vzhledem k tomu, že se front-end aplikace hostuje v úložišti objektů blob, má jiný název domény než aplikace funkcí Azure.</span><span class="sxs-lookup"><span data-stu-id="c08b6-188">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="c08b6-189">Aby JavaScript na straně klienta mohl úspěšně volat funkci, kterou jste právě vytvořili, je potřeba v aplikaci funkcí nakonfigurovat sdílení prostředků různého původu (CORS).</span><span class="sxs-lookup"><span data-stu-id="c08b6-189">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="c08b6-190">V levém navigačním panelu okna aplikace funkcí klikněte na název vaší aplikace funkcí.</span><span class="sxs-lookup"><span data-stu-id="c08b6-190">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="c08b6-191">Kliknutím na **Funkce platformy** zobrazte seznam pokročilých funkcí.</span><span class="sxs-lookup"><span data-stu-id="c08b6-191">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="c08b6-192">V části **API** klikněte na **CORS**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-192">Under **API**, click **CORS**.</span></span>

    ![Výběr CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="c08b6-194">Přidejte povolení původu pro adresu URL aplikace z předchozího modulu a vynechte koncový znak **/** (příklad: `https://firstserverlessweb.z4.web.core.windows.net`).</span><span class="sxs-lookup"><span data-stu-id="c08b6-194">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![Nastavení CORS ukazující přidání adresy URL webové aplikace bez serveru](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="c08b6-196">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-196">Click **Save**.</span></span>

1. <span data-ttu-id="c08b6-197">C#</span><span class="sxs-lookup"><span data-stu-id="c08b6-197">C#</span></span>

   1. <span data-ttu-id="c08b6-198">(C#) Vraťte se do funkce `GetUploadUrl` a pak vyberte kartu **Integrovat**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-198">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="c08b6-199">(C#) V části Vybrané metody HTTP vyberte **MOŽNOSTI**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-199">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="c08b6-200">Měly by být vybrané možnosti **GET**, **POST** a **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-200">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="c08b6-201">CORS využívá metodu OPTIONS, která v případě funkcí jazyka C# není vybraná ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="c08b6-201">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="c08b6-202">(C#) Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-202">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="c08b6-203">Pořád ještě na portálu přejděte k aplikaci funkcí, vyberte kartu **Přehled** a kliknutím na **Restartovat** zajistěte, aby se změny CORS projevily.</span><span class="sxs-lookup"><span data-stu-id="c08b6-203">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="c08b6-204">Konfigurace CORS v účtu úložiště</span><span class="sxs-lookup"><span data-stu-id="c08b6-204">Configure CORS in the Storage account</span></span>

<span data-ttu-id="c08b6-205">Vzhledem k tomu, že aplikace také provádí volání JavaScriptu na straně klienta do služby Blob Storage za účelem nahrání souborů, je potřeba CORS nakonfigurovat také v účtu úložiště.</span><span class="sxs-lookup"><span data-stu-id="c08b6-205">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="c08b6-206">Spuštěním následujícího příkazu povolte nahrávání souborů jakéhokoli původu do účtu úložiště.</span><span class="sxs-lookup"><span data-stu-id="c08b6-206">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="c08b6-207">Úprava webové aplikace pro nahrávání obrázků</span><span class="sxs-lookup"><span data-stu-id="c08b6-207">Modify the web app to upload images</span></span>

<span data-ttu-id="c08b6-208">Webová aplikace načítá nastavení ze souboru **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-208">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="c08b6-209">V následujících krocích tento soubor vytvoříte pomocí služby Cloud Shell a pak nastavíte `window.apiBaseUrl` na adresu URL aplikace funkcí a `window.blobBaseUrl` na adresu URL koncového bodu služby Azure Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="c08b6-209">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="c08b6-210">Ve službě Cloud Shell se ujistěte, že je aktuálním adresářem složka **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-210">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="c08b6-211">Odešlete dotaz na adresu URL aplikace funkcí a uložte ji do proměnné prostředí Bash **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-211">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="c08b6-212">Ověřte správné nastavení proměnné.</span><span class="sxs-lookup"><span data-stu-id="c08b6-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="c08b6-213">Pokud chcete nastavit základní identifikátor URI pro volání rozhraní API do vaší aplikace funkcí, vytvořte soubor **settings.js** a přidejte do něj adresu URL aplikace funkcí, jak je znázorněno v následujícím příkladu.</span><span class="sxs-lookup"><span data-stu-id="c08b6-213">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="c08b6-214">Změnu můžete provést spuštěním následujícího příkazu nebo pomocí editoru příkazového řádku, jako je VIM.</span><span class="sxs-lookup"><span data-stu-id="c08b6-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="c08b6-215">Ověřte úspěšný zápis souboru.</span><span class="sxs-lookup"><span data-stu-id="c08b6-215">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="c08b6-216">Odešlete dotaz na základní adresu URL služby Blob Storage a uložte ji do proměnné prostředí Bash **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-216">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="c08b6-217">Ověřte správné nastavení proměnné.</span><span class="sxs-lookup"><span data-stu-id="c08b6-217">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="c08b6-218">Pokud chcete nastavit základní identifikátor URI pro volání rozhraní API do vaší aplikace funkcí, přidejte do souboru **settings.js** adresu URL úložiště, jak je znázorněno na následujícím řádku kódu.</span><span class="sxs-lookup"><span data-stu-id="c08b6-218">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="c08b6-219">Změnu můžete provést spuštěním následujícího příkazu nebo pomocí editoru příkazového řádku, jako je VIM.</span><span class="sxs-lookup"><span data-stu-id="c08b6-219">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="c08b6-220">Ověřte, že se soubor úspěšně zapsal a obsahuje teď 2 řádky.</span><span class="sxs-lookup"><span data-stu-id="c08b6-220">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="c08b6-221">Nahrajte soubor do úložiště objektů blob.</span><span class="sxs-lookup"><span data-stu-id="c08b6-221">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="c08b6-222">Test webové aplikace</span><span class="sxs-lookup"><span data-stu-id="c08b6-222">Test the web application</span></span>

<span data-ttu-id="c08b6-223">Aplikace galerie v tuto chvíli umožňuje nahrát obrázek do úložiště objektů blob, ale ještě nedokáže zobrazit obrázky.</span><span class="sxs-lookup"><span data-stu-id="c08b6-223">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="c08b6-224">Pokusí se zavolat funkci `GetImages`, která ještě neexistuje, protože ji vytvoříte v pozdějším modulu.</span><span class="sxs-lookup"><span data-stu-id="c08b6-224">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="c08b6-225">Toto volání selže a webová stránka se zdánlivě zasekne na probíhající analýze, ale vybraný obrázek se úspěšně nahraje.</span><span class="sxs-lookup"><span data-stu-id="c08b6-225">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="c08b6-226">Úspěšné nahrání obrázku můžete ověřit tak, že zkontrolujete obsah kontejneru **images** na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="c08b6-226">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="c08b6-227">V okně prohlížeče přejděte do aplikace.</span><span class="sxs-lookup"><span data-stu-id="c08b6-227">In a browser window, browse to the application.</span></span> <span data-ttu-id="c08b6-228">Vyberte soubor obrázku a nahrajte ho.</span><span class="sxs-lookup"><span data-stu-id="c08b6-228">Select an image file and upload it.</span></span> <span data-ttu-id="c08b6-229">Nahrávání se dokončí, ale protože jsme ještě nepřidali schopnost zobrazit obrázky, v aplikaci se nahraná fotografie nezobrazí.</span><span class="sxs-lookup"><span data-stu-id="c08b6-229">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="c08b6-230">(Webová stránka se zdánlivě zasekne na probíhající analýze obrázku, později to opravíte.)</span><span class="sxs-lookup"><span data-stu-id="c08b6-230">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="c08b6-231">Ve službě Cloud Shell ověřte nahrání obrázku do kontejneru **images**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-231">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="c08b6-232">Než budete pokračovat k dalšímu kurzu, odstraňte všechny soubory v kontejneru **images**.</span><span class="sxs-lookup"><span data-stu-id="c08b6-232">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="c08b6-233">Souhrn</span><span class="sxs-lookup"><span data-stu-id="c08b6-233">Summary</span></span>

<span data-ttu-id="c08b6-234">V tomto modulu jste vytvořili aplikaci funkcí Azure a zjistili jste, jak pomocí funkce bez serveru umožnit webové aplikaci nahrávat obrázky do úložiště objektů blob.</span><span class="sxs-lookup"><span data-stu-id="c08b6-234">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="c08b6-235">Dále se dozvíte, jak pro nahrané obrázky vytvářet miniatury pomocí funkce bez serveru aktivované objektem blob.</span><span class="sxs-lookup"><span data-stu-id="c08b6-235">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
