<properties
   pageTitle="Veröffentlichen und Bereitstellen einer Azure-Anwendung in Visual Studio"
   description="Lernen Sie die Verfahren zum Einrichten von Cloud- und Speicherkontodiensten und zum Konfigurieren Ihrer Azure-Anwendung kennen."
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
   ms.date="09/28/2015"
   ms.author="tarcher" />

# Veröffentlichen und Bereitstellen einer Azure-Anwendung in Visual Studio

## Übersicht

Bevor Sie ein Clouddienstprojekt veröffentlichen können, müssen Sie die folgenden Dienste einrichten:

- Einen **Clouddienst** zum Ausführen Ihrer Rollen in der Azure-Umgebung

- Ein **Speicherkonto**, das Zugriff auf die Blob-, Warteschlangen- und Tabellendienste bereitstellt

Befolgen Sie die folgenden Verfahren, um diese Dienste einzurichten und Ihre Anwendung zu konfigurieren.


## Erstellen eines Clouddiensts

Um einen Clouddienst in Azure zu veröffentlichen, müssen Sie zunächst einen Clouddienst erstellen, in dem Ihre Rollen in der Azure-Umgebung ausgeführt werden. Sie können einen Clouddienst im Azure-Verwaltungsportal erstellen, wie im Abschnitt **So erstellen Sie einen Clouddienst über das Azure-Verwaltungsportal** weiter unten in diesem Artikel beschrieben. Sie können auch mit dem Veröffentlichungs-Assistenten einen Clouddienst in Visual Studio erstellen.

### So erstellen Sie einen Clouddienst in Visual Studio

1. Öffnen Sie das Kontextmenü für das Azure-Projekt, und klicken Sie auf **Veröffentlichen**.

    ![VST\_PublishMenu](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/vst-publish-menu.png)

1. Wenn Sie nicht angemeldet sind, melden Sie sich mit Ihrem Benutzernamen und Kennwort für das Microsoft-Konto oder Organisationskonto an, das Ihrem Azure-Abonnement zugeordnet ist.

1. Klicken Sie auf die Schaltfläche **Weiter**, um zur Seite **Einstellungen** zu gelangen.

    ![Allgemeine Einstellungen des Veröffentlichungs-Assistenten](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/publish-settings-page.png)

1. Klicken Sie in der Liste **Clouddienste** auf **Neu erstellen**. Das Dialogfeld **Azure-Dienste erstellen** wird angezeigt.

1. Geben Sie den Namen Ihres Clouddiensts ein. Der Name bildet einen Teil der URL des Diensts und muss daher eindeutig sein. Groß-/Kleinschreibung wird beim Namen nicht unterschieden.

### So erstellen Sie einen Clouddienst über das Azure-Verwaltungsportal

1. Melden Sie sich auf der Microsoft-Website beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=253103) an.

1. (Optional) Um eine Liste der Clouddienste anzuzeigen, die Sie bereits erstellt haben, klicken Sie links auf der Seite auf "Clouddienste".

1. Klicken Sie links unten auf das **+**-Symbol, und wählen Sie dann im eingeblendeten Menü **Clouddienst** aus. Ein weiterer Bildschirm mit den beiden Optionen **Schnellerfassung** und **Benutzerdefiniert erstellen** wird angezeigt. Bei Auswahl von **Schnellerfassung** können Sie einen Clouddienst durch Angabe der URL und Region erstellen, in der dieser physisch gehostet wird. Bei Auswahl von **Benutzerdefiniert erstellen** können Sie einen Clouddienst sofort veröffentlichen, indem Sie eine Paketdatei (.cspkg), eine Konfigurationsdatei (.cscfg) und ein Zertifikat angeben. "Benutzerdefiniert erstellen" ist nicht erforderlich, wenn Sie beabsichtigen, Ihren Clouddienst mithilfe des Befehls **Veröffentlichen** in einem Azure-Projekt zu veröffentlichen. Der Befehl **Veröffentlichen** ist im Kontextmenü für ein Azure-Projekt verfügbar.

