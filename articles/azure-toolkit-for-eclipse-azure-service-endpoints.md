<properties
    pageTitle="Azure-Dienstendpunkte"
    description="Beschreibt die Einstellungen für Azure-Dienstendpunkte im Azure-Toolkit für Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="03/04/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# Azure-Dienstendpunkte #

Azure-Dienstendpunkte bestimmen, ob Ihre Anwendung auf der globalen Azure-Plattform bereitgestellt und verwaltet wird, in Azure von 21Vianet in China, oder auf einer privaten Azure-Plattform. Über das Dialogfeld **Service Endpoints** kann festgelegt werden, welche Dienstendpunkte Sie nutzen möchten. Zum Öffnen des Dialogfelds **Service Endpoints** in Eclipse klicken Sie auf **Window**, dann auf **Preferences**, erweitern Sie **Azure**, und klicken Sie dann auf **Service Endpoints**. Durch das Festlegen des Felds **Active Set** wird bestimmt, welche Azure-Dienstendpunkte für die Azure-Projekte in Ihrem aktuellen Arbeitsbereich verwendet werden.

Im Folgenden ist das Dialogfeld **Dienstendpunkte** dargestellt.

![][ic719493]

## So stellen Sie die Dienstendpunkte ein ##

Gehen Sie im Dialogfeld **Service Endpoints** auf eine der folgenden Arten vor:

* Um die globale Azure-Plattform zu verwenden, wählen Sie in der Dropdownliste **Active Set** die Option **windowsazure.com** aus, und klicken Sie auf **OK**.
* Falls Sie die Azure-Bereitstellung bei 21Vianet in China verwenden möchten, wählen Sie in der Dropdownliste **Active Set** die Option **windowsazure.cn (China)** aus, und klicken Sie auf **OK**.
* Wenn Sie eine private Azure-Plattform verwenden möchten:
    1. Klicken Sie auf **Bearbeiten**.
    2. Es wird ein Dialogfeld geöffnet, das Sie darüber informiert, dass das Dialogfeld **Service Endpoints** geschlossen und die Voreinstellungsgruppendatei geöffnet wird. Klicken Sie auf **OK**.
    3. Erstellen Sie in der Datei „preferencesets.xml“ ein neues `preferenceset`-Element. Erstellen Sie für dieses neue Element die Attribute `name`, `blob`, `management`, `portalURL` und `publishsettings`, und vergeben Sie entsprechende Werte in Übereinstimmung mit Ihrer privaten Azure-Plattform. Sie können die Werte für die vorhandenen `preferenceset`-Elemente als Vorlagen verwenden. **Hinweis**: Der Wert für das `blob`-Attribut muss den Text „Blob“ in der URL enthalten.
    4. Speichern und schließen Sie „preferencesets.xml“.
    5. Öffnen Sie erneut das Dialogfeld **Service Endpoints**.
    6. Wählen Sie in der Dropdownliste **Active Set** die zuvor erstellte aktive Menge aus, und klicken Sie auf **OK**.
    7. Nach dem Erstellen des Elements für die private Azure-Plattform `preferenceset` können die ihr zugewiesenen Werte durch Klicken auf die Schaltfläche **Edit** im Dialogfeld **Service Endpoint** geändert werden. Sie können auch mehrere `preferenceset`-Elemente für private Azure-Plattformen erstellen.

## Weitere Informationen ##

[Azure-Toolkit für Eclipse][]

[Installieren des Azure-Toolkits für Eclipse][]

[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)][]

Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Java Developer Center][] (in englischer Sprache).

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure-Toolkit für Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse (in englischer Sprache)]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installieren des Azure-Toolkits für Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png

<!---HONumber=AcomDC_0309_2016-->