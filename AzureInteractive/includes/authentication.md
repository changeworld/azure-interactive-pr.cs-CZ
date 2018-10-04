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
<span data-ttu-id="64827-103">Ověřování pomocí služby Azure App Service umožňuje podporu ověřování na klíč v aplikaci funkcí Azure.</span><span class="sxs-lookup"><span data-stu-id="64827-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="64827-104">Bezproblémově se integruje s Facebookem, Twitterem, účtem Microsoft, Google a Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="64827-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="64827-105">Přidáním ověřování pomocí služby App Service zajistíte ochranu back-endových rozhraní API vaší webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="64827-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="64827-106">Povolení ověřování pomocí služby App Service</span><span class="sxs-lookup"><span data-stu-id="64827-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="64827-107">Otevřete aplikaci funkcí na webu Azure Portal.</span><span class="sxs-lookup"><span data-stu-id="64827-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="64827-108">V části **Funkce platformy** vyberte **Ověřování/autorizace**.</span><span class="sxs-lookup"><span data-stu-id="64827-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Výběr možnosti Ověřování/autorizace](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="64827-110">Vyberte tyto hodnoty:</span><span class="sxs-lookup"><span data-stu-id="64827-110">Select the following values:</span></span>
    
    | <span data-ttu-id="64827-111">Nastavení</span><span class="sxs-lookup"><span data-stu-id="64827-111">Setting</span></span>      |  <span data-ttu-id="64827-112">Navrhovaná hodnota</span><span class="sxs-lookup"><span data-stu-id="64827-112">Suggested value</span></span>   | <span data-ttu-id="64827-113">Popis</span><span class="sxs-lookup"><span data-stu-id="64827-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="64827-114">**Ověřování pomocí služby App Service**</span><span class="sxs-lookup"><span data-stu-id="64827-114">**App Service Authentication**</span></span> | <span data-ttu-id="64827-115">Zapnuto</span><span class="sxs-lookup"><span data-stu-id="64827-115">On</span></span> | <span data-ttu-id="64827-116">Povolí se ověřování.</span><span class="sxs-lookup"><span data-stu-id="64827-116">Enable authentication.</span></span> |
    | <span data-ttu-id="64827-117">**Akce, která se má provést, když požadavek nebude ověřený**</span><span class="sxs-lookup"><span data-stu-id="64827-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="64827-118">Přihlásit se přes Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="64827-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="64827-119">Vyberte nakonfigurovanou metodu ověřování (níže).</span><span class="sxs-lookup"><span data-stu-id="64827-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="64827-120">**Zprostředkovatelé ověřování**</span><span class="sxs-lookup"><span data-stu-id="64827-120">**Authentication Providers**</span></span> | <span data-ttu-id="64827-121">Viz níže</span><span class="sxs-lookup"><span data-stu-id="64827-121">See below</span></span> | <span data-ttu-id="64827-122">Viz níže</span><span class="sxs-lookup"><span data-stu-id="64827-122">See below</span></span> |
    | <span data-ttu-id="64827-123">**Úložiště tokenů**</span><span class="sxs-lookup"><span data-stu-id="64827-123">**Token store**</span></span> | <span data-ttu-id="64827-124">Zapnuto</span><span class="sxs-lookup"><span data-stu-id="64827-124">On</span></span> | <span data-ttu-id="64827-125">Službě App Service se povolí ukládání a správa tokenů.</span><span class="sxs-lookup"><span data-stu-id="64827-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="64827-126">**Povolené externí adresy URL pro přesměrování**</span><span class="sxs-lookup"><span data-stu-id="64827-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="64827-127">Adresa URL vaší aplikace, například: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="64827-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="64827-128">Adresy URL, na které má služba App Service povoleno provést přesměrování po ověření uživatele.</span><span class="sxs-lookup"><span data-stu-id="64827-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="64827-129">Výběrem **Azure Active Directory** zobrazte **Nastavení služby Azure Active Directory**.</span><span class="sxs-lookup"><span data-stu-id="64827-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="64827-130">Jako **Režim správy** vyberte **Expresní** a vyplňte následující informace.</span><span class="sxs-lookup"><span data-stu-id="64827-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="64827-131">Nastavení</span><span class="sxs-lookup"><span data-stu-id="64827-131">Setting</span></span>      |  <span data-ttu-id="64827-132">Navrhovaná hodnota</span><span class="sxs-lookup"><span data-stu-id="64827-132">Suggested value</span></span>   | <span data-ttu-id="64827-133">Popis</span><span class="sxs-lookup"><span data-stu-id="64827-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="64827-134">**Režim správy**</span><span class="sxs-lookup"><span data-stu-id="64827-134">**Management mode**</span></span> | <span data-ttu-id="64827-135">Expresní, Vytvořit novou aplikaci AD</span><span class="sxs-lookup"><span data-stu-id="64827-135">Express, Create new AD app</span></span> | <span data-ttu-id="64827-136">Automaticky se nastaví instanční objekt a ověřování pomocí Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="64827-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="64827-137">**Vytvořit aplikaci**</span><span class="sxs-lookup"><span data-stu-id="64827-137">**Create app**</span></span> | <span data-ttu-id="64827-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="64827-138">my-serverless-webapp</span></span> | <span data-ttu-id="64827-139">Zadejte jedinečný název aplikace.</span><span class="sxs-lookup"><span data-stu-id="64827-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="64827-140">Kliknutím na **OK** uložte nastavení Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="64827-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Ověřování/autorizace a nastavení služby Azure Active Directory](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="64827-142">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="64827-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="64827-143">Úprava webové aplikace za účelem povolení ověřování</span><span class="sxs-lookup"><span data-stu-id="64827-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="64827-144">Ve službě Cloud Shell se ujistěte, že je aktuálním adresářem složka **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="64827-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="64827-145">Pokud chcete ve své aplikaci funkcí povolit ověřování, přidejte následující řádek kódu do souboru **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="64827-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="64827-146">Proveďte změnu a pomocí následujících příkazů nebo editoru příkazového řádku, jako je VIM, zobrazte výsledek.</span><span class="sxs-lookup"><span data-stu-id="64827-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="64827-147">Zkontrolujte, že došlo ke změně souboru.</span><span class="sxs-lookup"><span data-stu-id="64827-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="64827-148">Nahrajte soubor do úložiště objektů blob.</span><span class="sxs-lookup"><span data-stu-id="64827-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="64827-149">Testování aplikace</span><span class="sxs-lookup"><span data-stu-id="64827-149">Test the application</span></span>

1. <span data-ttu-id="64827-150">Otevřete aplikaci v prohlížeči.</span><span class="sxs-lookup"><span data-stu-id="64827-150">Open the application in a browser.</span></span> <span data-ttu-id="64827-151">Klikněte na **Log in** (Přihlásit se) a přihlaste se.</span><span class="sxs-lookup"><span data-stu-id="64827-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="64827-152">Vyberte soubor obrázku a nahrajte ho.</span><span class="sxs-lookup"><span data-stu-id="64827-152">Select an image file and upload it.</span></span>

    ![Přihlašovací stránka](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="64827-154">Souhrn</span><span class="sxs-lookup"><span data-stu-id="64827-154">Summary</span></span>

<span data-ttu-id="64827-155">V tomto modulu jste zjistili, jak do aplikace přidat ověřování pomocí služby Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="64827-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
