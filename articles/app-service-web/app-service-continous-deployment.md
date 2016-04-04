<properties
	pageTitle="Kontinuierliche Bereitstellung in Azure App Service"
	description="Erfahren Sie, wie Sie die kontinuierliche Bereitstellung in Azure App Service aktivieren."
	services="app-service"
	documentationCenter=""
	authors="dariac"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="dariac"/>
    
# Kontinuierliche Bereitstellung in Azure App Service

Die Integration von [Azure App Service] in BitBucket, GitHub und Visual Studio Team Services (VSTS) ermöglicht einen Workflow für die kontinuierliche Bereitstellung. Hierbei werden die neuesten Updates aus Ihrem Projekt, das in einem der genannten Dienste veröffentlicht ist, per Pull nach Azure übertragen. Die kontinuierliche Bereitstellung ist hervorragend für Projekte geeignet, bei denen häufig zahlreiche Beiträge integriert werden.

## <a name="overview"></a>Aktivieren der kontinuierlichen Bereitstellung

Führen Sie die folgenden Schritte aus, um die kontinuierliche Bereitstellung zu aktivieren:

1. Veröffentlichen Sie Ihren App-Inhalt indem Repository, das für die kontinuierliche Bereitstellung verwendet werden soll. Weitere Informationen zum Veröffentlichen Ihres Projekts in einem dieser Dienst finden Sie unter [Create a repo (GitHub)] (Erstellen eines Repositorys), [Create a repo (BitBucket)] (Erstellen eines Repositorys) und [Erste Schritte mit VSTS].

2. Klicken Sie im [Azure-Portal] auf dem Blatt Ihrer App auf **Einstellungen > Bereitstellungsquelle**. Klicken Sie auf **Quelle auswählen** und dann z. B. auf **GitHub**.

	![](./media/app-service-continous-deployment/cd_options.png)
	
3. Schließen Sie den Autorisierungsworkflow ab.

