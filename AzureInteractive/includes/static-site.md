Úložiště objektů blob v Azure je rozsáhle škálovatelná služba s nízkými náklady, kterou je možné použít k hostování statických souborů. V tomto kurzu ji použijete k obsluze statického obsahu (například soubory HTML, JavaScript a CSS) pro webovou aplikaci, kterou vytváříte.

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Účet úložiště je prostředek Azure, který umožňuje ukládat tabulky, fronty, soubory, objekty blob a disky virtuálních počítačů.

1. Přihlaste se do služby Cloud Shell (Bash) výběrem tlačítka **Vstoupit do detailního režimu**. Toto tlačítko se v závislosti na šířce okna prohlížeče nachází v pravé horní části nebo v dolní části stránky. V detailním režimu se okno služby Cloud Shell ukotví na pravé straně okna prohlížeče, abyste mohli snadno spouštět příkazy ukázané v tomto kurzu.

1. Skupina prostředků v Azure je kontejner, který pro usnadnění správy obsahuje související prostředky Azure. Vytvořte novou skupinu prostředků **first-serverless-app**.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. Statický obsah (soubory HTML, CSS a JavaScript) pro tento kurz se hostuje ve službě Blob Storage. Blob Storage vyžaduje účet úložiště. Vytvořte ve skupině prostředků účet úložiště (pro obecné účely v2). Nahraďte `<storage account name>` jedinečným názvem.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. Pomocí panelu hledání v horní části webu [Azure Portal](https://portal.azure.com) vyhledejte účet úložiště, který jste právě vytvořili, a otevřete ho.

1. Na levém navigačním panelu vyberte **Statický web (Preview)** a nakonfigurujte kontejner pro hostování statického webu.
    - Výběrem možnosti **Povoleno** povolte statický web.
    - Jako název dokumentu indexu zadejte *index.html*. Pole již obsahuje *index.html* šedým písmem, ale to je pouze ukázkový text, stále musíte tuto hodnotu do pole zadat.
    - Klikněte na **Uložit**.
    
    ![Zadání nastavení statického webu](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Uložte si **Primární koncový bod** někam, odkud ho budete moct při procházení kurzu jednoduše kopírovat. Toto je adresa URL vaší webové aplikace.

## <a name="upload-the-web-application"></a>Nahrání webové aplikace

1. Zdrojové soubory aplikace, kterou v tomto kurzu vytváříte, se nacházejí v [úložišti GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application). Ujistěte se, že jste ve službě Cloud Shell ve svém domovském adresáři, a naklonujte toto úložiště.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    Úložiště se naklonuje do umístění `/home/<username>/functions-first-serverless-web-application`.

1. Klientská webová aplikace se nachází ve složce **www** a je vytvořená pomocí architektury JavaScript Vue.js. Přejděte do této složky a spuštěním příkazů npm nainstalujte závislosti aplikace a sestavte aplikaci. Dokončení posledního z těchto příkazů může trvat několik minut.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    Aplikace se vygeneruje ve složce **dist**.

1. Přejděte do adresáře **dist** a nahrajte aplikaci do kontejneru objektů blob **$web**.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Ve webovém prohlížeči otevřete adresu URL primárního koncového bodu statického webu v úložišti a zobrazte aplikaci.

    ![Úvodní stránka první webové aplikace bez serveru](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Souhrn

V tomto modulu jste vytvořili skupinu prostředků **first-serverless-app** obsahující účet úložiště. Kontejner objektů blob **$web** v tomto účtu úložišti ukládá statický obsah vaší webové aplikace a zpřístupňuje obsah pro veřejnost. Dále se dozvíte, jak z této webové aplikace pomocí funkce bez serveru nahrát obrázky do úložiště objektů blob.
