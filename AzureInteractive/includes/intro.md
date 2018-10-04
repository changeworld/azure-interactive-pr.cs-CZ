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
V tomto kurzu nasadíte jednoduchou webovou aplikaci s uživatelským rozhraním v jazyce HTML. Back-end bez serveru umožní aplikaci nahrávat obrázky a automaticky pro ně získávat popisné titulky.

![Spuštěná webová aplikace](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

Následující diagram ukazuje služby Azure, které aplikace využívá:

1. Blob Storage obsluhuje statický obsah (soubory HTML, CSS, JS) a ukládá obrázky.
2. Azure Functions spravuje nahrávání a změnu velikosti obrázků a ukládání metadat.
3. Cosmos DB ukládá metadata obrázků.
4. Logic Apps získává titulky obrázků z rozhraní API pro počítačové zpracování obrazu.
5. Azure Active Directory obstarává ověřování uživatelů.

![Diagram architektury řešení](media/functions-first-serverless-web-app/0-architecture.jpg)

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Nakonfigurovat úložiště objektů blob v Azure jako hostitele statického webu a nahraných obrázků.
> * Nahrát obrázky do úložiště objektů blob v Azure pomocí služby Azure Functions.
> * Změnit velikost obrázků pomocí služby Azure Functions.
> * Ukládat metadata obrázků ve službě Azure Cosmos DB.
> * Automaticky generovat titulky obrázků pomocí rozhraní API pro počítačové zpracování obrazu služeb Cognitive Services.
> * Zabezpečit webovou aplikaci prostřednictvím ověřování uživatelů pomocí Azure Active Directory.
