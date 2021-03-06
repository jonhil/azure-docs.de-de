---
title: Die Qualifikation „Bildanalyse“ der kognitiven Suche – Azure Search
description: Extrahieren Sie semantischen Text durch Bildanalyse mithilfe des kognitiven Skills „Bildanalyse“ in einer Azure Search-Anreicherungspipeline.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fc8780c5b99ce98a55a6cb08cfaa6585e5a4e89a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313308"
---
#   <a name="image-analysis-cognitive-skill"></a>Bildanalyse – kognitiver Skill

Der Skill **Bildanalyse** extrahiert einen umfangreichen Satz von visuellen Merkmalen aus dem Bildinhalt. So können Sie beispielsweise anhand eines Bilds eine Beschriftung erstellen, Tags generieren oder Prominente und Sehenswürdigkeiten identifizieren.

> [!NOTE]
> Ab dem 21. Dezember 2018 können Sie Cognitive Services-Ressourcen einer Azure Search-Qualifikationsgruppe zuordnen. Dies ermöglicht uns, für die Ausführung von Qualifikationsgruppen mit der Gebührenberechnung zu beginnen. Außerdem beginnen wir an diesem Datum damit, die Bildextraktion als Teil der Aufschlüsselung von Dokumenten zu berechnen. Die Textextraktion aus Dokumenten wird weiterhin ohne Zusatzkosten angeboten.
>
> Die Ausführung interner Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion entsprechen den Vorschaupreisen. Sie werden auf der [Preisseite von Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) beschrieben. [Weitere Informationen](cognitive-search-attach-cognitive-services.md).


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Skillparameter

Bei den Parametern wird zwischen Groß- und Kleinschreibung unterschieden.

| Parametername     | BESCHREIBUNG |
|--------------------|-------------|
| defaultLanguageCode   |  Eine Zeichenfolge, die angibt, welche Sprache zurückgegeben werden soll. Der Dienst gibt die Ergebnisse der Erkennung in einer bestimmten Sprache zurück. Wenn dieser Parameter nicht angegeben wird, ist der Standardwert „en“. <br/><br/>Unterstützte Sprachen: <br/>*en*: Englisch (Standard) <br/> *zh*: vereinfachtes Chinesisch|
|visualFeatures |   Ein Array aus Zeichenfolgen, die angibt, welche Arten von visuellen Merkmalen zurückgegeben werden sollen. Folgende Arten von visuellen Merkmalen sind gültig:  <ul><li> *categories*: Kategorisiert Bildinhalte gemäß einer Taxonomie, die in der [Dokumentation](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy) zu Cognitive Services definiert ist.</li><li> *tags*: Erstellt Tags für das Bild in einer detaillierten Liste aus Wörtern, die sich auf den Bildinhalt beziehen.</li><li>*Description*: Beschreibt den Bildinhalt mit einem vollständigen Satz.</li><li>*Faces*: Erkennt, ob Gesichter vorhanden sind. Wenn Gesichter vorhanden sind, generiert dieses Merkmal Informationen zu Koordinaten, Geschlecht und Alter.</li><li> *ImageType*: Erkennt, ob das Bild eine pixelbasierte Rastergrafik oder eine linienbasierte Vektorgrafik ist.</li><li>   *Color*: Bestimmt die Akzentfarbe und die dominante Farbe und ermittelt, ob ein Bild schwarzweiß ist.</li><li>*Adult*: Erkennt, ob das Bild pornografische Natur ist (erkennt Nacktheit oder sexuelle Handlungen). Zweideutige und freizügige Inhalte werden ebenfalls erkannt.</li></ul> Bei den Namen der visuellen Merkmale wird die Groß- und Kleinschreibung beachtet.|
| details   | Ein Array aus Zeichenfolgen, die angeben, welche domänenspezifischen Informationen zurückgegeben werden sollen. Folgende Arten von visuellen Merkmalen sind gültig: <ul><li>*Celebrities*: Identifiziert Prominente in einem Bild.</li><li>*Landmarks*: Identifiziert Sehenswürdigkeiten in einem Bild.</li></ul>
 |

## <a name="skill-inputs"></a>Skilleingaben

| Eingabename      | BESCHREIBUNG                                          |
|---------------|------------------------------------------------------|
| image         | Komplexer Typ. Arbeitet derzeit mit dem Feld „/document/normalized_images“, das vom Azure Blob-Indexer generiert wird, wenn ```imageAction``` auf ```generateNormalizedImages``` gesetzt ist. Weitere Informationen finden Sie im [Beispiel](#sample-output).|



##  <a name="sample-definition"></a>Beispieldefinition
```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>Beispieleingabe

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {                
                "image":  {
                               "data": "BASE64 ENCODED STRING OF A JPEG IMAGE",
                               "width": 500,
                               "height": 300,
                               "originalWidth": 5000,  
                               "originalHeight": 3000,
                               "rotationFromOriginal": 90,
                               "contentOffset": 500  
                           }
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Beispielausgabe

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceBoundingBox": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceBoundingBox": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBwImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>Auftretende Fehler
In den folgenden Fällen werden keine Elemente extrahiert.

| Fehlercode | BESCHREIBUNG |
|------------|-------------|
| NotSupportedLanguage | Die angegebene Sprache wird nicht unterstützt. |
| InvalidImageUrl | Die Bild-URL ist falsch formatiert, oder es kann nicht darauf zugegriffen werden.|
| InvalidImageFormat | Bei den Eingabedaten handelt es sich nicht um ein gültiges Bild. |
| InvalidImageSize | Das Eingabebild ist zu groß. |
| NotSupportedVisualFeature  | Der angegebene Merkmaltyp ist ungültig. |
| NotSupportedImage | Nicht unterstütztes Bild, z.B. Kinderpornografie. |
| InvalidDetails | Nicht unterstütztes domänenspezifisches Modell. |

## <a name="see-also"></a>Weitere Informationen

+ [Vordefinierte Skills](cognitive-search-predefined-skills.md)
+ [Definieren eines Skillsets](cognitive-search-defining-skillset.md)
+ [Erstellen eines Indexers (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
