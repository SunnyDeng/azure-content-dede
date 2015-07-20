<properties title="Creating an Oracle WebLogic Server 12c and Oracle Database 12c Virtual Machine in Azure" pageTitle="Erstellen eines virtuellen Oracle WebLogic Server 12- und Oracle Database 12c-Computers in Azure" description="Durchlaufen Sie ein Beispiel zum Erstellen eines Oracle WebLogic Server 12c- und Oracle Database 12c-Images unter Windows Server 2012 in Microsoft Azure." services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Erstellen eines virtuellen Oracle WebLogic Server 12- und Oracle Database 12c-Computers in Azure
Das folgende Beispiel zeigt, wie Sie einen virtuellen Computer in Azure bereitstellen, der auf einem von Microsoft bereitgestellten Oracle WebLogic Server 12c- und Oracle Database 12-Image unter Windows Server 2012 basiert.

##Erstellen eines virtuellen Oracle WebLogic Server 12- und Oracle Database 12c-Computers in Azure

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com/) an.

2.	Klicken Sie auf **Marketplace**, dann auf **Compute**, und geben Sie **Oracle** in das Suchfeld ein.

3.	Wählten Sie das Image **Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012** oder **Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012** aus. Überprüfen Sie die Informationen zum Image, das Sie auswählen möchten (z. B. empfohlene Mindestgröße), und klicken Sie dann auf **Weiter**.

4.	Geben Sie einen **Hostnamen** für den virtuellen Computer an.

5.	Geben Sie einen **Benutzernamen** für den virtuellen Computer an. Beachten Sie, dass dieser Benutzer für die Remote-Anmeldung auf dem virtuellen Computer verwendet wird. Dies ist nicht der Oracle-Datenbank-Benutzername.

6.	Geben Sie ein Kennwort für den virtuellen Computer ein, und bestätigen Sie es, oder geben Sie einen öffentlichen SSH-Schlüssel an.

7.	Wählen Sie einen **Tarif** aus. Beachten Sie, dass die empfohlenen Preiskategorien standardmäßig angezeigt werden. Um alle Konfigurationsoptionen anzuzeigen, klicken Sie oben rechts auf "Alle anzeigen".

8. Legen Sie "Optionale Konfiguration" nach Bedarf fest. Beachten Sie dabei folgende Überlegungen:

	1. Lassen Sie **Speicherkonto** unverändert, um ein neues Speicherkonto mit dem Namen des virtuellen Computers zu erstellen.

	2. Legen Sie für **Verfügbarkeitsgruppe** "Nicht konfiguriert" fest.

	3. Fügen Sie zu diesem Zeitpunkt keine **Endpunkte** hinzu.

9.	Wählen Sie eine [Ressourcengruppe](resource-group-portal.md) aus oder erstellen Sie eine Ressourcengruppe

10. Wählen Sie ein **Abonnement** aus.

11. Wählen Sie einen **Standort** aus.


##Erstellen der auf dem virtuellen Computer gehosteten Datenbank
Folgen Sie den Anweisungen in [Erstellen eines virtuellen Oracle Database 12c-Computers in Azure](virtual-machines-creating-oracle-database-virtual-machine.md) ab dem Abschnitt **Erstellen der Datenbank mithilfe des virtuellen Oracle Database 12c-Computers in Azure**-

##Konfigurieren des auf dem virtuellen Computer gehosteten Oracle WebLogic Server 12c
Folgen Sie den Anweisungen in [Erstellen eines virtuellen Oracle Database 12c-Computers in Azure](virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine.md) ab dem Abschnitt **Konfigurieren des virtuellen Oracle Database 12c-Computers in Azure**. Wenn Sie ein WebLogic Server-Cluster einrichten möchten, finden Sie weitere Informationen unter [Erstellen eines Oracle WebLogic Server 12c-Clusters in Azure](virtual-machines-creating-oracle-webLogic-server-12c-cluster.md).

##Zusätzliche Ressourcen
[Images virtueller Oracle-Computer – verschiedene Überlegungen](miscellaneous-considerations-for-oracle-virtual-machine-images-new-article.md)

[Oracle Virtual Machine images for Azure](virtual-machines-oracle-list-oracle-virtual-machine-images.md) (Images von virtuellen Oracle-Computern für Azure; in englischer Sprache)

[Connecting to Oracle Database from a Java Application](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136) (Herstellen einer Verbindung mit Oracle Database in einer Java-Anwendung; in englischer Sprache)

[Verwendung von Oracle WebLogic Server 12c mit Linux in Microsoft Azure](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=July15_HO2-->