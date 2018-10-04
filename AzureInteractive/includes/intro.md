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
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460020"
---
<span data-ttu-id="7e9ce-103">V tomto kurzu nasadíte jednoduchou webovou aplikaci s uživatelským rozhraním v jazyce HTML.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="7e9ce-104">Back-end bez serveru umožní aplikaci nahrávat obrázky a automaticky pro ně získávat popisné titulky.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Spuštěná webová aplikace](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="7e9ce-106">Následující diagram ukazuje služby Azure, které aplikace využívá:</span><span class="sxs-lookup"><span data-stu-id="7e9ce-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="7e9ce-107">Blob Storage obsluhuje statický obsah (soubory HTML, CSS, JS) a ukládá obrázky.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="7e9ce-108">Azure Functions spravuje nahrávání a změnu velikosti obrázků a ukládání metadat.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="7e9ce-109">Cosmos DB ukládá metadata obrázků.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="7e9ce-110">Logic Apps získává titulky obrázků z rozhraní API pro počítačové zpracování obrazu.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="7e9ce-111">Azure Active Directory obstarává ověřování uživatelů.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-111">Azure Active Directory manages user authentication.</span></span>

![Diagram architektury řešení](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="7e9ce-113">V tomto kurzu se naučíte:</span><span class="sxs-lookup"><span data-stu-id="7e9ce-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="7e9ce-114">Nakonfigurovat úložiště objektů blob v Azure jako hostitele statického webu a nahraných obrázků.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="7e9ce-115">Nahrát obrázky do úložiště objektů blob v Azure pomocí služby Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="7e9ce-116">Změnit velikost obrázků pomocí služby Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="7e9ce-117">Ukládat metadata obrázků ve službě Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="7e9ce-118">Automaticky generovat titulky obrázků pomocí rozhraní API pro počítačové zpracování obrazu služeb Cognitive Services.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="7e9ce-119">Zabezpečit webovou aplikaci prostřednictvím ověřování uživatelů pomocí Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="7e9ce-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>
