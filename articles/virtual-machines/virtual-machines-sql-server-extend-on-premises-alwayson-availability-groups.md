<properties 
	pageTitle="Erweitern von lokalen AlwaysOn-Verfügbarkeitsgruppen auf Azure | Microsoft Azure"
	description="In diesem Lernprogramm werden mit dem klassischen Bereitstellungsmodell erstellte Ressourcen verwendet. Es beschreibt die Verwendung des Assistenten zum Hinzufügen von Replikaten in SQL Server Management Studio (SSMS) zum Hinzufügen eines Replikats einer AlwaysOn-Verfügbarkeitsgruppe in Azure."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="11/13/2015"
	ms.author="jroth" />

# Erweitern von lokalen AlwaysOn-Verfügbarkeitsgruppen auf Azure

AlwaysOn-Verfügbarkeitsgruppen bieten hohe Verfügbarkeit für Datenbankgruppen durch Hinzufügen sekundärer Replikate. Diese Replikate ermöglichen den Failover von Datenbanken bei Ausfällen. Darüber hinaus können Sie für das Auslagern von Lesearbeitslasten oder Sicherungsaufgaben verwendet werden.

Sie können lokale Verfügbarkeitsgruppen nach Microsoft Azure erweitern, indem Sie eine oder mehrere Azure-VMs mit SQL Server bereitstellen und diese dann Ihren lokalen Verfügbarkeitsgruppen als Replikate hinzufügen.

Dieses Lernprogramm setzt voraus, dass Sie über die folgenden Punkte verfügen:

- Ein aktives Azure-Abonnement. Sie können sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial) registrieren.

- Eine vorhandene, lokale AlwaysOn-Verfügbarkeitsgruppe. Weitere Informationen zu Verfügbarkeitsgruppen finden Sie unter [AlwaysOn-Verfügbarkeitsgruppen](https://msdn.microsoft.com/library/hh510230.aspx).

- Konnektivität zwischen dem lokalen Netzwerk und Ihrem virtuellen Azure-Netzwerk. Weitere Informationen zum Erstellen dieses virtuellen Netzwerks finden Sie unter [Konfigurieren eines Site-to-Site-VPN im Azure-Portal](../vpn-gateway/vpn-gateway-site-to-site-create.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.

## Assistent zum Hinzufügen von Azure-Replikaten

In diesem Abschnitt erfahren Sie, wie Sie den **Assistenten zum Hinzufügen von Azure-Replikaten** verwenden können, um Ihre AlwaysOn-Verfügbarkeitsgruppenlösung um Azure-Replikate erweitern können.

1. Erweitern Sie in SQL Server Management Studio **Hohe Verfügbarkeit mit AlwaysOn** > **Verfügbarkeitsgruppen** > **[Name Ihrer Verfügbarkeitsgruppe]**.

1. Klicken Sie mit der rechten Maustaste auf **Verfügbarkeitsreplikate**, und klicken Sie dann auf **Replikat hinzufügen**.

1. Standardmäßig wird der **Assistent zum Hinzufügen von Replikaten zu Verfügbarkeitsgruppen** angezeigt. Klicken Sie auf **Weiter**. Wenn Sie bei einer früheren Ausführung des Assistenten unten auf der Seite die Option **Diese Seite nicht mehr anzeigen** aktiviert haben, wird dieser Bildschirm nicht angezeigt.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742861.png)

1. In diesem Fall müssen Sie Verbindungen mit allen vorhandenen sekundären Replikaten herstellen. Klicken Sie neben den einzelnen Replikaten auf **Verbinden…** oder alternativ unten auf dem Bildschirm auf **Alle verbinden…**. Klicken Sie nach der Authentifizierung auf **Weiter**, um zum nächsten Bildschirm zu gelangen.

1. Auf der Seite **Replikate angeben** sind entlang der Oberkante mehrere Registerkarten aufgeführt: **Replikate**, **Endpunkte**, **Sicherungseinstellungen** und **Listener**. Klicken Sie auf der Registerkarte **Replikate** auf **Azure-Replikat hinzufügen…**, um den Assistenten zum Hinzufügen von Azure-Replikaten zu starten.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742863.png)

1. Wählen Sie ein vorhandenes Azure-Verwaltungszertifikat aus dem lokalen Windows-Zertifikatspeicher aus, wenn Sie zuvor eins installiert hatten. Wählen Sie die ID eines Azure-Abonnements aus, wenn Sie zuvor eins verwendet hatten, oder geben Sie sie ein. Sie können auf „Download“ klicken, um ein Azure-Verwaltungszertifikat herunterzuladen und zu installieren und mithilfe eines Azure-Kontos die Abonnementliste herunterzuladen.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742864.png)

1. Sie müssen jedes Feld auf der Seite mit Werten auffüllen, die zum Erstellen es virtuellen Azure-Computers (VM) verwendet werden, der für das Hosten des Replikats bestimmt ist.

	|Einstellung|Beschreibung|
