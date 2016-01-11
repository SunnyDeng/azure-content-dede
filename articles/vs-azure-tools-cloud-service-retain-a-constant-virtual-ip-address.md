<properties
   pageTitle="Beibehalten einer konstanten virtuellen IP-Adresse für einen Clouddienst | Microsoft Azure"
   description="Erfahren Sie, wie Sie sicherstellen können, dass die virtuelle IP-Adresse (VIP) Ihres Azure-Clouddiensts beibehalten wird."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="12/19/2015"
   ms.author="tarcher" />

# Beibehalten einer konstanten virtuellen IP-Adresse für einen Clouddienst

Wenn Sie einen in Azure gehosteten Clouddienst aktualisieren, müssen Sie möglicherweise sicherstellen, dass die virtuelle IP-Adresse (VIP) des Diensts unverändert bleibt. Viele Domänenverwaltungsdienste nutzen DNS (Domain Name System) für die Registrierung der Domänennamen. DNS funktioniert nur, wenn die VIP unverändert bleibt. Mithilfe des **Veröffentlichungs-Assistenten** in Azure Tools können Sie sicherstellen, dass die VIP Ihres Clouddiensts beibehalten wird, wenn Sie den Dienst aktualisieren. Weitere Informationen zur Verwendung der DNS-Domänenverwaltung für Clouddienste finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst](cloud-services-custom-domain-name.md).

## Veröffentlichen eines Clouddiensts, ohne die VIP zu ändern

Die VIP eines Clouddiensts wird zugeordnet, wenn der Clouddienst erstmalig in einer bestimmten Umgebung in Azure bereitgestellt wird (z. B. in der Produktionsumgebung). Die VIP ändert sich nur, wenn Sie die Bereitstellung explizit löschen, oder wenn sie durch den Bereitstellungsaktualisierungsvorgang implizit gelöscht wird. Wenn Sie die VIP beibehalten möchten, dürfen Sie die Bereitstellung nicht löschen. Außerdem müssen Sie sicherstellen, dass Visual Studio die Bereitstellung nicht automatisch löscht. Dieses Verhalten kann gesteuert werden, indem Sie im **Veröffentlichungs-Assistenten** die entsprechenden Bereitstellungseinstellungen festlegen. Dieser Assistent unterstützt verschiedene Bereitstellungsoptionen. Sie können eine neue Bereitstellung oder eine Updatebereitstellung angeben, wobei die Aktualisierung inkrementell oder gleichzeitig erfolgen kann. Bei beiden Arten von Updatebereitstellungen wird die VIP beibehalten. Eine Definition dieser Bereitstellungstypen finden Sie unter [Assistent zum Veröffentlichen der Azure-Anwendung](vs-azure-tools-publish-azure-application-wizard.md). Darüber hinaus können Sie festlegen, ob die vorherige Bereitstellung eines Clouddiensts gelöscht wird, wenn ein Fehler auftritt. Die VIP wird möglicherweise unerwartet geändert, wenn diese Option nicht ordnungsgemäß angezeigt wird.

### Aktualisieren eines Clouddiensts, ohne die VIP zu ändern

1. Wenn Sie Ihren Clouddienst mindestens einmal bereitgestellt haben, öffnen Sie das Kontextmenü für den Knoten Ihres Azure-Projekts und wählen **Veröffentlichen**. Der Assistent **Azure-Anwendung veröffentlichen** wird geöffnet.

1. Wählen Sie in der Liste der Abonnements das Abonnement aus, für das die Bereitstellung erfolgen soll, und klicken Sie dann auf **Weiter**. Die Seite **Einstellungen** des Assistenten wird geöffnet.

1. Überprüfen Sie auf der Registerkarte **Allgemeine Einstellungen**, ob der Name des Clouddiensts, für den die Bereitstellung durchgeführt wird, sowie die Angaben für **Umgebung**, **Buildkonfiguration** und **Dienstkonfiguration** richtig sind.

1. Überprüfen Sie auf der Registerkarte **Erweiterte Einstellungen**, ob das richtige Speicherkonto und die richtige Bereitstellungsbezeichnung angegeben sind. Auf dieser Registerkarte muss zudem sichergestellt werden, dass das Kontrollkästchen **Bereitstellung bei Fehler löschen** deaktiviert und das Kontrollkästchen **Bereitstellungsaktualisierung** aktiviert ist. Durch Aktivierung des Kontrollkästchens **Bereitstellungsaktualisierung** wird sichergestellt, dass die Bereitstellung nicht gelöscht und die VIP beibehalten wird, wenn Sie die Anwendung erneut veröffentlichen. Durch Deaktivierung von **Bereitstellung bei Fehler löschen** stellen Sie sicher, dass die VIP beibehalten wird, wenn während der Bereitstellung ein Fehler auftritt.

1. Um festzulegen, wie die Rollen aktualisiert werden sollen, klicken Sie neben dem Feld **Bereitstellungsaktualisierung** auf den Link **Einstellungen** und wählen im Dialogfeld **Bereitstellungsaktualisierungs-Einstellungen** die Option für inkrementelle oder gleichzeitige Updates. Bei Auswahl des inkrementellen Updates werden die Instanzen nacheinander aktualisiert. Die Verfügbarkeit der Anwendung wird also nicht unterbrochen. Wenn Sie ein gleichzeitiges Update festlegen, werden alle Instanzen gleichzeitig aktualisiert. Gleichzeitige Updates sind schneller, der Dienst ist während der Aktualisierung jedoch möglicherweise nicht verfügbar.

1. Wenn Sie die Einstellungen wie gewünscht festgelegt haben, klicken Sie auf **Weiter**.

1. Überprüfen Sie auf der Seite **Zusammenfassung** des Assistenten Ihre Einstellungen, und klicken Sie anschließend auf **Veröffentlichen**.

  >[AZURE.WARNING]Wenn bei der Bereitstellung ein Fehler auftritt, sollten Sie die Ursache ermitteln und die Bereitstellung umgehend wiederholen. Dadurch wird verhindert, dass der Clouddienst als beschädigt angezeigt wird.

## Nächste Schritte

Weitere Informationen zur Veröffentlichung in Azure über Visual Studio finden Sie unter [Assistent zum Veröffentlichen der Azure-Anwendung](vs-azure-tools-publish-azure-application-wizard.md).

<!---HONumber=AcomDC_1223_2015-->