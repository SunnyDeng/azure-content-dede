<properties
   pageTitle="Übertragen eines Azure-Abonnements | Microsoft Azure"
   description="Übertragung eines Azure-Abonnements auf einen anderen Benutzer, und einige häufig gestellte Fragen (FAQs) zu dem Prozess."
   services="billing"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="09/21/2015"
   ms.author="curtand;kareni;ruchic"/>

# Übertragen eines Azure-Abonnements

Möchten Sie:

- die Abrechnung für das Azure-Abonnement an eine andere Person übergeben?
- das Konto wechseln, das Sie für die Anmeldung in Azure verwenden? Vielleicht haben Sie Ihr Microsoft-Konto verwendet, wollten aber eigentlich Ihr Geschäfts- oder Schulkonto verwenden?
- ihre Azure-Abonnement von einem Verzeichnis ins andere verschieben?
- Azure und Office 365, die in verschiedenen Mandanten sind, konsolidieren?

Sie können dies jetzt problemlos für die nutzungsbasierte Bezahlung sowie für MSDN-, Action Pack- und BizSpark-Abonnements im Microsoft Azure Account Center tun. Wir haben die Möglichkeit hinzugefügt, Ihr Abonnement auf einen anderen Benutzer zu übertragen. Anders ausgedrückt können Sie jetzt das Administratorkonto für ein beliebiges Ihrer nutzungsbasierten Abonnements bzw. MSDN-, Action Pack- oder BizSpark-Abonnements ändern. Dabei ist es unerheblich, in welchem Land Sie sich befinden.

## Übertragen eines Azure-Abonnements

1.  Melden Sie sich bei <https://account.windowsazure.com/Subscriptions> an.

2.  Wählen Sie das Abonnement, das Sie übertragen möchten.

3.  Klicken Sie auf die Otion **Abonnement übertragen**.

    ![Registerkarte „Azure-Kontoabonnements“](./media/billing-subscription-transfer/image1.png)

4.  Folgen Sie den Anweisungen, um den Empfänger anzugeben.

    ![Dialogfeld „Übertragen des Abonnements“](./media/billing-subscription-transfer/image2.PNG)

5.  Der Empfänger erhält automatisch eine E-Mail mit einem Link zum Akzeptieren.

    ![„Übertragen des Abonnements“-E-Mail an den Empfänger](./media/billing-subscription-transfer/image3.png)

6.  Der Empfänger klickt auf den Link und folgt den Anweisungen. Außerdem gibt er seine Zahlungsinformationen ein.

    ![Website für die erste Abonnementübertragung](./media/billing-subscription-transfer/image4.PNG)

    ![Website für die zweite Abonnementübertragung](./media/billing-subscription-transfer/image5.PNG)

7. Erfolg! Das Abonnement ist jetzt übertragen.

## Häufig gestellte Fragen (FAQ)

-   **Führt eine Übertragung des Abonnements zu Ausfallzeiten?**

    Die Übertragung hat keine Auswirkung auf den Dienst. Effektiv wird dabei das Abonnement unter dem laufenden Kontoadministrator gekündigt, und ein neues unter dem Konto des Empfängers erstellt. Dabei werden die zugrunde liegenden Azure-Dienste dem neuen Abonnement zugeordnet. Die Abonnement-ID bleibt unverändert.

-   **Wie verwende ich diesen Mechanismus zum Ändern des Verzeichnisses für ein Abonnement?** – Ein Azure-Abonnement wird im Verzeichnis erstellt, zu dem das Administratorkonto gehört. Um das Verzeichnis zu ändern, müssen Sie also nur das Abonnements auf ein Benutzerkonto im Zielverzeichnis übertragen. Wenn der Benutzer die Schritte zum Akzeptieren der Übertragung abschließt, wird das Abonnement automatisch in das Zielverzeichnis verschoben.
   