|---|---|
|**Image**|Wählen Sie die gewünschte Kombination aus Betriebssystem und SQL Server aus|
|**Größe des virtuellen Computers**|Wählen Sie die Größe für die VM, die Ihren Geschäftsanforderungen optimal entspricht|
|**VM-Name**|Geben Sie einen eindeutigen Namen für den neuen virtuellen Computer an. Der Name muss zwischen 3 und 15 Zeichen enthalten, er darf nur aus Buchstaben, Zahlen und Bindestrichen bestehen und muss mit einem Buchstaben beginnen und mit einem Buchstaben oder einer Zahl enden.|
|**VM-Benutzername**|Geben Sie einen Benutzernamen für das Administratorkonto auf dem virtuellen Computer ein|
|**VM-Administratorkennwort**|Geben Sie ein Kennwort für das neue Konto an|
|**Kennwort bestätigen**|Bestätigen Sie das Kennwort für das neue Konto|
|**Virtuelles Netzwerk**|Geben Sie das virtuelle Azure-Netzwerk an, das die neue VM verwenden soll. Weitere Informationen über virtuelle Netzwerke erhalten Sie unter [Übersicht über virtuelle Netzwerke](..\virtual-network\virtual-networks-overview.md).|
|**Subnetz des virtuellen Netzwerks**|Geben Sie das Subnetz des virtuellen Netzwerks an, das die neue VM verwenden soll|
|**Domäne**|Bestätigen Sie, dass der voreingestellte Wert für die Domäne zutreffend ist|
|**Domänenbenutzername**|Geben Sie ein Konto an, das sich in der lokalen Administratorgruppe auf dem lokalen Clusterknoten befindet|
|**Kennwort**|Geben Sie das Kennwort für den Domänenbenutzernamen an|

1. Klicken Sie auf **OK**, um die Bereitstellungseinstellungen zu bestätigen.

1. Als Nächstes werden die rechtlichen Bestimmungen angezeigt. Lesen Sie die Bedingungen durch, und klicken Sie auf **OK**, wenn Sie ihnen zustimmen.

1. Die Seite **Replikate angeben** wird erneut angezeigt. Überprüfen Sie die Einstellungen für das neue Azure-Replikat auf den Registerkarten **Replikate**, **Endpunkte** und **Sicherungseinstellungen**. Ändern Sie die Einstellungen entsprechend Ihren Geschäftsanforderungen. Weitere Informationen zu den auf diesen Registerkarten aufgeführten Parametern finden Sie unter [Seite "Replikate angeben" (Assistent für neue Verfügbarkeitsgruppen/Assistent zum Hinzufügen von Replikaten)](https://msdn.microsoft.com/library/hh213088.aspx). Beachten Sie, dass für Verfügbarkeitsgruppen, die Azure-Replikate enthalten, keine Listener auf der Registerkarte "Listener" erstellt werden können. Ferner wird, wenn vor dem Ausführen des Assistenten bereits ein Listener erstellt wurde, eine Meldung angezeigt, die Sie darauf hinweist, dass der Listener von Azure nicht unterstützt wird. Das Erstellen von Listenern wird im Abschnitt **Erstellen von Listenern für Verfügbarkeitsgruppen** behandelt.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742865.png)

1. Klicken Sie auf **Weiter**.

1. Wählen Sie auf der Seite **Anfängliche Datensynchronisierung auswählen** die gewünschte Methode der Datensynchronisierung aus, und klicken Sie auf **Weiter**. Für die meisten Szenarien sollten Sie **Vollständige Datensynchronisation** auswählen. Weitere Informationen zu Methoden der Datensynchronisierung finden Sie unter [Seite "Anfängliche Datensynchronisierung auswählen" (Assistenten für AlwaysOn-Verfügbarkeitsgruppen)](https://msdn.microsoft.com/library/hh231021.aspx).

1. Überprüfen Sie die Ergebnisse auf der Seite **Überprüfung**. Beheben Sie noch bestehende Probleme, und führen Sie die Überprüfung ggf. erneut aus. Klicken Sie auf **Weiter**.

	![SQL](./media/virtual-machines-sql-server-extend-on-premises-alwayson-availability-groups/IC742866.png)

1. Überprüfen Sie die Einstellungen auf der Seite **Zusammenfassung**, und klicken Sie dann auf **Fertig stellen**.

1. Der Bereitstellungsvorgang beginnt. Wenn der Assistent erfolgreich abgeschlossen wird, klicken Sie auf **Schließen**, um den Assistenten zu verlassen.

>[AZURE.NOTE]Der Assistent zum Hinzufügen von Azure-Replikaten erstellt eine Protokolldatei unter <Users>"<Benutzername>\\AppData\\Local\\SQL Server\\AddReplicaWizard". Diese Protokolldatei kann zum Beheben von Fehlern in fehlerhaften Azure-Replikatbereitstellungen verwendet werden. Wenn der Assistent keinerlei Aktionen ausführt, wird ein Rollback aller zuvor vorgenommenen Vorgänge ausgeführt, einschließlich der Löschung der bereitgestellten VM.

## Erstellen eines Listeners für eine Verfügbarkeitsgruppe

Nach dem Erstellen der Verfügbarkeitsgruppe sollten Sie einen Listener für Clientverbindungen mit den Replikaten erstellen. Listener leiten eingehende Verbindungen entweder an das primäre oder an ein schreibgeschütztes sekundäres Replikat weiter. Weitere Informationen zu Listenern finden Sie unter [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md).

## Nächste Schritte

Über die Verwendung des **Assistenten zum Hinzufügen von Azure-Replikaten** zum Erweitern Ihrer AlwaysOn-Verfügbarkeitsgruppe in Azure hinaus können Sie auch einen Teil Ihrer SQL Server-Workloads vollständig nach Azure auslagern. Informationen zum Einstieg finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md).

Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server auf virtuellen Azure-Computern](virtual-machines-sql-server-infrastructure-services.md).

<!---HONumber=AcomDC_1125_2015-->