---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459989"
---
<span data-ttu-id="bb424-103">Úspěšně jste s využitím služeb Azure vytvořili plnohodnotnou aplikaci bez serveru.</span><span class="sxs-lookup"><span data-stu-id="bb424-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="bb424-104">Vyčištění prostředků</span><span class="sxs-lookup"><span data-stu-id="bb424-104">Clean up resources</span></span>

<span data-ttu-id="bb424-105">Jakmile skončíte s prací s touto aplikací, můžete pomocí následujícího příkazu odstranit všechny prostředky vytvořené v průběhu kurzu:</span><span class="sxs-lookup"><span data-stu-id="bb424-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="bb424-106">Po zobrazení výzvy zadejte `y`.</span><span class="sxs-lookup"><span data-stu-id="bb424-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="bb424-107">Další kroky</span><span class="sxs-lookup"><span data-stu-id="bb424-107">Next steps</span></span>

<span data-ttu-id="bb424-108">V tomto kurzu jste se naučili:</span><span class="sxs-lookup"><span data-stu-id="bb424-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="bb424-109">Nakonfigurovat úložiště objektů blob v Azure jako hostitele statického webu a nahraných obrázků.</span><span class="sxs-lookup"><span data-stu-id="bb424-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="bb424-110">Nahrát obrázky do úložiště objektů blob v Azure pomocí služby Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="bb424-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="bb424-111">Změnit velikost obrázků pomocí služby Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="bb424-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="bb424-112">Ukládat metadata obrázků ve službě Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="bb424-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="bb424-113">Automaticky generovat titulky obrázků pomocí rozhraní API pro počítačové zpracování obrazu služeb Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="bb424-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="bb424-114">Zabezpečit webovou aplikaci prostřednictvím ověřování uživatelů pomocí Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="bb424-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="bb424-115">V dalším kurzu Logic Apps se dozvíte, jak ke službě Functions připojit ještě více služeb.</span><span class="sxs-lookup"><span data-stu-id="bb424-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="bb424-116">Služba Functions s Logic Apps</span><span class="sxs-lookup"><span data-stu-id="bb424-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
