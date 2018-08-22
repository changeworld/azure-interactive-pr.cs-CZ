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
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079239"
---
Azure Cosmos DB je globálně distribuovaná databáze Microsoftu bez serveru pro více modelů. V tomto modulu zjistíte, jak pomocí služby Azure Functions ukládat a načítat metadata obrázků jako dokumenty JSON ve službě Cosmos DB.

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Vytvoření účtu, databáze a kolekce Cosmos DB

Účet Cosmos DB je prostředek Azure, který obsahuje databáze Cosmos DB.

1. Ujistěte se, že jste stále přihlášeni ve službě Cloud Shell.  Pokud ne, výběrem možnosti **Vstoupit do detailního režimu** otevřete okno služby Cloud Shell. 

1. Vytvořte účet Cosmos DB s jedinečným názvem ve stejné skupině prostředků jako ostatní prostředky v tomto kurzu.

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. Po vytvoření účtu Cosmos DB v tomto účtu vytvořte novou databázi **imagesdb**.

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. Po vytvoření databáze v této databázi vytvořte novou kolekci **images** s propustností 400 jednotek žádostí (RU).

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>Uložení dokumentu do služby Cosmos DB při vytvoření miniatury

Výstupní vazba služby Cosmos DB umožňuje vytvářet dokumenty v kolekci Cosmos DB ze služby Azure Functions. V následujících krocích nakonfigurujete výstupní vazbu služby Cosmos DB ve funkci **ResizeImage** a upravíte tuto funkci tak, aby vracela dokument (objekt), který se má uložit.

1. Otevřete aplikaci funkcí na webu Azure Portal.

1. V levém navigačním panelu rozbalte funkci **ResizeImage** a pak vyberte **Integrovat**.

1. V části **Výstupy** klikněte na **Nový výstup**.

1. Vyhledejte položku **Azure Cosmos DB** a vyberte ji. Pak klikněte na **Vybrat**.

    ![Výběr možnosti Nový výstup](media/functions-first-serverless-web-app/4-new-output.jpg)

1. Do polí v části **Výstup služby Azure Cosmos DB** vyplňte následující hodnoty.

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | ---|
    | **Název parametru dokumentu** | Vyberte **Používat návratovou hodnotu funkce**. | Hodnota textového pole se automaticky nastaví na **$return**. |
    | **Název databáze** | imagesdb | Použijte název databáze, kterou jste vytvořili. |
    | **Název kolekce** | images | Použijte název kolekce, kterou jste vytvořili. |

1. Vedle **Připojení účtu Azure Cosmos DB** klikněte na **nový**. Vyberte účet Cosmos DB, který jste vytvořili dříve.

    ![Zadání nastavení výstupní vazby služby Azure Cosmos DB](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. Kliknutím na **Uložit** vytvořte výstupní vazbu služby Cosmos DB.

1. Otevřete funkci kliknutím na název funkce **ResizeImage** na levé straně.

1. **C#**

    1. (C#) Změňte návratový typ funkce z hodnoty **void** na **object**.

    1. (C#) Na konec funkce přidejte následující blok kódu vracející dokument, který se má uložit:
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![Upravený soubor run.csx s funkcí ResizeImages](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript**

    1. (JavaScript) Změňte příkaz `context.done()` v klauzuli `else` tak, aby vracel dokument, který se má uložit do služby Cosmos DB.

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.

1. Klikněte na **Uložit**. Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně uložila a nedošlo k žádným chybám.


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Vytvoření funkce pro výpis obrázků ze služby Cosmos DB

Webová aplikace k načtení metadat obrázků ze služby Cosmos DB vyžaduje rozhraní API. V následujících krocích vytvoříte funkci aktivovanou protokolem HTTP, která používá vstupní vazbu služby Cosmos DB k dotazování kolekce databáze.

1. Ve vaší aplikaci funkcí najeďte myší na **Funkce** na levé straně a kliknutím na **+** vytvořte novou funkci.

1. Vyhledejte šablonu **HttpTrigger** a vyberte ji.

1. Použijte následující hodnoty k vytvoření funkce, která vygeneruje adresu URL pro získání obrázků.

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | ---|
    | **Pojmenujte svoji funkci** | GetImages | Zadejte tento název přesně tak, jak je uvedený, aby aplikace funkci zjistila. |
    | **Úroveň autorizace** | Anonymní | Povolte k funkci veřejný přístup. |

1. Klikněte na možnost **Vytvořit**.

1. Po vytvoření nové funkce klikněte na **Integrovat** pod názvem funkce na levém navigačním panelu.

1. Klikněte na **Nový vstup** a vyberte **Azure Cosmos DB**. 

    ![Výběr možnosti Nový vstup](media/functions-first-serverless-web-app/4-new-input.jpg)

1. Klikněte na **Vybrat**.

1. Vyplňte následující hodnoty:

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | ---|
    | **Název parametru dokumentu** | documents | Tento název odpovídá názvu parametru ve funkci. |
    | **Název databáze** | imagesdb |  |
    | **Název kolekce** | images |  |
    | **Dotaz SQL** | select * from c order by c._ts desc | Získání dokumentů od nejnovějšího po nejstarší. |
    | **Připojení účtu Azure Cosmos DB** | Vyberte existující připojovací řetězec. |  |

1. Kliknutím na **Uložit** vytvořte vstupní vazbu.

1. **C#**

    1. Kliknutím na název funkce otevřete okno kódu a pak nahraďte veškerý obsah souboru **run.csx** obsahem souboru [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).

1. **JavaScript**

    1. Kliknutím na název funkce otevřete okno kódu a pak nahraďte veškerý obsah souboru **index.js** obsahem souboru [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).

1. Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.

1. Klikněte na **Uložit**. Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně uložila a nedošlo k žádným chybám.


## <a name="test-the-application"></a>Testování aplikace

1. Otevřete aplikaci v prohlížeči. Vyberte soubor obrázku a nahrajte ho.

1. Po několika sekundách se na stránce zobrazí miniatura nového obrázku.

1. Na webu Azure Portal pomocí vyhledávacího pole vyhledejte váš účet Cosmos DB podle názvu. Klikněte na něj a otevřete ho.

1. Na levé straně klikněte na **Průzkumník dat**, ve kterém můžete procházet kolekce a dokumenty.

1. V databázi **imagesdb** vyberte kolekci **images**.

1. Ověřte vytvoření dokumentu pro nahraný obrázek.

    ![Průzkumník dat ukazující dokument pro nahraný obrázek](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>Souhrn

V tomto modulu jste zjistili, jak vytvořit účet, databázi a kolekci Cosmos DB. Také jste zjistili, jak pomocí vazeb služby Cosmos DB ukládat a načítat metadata obrázků v kolekci Cosmos DB. Dále se dozvíte, jak pro jednotlivé nahrané obrázky automaticky generovat titulky pomocí služeb Microsoft Cognitive Services.