1. Wählen Sie **Schnellerfassung**, um den Clouddienst später mit Visual Studio zu veröffentlichen.

1. Geben Sie einen Namen für Ihren Clouddienst an. Die vollständige URL wird neben dem Namen angezeigt.

1. Wählen Sie in der Liste die Region, in der sich die meisten Ihrer Benutzer befinden.

1. Klicken Sie am unteren Rand des Fensters auf den Link **Clouddienst erstellen**.

## Erstellen eines Speicherkontos

Ein Speicherkonto bietet Zugriff auf die Blob-, Warteschlangen- und Tabellendienste. Sie können ein Speicherkonto in Visual Studio oder im [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=253103) erstellen.

### So erstellen ein Speicherkonto mithilfe von Visual Studio

1. Öffnen Sie im **Projektmappen-Explorer** das Kontextmenü für den Knoten **Speicher**, und wählen Sie dann **Speicherkonto erstellen** aus.

    ![Erstellen eines neuen Azure-Speicherkontos](./media/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio/IC744166.png)

1. Wählen Sie im Dialogfeld **Speicherkonto erstellen** die folgenden Informationen aus, oder geben Sie sie ein.
    - Das Azure-Abonnement, dem Sie das Speicherkonto hinzufügen möchten
    - Den Namen, den Sie für das neue Speicherkonto verwenden möchten
    - Die Region oder Affinitätsgruppe (z. B. USA, Westen oder Ostasien)
    - Den Typ der Replikation, den Sie für das Speicherkonto verwenden möchten, zum Beispiel "Georedundant"

1. Wenn Sie fertig sind, wählen Sie **Erstellen** aus. Das neue Speicherkonto wird im **Server-Explorer** in der Liste **Speicher** angezeigt.

### So erstellen Sie ein Speicherkonto im Azure-Verwaltungsportal

1. Melden Sie sich auf der Microsoft-Website beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkId=253103) an.

1. (Optional) Um Ihre Speicherkonten anzuzeigen, klicken Sie links auf der Seite auf den Link **Speicher**.

1. Klicken Sie links unten auf der Seite auf das **+**-Symbol.

1. Wählen Sie im angezeigten Menü erst **Speicher** und dann **Schnellerfassung** aus.

1. Geben Sie dem Speicherkonto einen Namen, der zu einer eindeutigen URL führt.

1. Benennen Sie den Clouddienst. Die vollständige URL wird neben dem Namen angezeigt.

1. Wählen Sie in der Liste der Regionen die Region, in der sich die meisten Ihrer Benutzer befinden.

