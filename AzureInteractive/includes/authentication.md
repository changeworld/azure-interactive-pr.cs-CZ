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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460037"
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
