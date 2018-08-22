<span data-ttu-id="be3f4-101">Úložiště objektů blob v Azure je rozsáhle škálovatelná služba s nízkými náklady, kterou je možné použít k hostování statických souborů.</span><span class="sxs-lookup"><span data-stu-id="be3f4-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="be3f4-102">V tomto kurzu ji použijete k obsluze statického obsahu (například soubory HTML, JavaScript a CSS) pro webovou aplikaci, kterou vytváříte.</span><span class="sxs-lookup"><span data-stu-id="be3f4-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="be3f4-103">Vytvoření účtu úložiště</span><span class="sxs-lookup"><span data-stu-id="be3f4-103">Create a Storage account</span></span>

<span data-ttu-id="be3f4-104">Účet úložiště je prostředek Azure, který umožňuje ukládat tabulky, fronty, soubory, objekty blob a disky virtuálních počítačů.</span><span class="sxs-lookup"><span data-stu-id="be3f4-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="be3f4-105">Přihlaste se do služby Cloud Shell (Bash) výběrem tlačítka **Vstoupit do detailního režimu**.</span><span class="sxs-lookup"><span data-stu-id="be3f4-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="be3f4-106">Toto tlačítko se v závislosti na šířce okna prohlížeče nachází v pravé horní části nebo v dolní části stránky.</span><span class="sxs-lookup"><span data-stu-id="be3f4-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="be3f4-107">V detailním režimu se okno služby Cloud Shell ukotví na pravé straně okna prohlížeče, abyste mohli snadno spouštět příkazy ukázané v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="be3f4-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="be3f4-108">Skupina prostředků v Azure je kontejner, který pro usnadnění správy obsahuje související prostředky Azure.</span><span class="sxs-lookup"><span data-stu-id="be3f4-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="be3f4-109">Vytvořte novou skupinu prostředků **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="be3f4-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="be3f4-110">Statický obsah (soubory HTML, CSS a JavaScript) pro tento kurz se hostuje ve službě Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="be3f4-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="be3f4-111">Blob Storage vyžaduje účet úložiště.</span><span class="sxs-lookup"><span data-stu-id="be3f4-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="be3f4-112">Vytvořte ve skupině prostředků účet úložiště (pro obecné účely v2).</span><span class="sxs-lookup"><span data-stu-id="be3f4-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="be3f4-113">Nahraďte `<storage account name>` jedinečným názvem.</span><span class="sxs-lookup"><span data-stu-id="be3f4-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="be3f4-114">Pomocí panelu hledání v horní části webu [Azure Portal](https://portal.azure.com) vyhledejte účet úložiště, který jste právě vytvořili, a otevřete ho.</span><span class="sxs-lookup"><span data-stu-id="be3f4-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="be3f4-115">Na levém navigačním panelu vyberte **Statický web (Preview)** a nakonfigurujte kontejner pro hostování statického webu.</span><span class="sxs-lookup"><span data-stu-id="be3f4-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="be3f4-116">Výběrem možnosti **Povoleno** povolte statický web.</span><span class="sxs-lookup"><span data-stu-id="be3f4-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="be3f4-117">Jako název dokumentu indexu zadejte *index.html*.</span><span class="sxs-lookup"><span data-stu-id="be3f4-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="be3f4-118">Pole již obsahuje *index.html* šedým písmem, ale to je pouze ukázkový text, stále musíte tuto hodnotu do pole zadat.</span><span class="sxs-lookup"><span data-stu-id="be3f4-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="be3f4-119">Klikněte na **Uložit**.</span><span class="sxs-lookup"><span data-stu-id="be3f4-119">Click **Save**.</span></span>
    
    ![Zadání nastavení statického webu](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="be3f4-121">Uložte si **Primární koncový bod** někam, odkud ho budete moct při procházení kurzu jednoduše kopírovat.</span><span class="sxs-lookup"><span data-stu-id="be3f4-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="be3f4-122">Toto je adresa URL vaší webové aplikace.</span><span class="sxs-lookup"><span data-stu-id="be3f4-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="be3f4-123">Nahrání webové aplikace</span><span class="sxs-lookup"><span data-stu-id="be3f4-123">Upload the web application</span></span>

1. <span data-ttu-id="be3f4-124">Zdrojové soubory aplikace, kterou v tomto kurzu vytváříte, se nacházejí v [úložišti GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span><span class="sxs-lookup"><span data-stu-id="be3f4-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="be3f4-125">Ujistěte se, že jste ve službě Cloud Shell ve svém domovském adresáři, a naklonujte toto úložiště.</span><span class="sxs-lookup"><span data-stu-id="be3f4-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="be3f4-126">Úložiště se naklonuje do umístění `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="be3f4-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="be3f4-127">Klientská webová aplikace se nachází ve složce **www** a je vytvořená pomocí architektury JavaScript Vue.js.</span><span class="sxs-lookup"><span data-stu-id="be3f4-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="be3f4-128">Přejděte do této složky a spuštěním příkazů npm nainstalujte závislosti aplikace a sestavte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="be3f4-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="be3f4-129">Dokončení posledního z těchto příkazů může trvat několik minut.</span><span class="sxs-lookup"><span data-stu-id="be3f4-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="be3f4-130">Aplikace se vygeneruje ve složce **dist**.</span><span class="sxs-lookup"><span data-stu-id="be3f4-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="be3f4-131">Přejděte do adresáře **dist** a nahrajte aplikaci do kontejneru objektů blob **$web**.</span><span class="sxs-lookup"><span data-stu-id="be3f4-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="be3f4-132">Ve webovém prohlížeči otevřete adresu URL primárního koncového bodu statického webu v úložišti a zobrazte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="be3f4-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Úvodní stránka první webové aplikace bez serveru](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="be3f4-134">Souhrn</span><span class="sxs-lookup"><span data-stu-id="be3f4-134">Summary</span></span>

<span data-ttu-id="be3f4-135">V tomto modulu jste vytvořili skupinu prostředků **first-serverless-app** obsahující účet úložiště.</span><span class="sxs-lookup"><span data-stu-id="be3f4-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="be3f4-136">Kontejner objektů blob **$web** v tomto účtu úložišti ukládá statický obsah vaší webové aplikace a zpřístupňuje obsah pro veřejnost.</span><span class="sxs-lookup"><span data-stu-id="be3f4-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="be3f4-137">Dále se dozvíte, jak z této webové aplikace pomocí funkce bez serveru nahrát obrázky do úložiště objektů blob.</span><span class="sxs-lookup"><span data-stu-id="be3f4-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
