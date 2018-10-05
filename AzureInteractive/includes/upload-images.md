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
ms.openlocfilehash: 51c7d3e64424d499b473f3b138ce249a9cfd0182
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460075"
---
Aplikace, kterou vytváříte, je fotogalerie. Pomocí JavaScriptu na straně klienta volá rozhraní API za účelem nahrání a zobrazení obrázků. V tomto modulu vytvoříte rozhraní API s funkcí bez serveru, která vygeneruje časově omezenou adresu URL pro nahrání obrázku. Webová aplikace použije vygenerovanou adresu URL k nahrání obrázku do úložiště objektů blob pomocí [rozhraní REST API úložiště objektů Blob](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

## <a name="create-a-blob-storage-container-for-images"></a>Vytvoření kontejneru úložiště objektů blob pro obrázky

Aplikace k nahrávání a hostování obrázků vyžaduje samostatný kontejner úložiště.

1. Ujistěte se, že jste stále přihlášeni ve službě Cloud Shell (Bash). Pokud ne, výběrem možnosti **Vstoupit do detailního režimu** otevřete okno služby Cloud Shell.

1.  Vytvořte ve svém účtu úložiště nový kontejner **images** s veřejným přístupem ke všem objektům blob.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Vytvoření aplikace Azure Function App

Azure Functions je služba umožňující spouštění funkcí bez serveru. Funkci bez serveru můžou aktivovat (volat) události, jako je požadavek HTTP nebo vytvoření objektu blob v kontejneru úložiště.

Aplikace funkcí Azure je kontejner pro jednu nebo několik funkcí bez serveru.

Vytvořte novou aplikaci funkcí Azure s jedinečným názvem **first-serverless-app** ve skupině prostředků, kterou jste vytvořili dříve. Aplikace funkcí vyžaduje účet úložiště. V tomto kurzu použijete existující účet úložiště.

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a>Konfigurace aplikace Function App

Aplikace funkcí v tomto kurzu vyžaduje modul runtime služby Functions verze 1.x. Nastavení aplikace `FUNCTIONS_WORKER_RUNTIME` na `~1` připne aplikaci funkcí k nejnovější verzi 1.x. Pomocí příkazu [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) nastavte nastavení aplikace.

V následujícím příkazu Azure CLI je <app_name> název vaší aplikace funkcí.

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_WORKER_RUNTIME=~1
```

## <a name="create-an-http-triggered-serverless-function"></a>Vytvoření funkce bez serveru aktivované protokolem HTTP

Webová aplikace fotogalerie do funkce bez serveru odešle požadavek HTTP na vygenerování časově omezené adresy URL pro zabezpečené nahrání obrázku do úložiště objektů blob. Funkce se aktivuje požadavkem HTTP a pomocí sady SDK služby Azure Storage vygeneruje a vrátí zabezpečenou adresu URL.

1. Po vytvoření aplikace funkcí ji pomocí vyhledávacího pole vyhledejte na webu Azure Portal a kliknutím ji otevřete.

    ![Otevření aplikace funkcí](media/functions-first-serverless-web-app/2-search-function-app.png)

1. V levém navigačním panelu okna aplikace funkcí najeďte myší na **Funkce** a kliknutím na **+** začněte vytvářet novou funkci bez serveru.

    ![Vytvoření nové funkce](media/functions-first-serverless-web-app/2-new-function.png)

1. Kliknutím na **Vlastní funkce** zobrazte seznam šablon funkcí.

1. Vyhledejte šablonu **HttpTrigger** a klikněte na jazyk, který chcete použít (C# nebo JavaScript).

1. Použijte následující hodnoty k vytvoření funkce, která vygeneruje adresu URL pro nahrání objektu blob.

    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | ---|
    | **Jazyk** | C# nebo JavaScript | Vyberte jazyk, který chcete použít. |
    | **Pojmenujte svoji funkci** | GetUploadUrl | Zadejte tento název přesně tak, jak je uvedený, aby aplikace funkci zjistila. |
    | **Úroveň autorizace** | Anonymní | Povolte k funkci veřejný přístup. |

    ![Zadání nastavení nové funkce aktivované protokolem HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. Funkci vytvoříte kliknutím na **Vytvořit**.

1. **C#** 

    1. Jakmile se zobrazí zdrojový kód funkce, nahraďte veškerý obsah souboru **run.csx** obsahem souboru [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).

1. **JavaScript** 

    1. (JavaScript) Tato funkce k vygenerování tokenu sdíleného přístupového podpisu (SAS) nezbytného k vytvoření zabezpečené adresy URL vyžaduje balíček `azure-storage` z npm. Pokud chcete tento balíček npm nainstalovat, na levém navigačním panelu klikněte na název aplikace Function App a pak klikněte na **Funkce platformy**.

    1. (JavaScript) Kliknutím na **Konzola** zobrazte okno konzoly.

        ![Otevření okna konzoly](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) Spuštěním příkazu `cd d:\home\site\wwwroot` se ujistěte, že je aktuální adresář **d:\home\site\wwwroot**.

    1. (JavaScript) Spuštěním příkazu `npm init -y` vytvořte prázdný soubor **package.json**.

    1. (JavaScript) Spuštěním příkazu `npm install --save azure-storage` v konzole nainstalujte balíček a uložte ho do souboru **package.json**. Dokončení operace může pár minut trvat.

    1. (JavaScript) Kliknutím na název funkce (**GetUploadUrl**) v levém navigačním panelu zobrazte funkci a pak nahraďte veškerý obsah souboru **index.js** obsahem souboru [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).
    
        ![Aktualizovaný soubor index.js](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. Kliknutím na **Protokoly** pod oknem kódu rozbalte panel protokolů.

1. Klikněte na **Uložit**. Zkontrolujte panel protokolů a ujistěte se, že se funkce úspěšně zkompilovala.

Funkce vygeneruje tzv. adresu URL sdíleného přístupového podpisu (SAS), která slouží k nahrání souboru do úložiště objektů blob. Tato adresa URL SAS má krátkodobou platnost a umožňuje nahrání pouze jednoho souboru. Další informace o [používání sdílených přístupových podpisů](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) najdete v dokumentaci k úložišti objektů blob.


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>Přidání proměnné prostředí pro připojovací řetězec úložiště

Funkce, kterou jste právě vytvořili, vyžaduje připojovací řetězec účtu úložiště, aby mohla vygenerovat adresu URL SAS. Místo pevného zakódování připojovacího řetězce do těla funkce ho můžete uložit jako nastavení aplikace. Nastavení aplikace jsou přístupná jako proměnné prostředí pro všechny funkce v aplikaci funkcí.

1. Ve službě Cloud Shell odešlete dotaz na připojovací řetězec účtu úložiště a uložte ho do proměnné prostředí Bash **STORAGE_CONNECTION_STRING**.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    Ověřte úspěšné nastavení proměnné.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. Vytvořte nové nastavení aplikace **AZURE_STORAGE_CONNECTION_STRING** s použitím hodnoty uložené v předchozím kroku.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    Ověřte, že výstup příkazu obsahuje nové nastavení aplikace se správnou hodnotou.


## <a name="test-the-serverless-function"></a>Test funkce bez serveru

Kromě vytváření a úprav funkcí poskytuje Azure Portal také integrovaný nástroj pro testování funkcí.

1. Pokud chcete otestovat funkci bez serveru HTTP, kliknutím na kartu **Test** na pravé straně okna kódu rozbalte panel testování.

1. U položky **Metoda HTTP** změňte hodnotu na **GET**.

1. V části **Dotaz** klikněte na *Přidat parametr** a přidejte následující parametr:

    | jméno      |  hodnota   | 
    | --- | --- |
    | filename | image1.jpg |

1. Kliknutím na **Spustit** na panelu testování odešlete do funkce požadavek HTTP.

1. Funkce ve výstupu vrátí adresu URL pro nahrání. Spuštění funkce se zobrazí na panelu protokolů.

    ![Protokoly ukazující úspěšné spuštění funkce](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>Konfigurace CORS v aplikaci funkcí

Vzhledem k tomu, že se front-end aplikace hostuje v úložišti objektů blob, má jiný název domény než aplikace funkcí Azure. Aby JavaScript na straně klienta mohl úspěšně volat funkci, kterou jste právě vytvořili, je potřeba v aplikaci funkcí nakonfigurovat sdílení prostředků různého původu (CORS).

1. V levém navigačním panelu okna aplikace funkcí klikněte na název vaší aplikace funkcí.

1. Kliknutím na **Funkce platformy** zobrazte seznam pokročilých funkcí.

1. V části **API** klikněte na **CORS**.

    ![Výběr CORS](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. Přidejte povolení původu pro adresu URL aplikace z předchozího modulu a vynechte koncový znak **/** (příklad: `https://firstserverlessweb.z4.web.core.windows.net`).

    ![Nastavení CORS ukazující přidání adresy URL webové aplikace bez serveru](media/functions-first-serverless-web-app/2-add-cors.png)

1. Klikněte na **Uložit**.

1. C#

   1. (C#) Vraťte se do funkce `GetUploadUrl` a pak vyberte kartu **Integrovat**.

   1. (C#) V části Vybrané metody HTTP vyberte **MOŽNOSTI**.

      Měly by být vybrané možnosti **GET**, **POST** a **OPTIONS**. CORS využívá metodu OPTIONS, která v případě funkcí jazyka C# není vybraná ve výchozím nastavení.  

   1. (C#) Klikněte na **Uložit**.

1. Pořád ještě na portálu přejděte k aplikaci funkcí, vyberte kartu **Přehled** a kliknutím na **Restartovat** zajistěte, aby se změny CORS projevily.

## <a name="configure-cors-in-the-storage-account"></a>Konfigurace CORS v účtu úložiště

Vzhledem k tomu, že aplikace také provádí volání JavaScriptu na straně klienta do služby Blob Storage za účelem nahrání souborů, je potřeba CORS nakonfigurovat také v účtu úložiště.

1. Spuštěním následujícího příkazu povolte nahrávání souborů jakéhokoli původu do účtu úložiště.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>Úprava webové aplikace pro nahrávání obrázků

Webová aplikace načítá nastavení ze souboru **settings.js**. V následujících krocích tento soubor vytvoříte pomocí služby Cloud Shell a pak nastavíte `window.apiBaseUrl` na adresu URL aplikace funkcí a `window.blobBaseUrl` na adresu URL koncového bodu služby Azure Blob Storage.

1. Ve službě Cloud Shell se ujistěte, že je aktuálním adresářem složka **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Odešlete dotaz na adresu URL aplikace funkcí a uložte ji do proměnné prostředí Bash **FUNCTION_APP_URL**.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    Ověřte správné nastavení proměnné.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. Pokud chcete nastavit základní identifikátor URI pro volání rozhraní API do vaší aplikace funkcí, vytvořte soubor **settings.js** a přidejte do něj adresu URL aplikace funkcí, jak je znázorněno v následujícím příkladu.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    Změnu můžete provést spuštěním následujícího příkazu nebo pomocí editoru příkazového řádku, jako je VIM.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    Ověřte úspěšný zápis souboru.

    ```azurecli
    cat settings.js
    ```

1. Odešlete dotaz na základní adresu URL služby Blob Storage a uložte ji do proměnné prostředí Bash **BLOB_BASE_URL**.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    Ověřte správné nastavení proměnné.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. Pokud chcete nastavit základní identifikátor URI pro volání rozhraní API do vaší aplikace funkcí, přidejte do souboru **settings.js** adresu URL úložiště, jak je znázorněno na následujícím řádku kódu.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    Změnu můžete provést spuštěním následujícího příkazu nebo pomocí editoru příkazového řádku, jako je VIM.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    Ověřte, že se soubor úspěšně zapsal a obsahuje teď 2 řádky.

    ```azurecli
    cat settings.js
    ```

1. Nahrajte soubor do úložiště objektů blob.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>Test webové aplikace

Aplikace galerie v tuto chvíli umožňuje nahrát obrázek do úložiště objektů blob, ale ještě nedokáže zobrazit obrázky. Pokusí se zavolat funkci `GetImages`, která ještě neexistuje, protože ji vytvoříte v pozdějším modulu. Toto volání selže a webová stránka se zdánlivě zasekne na probíhající analýze, ale vybraný obrázek se úspěšně nahraje.

Úspěšné nahrání obrázku můžete ověřit tak, že zkontrolujete obsah kontejneru **images** na webu Azure Portal.

1. V okně prohlížeče přejděte do aplikace. Vyberte soubor obrázku a nahrajte ho. Nahrávání se dokončí, ale protože jsme ještě nepřidali schopnost zobrazit obrázky, v aplikaci se nahraná fotografie nezobrazí. (Webová stránka se zdánlivě zasekne na probíhající analýze obrázku, později to opravíte.)

1. Ve službě Cloud Shell ověřte nahrání obrázku do kontejneru **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Než budete pokračovat k dalšímu kurzu, odstraňte všechny soubory v kontejneru **images**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>Souhrn

V tomto modulu jste vytvořili aplikaci funkcí Azure a zjistili jste, jak pomocí funkce bez serveru umožnit webové aplikaci nahrávat obrázky do úložiště objektů blob. Dále se dozvíte, jak pro nahrané obrázky vytvářet miniatury pomocí funkce bez serveru aktivované objektem blob.