-   **Wenn ich die Abrechnung eines Abonnements aus einer anderen Organisation übernehme, kann diese weiterhin auf meine Ressourcen zugreifen?**

    Wenn das Abonnement auf einen anderen Mandanten übertragen wird, verlieren die Benutzer, die dem vorherigen Mandanten zugeordnet sind, den Zugriff auf das Abonnement. Selbst wenn ein Benutzer kein Dienstadministrator oder Co-Administrator mehr ist, hat er über andere Sicherheitsmechanismen möglicherweise immer noch Zugriff auf das Abonnement. Dazu gehören: – Verwaltungszertifikate, die dem Benutzer Administratorrechte auf Abonnementressourcen gewähren. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx) – Zugriffschlüssel für Dienste wie Storage. Weitere Informationen finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys) – RAS-Anmeldeinformationen für Dienste wie Azure Virtual Machines.

    Dies ist keine vollständige Liste. Der Empfänger sollte sich überlegen, ob er dem Dienst zugeordnete Schlüssel aktualisiert, wenn der Zugriff auf die Ressourcen eingeschränkt werden soll. Die meisten Ressourcen können wie folgt aktualisiert werden:

    1.   Öffnen Sie das Azure-Portal: [**https://portal.azure.com*](https://portal.azure.com)

    2.    Klicken Sie auf alle „Browse All -&gt; All Resources“.

    3.    Wählen Sie die Ressource. Daraufhin wird das Blatt "Ressourcen" geöffnet.

    4.    Klicken Sie im Blatt "Ressourcen" auf **Einstellungen**. Hier können Sie die vorhandenen Schlüssel anzeigen und aktualisieren.


-   **Wenn ich das Abonnement in der Mitte des Abrechnungszyklus übertrage, zahlt der Empfänger die Rechnung für den gesamten Zyklus?**

    Der Absender ist zuständig für die Zahlung für jegliche Nutzung, die bis zu dem Zeitpunkt gemeldet wurde, an dem die Übertragung abgeschlossen war. Der Empfänger ist verantwortlich für die Verwendung, die vom Zeitpunkt der Übertragung an berichtet wird. Möglicherweise gibt es Nutzung, die vor der Übertragung stattgefunden hat, aber danach gemeldet wurde. Dies wird in der Rechnung des Empfängers enthalten sein.

-   **Verfügt der Empfänger über Zugriff auf den Nutzungs- und Abrechnungsverlauf?**

    Zu diesem Zeitpunkt ist die einzige Information, die dem Empfänger angezeigt wird, der Betrag der letzten Rechnung (oder der derzeitige Kontostand, wenn das Abonnement übertragen wurde, bevor die erste Rechnung generiert wurde). Der restliche Nutzungs- und Abrechnungsverlauf wird nicht mit dem Abonnement übertragen.

-   **Kann das Angebot während der Übertragung geändert werden?**

    Das Angebot muss unverändert bleiben. Um Ihr Angebot zu ändern, müssen Sie sich [an den Support wenden](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **Kann ein Abonnement auf ein Benutzerkonto in einem anderen Land übertragen werden?**

    Nein, zu diesem Zeitpunkt wird dies nicht unterstützt. Das Benutzerkonto des Empfängers muss sich im gleichen Land befinden.

-   **Kann der Empfänger einen anderen Zahlungsmechanismus nutzen?**

    Ja, und in der Tat können Sie diesen Mechanismus dazu nutzen, um die Zahlungsmethode für Ihr Abonnement von Rechnung auf Kreditkarte zu ändern. Führen Sie nur die Übertragung auf ein anderes Konto durch, und geben Sie Ihre Kreditkartendaten beim Empfang des Abonnements ein. Es gibt hier Einschränkungen: der Abrechnungsverlauf des Abonnements ist nun auf zwei Konten aufgeteilt. Der Vorteil ist jedoch, dass Sie sich dazu nicht an den [Support wenden](http://go.microsoft.com/fwlink/?LinkID=619338) müssen.

## Nächste Schritte nach dem Übernehmen des Besitzes eines Abonnements

1. Sie sind nun der Kontoadministrator. Überprüfen und aktualisieren Sie den Dienstadministrator und die Co-Administratoren. Sie verwalten die Administratoren im [Azure-Verwaltungsportal](https://manage.windowsazure.com) unter "Einstellungen". [Weitere Informationen](http://go.microsoft.com/fwlink/?LinkID=533293).
2. Sie können auch die rollenbasierte Zugriffskontrolle (RBAC) für Ihr Abonnement und die Dienste verwenden. Weitere Informationen finden Sie im [Azure-Vorschauportal](https://portal.azure.com) ([Weitere Informationen zur rollenbasierten Zugriffssteuerung](http://go.microsoft.com/fwlink/?LinkID=544802))
3. Aktualisieren Sie die Anmeldeinformationen für die Dienste dieses Abonnements. Diese umfassen:
    -   Verwaltungszertifikate, die dem Benutzer Administratorrechte auf Abonnementressourcen gewähren. Weitere Informationen finden Sie unter [Erstellen und Hochladen eines Verwaltungszertifikats für Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx).
    -	Tastenkombinationen für Dienste wie Storage. Weitere Informationen finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
    -	RAS-Anmeldeinformationen für Dienste wie Azure Virtual Machines
4. Aktualisieren Sie die Abrechnungswarnungen für dieses Abonnement im [Azure Account Center](https://account.windowsazure.com/Subscriptions) ([weitere Informationen](http://go.microsoft.com/fwlink/?LinkID=533292)).
5. 	Wenn Sie mit einem Partner arbeiten, sollten Sie die Partner-ID für dieses Abonnement aktualisieren. Sie können diese im [Azure Account Center](https://account.windowsazure.com/Subscriptions) durchführen.

<!---HONumber=AcomDC_1125_2015-->