4. Wählen Sie auf dem Blatt **Bereitstellungsquelle** das Projekt und den Branch für die Bereitstellung aus. Wenn Sie fertig sind, klicken Sie auf **OK**.
  
	![](./media/app-service-continous-deployment/github_option.png)

	> [AZURE.NOTE] Wenn Sie die kontinuierliche Bereitstellung mit GitHub oder BitBucket aktivieren, werden öffentliche und private Projekte angezeigt.

    App Service erstellt eine Verknüpfung mit dem ausgewählten Repository, ruft die Dateien aus dem angegebenen Branch per Pull ab und verwaltet einen Klon Ihres Repositorys für Ihre App Service-App. Wenn Sie VSTS für die kontinuierliche Bereitstellung aus dem Azure-Portal konfigurieren, wird für die Integration das [Kudu-Bereitstellungsmodul](https://github.com/projectkudu/kudu/wiki) von App Service verwendet, das bereits mit jedem `git push` Build- und Bereitstellungsaufgaben automatisiert. Es ist nicht erforderlich, die kontinuierliche Bereitstellung in VSTS separat einzurichten. Nach Abschluss dieses Vorgangs wird im Bereich **Bereitstellung** des Blatts Ihrer App die Meldung **Aktive Bereitstellung** angezeigt, die auf die erfolgreiche Bereitstellung hinweist.

5. Um zu überprüfen, dass die App erfolgreich bereitgestellt wurde, klicken Sie im Azure-Portal oben auf dem Blatt der App auf **URL**.

6. Um sicherzustellen, dass die kontinuierliche Bereitstellung aus dem Repository Ihrer Wahl stattfindet, übertragen Sie eine Änderung per Push in das Repository. Ihre App sollte aktualisiert werden und die Änderungen kurz nach dem Push ins Repository widerspiegeln. Sie können auf dem Blatt **Bereitstellungen** Ihrer App überprüfen, ob das Update abgerufen wurde.

## <a name="VSsolution"></a>Kontinuierliche Bereitstellung einer Visual Studio-Projektmappe 

Eine Visual Studio-Projektmappe lässt sich genauso leicht mithilfe von Push an Azure App Service übertragen wie eine einfache index.html-Datei. Der App Service-Bereitstellungsprozess optimiert alle Details, darunter das Wiederherstellen von NuGet-Abhängigkeiten und die Erstellung der Binärdateien der Anwendung. Sie können die Best Practices der Quellcodeverwaltung befolgen und Code ausschließlich in Ihrem Git-Repository verwalten, während die App Service-Bereitstellung den Rest erledigt.

Die Schritte zur Pushübertragung der Visual Studio-Projektmappe an App Service sind mit denen im [vorherigen Abschnitt](#overview) identisch, wenn Sie Ihre Projektmappe und das Repository wie folgt konfigurieren:

-	Verwenden Sie die Visual Studio-Option zur Quellcodeverwaltung, um wie in der nachstehenden Abbildung eine `.gitignore`-Datei zu generieren, oder fügen Sie Ihrem Repositorystamm manuell eine `.gitignore`-Datei hinzu, die einen ähnlichen Inhalt hat wie dieses [.gitignore-Beispiel](https://github.com/github/gitignore/blob/master/VisualStudio.gitignore). 

    ![](./media/app-service-continous-deployment/VS_source_control.png)
 
-	Fügen Sie Ihrem Repository die gesamte Verzeichnisstruktur des Projekts hinzu, wobei sich die .sln-Datei im Stammverzeichnis des Repositorys befinden muss.

Nachdem Sie Ihr Repository wie beschrieben eingerichtet und Ihre App in Azure für die kontinuierliche Veröffentlichung aus einem der Online-Git-Repositorys konfiguriert haben, können Sie Ihre ASP.NET-Anwendung lokal in Visual Studio entwickeln und Ihren Code kontinuierlich bereitstellen, indem Sie einfach Ihre Änderungen mithilfe von Push an Ihr Online-Git-Repository übertragen.

## <a name="disableCD"></a>Deaktivieren der kontinuierlichen Bereitstellung

Führen Sie die folgenden Schritte aus, um die kontinuierliche Bereitstellung zu deaktivieren:

1. Klicken Sie im [Azure-Portal] auf dem Blatt Ihrer App auf **Einstellungen > Bereitstellungsquelle**. Klicken Sie dann auf dem Blatt **Bereitstellungen** auf **Trennen**.

    ![](./media/app-service-continous-deployment/cd_disconnect.png)

2. Nachdem Sie die Aufforderung zum Bestätigen mit **Ja** beantwortet haben, können Sie zum Blatt Ihrer App zurückkehren und auf **Einstellungen > Bereitstellungsquelle** klicken, wenn Sie die Veröffentlichung aus einer anderen Quelle einrichten möchten.

## Zusätzliche Ressourcen

* [Verwenden von PowerShell für Azure]
* [Verwenden des Azure-Befehlszeilentools für Mac und Linux]
* [Git-Dokumentation]
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

[Azure App Service]: https://azure.microsoft.com/de-DE/documentation/articles/app-service-changes-existing-services/
[Azure-Portal]: https://portal.azure.com
[VSTS Portal]: https://www.visualstudio.com/de-DE/products/visual-studio-team-services-vs.aspx
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Verwenden von PowerShell für Azure]: ../articles/install-configure-powershell.md
[Verwenden des Azure-Befehlszeilentools für Mac und Linux]: ../articles/xplat-cli-install.md
[Git-Dokumentation]: http://git-scm.com/documentation

[Create a repo (GitHub)]: https://help.github.com/articles/create-a-repo
[Create a repo (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[Erste Schritte mit VSTS]: https://www.visualstudio.com/get-started/overview-of-get-started-tasks-vs
[Continuous delivery to Azure using Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!---HONumber=AcomDC_0323_2016-->