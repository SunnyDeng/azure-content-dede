<properties 
	pageTitle="Erste Schritte mit Transparent Data Encryption (TDE) in SQL Data Warehouse | Microsoft Azure" 
	description="Erste Schritte mit Transparent Data Encryption (TDE) in SQL Data Warehouse" 
	services="sql-data-warehouse" 
	documentationCenter="" 
	authors="twounder" 
	manager="barbkess" 
	editor=""/>

<tags 
	ms.service="sql-data-warehouse" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="twounder"/>
 
# Erste Schritte mit Transparent Data Encryption (TDE) in SQL Data Warehouse
> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

Azure SQL Data Warehouse Transparent Data Encryption (TDE) ist ein zusätzlicher Schutz vor der Bedrohung durch schädliche Aktivitäten. Hierzu werden die Schritte für die Echtzeitverschlüsselung und -entschlüsselung der Datenbank, die dazugehörigen Backups und die Transaktionsprotokolldateien im Ruhezustand ausgeführt, ohne dass Änderungen an der Anwendung erforderlich sind.

TDE verschlüsselt die Speicherung einer gesamten Datenbank, indem ein symmetrischer Schlüssel verwendet wird, der als Datenbankverschlüsselungsschlüssel bezeichnet wird. In SQL-Datenbank wird der Datenbankverschlüsselungsschlüssel mit einem integrierten Serverzertifikat geschützt. Das integrierte Serverzertifikat ist für jeden SQL-Datenbank-Server eindeutig. Microsoft führt für diese Zertifikate nach spätestens 90 Tagen automatisch eine Rotation durch. Eine allgemeine Beschreibung von TDE finden Sie unter [Transparente Datenverschlüsselung (TDE)].

##Aktivieren der Verschlüsselung

Führen Sie die folgenden Schritte aus, um TDE für ein SQL Data Warehouse zu aktivieren:

1. Öffnen Sie die Datenbank im [Azure-Portal](https://portal.azure.com).
2. Klicken Sie im Datenbank-Blatt auf die Schaltfläche **Einstellungen**.	
3. Wählen Sie die Option **Transparent data encryption** aus. ![][1] 
4. Wählen Sie die Einstellung **Ein** aus. ![][2] 
5. Wählen Sie **Speichern** aus. ![][3]  

##Deaktivieren der Verschlüsselung

Führen Sie die folgenden Schritte aus, um TDE für ein SQL Data Warehouse zu deaktivieren:

1. Öffnen Sie die Datenbank im [Azure-Portal](https://portal.azure.com).
2. Klicken Sie im Datenbank-Blatt auf die Schaltfläche **Einstellungen**.	
3. Wählen Sie die Option **Transparent data encryption** aus. ![][1] 
4. Wählen Sie die Einstellung **Aus** aus. ![][4] 
5. Wählen Sie **Speichern** aus. ![][5]  




<!--Anchors-->
[Transparente Datenverschlüsselung (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

<!---HONumber=AcomDC_1125_2015-->