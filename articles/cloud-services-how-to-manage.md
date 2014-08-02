<properties linkid="manage-services-how-to-manage-a-cloud-service" urlDisplayName="How to manage" pageTitle="How to manage a cloud service - Azure" metaKeywords="Azure manage cloud services, Azure Management Portal cloud services" description="Learn how to manage cloud services in the Azure Management Portal." metaCanonical="" services="cloud-services" documentationCenter="" title="How to Manage Cloud Services" authors="ryanwi" solutions="" manager="" editor="" />

Verwalten von Clouddiensten
===========================

[WACOM.INCLUDE [Haftungsausschluss](../includes/disclaimer.md)]

Im Bereich **Clouddienste** des Azure-Verwaltungsportals können Sie eine Dienstrolle oder eine Bereitstellung aktualisieren, eine Bereitstellung zur Produktion heraufstufen, Ressourcen mit Ihrem Clouddienst verknüpfen, sodass Sie die Ressourcenabhängigkeiten sehen und die Ressourcen zusammen skalieren können, und einen Clouddienst oder eine Bereitstellung löschen.

Inhaltsverzeichnis
------------------

-   [Vorgehensweise: Aktualisieren einer Clouddienstrolle oder -bereitstellung](#updaterole)
-   [Vorgehensweise: Austauschen von Bereitstellungen, um eine Bereitstellung zur Produktion heraufzustufen](#swap)
-   [Vorgehensweise: Verknüpfen einer Ressource mit einem Clouddienst](#linkresources)
-   [Vorgehensweise: Löschen von Bereitstellungen und eines Clouddiensts](#deletecloudservice)

Vorgehensweise: Aktualisieren einer Clouddienstrolle oder -bereitstellung
-------------------------------------------------------------------------

Wenn Sie den Anwendungscode für Ihren Clouddienst aktualisieren müssen, verwenden Sie **Aktualisieren** im Dashboard auf der Seite **Clouddienste** oder auf der Seite **Instanzen**. Sie können eine oder alle Rollen aktualisieren. Sie müssen ein neues Dienstpaket und eine neue Dienstkonfigurationsdatei hochladen.

1.  Klicken Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) auf dem Dashboard auf der Seite **Clouddienste** oder auf der Seite **Instanzen** auf **Aktualisieren**.

    **Update Deployment** wird geöffnet.

    ![Bereitstellung aktualisieren](./media/cloud-services-how-to-manage/CloudServices_UpdateDeployment.png)

2.  Geben Sie in **Bereitstellungsbezeichnung** einen Namen ein, um die Bereitstellung zu kennzeichnen (z. B. meinclouddienstv2). Sie finden den Bereitstellungsnamen unter **Schnellstart** im Dashboard.

3.  Klicken Sie unter **Paketdatei** auf **Durchsuchen**, um die Dienstpaketdatei (.cspkg) hochzuladen.

4.  Klicken Sie unter **Konfigurationsdatei** auf **Durchsuchen**, um die Dienstkonfigurationsdatei (.cscfg) hochzuladen.

5.  Wählen Sie unter **Rolle** die Option **Alle**, wenn Sie alle Rollen im Clouddienst aktualisieren möchten. Wählen Sie zum Ausführen einer Rollenaktualisierung die zu aktualisierende Rolle aus. Auch wenn Sie eine bestimmte Rolle zum Aktualisieren auswählen, werden die Updates in der Dienstkonfigurationsdatei auf alle Rollen angewendet.

6.  Wenn durch das Update die Anzahl der Rollen oder die Größe einer Rolle geändert wird, aktivieren Sie das Kontrollkästchen **Allow update if role sizes or number of roles changes**, um die Fortsetzung des Updates zu ermöglichen.

    Wenn Sie die Größe einer Rolle (d. h. die Größe eines virtuellen Computers, der eine Rolleninstanz hostet) oder die Anzahl der Rollen ändern, denken Sie daran, dass für jede Rolleninstanz (virtueller Computer) ein neues Image erstellt werden muss und dass lokale Daten verloren gehen.

7.  Wenn Dienstrollen nur eine Rolleninstanz haben, aktivieren Sie das Kontrollkästchen **Update even if one or more role contain a single instance**, damit das Update fortgesetzt werden kann.

    Azure kann während des Updates eines Clouddiensts nur dann eine Dienstverfügbarkeit von 99,95 Prozent garantieren, wenn jede Rolle mindestens zwei Rolleninstanzen (virtuelle Computer) hat. In diesem Fall kann ein virtueller Computer Clientanforderungen verarbeiten, während der andere aktualisiert wird.

8.  Klicken Sie auf OK (Häkchen), um mit dem Update des Diensts zu beginnen.

Vorgehensweise: Austauschen von Bereitstellungen, um eine Bereitstellung zur Produktion heraufzustufen
------------------------------------------------------------------------------------------------------

Verwenden Sie **Austauschen**, um eine Stagingbereitstellung eines Clouddiensts zur Produktion heraufzustufen. Wenn Sie beschließen, eine neue Version eines Clouddiensts bereitzustellen, können Sie die neue Version in der Clouddienst-Stagingumgebung bereitstellen und testen, während Ihre Kunden die aktuelle Version in der Produktion einsetzen. Wenn Sie bereit sind, die neue Version zur Produktion heraufzustufen, können Sie mit **Austauschen** die URLs austauschen, mit denen die beiden Bereitstellungen adressiert werden.

Sie können Bereitstellungen über die Seite **Clouddienste** oder über das Dashboard austauschen.

1.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Clouddienste**.

2.  Klicken Sie in der Liste der Clouddienste auf den Clouddienst, um ihn auszuwählen.

3.  Klicken Sie auf **Austauschen**.

    Die folgende Bestätigungsaufforderung wird geöffnet.

    ![Austauschen von Clouddiensten](./media/cloud-services-how-to-manage/CloudServices_Swap.png)

4.  Klicken Sie nach der Verifizierung der Bereitstellungsinformationen auf **Ja**, um die Bereitstellungen auszutauschen.

    Der Austausch der Bereitstellungen erfolgt schnell, da sich nur die virtuellen IP-Adressen für die Bereitstellungen ändern.

    Um Rechenkosten zu sparen, können Sie die Bereitstellung in der Stagingumgebung löschen, wenn Sie sicher sind, dass die neue Produktionsbereitstellung wie erwartet funktioniert.

Vorgehensweise: Verknüpfen einer Ressource mit einem Clouddienst
----------------------------------------------------------------

Um die Abhängigkeiten des Clouddiensts von anderen Ressourcen anzuzeigen, können Sie eine Azure-SQL-Datenbankinstanz oder ein Speicherkonto mit dem Clouddienst verknüpfen. Sie können Ressourcen auf der Seite **Verknüpfte Ressourcen** verknüpfen bzw. deren Verknüpfung aufheben. Überwachen Sie ihre Nutzung anschließend im Clouddienst-Dashboard. Wenn für ein verknüpftes Speicherkonto die Überwachung aktiviert ist, können Sie die gesamten Anforderungen im Clouddienst-Dashboard überwachen.

Verwenden Sie **Verknüpfen**, um eine neue oder vorhandene SQL-Datenbankinstanz oder ein Speicherkonto mit Ihrem Clouddienst zu verknüpfen. Anschließend können Sie die Datenbank sowie die Clouddienstrolle, die diese verwendet, auf der Seite **Skalieren** skalieren. (Ein Speicherkonto wird mit zunehmender Nutzung automatisch skaliert.) Weitere Informationen finden Sie unter [How to Scale a Cloud Service and Linked Resources](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-scale-a-cloud-service/) (Skalieren eines Clouddiensts und verknüpfter Ressourcen – in englischer Sprache).

Sie können die Datenbank außerdem im Knoten **Datenbanken** des Verwaltungsportals überwachen, verwalten und skalieren.

Das "Verknüpfen" einer Ressource bedeutet in diesem Fall nicht, dass Ihre App mit der Ressource verbunden wird. Wenn Sie mithilfe von **Verknüpfen** eine neue Datenbank erstellen, müssen Sie dem Anwendungscode die Verbindungszeichenfolgen hinzufügen und den Clouddienst anschließend aktualisieren. Verbindungszeichenfolgen müssen Sie auch hinzufügen, wenn Ihre App Ressourcen in einem verknüpften Speicherkonto verwendet.

Im Folgenden wird beschrieben, wie eine neue SQL-Datenbankinstanz, die auf einem neuen SQL-Datenbankserver bereitgestellt ist, mit einem Clouddienst verknüpft wird.

### Verknüpfen einer SQL-Datenbankinstanz mit einem Clouddienst

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Clouddienste**. Klicken Sie anschließend auf den Namen des Clouddiensts, um das Dashboard zu öffnen.

2.  Klicken Sie auf **Linked Resources**.

    Die Seite **Linked Resources** wird geöffnet.

    ![Seite "Linked Resources"](./media/cloud-services-how-to-manage/CloudServices_LinkedResourcesPage.png)

3.  Klicken Sie auf **Link a Resource** oder auf **Verknüpfen**.

    Der Assistent **Link Resource** wird gestartet.

    ![Verknüpfen, Seite 1](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkPage1.png)

4.  Klicken Sie auf **Create a new resource** oder auf **Link an existing resource**.

5.  Wählen Sie den Typ der zu verknüpfenden Ressource. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **SQL-Datenbank**. (Das Vorschau-Verwaltungsportal unterstützt nicht das Verknüpfen eines Speicherkontos mit einem Clouddienst.)

6.  Um die Datenbankkonfiguration abzuschließen, folgen Sie den Anweisungen in der Hilfe für den Bereich **SQL-Datenbanken** im Verwaltungsportal.

    Sie können den Fortschritt der Verknüpfungsoperation im Nachrichtenbereich verfolgen.

    ![Fortschritt der Verknüpfung](./media/cloud-services-how-to-manage/CloudServices_LinkedResources_LinkProgress.png)

    Wenn das Verknüpfen abgeschlossen ist, können Sie den Status der verknüpften Ressource im Clouddienst-Dashboard überwachen. Informationen zum Skalieren einer verknüpften SQL-Datenbank finden Sie unter [Skalieren eines Clouddiensts und verknüpfter Ressourcen](../how-to-scale-a-cloud-service/).

### Aufheben der Verknüpfung einer verknüpften Ressource

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Clouddienste**. Klicken Sie anschließend auf den Namen des Clouddiensts, um das Dashboard zu öffnen.

2.  Klicken Sie auf **Linked Resources**, und wählen Sie die Ressource anschließend aus.

3.  Klicken Sie auf **Unlink**. Klicken Sie dann an der Bestätigungsaufforderung auf **Ja**.

    Das Aufheben der Verknüpfung einer SQL-Datenbank hat keine Auswirkungen auf die Datenbank oder die Verbindungen der Anwendung mit der Datenbank. Sie können die Datenbank weiterhin im Bereich **SQL-Datenbanken** des Verwaltungsportals verwalten.

Vorgehensweise: Löschen von Bereitstellungen und eines Clouddiensts
-------------------------------------------------------------------

Bevor Sie einen Clouddienst löschen können, müssen Sie die einzelnen bestehenden Bereitstellungen löschen.

Um Rechenkosten zu sparen, können Sie Ihre Stagingbereitstellung löschen, nachdem Sie verifiziert haben, dass die Produktionsbereitstellung wie erwartet funktioniert. Rechenkosten für Rolleninstanzen fallen auch dann an, wenn ein Clouddienst nicht ausgeführt wird.

Gehen Sie folgendermaßen vor, um eine Bereitstellung oder Ihren Clouddienst zu löschen.

1.  Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com/) auf **Clouddienste**.

2.  Wählen Sie den Clouddienst aus, und klicken Sie dann auf **Löschen**. (Um einen Clouddienst auszuwählen, ohne das Dashboard zu öffnen, klicken Sie im Clouddiensteintrag auf eine beliebige Stelle außerhalb des Namens.)

    Wenn Sie eine Bereitstellung in Staging oder Produktion haben, wird unten im Fenster ein Auswahlmenü ähnlich dem folgenden angezeigt. Bevor Sie den Clouddienst löschen können, müssen Sie bestehende Bereitstellungen löschen.

    ![Menü "Löschen"](./media/cloud-services-how-to-manage/CloudServices_DeleteMenu.png)

3.  Klicken Sie zum Löschen einer Bereitstellung auf **Delete production deployment** oder auf **Stagingbereitstellung löschenDelete staging deployment**. Klicken Sie dann an der Bestätigungsaufforderung auf **Ja**.

4.  Wenn Sie den Clouddienst löschen möchten, wiederholen Schritt 3 bei Bedarf, um die andere Bereitstellung zu löschen.

5.  Klicken Sie zum Löschen des Clouddiensts auf **Clouddienst löschen**. Klicken Sie dann an der Bestätigungsaufforderung auf **Ja**.

> [WACOM.NOTE] Wenn für den Clouddienst die ausführliche Überwachung konfiguriert ist, löscht Azure die Überwachungsdaten aus Ihrem Speicherkonto nicht, wenn Sie den Clouddienst löschen. Sie müssen die Daten manuell löschen. Informationen zum Speicherort der Metriktabellen finden Sie unter "Vorgehensweise: Zugreifen auf ausführliche Überwachungsdaten außerhalb des Verwaltungsportals" in [Überwachen von Clouddiensten](https://www.windowsazure.com/en-us/manage/services/cloud-services/how-to-monitor-a-cloud-service/).

