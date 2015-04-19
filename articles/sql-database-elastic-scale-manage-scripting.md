<properties 
	pageTitle="Skripterstellung für die elastische Skalierung mit Skripts" 
	description="Erstellen Sie Skripts für Elastic Scale-Aufgaben mit PowerShell und Azure Automation Service Runbooks." 
	services="sql-database" 
	documentationCenter="" 
	manager="stuartozer" 
	authors="Joseidz" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="Joseidz@microsoft.com"/>

# Verwalten der elastischen Skalierung mit Skripts


## Azure Automation-Dienst 

[Azure Automation](http://azure.microsoft.com/documentation/services/automation/) ergänzt die Azure-Plattform durch einen leistungsfähigen und dringend benötigten Dienst zur Ausführung des PowerShell-Workflows. Jetzt können Sie Wartungsaufgaben automatisieren, deren Ausführung im Rahmen der allgemeinen Azure-Portalfunktionalität schwierig ist.  Erstellen Sie einfach einen PowerShell-Workflow (in Azure Automation als **Runbook** bezeichnet), laden Sie ihn in die Cloud hoch, und legen Sie einen Zeitplan für die Ausführung des Runbooks fest. Dieses Dokument bietet Informationen zum End-to-End-Setup von Azure Automation für einige Shard-Elastizitäts-Beispiele. Weitere Informationen finden Sie in der [Ankündigung der Vorschau](http://blogs.technet.com/b/in_the_cloud/archive/2014/04/15/announcing-the-microsoft-azure-automation-preview.aspx). Oder melden Sie sich für ein Azure-[Abonnement](https://account.windowsazure.com/PreviewFeatures?fid=automation) an.

In diesem Beispiel wird Azure Automation als Ausführungsumgebung für den Zeitplan und die Arbeitsauslastung verwendet. Stellen Sie sich Azure Automation als Ihren [SQL-Agent in der Cloud](http://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) vor. 

Zusätzlich zu diesem Dokument werden die folgenden Ressourcen bereitgestellt:

* [Erste Schritte mit Azure Automation](http://azure.microsoft.com/documentation/articles/automation-create-runbook-from-samples/)
* [Schrittweise Anleitung: Erste Schritte mit der NEUEN Microsoft Azure Automation-Vorschaufunktion](http://blogs.technet.com/b/keithmayer/archive/2014/04/04/step-by-step-getting-started-with-windows-azure-automation.aspx) 
* [Microsoft Azure Automation](http://blogs.technet.com/b/cbernier/archive/2014/04/08/microsoft-azure-automation.aspx) 
* Stellen Sie Azure Automation-spezifische Fragen im [Automation-Forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).  


## Voraussetzungen

[Melden Sie sich an](http://azure.microsoft.com/services/preview/), und [machen](http://azure.microsoft.com/documentation/articles/automation-create-runbook-from-samples/) Sie sich mit dem Microsoft Azure Automation Preview-Dienst vertraut.


## PowerShell-Dateien für Shard-Elastizität

Die folgende Gruppe von PowerShell-Dateien enthält die grundlegenden Befehle zur Realisierung von horizontalen und vertikalen Skalierungsszenarien mit Azure Automation. 

Diese Beispiele veranschaulichen die Ausführung von einfachen Shard-Elastizitäts-Aufgaben mithilfe der PowerShell-Beispielmodule. In Kombination mit dem Microsoft Azure Automation-Dienst und den entsprechenden Azure Automation-Runbooks können Sie automatisierte und geplante Aufträge erstellen, die ein neues Shard bereitstellen und/oder das Leistungsniveau bestimmter Shards basierend auf einer Gruppe von Regeln ändern. 

**SetupShardedEnvironment.ps1**: Dieses PowerShell-Runbook führt ein einmaliges Setup einer Sharding-Umgebung einschließlich eines Shard-Map-Managers und einer Shard-Map für den Bereich aus. 

**ProvisionByDate.ps1**: Stellt eine neue Datenbank für die Arbeitsauslastung des nächsten Tags im Voraus bereit. Die Datenbank wird basierend auf dem Datumsstempel (JJJJMMTT) erstellt und benannt und mit dem Shard Map Manager als Bereich [JJJJMMTT, JJJJMMTT + 1T) registriert. 

**ProvisionBySize.ps1**: Stellt eine neue Datenbank bereit, wenn die aktuelle Datenbank ihre Kapazitätsgrenze erreicht. 

**ReduceServiceTier.ps1**: Durchläuft die Shards in einer bereitgestellten Shard-Map und ermittelt für jedes einzelne Shard, ob dieses ein Kandidat für eine Reduzierung der Leistungsebene ist. Ob ein Shard ein geeigneter Kandidat ist, wird anhand von zwei Kriterien bestimmt: (1) die aktuelle Dienstebene des Shards und (2) das Alter der Datenbank.  

**ShardManagement.psm1**: Stellt einen Satz von Methoden für die Interaktion mit dem Shard-Map-Manager bereit. 

**SqlDatabaseHelpers.psm1**: Stellt einen Satz von Methoden für die Interaktion mit Azure SQL-Datenbanken bereit. 

**ShardElasticity.psm1**: Stellt einen Satz von Methoden zum Ausführen von horizontalen und vertikalen Skalierungsvorgängen bereit. 

**ShardElasticityModule.psd1**: Stellt einen Satz von Methoden für die Interaktion mit der elastischen Skalierung und Azure SQL-DB bereit. 

## Kosten:

Beachten Sie, dass durch die Ausführung der PowerShell-Beispiel-Skripts Datenbanken erstellt werden, die mit tatsächlichen Kosten für den Besitzer des Abonnements verbunden sind. Für die zugrunde liegenden Azure SQL-Datenbanken [fallen die gleichen Gebühren](http://azure.microsoft.com/pricing/details/sql-database/) an wie für jede andere Azure SQL-Datenbank an.  Die folgenden Preise gelten ab dem 1. November: 

* Das SetupShardedEnvironment-Runbook erstellt den Shard Map Manager in einer Basic-Datenbank ($0,0069/Stunde) und stellt auch das erste Shard in einer Basic-Datenbank ($0,0069/Stunde) bereit. 

* ProvisionBySize- und ProvisionByDate-Runbooks stellen eine Standard-S0-Datenbank ($0,0208/Stunde) bereit.  Zur Reduzierung dieser Kosten wird die Dienstebene der neu bereitgestellten Datenbank, wenn sie in Verbindung mit dem ReduceServiceTier-Runbook ausgeführt wird, nach einem Tag von Standard S0 ($0,0208/Stunde) auf Basic ($0,0069/Stunde) herabgesetzt. 

Im Rahmen der bereitgestellten Beispiele fallen für die Verwendung von [Azure Automation](http://azure.microsoft.com/pricing/details/automation/) derzeit keine Gebühren für den Besitzer des Abonnements an.  Ausführliche Informationen finden Sie auf der [Seite mit den Azure Automation-Preisen](http://azure.microsoft.com/pricing/details/automation/). 

## So laden Sie die Runbooks 

1. Laden Sie die Datei **ShardElasticity.zip** herunter, und extrahieren Sie den Inhalt.
2. [Fügen Sie Verweise auf die Binärdateien für die elastische Skalierung mithilfe von NuGet hinzu](sql-database-elastic-scale-add-references-visual-studio.md)
3. Suchen Sie die Clientbinärdatei für die elastische Skalierung (**Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll**).
4. Legen Sie die DLL im Ordner "ShardElasticityModule" ab, und zippen Sie den Ordner. 
3. Laden Sie die Datei "ShardElasticityModule.zip"  als **Ressource** in Ihr Azure Automation-Konto hoch. 
4. Erstellen Sie in Azure Automation **Ressourcenanmeldeinformationen** mit der Bezeichnung  *ElasticScaleCredential*, die den Benutzernamen und das Kennwort für Ihren Azure SQL-Datenbankserver enthalten. 
5. Erstellen Sie eine **Ressourcenvariable** mit der Bezeichnung  *SqlServerName* für den vollqualifizierten Namen des Azure SQL-Datenbankservers. 
5. Laden Sie **SetupShardedEnvironment.ps1**, **ProvisionBySize.ps1**, **ProvisionByDate.ps1** und **ProvisionByDate.ps1** als Runbooks hoch. 
6. Führen Sie zur Einrichtung der Shardingumgebung einen einmaligen Test des **SetupShardedEnvironment.ps1**-Runbooks durch. 
7. Veröffentlichen Sie mindestens eines der verbleibenden Runbooks, und verknüpfen Sie die Runbooks mit einem Zeitplan. 
8. Beobachten Sie die Ausgabe des Runbooks auf der Registerkarte **AUFTRÄGE**. 

Wenn Sie die kurzen Beispielanweisungen nicht erfolgreich ausführen konnten, finden Sie weitere Informationen in den ausführlichen Beispielanweisungen.  

## So verwenden Sie Runbooks

1. PowerShell-Modul erstellen und packen 
2. Microsoft Azure Automation-Konto erstellen 
3. PowerShell-Modul in Azure Automation als Ressource hochladen 
4. Azure Automation-Anmeldeinformationen und variable Ressourcen erstellen 
5. PowerShell-Runbooks in Azure Automation hochladen 
6. Sharding-Umgebung einrichten 
7. Automation-Runbooks testen 
8. Runbooks veröffentlichen 
9. Zeitplan für Runbooks erstellen 


## PowerShell-Modul erstellen und packen 

Der erste Schritt besteht darin, ein PowerShell-Modul zu erstellen, das auf die Assemblys für die elastische Skalierung verweist, und dieses Modul zu packen, damit es in den Azure Automation-Dienst als Ressource hochgeladen werden kann. 

1. Laden Sie die Datei "ShardElasticity.zip" herunter.
2. Extrahieren Sie alle Inhalte.

    ![Extract all][1]
3. Rufen Sie die Client-DLL für die elastische Skalierung (d. h. "Microsoft.Azure.SqlDatabase.ElasticScale.Client.dll") ab, und kopieren Sie die folgenden Dateien in den lokalen Ordner "ShardElasticityModule", den Sie in Schritt 1 heruntergeladen haben. Dies kann auf zwei Arten erfolgen:  Dies kann auf zwei Arten erfolgen: 1) Laden Sie die DLL über das NuGet-Paket für die elastische Skalierung [Link] oder 2) über das Starter-Kit-Projekt für die elastische Skalierung [Link] (muss erstellt werden) herunter. Wechseln Sie zu "\bin\Debug\", um die DLL abzurufen.

    ![Obtain Dll][2]

4. Zippen Sie den Ordner "ShardElasticityModule". 

    Hinweis: Azure-Automatisierung erfordert mehrere Namenskonventionen: Wenn der Name des Moduls "ShardElasticityModule.psm1" lautet, muss der Name der ZIP-Datei genauso lauten (ShardElasticityModule.zip). Die Zip-Datei enthält den Ordner "ShardElasticityModule" (der mit dem Namen des Moduls übereinstimmende Name), der wiederum die psm1-Datei enthält. Falls diese Struktur nicht befolgt wird, kann das Modul in Azure Automation nicht entpackt werden.

5.    Nachdem Sie überprüft haben, ob der Inhalt und die Struktur des ZIP-Ordners den Anforderungen entsprechen, fahren Sie mit dem nächsten Schritt fort. Die Struktur sollte wie folgt aussehen:

    ![dll][3]


## Anmelden für die Azure Automation-Vorschau

1. Wechseln Sie zu den [Azure-Vorschaufeatures](http://azure.microsoft.com/services/preview/).
    
2. Klicken Sie auf **Testen**.

    ![Click Try It][8]

2. Wechseln Sie zum [Microsoft Azure-Portal](https://manage.windowsazure.com/microsoft.onmicrosoft.com).
3. Klicken Sie auf **Automatisierung**.

    ![Automation][4]
4. Klicken Sie am unteren Bildschirmrand auf **Erstellen**. 
5. Geben Sie an der nachfolgend dargestellten Eingabeaufforderung einen gültigen Kontonamen ein, und klicken Sie auf das Häkchen in der unteren rechten Ecke des Felds.

    ![Prompt][5] 
5. Fahren Sie mit dem nächsten Schritt fort. Bei einer erfolgreichen Durchführung ähnelt das Ergebnis der Abbildung unten.

    ![success][6]

## PowerShell-Modul in Azure Automation als Ressource hochladen 

Laden Sie das weiter oben erstellte PowerShell-Modul in Ihr Azure Automation-Konto hoch. Das Modul enthält beispielsweise eine Reihe von Shard-Elastizitäts-Funktionen und DLLs für die elastische Skalierung, die von den Runbooks referenziert werden können. 

1. Klicken Sie im Menüband am oberen Bildschirmrand auf **RESSOURCEN**.
2. Klicken Sie unten auf der Seite auf **MODUL IMPORTIEREN**. 
3. Klicken Sie auf **DATEI SUCHEN...**, und suchen Sie die oben erwähnte Datei **ShardElasticityModule.zip**. 
4. Wenn Sie die richtige Datei ausgewählt haben, klicken Sie auf das Häkchen in der unteren rechten Ecke des Felds, um sie zu importieren. Der Azure Automation-Dienst importiert das Modul. 
5. Fahren Sie mit dem nächsten Schritt fort. Bei einer erfolgreichen Durchführung ähnelt das Ergebnis der Abbildung unten. Wenn das Modul nicht erfolgreich importiert wurde, stellen Sie sicher, dass die ZIP-Datei den oben beschriebenen Konventionen entspricht.

    ![Assets][10] 
      
## Azure Automation-Anmeldeinformationen und variable Ressourcen erstellen 

Statt einer Hardcodierung der Anmeldeinformationen und häufig verwendeten Variablen in den Runbooks kann Azure Automation Anmeldeinformationen bzw. variable Ressourcen erstellen, die von vielen Runbooks referenziert werden können. Ein Kennwort muss dann beispielsweise nur an einem Ort geändert werden. 

1. Wählen Sie das neue Azure Automation-Konto aus, dass Sie gerade erstellt haben.
2. Klicken Sie unter dem Konto **ShardElasticityExamples** im Menüband auf **RESSOURCEN**.
3. Klicken Sie am unteren Bildschirmrand auf **EINSTELLUNG HINZUFÜGEN**.  
4. Klicken Sie auf **ANMELDEINFORMATIONEN HINZUFÜGEN**. 

    ![Add credential][9]
4. Wählen Sie **Windows PowerShell-Anmeldeinformationen** als **ANMELDEINFORMATIONSTYP** und **ElasticScaleCredential** als Namen aus. Eine Beschreibung ist optional.  
5. Klicken Sie auf den Pfeil in der rechten unteren Ecke des Felds. 

    Hinweis: Wenn Sie die Runbooks ohne Änderungen verwenden möchten, verwenden Sie die Variablennamen wörtlich und gemäß den Anweisungen. Die Variablennamen werden von den Runbooks referenziert. 
5. Fügen Sie (zweimal) den Benutzernamen und das Kennwort für den Azure SQL-Datenbankserver ein, auf dem Sie die Beispiele für die Shard-Elastizität ausführen möchten. 
 
6. Um die variable Ressource zu erstellen, klicken Sie auf **EINSTELLUNG HINZUFÜGEN** und wählen dann **VARIABLE HINZUFÜGEN** aus. 

    ![Add variable][7]
7. Erstellen Sie für dieses Lernprogramms eine Variable für den vollständig qualifizierten Namen des Azure SQL-Datenbankservers, der den Shard Map Manager und die Shardingdatenbanken enthalten soll. Wählen Sie **String** als **VARIABLENTYP** aus, und geben Sie **SqlServerName** ein. Klicken Sie auf den Pfeil, um fortzufahren. 
8. Geben Sie den vollständig qualifizierten Namen des Azure SQL-Datenbankservers als WERT ein, und klicken Sie auf das Häkchen. 
9. Sie haben nun Anmeldeinformationen und eine variable Ressource erstellt, die in den Shard-Elastizitäts-Runbooks verwendet werden.  Fahren Sie mit dem nächsten Schritt fort. Bei einer erfolgreichen Durchführung sieht das Ergebnis wie folgt aus: 
    

## PowerShell-Runbooks in Azure Automation hochladen 

Laden Sie die vier als Beispiele bereitgestellten PowerShell-Runbooks hoch. 

1. Klicken Sie zum Hochladen eines neuen Azure Automation-Runbooks im Menüband auf **RUNBOOKS**. 
2. Klicken Sie am unteren Bildschirmrand auf **IMPORTIEREN**. 
3. Navigieren Sie zum Ordner, in dem die Datei enthalten ist, wählen Sie **SetupShardedEnvironment.ps1** aus, und klicken Sie auf das Häkchen. 
4. Wiederholen Sie die Schritte 2 und 3 für die drei verbleibenden PowerShell-Runbooks (**ProvisionByDate.ps1**, **ProvisionBySize.ps1** und **ReduceServiceTier.ps1**). 
5. Fahren Sie mit dem nächsten Schritt fort. 

## Sharding-Umgebung einrichten 

Im nächsten Schritt wird ein **SetupShardedEnvironment**-Runbook ausgeführt, das eine Shardingumgebung, einschließlich einer Shard Map Manager-Datenbank, einer Shard Map für den Bereich und eines Shards für den aktuellen Tag, bereitstellt.   

1. Wählen Sie das **SetupShardedEnvironment**-Runbook aus, indem Sie auf den Namen klicken. 
2. Klicken Sie im Menüband auf **AUTOR**. 
3. In diesem Bildschirm wird der Code angezeigt, der das Runbook bildet. (Sie können den Code ggf. in diesem Bildschirm bearbeiten.) Um die Shardingumgebung einzurichten, klicken Sie am unteren Bildschirmrand auf die Schaltfläche **TESTEN**. Bestätigen Sie, dass Sie das Runbook speichern und testen möchten. 
4. Sie können den Status des Auftrags im Ausgabebereich verfolgen. Nach Abschluss des Vorgangs enthält der angegebene Azure SQL-Datenbankserver eine Shard Map Manager-Datenbank und eine Sharddatenbank. Das **SetupShardedEnvironment**-Runbook dient nur zur Bereitstellung der Shardingumgebung. Eine wiederholte Ausführung des Runbooks ist nicht vorgesehen. Fahren Sie mit dem nächsten Schritt fort.  

## Automation-Runbooks testen 

Testen Sie die erfolgreiche Ausführung der einzelnen Runbooks, bevor sie veröffentlicht werden und ein Zeitplan für sie festgelegt wird. 

1. Klicken Sie oben auf der Seite im Menüband auf **RUNBOOK**. 
2. Klicken Sie auf das Runbook **ProvisionByDate**.
3. Klicken Sie oben auf der Seite im Menüband auf **AUTOR**. 
4. Klicken Sie auf **SPEICHERN **und dann auf **TESTEN**.
5. Wiederholen Sie die Schritte für **ReduceServiceTier**. 

    Da sowohl **ProvisionBySize** als auch **ProvisionByDate** neue Shards (mithilfe unterschiedlicher Algorithmen) bereitstellen, muss **ProvisionByDate** zu diesem Zeitpunkt nicht ausgeführt werden. 

## Runbooks veröffentlichen 
Im nächsten Schritt wird das Runbook veröffentlicht, damit eingerichtet werden kann, dass es in regelmäßigen Abständen ausgeführt wird. 

1. Klicken Sie unten auf der Seite auf **VERÖFFENTLICHEN**. 
2. Klicken Sie auf **Veröffentlicht**. 
3. Fahren Sie mit dem nächsten Schritt fort.
 
## Zeitplan für Runbooks erstellen 

Der letzte Schritt besteht darin, einen Zeitplan zu erstellen und mit dem im obigen Schritt veröffentlichten Runbook zu verknüpfen. 

1. Klicken Sie oben auf der Seite auf **ZEITPLAN**. 
2. Klicken Sie auf **MIT NEUEM ZEITPLAN VERKNÜPFEN**.
3. Benennen Sie den Zeitplan entsprechend, und klicken Sie auf den Pfeil nach rechts.
4. Konfigurieren Sie den Zeitplan.
5. Klicken Sie abschließend auf das Häkchen am unteren Rand des Felds.
6. Nachdem der Auftrag basierend auf dem zuvor erstellten Zeitplan ausgeführt wurde, klicken Sie oben auf der Seite im Menüband auf die Option **AUFTRÄGE**, und wählen Sie den geplanten Auftrag aus. 

## Zusammenfassung 

Sie haben jetzt ein PowerShell-Modul erfolgreich erstellt und gepackt, das PowerShell-Modul in Azure Automation als Ressource hochgeladen und ein Runbook erstellt, getestet und veröffentlicht sowie dessen Zeitplan festgelegt.  Überwachen Sie die Integrität und den Status des Runbooks auf den Registerkarten "Dashboard" und "Aufträge". 

Die bereitgestellten Beispiele bieten nur einen kleinen Einblick in die Möglichkeiten, die sich durch die Kombination von elastischer Skalierung, Azure SQL-DB und Azure Automation ergeben. Experimentieren Sie mit diesen Beispielen, und bauen Sie darauf auf, um eine horizontale und/oder vertikale Skalierung Ihrer Azure SQL DB Elastic Scale-Anwendung zu ermöglichen. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-scripting/zip.png
[2]: ./media/sql-database-elastic-scale-scripting/dll.png
[3]: ./media/sql-database-elastic-scale-scripting/lookslikethis.png
[4]: ./media/sql-database-elastic-scale-scripting/automation.png
[5]: ./media/sql-database-elastic-scale-scripting/prompt.png
[6]: ./media/sql-database-elastic-scale-scripting/success.png
[7]: ./media/sql-database-elastic-scale-scripting/add-variable.png
[8]: ./media/sql-database-elastic-scale-scripting/sign-up.png
[9]: ./media/sql-database-elastic-scale-scripting/add-credential.png
[10]: ./media/sql-database-elastic-scale-scripting/assets.png

<!--HONumber=47-->
