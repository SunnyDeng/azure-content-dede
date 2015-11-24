<!--author=alkohli last changed: 11/16/15-->


#### So erstellen Sie einen neuen Dienst

1. Melden Sie sich mit Ihren Microsoft-Kontoanmeldeinformationen unter dieser URL beim Azure-Portal an: [http://azure.microsoft.com/](http://azure.microsoft.com/).

2. Klicken Sie im Azure-Portal auf **Neu** > **Datendienste** > **StorSimple-Manager** > **Schnellerfassung**.

3. Gehen Sie im angezeigten Formular folgendermaßen vor:
  1. Geben Sie einen eindeutigen **Namen** für Ihren Dienst an. Dies ist ein Anzeigename, der zum Identifizieren des Diensts verwendet werden kann. Der Name kann zwischen 2 und 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.
  2. Geben Sie einen **Standort** für Ihren Dienst an. Üblicherweise sollten Sie den Standort auswählen, welcher der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten liegt. Berücksichtigen Sie ggf. auch Folgendes: 
	 
		- If you have existing workloads in Azure that you also intend to deploy with your StorSimple device, you should use that datacenter.
		- Your StorSimple Manager service and Azure storage can be in two separate locations. In such a case, you are required to create the StorSimple Manager and Azure storage account separately. To create an Azure storage account, go to the Azure Storage service in the Azure portal and follow the steps in [Create an Azure Storage account](storage-create-storage-account.md#create-a-storage-account). After you create this account, add it to the StorSimple Manager service by following the steps in [Configure a new storage account for the service](storsimple-deployment-walkthrough.md#configure-a-new-storage-account-for-the-service).
		 
  3. Wählen Sie ein **Abonnement** aus der Dropdownliste aus. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. Dieses Feld wird nicht angezeigt, wenn Sie nur ein Abonnement besitzen.
  4. Aktivieren Sie **Neues Speicherkonto erstellen**, um automatisch ein Speicherkonto mit dem Dienst zu erstellen. Dieses Speicherkonto erhält einen speziellen Namen wie "storsimplebwv8c6dcnf". Wenn Ihre Daten an einem anderen Speicherort gespeichert werden sollen, deaktivieren Sie dieses Kontrollkästchen. 
  5. Klicken Sie auf **StorSimple-Manager erstellen**, um den Dienst zu erstellen.

   ![StorSimple-Manager erstellen](./media/storsimple-create-new-service/HCS_CreateAService-include.png)

  Sie werden auf die Startseite **Dienst** weitergeleitet. Die Diensterstellung dauert einige Minuten. Nachdem der Dienst erfolgreich erstellt wurde, werden Sie entsprechend benachrichtigt, und der Status des Diensts ändert sich in **Aktiv**.
 
   ![Diensterstellung](./media/storsimple-create-new-service/HCS_StorSimpleManagerServicePage-include.png)

![Video verfügbar](./media/storsimple-create-new-service/Video_icon.png) **Video verfügbar**

Sie können sich [hier](http://azure.microsoft.com/documentation/videos/create-a-storsimple-manager-service/) ein Video anschauen, in dem das Erstellen eines neuen StorSimple Manager-Services demonstriert wird.

<!---HONumber=Nov15_HO4-->