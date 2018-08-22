---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079245"
---
<span data-ttu-id="8eff3-103">Úspěšně jste s využitím služeb Azure vytvořili plnohodnotnou aplikaci bez serveru.</span><span class="sxs-lookup"><span data-stu-id="8eff3-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="8eff3-104">Vyčištění prostředků</span><span class="sxs-lookup"><span data-stu-id="8eff3-104">Clean up resources</span></span>

<span data-ttu-id="8eff3-105">Jakmile skončíte s prací s touto aplikací, můžete pomocí následujícího příkazu odstranit všechny prostředky vytvořené v průběhu kurzu:</span><span class="sxs-lookup"><span data-stu-id="8eff3-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="8eff3-106">Po zobrazení výzvy zadejte `y`.</span><span class="sxs-lookup"><span data-stu-id="8eff3-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="8eff3-107">Další kroky</span><span class="sxs-lookup"><span data-stu-id="8eff3-107">Next steps</span></span>

<span data-ttu-id="8eff3-108">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="8eff3-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="8eff3-109">Nakonfigurovat úložiště objektů blob v Azure jako hostitele statického webu a nahraných obrázků.</span><span class="sxs-lookup"><span data-stu-id="8eff3-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="8eff3-110">Nahrát obrázky do úložiště objektů blob v Azure pomocí služby Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="8eff3-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="8eff3-111">Změnit velikost obrázků pomocí služby Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="8eff3-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="8eff3-112">Ukládat metadata obrázků ve službě Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="8eff3-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="8eff3-113">Automaticky generovat titulky obrázků pomocí rozhraní API pro počítačové zpracování obrazu služeb Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="8eff3-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="8eff3-114">Zabezpečit webovou aplikaci prostřednictvím ověřování uživatelů pomocí Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="8eff3-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="8eff3-115">V dalším kurzu Logic Apps se dozvíte, jak ke službě Functions připojit ještě více služeb.</span><span class="sxs-lookup"><span data-stu-id="8eff3-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="8eff3-116">Služba Functions s Logic Apps</span><span class="sxs-lookup"><span data-stu-id="8eff3-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)