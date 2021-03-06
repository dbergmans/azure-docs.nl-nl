---
title: Node.js gebruiken om een query uit te voeren voor een Azure SQL-database | Microsoft Docs
description: In dit onderwerp ziet u hoe u Node.js gebruikt om een programma te maken dat is verbonden met een Azure SQL-database, en hoe u een query voor deze database uitvoert met behulp van Transact-SQL-instructies.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: nodejs
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: c6cfbd330f77c5a2f448093e58684032a84a41d9
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063469"
---
# <a name="use-nodejs-to-query-an-azure-sql-database"></a>Node.js gebruiken om een query uit te voeren voor een Azure SQL-database

In deze snelstart wordt gedemonstreerd hoe u [Node.js](https://nodejs.org/en/) gebruikt om een programma te maken dat verbinding maakt met een Azure SQL-database, en hoe u Transact-SQL-instructies gebruikt om een query uit te voeren voor gegevens.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over het volgende beschikt om deze snelstart te voltooien:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Een [firewallregel op serverniveau](sql-database-get-started-portal-firewall.md) voor het openbare IP-adres van de computer die u gebruikt voor deze snelstart.

- U hebt Node.js en verwante software voor het besturingssysteem geïnstalleerd:
    - **MacOS**: installeer Homebrew en Node.js, en installeer vervolgens het ODBC-stuurprogramma en SQLCMD. Zie [Stap 1.2 en 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).
    - **Ubuntu**: installeer Node.js en installeer vervolgens het ODBC-stuurprogramma en SQLCMD. Zie [Stap 1.2 en 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).
    - **Windows**: installeer Chocolatey en Node.js, en installeer vervolgens het ODBC-stuurprogramma en SQL CMD. Zie [Stap 1.2 en 1.3](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

## <a name="sql-server-connection-information"></a>SQL Server-verbindingsgegevens

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

> [!IMPORTANT]
> U moet een firewallregel hebben ingesteld voor het openbare IP-adres van de computer waarop u deze zelfstudie uitvoert. Als u een andere computer gebruikt of een ander openbaar IP-adres hebt, maakt u een [firewallregel op serverniveau met behulp van Azure Portal](sql-database-get-started-portal-firewall.md). 

## <a name="create-a-nodejs-project"></a>Een Node.js-project maken

Open een opdrachtprompt en maak een map met de naam *sqltest*. Navigeer naar de map die u hebt gemaakt, en voer de volgende opdracht uit:

    
    npm init -y
    npm install tedious
    npm install async
    

## <a name="insert-code-to-query-sql-database"></a>Code invoegen om een query uit te voeren voor een SQL-database

1. Maak een nieuw bestand in uw ontwikkelomgeving of favoriete teksteditor **sqltest.js**.

2. Vervang de inhoud door de volgende code en voeg de juiste waarden toe voor de server, de database, de gebruiker en het wachtwoord.

   ```js
   var Connection = require('tedious').Connection;
   var Request = require('tedious').Request;

   // Create connection to database
   var config = 
      {
        userName: 'someuser', // update me
        password: 'somepassword', // update me
        server: 'edmacasqlserver.database.windows.net', // update me
        options: 
           {
              database: 'somedb' //update me
              , encrypt: true
           }
      }
   var connection = new Connection(config);

   // Attempt to connect and execute queries if connection goes through
   connection.on('connect', function(err) 
      {
        if (err) 
          {
             console.log(err)
          }
       else
          {
              queryDatabase()
          }
      }
    );

   function queryDatabase()
      { console.log('Reading rows from the Table...');

          // Read all rows from table
        request = new Request(
             "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
                function(err, rowCount, rows) 
                   {
                       console.log(rowCount + ' row(s) returned');
                       process.exit();
                   }
               );
    
        request.on('row', function(columns) {
           columns.forEach(function(column) {
               console.log("%s\t%s", column.metadata.colName, column.value);
            });
                });
        connection.execSql(request);
      }
```

## <a name="run-the-code"></a>De code uitvoeren

1. Voer bij de opdrachtprompt de volgende opdrachten uit:

   ```js
   node sqltest.js
   ```

2. Controleer of de bovenste 20 rijen worden geretourneerd, en sluit vervolgens het toepassingsvenster.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [Microsoft-stuurprogramma Node.js voor SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- Meer informatie over [verbinding maken met en een query uitvoeren voor een Azure SQL-database met behulp van .NET Core](sql-database-connect-query-dotnet-core.md) in Windows/Linux/macOS.  
- Meer informatie over [Aan de slag met .NET Core in Windows/Linux/macOS met behulp van de opdrachtregel](/dotnet/core/tutorials/using-with-xplat-cli).
- Meer informatie over [Uw eerste Azure SQL-database ontwerpen met behulp van SSMS](sql-database-design-first-database.md) of [Uw eerste Azure SQL-database ontwerpen met behulp van .NET](sql-database-design-first-database-csharp.md).
- Meer informatie over [Verbinding maken en query's uitvoeren met SSMS](sql-database-connect-query-ssms.md)
- Meer informatie over [Verbinding maken en query's uitvoeren met Visual Studio Code](sql-database-connect-query-vscode.md).

