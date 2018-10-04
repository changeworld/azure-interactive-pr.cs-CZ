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
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459990"
---
Aplikace je v tuto chvíli funkční galerií, která umožňuje nahrávat a zobrazovat obrázky. V tomto modulu zjistíte, jak pomocí rozhraní API pro počítačové zpracování obrazu ze služeb Microsoft Cognitive Services generovat titulky pro nahrané obrázky a ukládat tyto titulky společně s metadaty obrázků ve službě Cosmos DB.

## <a name="create-a-computer-vision-account"></a>Vytvoření účtu rozhraní API pro počítačové zpracování obrazu

Microsoft Cognitive Services je kolekce služeb, které vývojářům umožňují vytvářet inteligentnější aplikace. Rozhraní API pro počítačové zpracování obrazu je služba bez serveru, která s využitím pokročilých algoritmů zpracovává obrázky a vrací informace o jednotlivých obrázcích. Nabízí bezplatnou úroveň, která poskytuje až 5 000 volání rozhraní API za měsíc.

1. Ujistěte se, že jste stále přihlášeni ve službě Cloud Shell. Pokud ne, výběrem možnosti **Vstoupit do detailního režimu** otevřete okno služby Cloud Shell. 

1. Vytvořte nový účet služby Cognitive Services typu **ComputerVision** a použijte pro něj jedinečný název v rámci vaší skupiny prostředků. Pokud chcete využít bezplatnou úroveň, jako skladovou položku použijte **F0**. Pokud již máte existující účet rozhraní API pro počítačové zpracování obrazu, možná budete muset vytvořit účet úrovně Standard (S1), za tu se však můžou účtovat určité poplatky.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Vytvoření nastavení aplikace Function App pro adresu URL a klíč rozhraní API pro počítačové zpracování obrazu

K volání rozhraní API pro počítačové zpracování obrazu se vyžaduje adresa URL a klíč.

1. Získejte klíč a adresu URL rozhraní API pro počítačové zpracování obrazu a uložte je do proměnných prostředí Bash.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. Vytvořte v aplikaci funkcí nastavení aplikace **COMP_VISION_KEY** a **COMP_VISION_URL**.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>Volání rozhraní API pro počítačové zpracování obrazu z funkce ResizeImage

V následujících krocích upravíte funkci **ResizeImage** tak, aby volala rozhraní API pro počítačové zpracování obrazu za účelem vytvoření popisu ke každému nahranému obrázku a uložení popisu ve službě Cosmos DB.

1. Otevřete svou aplikaci funkcí na webu Azure Portal.

1. **C#**

    1. (C#) V levém navigačním panelu vyhledejte funkci **ResizeImage** a otevřete okno s jejím kódem.

    1. (C#) Nahraďte kód obsahem souboru [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx). Tento kód pomocí `HttpClient` volá rozhraní API pro počítačové zpracování obrazu a ukládá výsledek volání ve službě Cosmos DB.

1. **JavaScript**

    1. (JavaScript) Tato funkce k volání rozhraní API pro počítačové zpracování obrazu přes protokol HTTP vyžaduje balíček `axios` z npm. Pokud chcete tento balíček npm nainstalovat, na levém navigačním panelu klikněte na název aplikace Function App a pak klikněte na **Funkce platformy**.

    1. (JavaScript) Kliknutím na **Konzola** zobrazte okno konzoly.

    1. (JavaScript) V konzole spusťte příkaz `npm install axios`. Dokončení operace může pár minut trvat.

    1. (JavaScript) Kliknutím na název funkce (**ResizeImage**) v levém navigačním panelu zobrazte funkci a pak nahraďte veškerý obsah souboru **index.js** obsahem souboru [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).

1. Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.

1. Klikněte na **Uložit**. Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně uložila a nedošlo k žádným chybám.


## <a name="test-the-application"></a>Testování aplikace

1. Otevřete aplikaci v prohlížeči. Vyberte soubor obrázku a nahrajte ho.

1. Po několika sekundách by se na stránce měla zobrazit miniatura nového obrázku. Najeďte na obrázek myší, aby se zobrazil popis vygenerovaný rozhraním API pro počítačové zpracování obrazu.


## <a name="summary"></a>Souhrn

V tomto modulu jste zjistili, jak pro jednotlivé nahrané obrázky automaticky generovat titulky pomocí rozhraní API pro počítačové zpracování obrazu služeb Microsoft Cognitive Services. Dále se dozvíte, jak do aplikace přidat ověřování pomocí služby Azure App Service.
