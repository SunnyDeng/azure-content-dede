<properties
   pageTitle="Häufige Ursachen für das zyklische Ausführen von Clouddienstrollen | Microsoft Azure"
   description="Eine Clouddienstrolle, die plötzlich zyklisch ausgeführt wird, kann zu erheblichen Ausfallzeiten führen. Hier sind einige allgemeine Probleme aufgeführt, die zum zyklischen Ausführen von Rollen führen. Dadurch werden Ausfallzeiten möglicherweise verringert."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Allgemeine Probleme, durch die Rollen zyklisch ausgeführt werden

Hier sind einige der allgemeinen Ursachen für Bereitstellungsprobleme sowie Tipps zur Problembehandlung aufgeführt, mit denen Sie diese Probleme beheben können. Wenn die Rolleninstanz nicht gestartet wird oder zwischen den Zuständen **initializing**, **busy**, and **stopping** wechselt, weist dies auf ein Problem mit einer Anwendung hin.

## Kontaktieren des Azure-Kundensupports

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten.

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).


## Fehlende Laufzeitabhängigkeiten

Wenn eine Rolle in Ihrer Anwendung auf einer Assembly basiert, die nicht Teil von .NET Framework oder der von Azure verwalteten Bibliothek ist, müssen Sie diese Assembly explizit in das Anwendungspaket einschließen. Bedenken Sie, dass andere Microsoft-Frameworks nicht standardmäßig in Azure verfügbar sind. Wenn Ihre Rolle auf solch einem Framework basiert, müssen Sie dem Anwendungspaket diese Assemblys hinzufügen.

Überprüfen Sie vor dem Erstellen und Packen der Anwendung Folgendes:

- Vergewissern Sie sich bei der Verwendung von Visual Studio, dass für jede referenzierte Assembly im Projekt, die nicht Teil von Azure SDK oder .NET Framework ist, die Eigenschaft **Lokale Kopie** auf **True** festgelegt ist.

- Stellen Sie sicher, dass die Datei **web.config** Datei nicht auf nicht verwendete Assemblys im **compilation**-Element verweist.

- Der **Buildvorgang** jeder CSHTML-Datei ist auf **Inhalt** festgelegt. Dadurch wird sichergestellt, dass die Dateien im Paket ordnungsgemäß angezeigt werden, und das Anzeigen anderer referenzierter Dateien im Paket ermöglicht.



## Die Assembly ist auf eine falsche Plattform ausgerichtet.

Azure ist eine 64-Bit-Umgebung. Aus diesem Grund funktionieren für eine 32-Bit-Umgebung kompilierte .NET-Assemblys nicht in Azure.



## Eine Rolle löst beim Initialisieren oder Beenden Ausnahmefehler aus.

Von den Methoden der [RoleEntryPoint]-Klasse ausgelöste Ausnahmen sind Ausnahmefehler. Zu diesen Methoden zählen [OnStart], [OnStop], und [Run]. Wenn ein Ausnahmefehler in einer der folgenden Methoden auftritt, wird die Rolle zyklisch ausgeführt. Wenn die Rolle zyklisch ausgeführt wird, wird bei jedem versuchten Start möglicherweise ein Ausnahmefehler ausgelöst.


## Die Rolle wird von der Run-Methode reaktiviert.

Die Methode [Run] ist für die Ausführung auf unbestimmte Zeit ausgelegt. Wenn Ihr Code die [Run]-Methode außer Kraft setzt, sollte sie sich für unbegrenzte Zeit im Ruhezustand befinden. Wird die [Run]-Methode jedoch reaktiviert, wird die Rolle zyklisch ausgeführt.




## Falsche DiagnosticsConnectionString-Einstellung

Wenn die Anwendung die Azure-Diagnose verwendet, muss Ihre Dienstkonfigurationsdatei die Konfigurationseinstellung `DiagnosticsConnectionString` angeben. Diese Einstellung muss eine HTTPS-Verbindung zum Speicherkonto in Azure angeben.

Überprüfen Sie vor der Bereitstellung des Anwendungspakets in Azure Folgendes, um sicherzustellen, dass die Einstellung `DiagnosticsConnectionString` korrekt ist:

- Die Einstellung `DiagnosticsConnectionString` verweist auf ein gültiges Speicherkonto in Azure. Standardmäßig verweist diese Einstellung auf das emulierte Speicherkonto. Daher müssen Sie diese Einstellung explizit ändern, bevor Sie das Anwendungspaket bereitstellen. Wenn Sie diese Einstellung nicht ändern, wird eine Ausnahme ausgelöst, wenn die Rolleninstanz versucht, den Diagnosemonitor zu starten. Dies kann dazu führen, dass die Rolleninstanz unbegrenzt zyklisch ausgeführt wird.

- Die Verbindungszeichenfolge wird im folgenden [Format](../storage/storage-configure-connection-string.md) angegeben (das Protokoll muss als HTTPS angegeben werden). Ersetzen Sie *MyAccountName* durch den Namen Ihres Speicherkontos und *MyAccountKey* durch den Zugriffsschlüssel:

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Wenn Sie Ihre Anwendung mit den Azure-Tools für Microsoft Visual Studio entwickeln, können Sie diesen Wert mithilfe der [Eigenschaftenseiten](https://msdn.microsoft.com/library/ee405486) festlegen.



## Das exportierte Zertifikat enthält keinen privaten Schlüssel.

Um eine Webrolle unter SSL auszuführen, müssen Sie sicherstellen, dass das exportierte Verwaltungszertifikat den privaten Schlüssel enthält. Wenn Sie zum Exportieren des Zertifikats den *Windows-Zertifikat-Manager* verwenden, aktivieren Sie unbedingt die Option *Ja, privaten Schlüssel exportieren*. Das Zertifikat muss im PFX-Format exportiert werden. Dies ist das einzige derzeit unterstützte Format.



## Nächste Schritte

Sehen Sie sich weitere [Artikel zur Problembehandlung](..\?tag=top-support-issue&service=cloud-services) für Clouddienste an.

Informieren Sie sich in der [Blogreihe von Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) über weitere Szenarien mit zyklischer Ausführung von Rollen.




[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx

<!---HONumber=AcomDC_0128_2016-->