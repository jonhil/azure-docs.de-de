---
title: 'Datenstichproben in SQLServer in Azure: Team Data Science-Prozess'
description: Erstellen von Stichproben aus Daten in Azure Blob Storage durch programmgesteuertes Herunterladen, um dann mit Prozeduren in Python-Code Stichproben zu erstellen.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0f9795e6a9a451ab1492e62fd54faea5894d99ae
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136493"
---
# <a name="heading"></a>Datenstichproben im Azure-Blob-Speicher

Dieser Artikel behandelt das Erstellen von Stichproben aus Daten in Azure Blob Storage durch programmgesteuertes Herunterladen, um dann mit Prozeduren in Python-Code Stichproben zu erstellen.

**Warum eine Datenstichprobe entnehmen?**
Wenn das zu analysierende Dataset groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Dies erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des Cortana-Analyseprozesses darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Modelle für das maschinelle Lernen zu erstellen.

Dieser Stichprobentask ist ein Schritt im [Team Data Science-Prozess (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="download-and-down-sample-data"></a>Download und Downsampling von Daten 
1. Laden Sie die Daten aus Azure Blob Storage mithilfe des Blobdiensts aus dem folgenden Python-Beispielcode herunter: 
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))

2. Lesen Sie die Daten aus der heruntergeladenen Datei in ein Pandas-DataFrame ein.
   
        import pandas as pd
   
        #directly ready from file on disk
        dataframe_blobdata = pd.read_csv(LOCALFILE)

3. Erstellen Sie mit der `random.choice`-Funktion von `numpy` wie folgt Stichproben:
   
        # A 1 percent sample
        sample_ratio = 0.01 
        sample_size = np.round(dataframe_blobdata.shape[0] * sample_ratio)
        sample_rows = np.random.choice(dataframe_blobdata.index.values, sample_size)
        dataframe_blobdata_sample = dataframe_blobdata.ix[sample_rows]

Sie können nun mit dem DataFrame von oben mit der 1-%-Stichprobe weitere Datensuchvorgänge durchführen und Funktionen verarbeiten.

## <a name="heading"></a>Hochladen von Daten und Einlesen in Azure Machine Learning
Mit dem folgenden Beispielcode können Sie ein Downsampling der Daten durchführen und diese direkt in Azure Machine Learning verwenden:

1. Schreiben Sie den DataFrame in eine lokale Datei:
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. Laden Sie die lokale Datei mit folgendem Beispielcode in ein Azure-Blob hoch:
   
        from azure.storage.blob import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading to the blob:"+ BLOBNAME)

3. Lesen Sie die Daten wie in der folgenden Abbildung dargestellt mit dem Azure Machine Learning-Modul [Import Data](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) aus dem Azure-Blob:

![Reader-Blob](./media/sample-data-blob/reader_blob.png)

