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
ms.openlocfilehash: 7e51d3cd0533b4fb64d7dfa783af55266d536f54
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079256"
---
<span data-ttu-id="e7568-103">Aplikace je v tuto chvíli funkční galerií, která umožňuje nahrávat a zobrazovat obrázky.</span><span class="sxs-lookup"><span data-stu-id="e7568-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="e7568-104">V tomto modulu zjistíte, jak pomocí rozhraní API pro počítačové zpracování obrazu ze služeb Microsoft Cognitive Services generovat titulky pro nahrané obrázky a ukládat tyto titulky společně s metadaty obrázků ve službě Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7568-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="e7568-105">Vytvoření účtu rozhraní API pro počítačové zpracování obrazu</span><span class="sxs-lookup"><span data-stu-id="e7568-105">Create a Computer Vision account</span></span>

<span data-ttu-id="e7568-106">Microsoft Cognitive Services je kolekce služeb, které vývojářům umožňují vytvářet inteligentnější aplikace.</span><span class="sxs-lookup"><span data-stu-id="e7568-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="e7568-107">Rozhraní API pro počítačové zpracování obrazu je služba bez serveru, která s využitím pokročilých algoritmů zpracovává obrázky a vrací informace o jednotlivých obrázcích.</span><span class="sxs-lookup"><span data-stu-id="e7568-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="e7568-108">Nabízí bezplatnou úroveň, která poskytuje až 5 000 volání rozhraní API za měsíc.</span><span class="sxs-lookup"><span data-stu-id="e7568-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="e7568-109">Ujistěte se, že jste stále přihlášeni ve službě Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="e7568-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="e7568-110">Pokud ne, výběrem možnosti **Vstoupit do detailního režimu** otevřete okno služby Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="e7568-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="e7568-111">Vytvořte nový účet služby Cognitive Services typu **ComputerVision** a použijte pro něj jedinečný název v rámci vaší skupiny prostředků.</span><span class="sxs-lookup"><span data-stu-id="e7568-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="e7568-112">Pokud chcete využít bezplatnou úroveň, jako skladovou položku použijte **F0**.</span><span class="sxs-lookup"><span data-stu-id="e7568-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="e7568-113">Pokud již máte existující účet rozhraní API pro počítačové zpracování obrazu, možná budete muset vytvořit účet úrovně Standard (S1), za tu se však můžou účtovat určité poplatky.</span><span class="sxs-lookup"><span data-stu-id="e7568-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="e7568-114">Vytvoření nastavení aplikace Function App pro adresu URL a klíč rozhraní API pro počítačové zpracování obrazu</span><span class="sxs-lookup"><span data-stu-id="e7568-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="e7568-115">K volání rozhraní API pro počítačové zpracování obrazu se vyžaduje adresa URL a klíč.</span><span class="sxs-lookup"><span data-stu-id="e7568-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="e7568-116">Získejte klíč a adresu URL rozhraní API pro počítačové zpracování obrazu a uložte je do proměnných prostředí Bash.</span><span class="sxs-lookup"><span data-stu-id="e7568-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="e7568-117">Vytvořte v aplikaci funkcí nastavení aplikace **COMP_VISION_KEY** a **COMP_VISION_URL**.</span><span class="sxs-lookup"><span data-stu-id="e7568-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="e7568-118">Volání rozhraní API pro počítačové zpracování obrazu z funkce ResizeImage</span><span class="sxs-lookup"><span data-stu-id="e7568-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="e7568-119">V následujících krocích upravíte funkci **ResizeImage** tak, aby volala rozhraní API pro počítačové zpracování obrazu za účelem vytvoření popisu ke každému nahranému obrázku a uložení popisu ve službě Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7568-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="e7568-120">Otevřete svou aplikaci funkcí na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="e7568-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="e7568-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="e7568-121">**C#**</span></span>

    1. <span data-ttu-id="e7568-122">(C#) V levém navigačním panelu vyhledejte funkci **ResizeImage** a otevřete okno s jejím kódem.</span><span class="sxs-lookup"><span data-stu-id="e7568-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="e7568-123">(C#) Nahraďte kód obsahem souboru [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span><span class="sxs-lookup"><span data-stu-id="e7568-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="e7568-124">Tento kód pomocí `HttpClient` volá rozhraní API pro počítačové zpracování obrazu a ukládá výsledek volání ve službě Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="e7568-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="e7568-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="e7568-125">**JavaScript**</span></span>

    1. <span data-ttu-id="e7568-126">(JavaScript) Tato funkce k volání rozhraní API pro počítačové zpracování obrazu přes protokol HTTP vyžaduje balíček `axios` z npm.</span><span class="sxs-lookup"><span data-stu-id="e7568-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="e7568-127">Pokud chcete tento balíček npm nainstalovat, na levém navigačním panelu klikněte na název aplikace Function App a pak klikněte na **Funkce platformy**.</span><span class="sxs-lookup"><span data-stu-id="e7568-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="e7568-128">(JavaScript) Kliknutím na **Konzola** zobrazte okno konzoly.</span><span class="sxs-lookup"><span data-stu-id="e7568-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="e7568-129">(JavaScript) V konzole spusťte příkaz `npm install axios`.</span><span class="sxs-lookup"><span data-stu-id="e7568-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="e7568-130">Dokončení operace může pár minut trvat.</span><span class="sxs-lookup"><span data-stu-id="e7568-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="e7568-131">(JavaScript) Kliknutím na název funkce (**ResizeImage**) v levém navigačním panelu zobrazte funkci a pak nahraďte veškerý obsah souboru **index.js** obsahem souboru [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span><span class="sxs-lookup"><span data-stu-id="e7568-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="e7568-132">Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.</span><span class="sxs-lookup"><span data-stu-id="e7568-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="e7568-133">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="e7568-133">Click **Save**.</span></span> <span data-ttu-id="e7568-134">Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně uložila a nedošlo k žádným chybám.</span><span class="sxs-lookup"><span data-stu-id="e7568-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="e7568-135">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="e7568-135">Test the application</span></span>

1. <span data-ttu-id="e7568-136">Otevřete aplikaci v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="e7568-136">Open the application in a browser.</span></span> <span data-ttu-id="e7568-137">Vyberte soubor obrázku a nahrajte ho.</span><span class="sxs-lookup"><span data-stu-id="e7568-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="e7568-138">Po několika sekundách by se na stránce měla zobrazit miniatura nového obrázku.</span><span class="sxs-lookup"><span data-stu-id="e7568-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="e7568-139">Najeďte na obrázek myší, aby se zobrazil popis vygenerovaný rozhraním API pro počítačové zpracování obrazu.</span><span class="sxs-lookup"><span data-stu-id="e7568-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="e7568-140">Souhrn</span><span class="sxs-lookup"><span data-stu-id="e7568-140">Summary</span></span>

<span data-ttu-id="e7568-141">V tomto modulu jste zjistili, jak pro jednotlivé nahrané obrázky automaticky generovat titulky pomocí rozhraní API pro počítačové zpracování obrazu služeb Microsoft Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="e7568-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="e7568-142">Dále se dozvíte, jak do aplikace přidat ověřování pomocí služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="e7568-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>