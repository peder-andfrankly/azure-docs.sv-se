---
title: 'Snabb start: Azure Blob Storage-bibliotek V12 – python'
description: I den här snabb starten får du lära dig hur du använder Azure Blob Storage klient bibliotek version 12 för python för att skapa en behållare och en BLOB i blob-lagring (objekt). Du får lära dig hur du hämtar bloben till din lokala dator och hur du visar alla blobar i en container.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: d589215cf79154bcc8aead1d6695bd4cf870fc0a
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423966"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-python"></a>Snabb start: klient biblioteket för Azure Blob Storage-V12 för python

Kom igång med Azure Blob Storages klient bibliotek V12 för python. Azure Blob Storage är Microsofts objektlagringslösning för molnet. Följ stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Blobblagring är optimerat för att lagra stora mängder ostrukturerade data.

> [!NOTE]
> För att komma igång med den tidigare SDK-versionen, se [snabb start: Azure Blob Storage klient bibliotek för python](storage-quickstart-blobs-python-legacy.md).

Använd klient biblioteket för Azure Blob Storage för att:

* Skapa en container
* Ladda upp en blob till Azure Storage
* Lista alla blobar i en behållare
* Ladda ned blobben till den lokala datorn
* Ta bort en container

[API Reference-dokumentation](/python/api/azure-storage-blob) | [biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [paket (Python-paket index)](https://pypi.org/project/azure-storage-blob/) | [exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
* Azure Storage-konto – [skapa ett lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) för ditt operativ system – 2,7, 3,5 eller högre

## <a name="setting-up"></a>Konfigurera

Det här avsnittet beskriver hur du förbereder ett projekt så att det fungerar med Azure Blob Storage-V12 för python.

### <a name="create-the-project"></a>Skapa projektet

Skapa ett python-program med namnet *BLOB-snabb start-V12*.

1. Skapa en ny katalog för projektet i ett konsol fönster (till exempel cmd, PowerShell eller bash).

    ```console
    mkdir blob-quickstart-v12
    ```

1. Växla till den nyss skapade *V12-katalogen för BLOB-snabb start* .

    ```console
    cd blob-quickstart-v12
    ```

1. På sidan *BLOB-snabb start-V12-* katalogen skapar du en annan katalog med namnet *data*. Det är här som BLOB-datafilerna ska skapas och lagras.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure Blob Storage-klient biblioteket för python-paket med hjälp av kommandot `pip install`.

```console
pip install azure-storage-blob
```

Det här kommandot installerar Azure Blob Storage-klientprogrammet för python-paket och alla bibliotek som det är beroende av. I det här fallet är det bara Azures kärn bibliotek för python.

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Från projekt katalogen:

1. Öppna en ny textfil i kod redigeraren
1. Lägg till `import`-uttryck
1. Skapa strukturen för programmet, inklusive mycket grundläggande undantags hantering

    Här är koden:

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. Spara den nya filen som *BLOB-QuickStart-V12.py* i katalogen *BLOB-snabb start-V12* .

[!INCLUDE [storage-quickstart-connection-string-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objekt modell

Azure Blob Storage är optimerat för att lagra enorma mängder ostrukturerade data. Ostrukturerade data är data som inte följer en viss datamodell eller definition, till exempel text eller binära data. I blobblagringen finns tre typer av resurser:

* Lagrings kontot
* En behållare i lagrings kontot
* En BLOB i behållaren

Följande diagram visar relationen mellan de här resurserna.

![Diagram över blobblagringens arkitektur](./media/storage-blob-introduction/blob1.png)

Använd följande python-klasser för att interagera med dessa resurser:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): klassen `BlobServiceClient` gör att du kan ändra Azure Storage resurser och blob-behållare.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): klassen `ContainerClient` gör att du kan ändra Azure Storage behållare och deras blobbar.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): klassen `BlobClient` gör att du kan ändra Azure Storage blobbar.

## <a name="code-examples"></a>Kod exempel

I de här exempel kods tycken visas hur du gör följande med klient biblioteket för Azure Blob Storage för python:

* [Hämta anslutnings strängen](#get-the-connection-string)
* [Skapa en behållare](#create-a-container)
* [Ladda upp blobbar till en behållare](#upload-blobs-to-a-container)
* [Visa en lista över blobarna i en behållare](#list-the-blobs-in-a-container)
* [Ladda ned blobbar](#download-blobs)
* [Ta bort en container](#delete-a-container)

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

Koden nedan hämtar anslutnings strängen för lagrings kontot från den miljö variabel som skapades i avsnittet [Konfigurera din lagrings anslutnings sträng](#configure-your-storage-connection-string) .

Lägg till den här koden inuti `try` blocket:

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called CONNECT_STR. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('CONNECT_STR')
```

### <a name="create-a-container"></a>Skapa en container

Välj ett namn för den nya behållaren. Koden nedan lägger till ett UUID-värde till behållar namnet för att säkerställa att det är unikt.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om namngivning av containrar och blobar finns i [Namngivning och referens av containrar, blobar och metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Skapa en instans av klassen [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) genom att anropa metoden [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) . Anropa sedan [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) -metoden för att skapa behållaren i ditt lagrings konto.

Lägg till den här koden i slutet av `try` blocket:

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>Ladda upp blobbar till en behållare

Följande kodfragment:

1. Skapar en textfil i den lokala katalogen.
1. Hämtar en referens till ett [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) -objekt genom att anropa metoden [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) på [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) från avsnittet [skapa en behållare](#create-a-container) .
1. Överför den lokala text filen till blobben genom att anropa metoden [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) .

Lägg till den här koden i slutet av `try` blocket:

```python
# Create a file in local Documents directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Visa en lista över blobarna i behållaren genom att anropa metoden [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) . I det här fallet har endast en BLOB lagts till i behållaren, så List åtgärden returnerar bara den en blob.

Lägg till den här koden i slutet av `try` blocket:

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned den tidigare skapade blobben genom att anropa metoden [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) . Exempel koden lägger till suffixet "DOWNLOAD" i fil namnet så att du kan se båda filerna i det lokala fil systemet.

Lägg till den här koden i slutet av `try` blocket:

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in Documents
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>Ta bort en container

Följande kod rensar resurserna som skapats av appen genom att ta bort hela behållaren med hjälp av metoden [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) . Du kan också ta bort de lokala filerna, om du vill.

Appen pausar indata från användaren genom att anropa `input()` innan den tar bort BLOB, container och lokala filer. Det här är en bra chans att verifiera att resurserna faktiskt har skapats korrekt innan de tas bort.

Lägg till den här koden i slutet av `try` blocket:

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>Kör koden

Den här appen skapar en test fil i din lokala mapp och laddar upp den till Blob Storage. Exemplet visar sedan blobarna i behållaren och laddar ned filen med ett nytt namn så att du kan jämföra de gamla och nya filerna.

Navigera till den katalog som innehåller *BLOB-QuickStart-V12.py* -filen och kör sedan följande `python` kommando för att köra appen.

```console
python blob-quickstart-v12.py
```

Utdata från appen liknar följande exempel:

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

Innan du påbörjar rensnings processen kontrollerar du mappen *dokument* för de två filerna. Du kan öppna dem och se att de är identiska.

När du har verifierat filerna trycker du på **RETUR** -tangenten för att ta bort testfilerna och slutföra demon.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig att ladda upp, ladda ned och lista blobar med python.

Om du vill se exempel appar för Blob Storage fortsätter du till:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 python-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* Mer information finns i [Azure SDK för python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md).
* För självstudier, exempel, snabb starter och annan dokumentation går du [till Azure för python-utvecklare](/azure/python/).
