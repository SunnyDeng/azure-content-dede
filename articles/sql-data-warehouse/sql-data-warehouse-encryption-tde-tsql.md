<properties 
   pageTitle="Erste Schritte mit SQL Data Warehouse Transparent Data Encryption (TDE) TSQL | Microsoft Azure" 
   description="Erste Schritte mit SQL Data Warehouse Transparent Data Encryption (TDE) TSQL" 
   services="sql-data-warehouse" 
   documentationCenter="" 
   authors="twounder" 
   manager="" 
   editor=""/>

<tags 
   ms.service="sql-data-warehouse" 
   ms.workload="data-management" 
   ms.tgt_pltfrm="na" 
   ms.devlang="na" 
   ms.topic="article" 
   ms.date="01/04/2016" 
   ms.author="twounder"/>
 
# Erste Schritte mit Transparent Data Encryption (TDE)
> [AZURE.SELECTOR]
- [Azure Classic Portal](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

Azure SQL Data Warehouse Transparent Data Encryption (TDE) ist ein zusätzlicher Schutz vor der Bedrohung durch schädliche Aktivitäten. Hierzu werden die Schritte für die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, die dazugehörigen Backups und die Transaktionsprotokolldateien im Ruhezustand ausgeführt, ohne dass Änderungen an der Anwendung erforderlich sind.

TDE verschlüsselt die Speicherung einer gesamten Datenbank, indem ein symmetrischer Schlüssel verwendet wird, der als Datenbankverschlüsselungsschlüssel bezeichnet wird. In SQL-Datenbank wird der Datenbankverschlüsselungsschlüssel mit einem integrierten Serverzertifikat geschützt. Das integrierte Serverzertifikat ist für jeden SQL-Datenbank-Server eindeutig. Microsoft führt für diese Zertifikate nach spätestens 90 Tagen automatisch eine Rotation durch. Eine allgemeine Beschreibung von TDE finden Sie unter [Transparente Datenverschlüsselung (TDE)].

##Aktivieren der Verschlüsselung

Führen Sie diese Schritte aus, um TDE für ein SQL Data Warehouse zu aktivieren:

1. Stellen Sie eine Verbindung mit der Datenbank *master* auf dem Server her, auf dem die Datenbank gehostet wird. Verwenden Sie eine Anmeldung, bei der es sich um einen Administrator oder ein Mitglied der Rolle **dbmanager** in der Datenbank „master“ handelt.
2. Führen Sie die folgende Anweisung aus, um die Datenbank zu verschlüsseln.

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

##Deaktivieren der Verschlüsselung

Führen Sie diese Schritte aus, um TDE für ein SQL Data Warehouse zu deaktivieren:

1. Stellen Sie eine Verbindung mit der Datenbank *master* her. Verwenden Sie eine Anmeldung, bei der es sich um einen Administrator oder ein Mitglied der Rolle **dbmanager** in der Datenbank „master“ handelt.
2. Führen Sie die folgende Anweisung aus, um die Datenbank zu verschlüsseln.

```
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

##Überprüfen der Verschlüsselung

Führen Sie die folgenden Schritte aus, um den Verschlüsselungsstatus für ein SQL Data Warehouse zu überprüfen:

1. Stellen Sie eine Verbindung mit der Datenbank *master* oder einer Instanzdatenbank her. Verwenden Sie eine Anmeldung, bei der es sich um einen Administrator oder ein Mitglied der Rolle **dbmanager** in der Datenbank „master“ handelt.
2. Führen Sie die folgende Anweisung aus, um die Datenbank zu verschlüsseln.

```
SELECT
	[name],
	[is_encrypted]
FROM
	sys.databases;
```

Das Ergebnis ```1``` steht für eine verschlüsselte Datenbank, und mit ```0``` wird eine nicht verschlüsselte Datenbank angegeben.

 
<!--Anchors-->
[Transparente Datenverschlüsselung (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->

<!--Link references-->

<!---HONumber=AcomDC_0107_2016-->