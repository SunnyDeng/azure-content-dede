<properties 
    pageTitle="Ändern der Dienstebene und Leistungsstufe einer Azure SQL-Datenbank mithilfe von PowerShell" 
    description="In „Ändern der Dienstebene und Leistungsstufe einer Azure SQL-Datenbank“ wird beschrieben, wie Sie die Dienstebene und Leistungsstufe Ihrer SQL-Datenbank mit PowerShell zentral hoch- oder herunterstufen. Ändern des Tarifs einer Azure SQL-Datenbank mit PowerShell." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/10/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Ändern der Dienstebene und Leistungsstufe (Tarif) einer SQL-Datenbank mit PowerShell

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


In diesem Artikel wird beschrieben, wie Sie die Dienstebene und Leistungsstufe Ihrer SQL-Datenbank mit PowerShell ändern.

Bestimmen Sie anhand der Informationen unter [Aktualisieren von Web-/Business-SQL-Datenbanken auf die neuen Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](sql-database-upgrade-new-service-tiers.md) und [Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](sql-database-service-tiers.md) die geeignete Dienstebene und Leistungsstufe für Ihre Azure SQL-Datenbank.

> [AZURE.IMPORTANT]Ein Ändern der Dienstebene und Leistungsstufe einer SQL-Datenbank ist ein Onlinevorgang. Dies bedeutet, dass die Datenbank während des gesamten Vorgangs ohne Ausfallzeit online und verfügbar bleibt.

- Für ein Downgrade einer Datenbank sollte die Datenbank kleiner als die in der Zieldienstebene maximal zulässige Größe sein. 
- Beim Aktualisieren einer Datenbank, für die die Option für [standardmäßige Georeplikation](https://msdn.microsoft.com/library/azure/dn758204.aspx) oder [aktive Georeplikation](https://msdn.microsoft.com/library/azure/dn741339.aspx) aktiviert ist, müssen Sie vor der Aktualisierung der primären Datenbank zunächst die zugehörigen sekundären Datenbanken auf die gewünschte Leistungsstufe aktualisieren.
- Beim Downgrade von einer Premium-Dienstebene müssen Sie zuerst alle geografischen Replikationsbeziehungen beenden. Sie können die im Thema [Beenden einer fortlaufenden Kopierbeziehung](https://msdn.microsoft.com/library/azure/dn741323.aspx) beschriebenen Schritte verwenden, um den Replikationsprozess zwischen der primären und aktiven sekundären Datenbank zu beenden.
- Die Angebote des Wiederherstellungsdienstes variieren für die verschiedenen Dienstebenen. Wenn Sie ein Downgrade durchführen, verlieren Sie eventuell die Möglichkeit einer Zeitpunktwiederherstellung, oder der Aufbewahrungszeitraum für Sicherungen verkürzt sich. Weitere Informationen finden Sie unter [Sichern und Wiederherstellen der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- Sie können innerhalb von 24 Stunden bis zu vier einzelne Datenbankänderungen (Dienstebene oder Leistungsstufen) vornehmen.
- Die neuen Eigenschaften für die Datenbank werden erst angewendet, wenn die Änderungen abgeschlossen sind.



**Damit Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:**

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Eine Azure SQL-Datenbank. Wenn Sie nicht über eine SQL-Datenbank verfügen, können Sie die Erstellung anhand der Schritte im folgenden Artikel durchführen: [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).
- Azure PowerShell. Sie können die Azure PowerShell-Module herunterladen und installieren, indem Sie den [Microsoft-Webplattform-Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409) ausführen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

Die Cmdlets zum Ändern der Dienststufe von Azure SQL-Datenbanken befinden sich im Azure-Ressourcen-Manager-Modul. Wenn Sie Azure PowerShell starten, werden die Cmdlets im Azure-Modul standardmäßig importiert. Um zum Azure-Ressourcen-Manager-Modul zu wechseln, verwenden Sie das Cmdlet "Switch-AzureMode".

	Switch-AzureMode -Name AzureResourceManager

Wenn beim Ausführen von **Switch-AzureMode** die Warnung „Das Cmdlet *Switch-AzureMode* ist veraltet und wird in zukünftigen Versionen nicht mehr enthalten sein“ angezeigt wird, liegt kein Problem vor. Fahren Sie einfach mit dem nächsten Schritt fort, um Ihre Anmeldeinformationen zu konfigurieren.

Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](powershell-azure-resource-manager.md).

## Konfigurieren der Anmeldeinformationen und Auswählen des Abonnements

Zuerst müssen Sie den Zugriff auf Ihr Azure-Konto einrichten. Starten Sie also PowerShell, und führen Sie dann das folgende Cmdlet aus. Geben Sie auf dem Anmeldebildschirm die E-Mail-Adresse und das Kennwort wie für die Anmeldung beim Azure-Portal ein.

	Add-AzureAccount

Nach der erfolgreichen Anmeldung werden einige Informationen auf dem Bildschirm angezeigt, wie die ID, mit der Sie sich angemeldet haben, und die Azure-Abonnements, auf die Sie zugreifen können.


### Auswählen des Azure-Abonnements

Um das Abonnement auszuwählen, benötigen Sie Ihre Abonnement-ID oder den Anmeldenamen für das Abonnement (**-SubscriptionName**). Sie können die Abonnement-ID aus den Informationen im vorherigen Schritt kopieren. Falls Sie über mehrere Abonnements verfügen und mehr Details benötigen, können Sie auch das **Get-AzureSubscription**-Cmdlet ausführen und die gewünschten Abonnementinformationen aus dem Resultset kopieren. Wenn Sie Ihre Abonnementinformationen haben, führen Sie das folgende Cmdlet aus:

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureSubscription -SubscriptionId $SubscriptionId

Nach dem erfolgreichen Ausführen von **Select-AzureSubscription** kehren Sie zur PowerShell-Eingabeaufforderung zurück. Wenn Sie über mehrere Abonnements verfügen, können Sie **Get-AzureSubscription** ausführen und überprüfen, ob das gewünschte Abonnement den Wert **IsCurrent: True** aufweist.


 


## Ändern der Dienstebene und Leistungsstufe Ihrer SQL-Datenbank

Führen Sie das **Set-AzureSqlDatabase**-Cmdlet aus, und legen Sie **-RequestedServiceObjectiveName** auf die Leistungsstufe des gewünschten Tarifs fest; z. B. *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, ...

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## PowerShell-Beispielskript zum Ändern der Dienstebene und Leistungsstufe Ihrer SQL-Datenbank

    
	Switch-AzureMode -Name AzureResourceManager
    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## Nächste Schritte

- [Horizontal hoch- und herunterskalieren](sql-database-elastic-scale-get-started.md)
- [Herstellen einer Verbindung mit einer SQL-Datenbank und Abfragen dieser Datenbank mit SSMS](sql-database-connect-query-ssms.md)
- [Exportieren einer Azure SQL-Datenbank](sql-database-export-powershell.md)

## Zusätzliche Ressourcen

- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [SQL-Datenbank-Dokumentation](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Oct15_HO1-->