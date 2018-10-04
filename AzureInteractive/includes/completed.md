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
Úspěšně jste s využitím služeb Azure vytvořili plnohodnotnou aplikaci bez serveru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile skončíte s prací s touto aplikací, můžete pomocí následujícího příkazu odstranit všechny prostředky vytvořené v průběhu kurzu:

```azurecli
az group delete --name first-serverless-app
```

Po zobrazení výzvy zadejte `y`.  

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Nakonfigurovat úložiště objektů blob v Azure jako hostitele statického webu a nahraných obrázků.
> * Nahrát obrázky do úložiště objektů blob v Azure pomocí služby Azure Functions.
> * Změnit velikost obrázků pomocí služby Azure Functions.
> * Ukládat metadata obrázků ve službě Azure Cosmos DB.
> * Automaticky generovat titulky obrázků pomocí rozhraní API pro počítačové zpracování obrazu služeb Cognitive Services.
> * Zabezpečit webovou aplikaci prostřednictvím ověřování uživatelů pomocí Azure Active Directory.

V dalším kurzu Logic Apps se dozvíte, jak ke službě Functions připojit ještě více služeb. 

> [!div class="nextstepaction"]
> [Služba Functions s Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
