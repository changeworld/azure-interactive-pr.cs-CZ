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