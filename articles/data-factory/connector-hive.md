---
title: Gegevens kopiëren van Hive met behulp van Azure Data Factory | Microsoft Docs
description: Ontdek hoe u gegevens kopiëren van Hive naar gegevensarchieven ondersteunde sink met behulp van een kopieeractiviteit in een Azure Data Factory-pijplijn.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: jingwang
ms.openlocfilehash: 379cc5412d317680afa9b03f0eea60c7f1a3b60d
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051079"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Gegevens kopiëren van Hive met behulp van Azure Data Factory 

In dit artikel bevat een overzicht van het gebruik van de Kopieeractiviteit in Azure Data Factory om gegevens te kopiëren van Hive. Dit is gebaseerd op de [activiteit overzicht kopiëren](copy-activity-overview.md) artikel met daarin een algemeen overzicht van de kopieeractiviteit.

## <a name="supported-capabilities"></a>Ondersteunde mogelijkheden

U kunt gegevens kopiëren van Hive met alle ondersteunde sink-gegevensarchief. Zie voor een lijst van opgeslagen gegevens die worden ondersteund als bronnen/put door met de kopieerbewerking de [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats) tabel.

Azure Data Factory biedt een ingebouwde stuurprogramma's zodat connectiviteit, dus u hoeft niet te gebruik van deze connector stuurprogramma handmatig installeren.

## <a name="getting-started"></a>Aan de slag

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

De volgende secties bevatten informatie over de eigenschappen die worden gebruikt voor het definiëren van Data Factory-entiteiten specifieke met Hive-connector.

## <a name="linked-service-properties"></a>Eigenschappen van de gekoppelde service

De volgende eigenschappen worden ondersteund voor Hive gekoppelde service:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type moet worden ingesteld op: **Hive** | Ja |
| host | IP-adres of de hostnaam de naam van de Hive-server, gescheiden door ';' voor meerdere hosts (alleen bij serviceDiscoveryMode ingeschakeld is).  | Ja |
| poort | De TCP-poort die de Hive-server gebruikt om te luisteren naar verbindingen van clients. Als u verbinding met Azure HDInsights maken, moet u poort opgeven als 443. | Ja |
| serverType | Het type van Hive-server. <br/>Toegestane waarden zijn: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Nee |
| thriftTransportProtocol | Het te gebruiken in de laag Thrift-transportprotocol. <br/>Toegestane waarden zijn: **binaire**, **SASL**, **HTTP** | Nee |
| authenticationType | De verificatiemethode die wordt gebruikt voor toegang tot de Hive-server. <br/>Toegestane waarden zijn: **anoniem**, **gebruikersnaam**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ja |
| serviceDiscoveryMode | True om aan te geven met de service ZooKeeper false niet.  | Nee |
| zooKeeperNameSpace | De naamruimte op ZooKeeper onder welke Server Hive 2 knooppunten worden toegevoegd.  | Nee |
| useNativeQuery | Geeft aan of het stuurprogramma systeemeigen HiveQL-query's gebruikt, of deze naar een equivalente formulier in HiveQL converteert.  | Nee |
| gebruikersnaam | De gebruikersnaam die u gebruikt voor toegang tot Hive-Server.  | Nee |
| wachtwoord | Het wachtwoord dat overeenkomt met de gebruiker. Dit veld markeren als een SecureString Bewaar deze zorgvuldig in Data Factory of [verwijzen naar een geheim dat is opgeslagen in Azure Key Vault](store-credentials-in-key-vault.md). | Nee |
| httpPath | De gedeeltelijke URL overeenkomt met de Hive-server.  | Nee |
| enableSsl | Geeft aan of de verbindingen met de server zijn versleuteld met SSL. De standaardwaarde is ingesteld op false.  | Nee |
| trustedCertPath | Het volledige pad van het .pem-bestand met de vertrouwde CA-certificaten voor het controleren van de server om verbinding te maken via SSL. Deze eigenschap kan alleen worden ingesteld wanneer u SSL op host zichzelf IR De standaardwaarde is het bestand cacerts.pem is geïnstalleerd met de IR  | Nee |
| useSystemTrustStore | Hiermee geeft u op of u een CA-certificaat uit het archief van de vertrouwensrelatie system of vanuit een opgegeven PEM-bestand. De standaardwaarde is ingesteld op false.  | Nee |
| allowHostNameCNMismatch | Geeft aan of moet de naam van een Certificeringsinstantie uitgegeven SSL-certificaat overeenkomen met de hostnaam van de server om verbinding te maken via SSL. De standaardwaarde is ingesteld op false.  | Nee |
| allowSelfSignedServerCert | Hiermee bepaalt u of zelfondertekende certificaten van de server. De standaardwaarde is ingesteld op false.  | Nee |
| connectVia | De [integratie Runtime](concepts-integration-runtime.md) moeten worden gebruikt voor het verbinding maken met het gegevensarchief. U kunt Self-hosted integratie Runtime of Azure integratie Runtime gebruiken (als uw gegevensarchief openbaar toegankelijk). Als niet wordt opgegeven, wordt de standaardwaarde Azure integratie Runtime. |Nee |

**Voorbeeld:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Eigenschappen van gegevensset

Zie voor een volledige lijst met secties en de eigenschappen die beschikbaar zijn voor het definiëren van gegevenssets, de [gegevenssets](concepts-datasets-linked-services.md) artikel. Deze sectie bevat een lijst met eigenschappen die worden ondersteund door het Hive-gegevensset.

Om gegevens te kopiëren van Hive, stel de eigenschap type van de gegevensset **HiveObject**. Er is geen aanvullende typespecifieke-eigenschap in dit type dataset.

**Voorbeeld**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschappen van de kopieeractiviteit

Zie voor een volledige lijst met secties en de eigenschappen die beschikbaar zijn voor het definiëren van activiteiten, de [pijplijnen](concepts-pipelines-activities.md) artikel. Deze sectie bevat een lijst met eigenschappen die ondersteund worden door Hive-bron.

### <a name="hivesource-as-source"></a>HiveSource als bron

Om gegevens te kopiëren van Hive, stelt u het brontype in de kopieerbewerking naar **HiveSource**. De volgende eigenschappen worden ondersteund in de kopieerbewerking **bron** sectie:

| Eigenschap | Beschrijving | Vereist |
|:--- |:--- |:--- |
| type | De eigenschap type van de bron voor kopiëren-activiteit moet worden ingesteld op: **HiveSource** | Ja |
| query | Gebruik de aangepaste SQL-query om gegevens te lezen. Bijvoorbeeld: `"SELECT * FROM MyTable"`. | Ja |

**Voorbeeld:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Volgende stappen
Zie voor een lijst met gegevensarchieven als bronnen en put wordt ondersteund door de kopieeractiviteit in Azure Data Factory, [ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats).
