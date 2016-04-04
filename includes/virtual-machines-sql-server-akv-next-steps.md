## Nächste Schritte
Nach dem Aktivieren der Azure-Schlüsseltresor-Integration können Sie die SQL Server-Verschlüsselung auf Ihrem virtuellen SQL-Computer aktivieren. Zuerst müssen Sie in Ihrem Schlüsseltresor einen asymmetrischen Schlüssel und in SQL Server auf Ihrem virtuellen Computer einen symmetrischen Schlüssel erstellen. Sie können dann T-SQL-Anweisungen ausführen, um die Verschlüsselung für Ihre Datenbanken und Backups zu aktivieren.

Es gibt verschiedene Arten der Verschlüsselung, die Sie nutzen können:

- [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
- [Verschlüsselte Sicherungen](https://msdn.microsoft.com/library/dn449489.aspx)
- [Column Level Encryption (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Die folgenden Transact-SQL-Skripts enthalten Beispiele für jeden dieser Bereiche.

>[AZURE.NOTE] Jedes Beispiel basiert auf diesen beiden erforderlichen Komponenten: einem asymmetrischen Schlüssel aus Ihrem Schlüsseltresor mit dem Namen **CONTOSO\_KEY** und Anmeldeinformationen, die mit der Funktion für die Azure-Schlüsseltresor-Integration erstellt wurden und den Namen **Azure\_EKM\_TDE\_cred** haben.

### Transparent Data Encryption (TDE)
1. Erstellen Sie eine SQL Server-Anmeldung, die vom Datenbankmodul für TDE verwendet werden kann, und fügen Sie die Anmeldeinformationen hinzu.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it loads a database 
		-- encrypted by TDE.
		CREATE LOGIN TDE_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the TDE Login to add the credential for use by the 
		-- Database Engine to access the key vault
		ALTER LOGIN TDE_Login 
		ADD CREDENTIAL Azure_EKM_TDE_cred;
		GO
	
2. Erstellen Sie den Datenbankverschlüsselungsschlüssel, der für TDE verwendet werden soll.
	
		USE ContosoDatabase;
		GO
		
		CREATE DATABASE ENCRYPTION KEY 
		WITH ALGORITHM = AES_128 
		ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
		GO
		
		-- Alter the database to enable transparent data encryption.
		ALTER DATABASE ContosoDatabase 
		SET ENCRYPTION ON;
		GO

### Verschlüsselte Sicherungen
1. Erstellen Sie eine SQL Server-Anmeldung, die vom Datenbankmodul für die Verschlüsselung von Sicherungen verwendet werden kann, und fügen Sie die Anmeldeinformationen hinzu.
	
		USE master;
		-- Create a SQL Server login associated with the asymmetric key 
		-- for the Database engine to use when it is encrypting the backup.
		CREATE LOGIN Backup_Login 
		FROM ASYMMETRIC KEY CONTOSO_KEY;
		GO 
		
		-- Alter the Encrypted Backup Login to add the credential for use by 
		-- the Database Engine to access the key vault
		ALTER LOGIN Backup_Login 
		ADD CREDENTIAL Azure_EKM_Backup_cred ;
		GO
	
2. Sichern Sie die Datenbank, indem Sie die Verschlüsselung mit dem asymmetrischen Schlüssel angeben, der im Schlüsseltresor gespeichert ist.
	
		USE master;
		BACKUP DATABASE [DATABASE_TO_BACKUP]
		TO DISK = N'[PATH TO BACKUP FILE]' 
		WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD, 
		ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
		GO

### Column Level Encryption (CLE)
Mit diesem Skript wird ein symmetrischer Schlüssel erstellt, der durch den asymmetrischen Schlüssel im Schlüsseltresor geschützt ist, und anschließend wird der symmetrische Schlüssel zum Verschlüsseln der Daten in der Datenbank verwendet.

	CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
	WITH ALGORITHM=AES_256
	ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	DECLARE @DATA VARBINARY(MAX);
	
	--Open the symmetric key for use in this session
	OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY 
	DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;
	
	--Encrypt syntax
	SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));
	
	-- Decrypt syntax
	SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));
	
	--Close the symmetric key
	CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;

## Weitere Ressourcen
Weitere Informationen zur Verwendung dieser Verschlüsselungsfunktionen finden Sie unter [Verwenden der erweiterbaren Schlüsselverwaltung mit SQL Server-Verschlüsselungsfunktionen](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Beachten Sie, dass bei den Schritten in diesem Artikel davon ausgegangen wird, dass bei Ihnen SQL Server bereits auf einem virtuellen Azure-Computer ausgeführt wird. Ist dies nicht der Fall, helfen Ihnen die Informationen unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](../articles/virtual-machines/virtual-machines-windows-classic-portal-sql.md) weiter. Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie in der [Übersicht zu SQL Server auf virtuellen Azure-Computern](../articles/virtual-machines/virtual-machines-windows-classic-sql-overview.md).

<!---HONumber=AcomDC_0323_2016-->