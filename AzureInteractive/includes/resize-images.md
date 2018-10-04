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
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460074"
---
V předchozím modulu jste viděli, jak funkce bez serveru může usnadnit zabezpečené nahrávání obrázků do úložiště objektů blob z webové aplikace. V tomto modulu vytvoříte další funkci bez serveru, která bude sledovat nahrané obrázky a vytvářet pro ně miniatury.

## <a name="create-a-blob-storage-container-for-thumbnails"></a>Vytvoření kontejneru úložiště objektů blob pro miniatury

Obrázky v plné velikosti se ukládají v kontejneru **images**. Potřebujete další kontejner pro ukládání miniatur těchto obrázků.

1. Ujistěte se, že jste stále přihlášeni ve službě Cloud Shell (Bash).  Pokud ne, výběrem možnosti **Vstoupit do detailního režimu** otevřete okno služby Cloud Shell. 

1. Vytvořte ve svém účtu úložiště nový kontejner **thumbnails** s veřejným přístupem ke všem objektům blob.

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>Vytvoření funkce bez serveru aktivované objektem blob

Trigger definuje, jak se funkce vyvolá. Funkce, kterou v dalších krocích vytvoříte, používá trigger objektu blob: funkce se automaticky vyvolá při nahrání objektu blob (soubor obrázku) do kontejneru **images**. Funkce musí mít jeden trigger. Triggery obsahují související data, což je obvykle datová část, která funkci aktivovala.

Vazby definují, jak funkce čte nebo zapisuje data ve službách Azure nebo službách třetích stran. Tato funkce vytvoří miniaturu obrázku, který ji aktivuje, a uloží miniaturu v kontejneru *thumbnails*.

1. Otevřete svou aplikaci funkcí na webu Azure Portal.

1. V levém navigačním panelu okna aplikace funkcí najeďte myší na **Funkce** a kliknutím na **+** začněte vytvářet novou funkci bez serveru. Pokud se zobrazí stránka Rychlý start, kliknutím na **Vlastní funkce** zobrazte seznam šablon funkcí.

1. Vyhledejte šablonu **BlobTrigger** a vyberte ji.

1. K vytvoření funkce, která při nahrávání obrázků vytváří miniatury, použijte tyto hodnoty.

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | ---|
    | **Jazyk** | C# nebo JavaScript | Zvolte preferovaný jazyk. |
    | **Pojmenujte svoji funkci** | ResizeImage | Zadejte tento název přesně tak, jak je uvedený, aby aplikace funkci zjistila. |
    | **Cesta** | images/{name} | Funkce se spustí, když se v kontejneru **images** objeví nový soubor. |
    | **Informace o účtu úložiště** | AZURE_STORAGE_CONNECTION_STRING | Použijte proměnnou prostředí s připojovacím řetězcem, kterou jste vytvořili dříve. |

    ![Zadání nastavení nové funkce](media/functions-first-serverless-web-app/3-new-function.png)

1. Funkci vytvoříte kliknutím na **Vytvořit**.

1. Po vytvoření funkce kliknutím na **Integrovat** zobrazte její trigger a vstupní a výstupní vazby.

1. Kliknutím na **Nový výstup** vytvořte novou výstupní vazbu.

    ![Výběr možnosti Nový výstup na kartě Integrovat](media/functions-first-serverless-web-app/3-new-output.jpg)

1. Vyberte **Azure Blob Storage** a klikněte na **Vybrat**. Možná se budete muset posunout dolů, aby se tlačítko **Vybrat** zobrazilo.

    ![Výběr služby Azure Blob Storage](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. Zadejte následující hodnoty.

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | ---|
    | **Název parametru objektu blob** | thumbnail | Funkce používá parametr s tímto názvem k zápisu miniatury. |
    | **Používat návratovou hodnotu funkce** | Ne |  |
    | **Cesta** | thumbnails/{name} | Miniatury se zapisují do kontejneru **thumbnails**. |
    | **Připojení k účtu úložiště** | AZURE_STORAGE_CONNECTION_STRING | Použijte proměnnou prostředí s připojovacím řetězcem, kterou jste vytvořili dříve. |

    ![Zadání nastavení pro výstupní vazbu objektu blob](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript**

    1. (JavaScript) Kliknutím na **Rozšířený editor** v pravém horním rohu okna zobrazte JSON představující vazby.

    1. (JavaScript) Do vazby `blobTrigger` přidejte vlastnost `dataType` s hodnotou `binary`. Tím se vazba nakonfiguruje tak, aby do funkce předávala obsah objektu blob jako binární data.

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. Kliknutím na **Uložit** vytvořte novou vazbu.

1. **C#**

    1. (C#) Výběrem názvu funkce **ResizeImage** na levém navigačním panelu otevřete zdrojový kód funkce.

    1. (C#) Funkce ke generování miniatur vyžaduje balíček NuGet **ImageResizer**. Balíčky NuGet se do funkcí jazyka C# přidávají pomocí souboru **project.json**. Pokud chcete tento soubor vytvořit, kliknutím na **Zobrazit soubory** na pravé straně zobrazte soubory, ze kterých se funkce skládá.
    
    1. (C#) Klikněte na **Přidat** a přidejte nový soubor **project.json**.
    
    1. (C#) Zkopírujte do nově vytvořeného souboru obsah souboru [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json). Uložte soubor. Balíčky se automaticky obnoví při aktualizaci souboru.
    
        ![Soubor project.json s balíčkem ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) V části **Zobrazit soubory** vyberte soubor **run.csx** a nahraďte jeho obsah obsahem souboru [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).

1. **JavaScript** 

    1. (JavaScript) Tato funkce ke změně velikosti fotografie vyžaduje balíček `jimp` z npm. Pokud chcete tento balíček npm nainstalovat, na levém navigačním panelu klikněte na název aplikace Function App a pak klikněte na **Funkce platformy**.

    1. (JavaScript) Kliknutím na **Konzola** zobrazte okno konzoly.

    1. (JavaScript) V konzole spusťte příkaz `npm install jimp`. Dokončení operace může pár minut trvat.

    1. (JavaScript) Kliknutím na název funkce **ResizeImage** v levém navigačním panelu zobrazte funkci a pak nahraďte veškerý obsah souboru **index.js** obsahem souboru [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).

1. Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.

1. Klikněte na **Uložit**. Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně uložila a nedošlo k žádným chybám.


## <a name="test-the-serverless-function"></a>Test funkce bez serveru

1. Otevřete aplikaci v prohlížeči. Vyberte soubor obrázku a nahrajte ho. Nahrávání se dokončí, ale protože jsme ještě nepřidali schopnost zobrazit obrázky, v aplikaci se nahraná fotografie nezobrazí.

1. Ve službě Cloud Shell ověřte nahrání obrázku do kontejneru **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Ověřte vytvoření miniatury v kontejneru **thumbnails**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. Získejte adresu URL miniatury.

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    Otevřete tuto adresu URL v prohlížeči a ověřte správné vytvoření miniatury.

1. Než budete pokračovat k dalšímu kurzu, odstraňte všechny soubory v kontejnerech **images** a **thumbnails**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>Souhrn

V tomto modulu jste vytvořili funkci bez serveru, která při každém nahrání obrázku do kontejneru úložiště objektů blob vytvoří jeho miniaturu. Dále se dozvíte, jak pomocí služby Azure Cosmos DB ukládat a vypisovat metadata obrázků.