1. Geben Sie an, ob Sie die Georeplikation aktivieren möchten. Wenn Sie die Georeplikation aktivieren, werden die Daten an mehreren physischen Standorten gespeichert, um die Möglichkeit des Verlusts zu verringern. Durch dieses Feature wird Speicher zwar teurer, aber Sie können die Kosten senken, indem Sie Geolocation aktivieren, wenn Sie das Speicherkonto erstellen, anstatt das Feature später hinzuzufügen. Weitere Informationen finden Sie unter [Georeplikation](http://go.microsoft.com/fwlink/?LinkId=253108).

1. Klicken Sie am unteren Rand des Fensters auf den Link **Speicherkonto erstellen**.

Nachdem Sie Ihr Speicherkonto erstellt haben, sehen Sie die URLs, über die Sie auf Ressourcen in jedem der Azure-Speicherdienste zugreifen können, und auch den primären und sekundären Zugriffsschlüssel für Ihr Konto. Sie verwenden diese Schlüssel, um an die Speicherdienste gerichtete Anforderungen zu authentifizieren.

>[AZURE.NOTE]Der sekundäre Zugriffsschlüssel bietet denselben Zugriff auf Ihr Speicherkonto wie der primäre Zugriffschlüssel und wird als Reserveschlüssel generiert, sollte die Sicherheit Ihres primären Zugriffsschlüssels gefährdet sein. Darüber hinaus wird empfohlen, dass Sie Ihre Zugriffsschlüssel regelmäßig neu generieren. Sie können die Einstellung einer Verbindungszeichenfolge so ändern, dass der sekundäre Schlüssel verwendet werden soll, während Sie den primären Schlüssel neu generieren. Anschließend können Sie die Einstellung so ändern, dass der neu erstellte primäre Schlüssel genutzt werden soll, während Sie den sekundären Schlüssel neu generieren.

## Konfigurieren Ihrer App für das Verwenden der vom Speicherkonto bereitgestellten Dienste

Sie müssen alle Rollen, die auf Speicherdienste zugreifen, für das Verwenden der Azure-Speicherdienste konfigurieren, die Sie erstellt haben. Zu diesem Zweck können Sie mehrere Dienstkonfigurationen für Ihr Azure-Projekt verwenden. Standardmäßig werden zwei im Ihrem Azure-Projekt erstellt. Mithilfe mehrerer Dienstkonfigurationen können Sie die gleiche Verbindungszeichenfolge in Ihrem Code verwenden, aber in jeder Dienstkonfiguration einen anderen Wert für eine Verbindungszeichenfolge angeben. Beispielsweise können Sie eine Dienstkonfiguration zum lokalen Ausführen und Debuggen Ihrer Anwendung mit dem Azure-Speicheremulator und eine andere Dienstkonfiguration zum Veröffentlichen Ihrer Anwendung in Azure nutzen. Weitere Informationen zu den Dienstkonfigurationen finden Sie unter [Konfigurieren Ihres Azure-Projekts mit mehreren Dienstkonfigurationen](vs-azure-tools-multiple-services-project-configurations.md).

### So konfigurieren Sie Ihre Anwendung für die Nutzung der Dienste, die vom Speicherkonto bereitgestellt werden

1. Öffnen Sie in Visual Studio Ihre Azure-Projektmappe. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für jede Rolle in Ihrem Azure-Projekt, die auf die Speicherdienste zugreift, und wählen Sie **Eigenschaften** aus. Eine Seite mit dem Namen der Rolle wird im Visual Studio-Editor angezeigt. Die Seite zeigt die Felder für die Registerkarte **Konfiguration** an.

1. Wählen Sie auf den Eigenschaftenseiten der Rolle **Einstellungen** aus.

1. Wählen Sie in der Liste **Dienstkonfiguration** den Namen der Dienstkonfiguration aus, die Sie bearbeiten möchten. Wenn Sie alle Dienstkonfigurationen für diese Rolle ändern möchten, können Sie **Alle Konfigurationen** auswählen. Weitere Informationen zum Aktualisieren von Dienstkonfigurationen finden Sie im Abschnitt **Verwalten von Verbindungszeichenfolgen für Speicherkonten** im Thema [Konfigurieren der Rollen für einen Azure-Clouddienst mit Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Um Einstellungen von Verbindungszeichenfolgen zu ändern, klicken Sie auf die Schaltfläche **...** neben der Einstellung. Das Dialogfeld **Verbindungszeichenfolge für den Speicher erstellen** wird angezeigt.

1. Wählen Sie unter **Verbinden mit** die Option **Ihr Abonnement** aus.

1. Wählen Sie in der Liste **Abonnement** Ihr Abonnement aus. Wenn die Liste der Abonnements nicht das gewünschte Abonnement enthält, klicken Sie auf den Link **Veröffentlichungseinstellungen herunterladen**.

1. Wählen Sie in der Liste **Kontoname** den Namen Ihres Speicherkontos aus. Azure Tools erhält Anmeldeinformationen für das Speicherkonto automatisch mithilfe der PUBLISHSETTINGS-Datei. Wenn Sie die Anmeldeinformationen für das Speicherkonto manuell angeben möchten, wählen Sie die Option **Manuell eingegebene Anmeldeinformationen** aus und fahren mit diesen Schritten fort. Sie erhalten Ihren Speicherkontonamen und Primärschlüssel über das [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885). Wenn Sie die Einstellungen für das Speicherkonto nicht manuell angeben möchten, klicken Sie auf **OK**, um das Dialogfeld zu schließen.

1. Klicken Sie auf den Link **Anmeldeinformationen für Speicherkonto eingeben**.

1. Geben Sie in das Feld **Kontoname** den Namen Ihres Azure-Speicherkontos ein.

    >[AZURE.NOTE]Melden Sie sich am Verwaltungsportal an, und klicken Sie dann auf die Schaltfläche **Speicher**. Im Portal wird eine Liste der Speicherkonten angezeigt. Wenn Sie ein Konto auswählen, wird eine entsprechende Seite geöffnet. Sie können den Namen des Speicherkontos von dieser Seite kopieren. Wenn Sie eine frühere Version des Verwaltungsportals verwenden, wird der Name Ihres Speicherkontos in der Ansicht **Speicherkonten** des Verwaltungsportals angezeigt. Um diesen Namen zu kopieren, markieren Sie ihn im Fenster **Eigenschaften** dieser Ansicht und drücken dann STRG+C. Um den Namen in Visual Studio einzufügen, wählen Sie das Textfeld **Kontoname** aus und drücken dann STRG+V.

1. Geben Sie in das Feld **Kontoschlüssel** den Primärschlüssel ein, oder kopieren Sie ihn aus dem [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/?LinkID=213885), und fügen Sie ihn dann ein. So kopieren Sie diesen Schlüssel aus dem Verwaltungsportal

    1. Klicken Sie unten auf der Seite für das entsprechende Speicherkonto auf die Schaltfläche **Schlüssel verwalten**.

    1. Markieren Sie auf der Seite **Schlüsselzugriff verwalten** den Text des primären Zugriffsschlüssels, und drücken Sie dann STRG+C.

    1. Fügen Sie in Azure Tools den Schlüssel in das Feld **Kontoschlüssel** ein.

    1. Sie müssen eine der folgenden Optionen auswählen, um zu bestimmen, wie der Dienst auf das Speicherkonto zugreift:
        - **HTTP verwenden**. Dies ist die Standardoption. Beispiel: `http://<account name>.blob.core.windows.net`.
        - **HTTPS verwenden** (für eine sichere Verbindung). Beispiel: `https://<accountname>.blob.core.windows.net`.
        - **Benutzerdefinierte Endpunkte angeben** (für jeden der drei Dienste). Sie können dann diese Endpunkte für den jeweiligen Dienst in das Feld eingeben.

        >[AZURE.NOTE]Wenn Sie benutzerdefinierte Endpunkte erstellen, können Sie eine komplexere Verbindungszeichenfolge erstellen. Wenn Sie dieses Zeichenfolgenformat verwenden, können Sie Speicherdienstendpunkte angeben, die einen benutzerdefinierten Domänennamen enthalten, den Sie für Ihr Speicherkonto mit dem Blobdienst registriert haben. Außerdem können Sie über eine SAS (Shared Access Signature) Zugriff auf ausschließlich Blobressourcen in einem einzelnen Container gewähren. Weitere Informationen zum Erstellen benutzerdefinierter Endpunkte finden Sie unter [Konfigurieren von Azure Storage-Verbindungszeichenfolgen](storage-configure-connection-string.md).

1. Um diese Änderungen an der Verbindungszeichenfolge zu speichern, klicken Sie auf die Speichern **OK** und dann auf der Symbolleiste auf die Schaltfläche **Speichern**. Nachdem Sie diese Änderungen gespeichert haben, erhalten Sie den Wert dieser Verbindungszeichenfolge in Ihrem Code mithilfe von [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx). Wenn Sie Ihre Anwendung in Azure veröffentlichen, wählen Sie die Dienstkonfiguration, die das Azure-Speicherkonto für die Verbindungszeichenfolge enthält. Nachdem die Anwendung veröffentlicht wurde, stellen Sie sicher, dass die Anwendung mit den Azure-Speicherdiensten wie erwartet funktioniert.

## Nächste Schritte

Weitere Informationen zum Veröffentlichen von Anwendungen in Azure aus Visual Studio finden Sie unter [Veröffentlichen eines Clouddiensts mit den Azure Tools](vs-azure-tools-publishing-a-cloud-service.md).

<!---HONumber=AcomDC_1217_2015-->