<properties 
   pageTitle="Erstellen eines neuen StorSimple-Manager-Diensts"
   description="Beschreibt das Erstellen einer neuen Instanz des StorSimple-Manager-Diensts."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/18/2015"
   ms.author="v-sharos" />


#### So erstellen Sie einen neuen Dienst

1. Melden Sie sich mit Ihren Microsoft-Kontoanmeldeinformationen beim Verwaltungsportal unter dieser URL an: [http://azure.microsoft.com/](http://azure.microsoft.com/).

2. Klicken Sie im Verwaltungsportal auf **+Neu** > **Datendienste** > **StorSimple-Manager** > **Schnellerfassung**.

3. Gehen Sie im angezeigten Formular folgendermaßen vor:
  1. Geben Sie einen eindeutigen **Namen** für Ihren Dienst an. Dies ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 2 und 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.
  2. Geben Sie einen **Standort** für Ihren Dienst an. Üblicherweise sollten Sie den Standort auswählen, welcher der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten liegt. Berücksichtigen Sie ggf. auch Folgendes: 
	 
		- Falls Sie vorhandene Workloads in Azure haben, die Sie auch mit Ihrem StorSimple-Gerät bereitstellen möchten, sollten Sie das Datencenter nutzen.
		- Der StorSimple Manager-Dienst und der Azure-Speicher können sich an zwei verschiedenen Standorten befinden. In einem solchen Fall müssen Sie das StorSimple Manager- und das Azure-Speicherkonto separat erstellen. Um ein Azure-Speicherkonto zu erstellen, wechseln Sie zum Azure Storage-Dienst im Verwaltungsportal und befolgen Sie die Schritte unter [Erstellen eines Azure-Speicherkontos](storage-create-storage-account.md#create-a-storage-account). Nachdem Sie dieses Konto erstellt haben, fügen Sie es zum StorSimple Manager hinzu, indem Sie die Schritte unter [Konfigurieren eines neuen Speicherkontos für den Dienst](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service) befolgen .
		 
  3. Wählen Sie ein **Abonnement** aus der Dropdownliste aus. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird nicht angezeigt, wenn Sie nur ein Abonnement besitzen.
  4. Aktivieren Sie **Neues Speicherkonto erstellen**, um automatisch ein Speicherkonto mit dem Dienst zu erstellen. Dieses Speicherkonto erhält einen speziellen Namen wie "storsimplebwv8c6dcnf". Wenn Ihre Daten an einem anderen Speicherort gespeichert werden sollen, deaktivieren Sie dieses Kontrollkästchen. 
  5. Klicken Sie auf **StorSimple-Manager erstellen**, um den Dienst zu erstellen.

   ![Erstellen eines Diensts](./media/storsimple-create-new-service/HCS_CreateAService-include.png)

  Sie werden auf die Startseite **Dienst** weitergeleitet. Die Diensterstellung dauert einige Minuten. Nachdem der Dienst erfolgreich erstellt wurde, werden Sie entsprechend benachrichtigt, und der Status des Diensts ändert sich in **Aktiv**.
 
   ![Diensterstellung](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

<!-----HONumber=August15_HO8-->