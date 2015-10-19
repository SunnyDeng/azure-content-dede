<properties
	pageTitle="Virtueller Computer mit Oracle WebLogic Server und Datenbank | Microsoft Azure"
	description="Erstellen Sie ein Azure-Image mit Oracle WebLogic Server 12c und Oracle Database 12c unter Windows Server 2012 mit dem Ressourcen-Manager-Bereitstellungsmodell."
	services="virtual-machines"
	authors="bbenz"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="06/22/2015"
	ms.author="bbenz" />

#Erstellen eines virtuellen Oracle WebLogic Server 12c- und Oracle Database 12c-Computers in Azure

In diesem Artikel wird gezeigt, wie Sie einen virtuellen Computer in Azure erstellen, der auf einem von Microsoft bereitgestellten Oracle WebLogic Server 12c- und Oracle Database 12-Image unter Windows Server 2012 basiert.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt das Erstellen eines Oracle WebLogic-Servers und einer Datenbank mit dem Ressourcen-Manager-Bereitstellungsmodell.


##So erstellen Sie einen virtuellen Oracle WebLogic Server 12c- und Oracle Database 12c-Computer in Azure

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Marketplace**, dann auf **Compute**, und geben Sie **Oracle** in das Suchfeld ein.

3.	Wählten Sie das Image **Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012** oder **Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012** aus. Überprüfen Sie die Informationen zum Image, das Sie auswählen möchten (z. B. empfohlene Mindestgröße), und klicken Sie dann auf **Weiter**.

4.	Geben Sie einen **Hostnamen** für den virtuellen Computer an.

5.	Geben Sie einen **Benutzernamen** für den virtuellen Computer an. Beachten Sie, dass dieser Benutzername für die Remoteanmeldung auf dem virtuellen Computer verwendet wird. Dies ist nicht der Benutzername für die Oracle-Datenbank.

6.	Geben Sie ein Kennwort für den virtuellen Computer ein, und bestätigen Sie es, oder geben Sie einen öffentlichen SSH-Schlüssel (Secure Shell) an.

7.	Wählen Sie einen **Tarif** aus. Beachten Sie, dass standardmäßig die empfohlenen Tarife angezeigt werden. Um alle Konfigurationsoptionen anzuzeigen, klicken Sie oben rechts auf **Alle anzeigen**.

8. Legen Sie die optionalen Konfigurationen je nach Bedarf fest. Beachten Sie folgende Überlegungen:

	a. Lassen Sie **Speicherkonto** unverändert, um ein neues Speicherkonto mit dem Namen des virtuellen Computers zu erstellen.

	b. Lassen Sie die **Verfügbarkeitsgruppe** auf **Nicht konfiguriert** festgelegt.

	c. Fügen Sie zu diesem Zeitpunkt keine Endpunkte hinzu.

9.	Wählen Sie eine Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe Weitere Informationen finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](resource-group-portal.md).

10. Wählen Sie ein **Abonnement** aus.

11. Wählen Sie einen **Standort** aus.


##So erstellen Sie die auf dem virtuellen Computer gehostete Datenbank

Folgen Sie den Anweisungen in [Erstellen eines virtuellen Oracle Database 12c-Computers in Azure](virtual-machines-creating-oracle-database-virtual-machine.md) ab dem Abschnitt **So erstellen Sie die Datenbank mithilfe des virtuellen Oracle Database 12c-Computers in Azure**.

##So konfigurieren Sie den auf dem virtuellen Computer gehosteten Oracle WebLogic Server 12c
Folgen Sie den Anweisungen in [Erstellen eines virtuellen Oracle WebLogic Server 12c-Computers in Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md) ab dem Abschnitt **So konfigurieren Sie den virtuellen Oracle WebLogic Server 12c-Computer in Azure**. Wenn Sie einen WebLogic Server-Cluster einrichten möchten, finden Sie weitere Informationen unter [Erstellen eines Oracle WebLogic Server 12c-Clusters in Azure](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md).

##Zusätzliche Ressourcen
[Verschiedene Überlegungen zu Images für virtuelle Oracle-Computer](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[Liste der Images virtueller Oracle-Computer](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

[Herstellen einer Verbindung mit der Oracle-Datenbank in einer Java-Anwendung](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Verwendung von Oracle WebLogic Server 12c mit Linux in Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=Oct15_HO2-->