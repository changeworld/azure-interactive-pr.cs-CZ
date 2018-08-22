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
ms.openlocfilehash: d1f9a07ce3d3b096b498e48b5c4f68c3454b2b37
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079251"
---
Ověřování pomocí služby Azure App Service umožňuje podporu ověřování na klíč v aplikaci funkcí Azure. Bezproblémově se integruje s Facebookem, Twitterem, účtem Microsoft, Google a Azure Active Directory. Přidáním ověřování pomocí služby App Service zajistíte ochranu back-endových rozhraní API vaší webové aplikace.

## <a name="enable-app-service-authentication"></a>Povolení ověřování pomocí služby App Service

1. Otevřete aplikaci funkcí na webu Azure Portal.

1. V části **Funkce platformy** vyberte **Ověřování/autorizace**.

    ![Výběr možnosti Ověřování/autorizace](media/functions-first-serverless-web-app/6-authorization.jpg)

1. Vyberte tyto hodnoty:
    
    | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
    | --- | --- | ---|
    | **Ověřování pomocí služby App Service** | Zapnuto | Povolí se ověřování. |
    | **Akce, která se má provést, když požadavek nebude ověřený** | Přihlásit se přes Azure Active Directory | Vyberte nakonfigurovanou metodu ověřování (níže). |
    | **Zprostředkovatelé ověřování** | Viz níže | Viz níže |
    | **Úložiště tokenů** | Zapnuto | Službě App Service se povolí ukládání a správa tokenů. |
    | **Povolené externí adresy URL pro přesměrování** | Adresa URL vaší aplikace, například: https://firstserverlessweb.z4.web.core.windows.net/ | Adresy URL, na které má služba App Service povoleno provést přesměrování po ověření uživatele. |

1. Výběrem **Azure Active Directory** zobrazte **Nastavení služby Azure Active Directory**.

    1. Jako **Režim správy** vyberte **Expresní** a vyplňte následující informace.
    
        | Nastavení      |  Navrhovaná hodnota   | Popis                                        |
        | --- | --- | ---|
        | **Režim správy** | Expresní, Vytvořit novou aplikaci AD | Automaticky se nastaví instanční objekt a ověřování pomocí Azure Active Directory. |
        | **Vytvořit aplikaci** | my-serverless-webapp | Zadejte jedinečný název aplikace. |
    
    1. Kliknutím na **OK** uložte nastavení Azure Active Directory.

    ![Ověřování/autorizace a nastavení služby Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. Klikněte na **Uložit**.


## <a name="modify-the-web-app-to-enable-authentication"></a>Úprava webové aplikace za účelem povolení ověřování

1. Ve službě Cloud Shell se ujistěte, že je aktuálním adresářem složka **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Pokud chcete ve své aplikaci funkcí povolit ověřování, přidejte následující řádek kódu do souboru **settings.js**.

    `window.authEnabled = true`

    Proveďte změnu a pomocí následujících příkazů nebo editoru příkazového řádku, jako je VIM, zobrazte výsledek.

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    Zkontrolujte, že došlo ke změně souboru.

    ```azurecli
    cat settings.js
    ```

1. Nahrajte soubor do úložiště objektů blob.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>Testování aplikace

1. Otevřete aplikaci v prohlížeči. Klikněte na **Log in** (Přihlásit se) a přihlaste se.

1. Vyberte soubor obrázku a nahrajte ho.

    ![Přihlašovací stránka](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>Souhrn

V tomto modulu jste zjistili, jak do aplikace přidat ověřování pomocí služby Azure App Service.
