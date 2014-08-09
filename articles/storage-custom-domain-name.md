<properties linkid="manage-services-storage-custom-dns-storage" urlDisplayName="custom dns storage" pageTitle="Configure a domain name for blob data in a storage account | Microsoft Azure" metaKeywords="" description="Learn how to configure a custom domain for accessing blob data in an Azure storage account." metaCanonical="" services="storage" documentationCenter="" title="Configure a custom domain name for blob data in a storage account" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

Konfigurieren eines benutzerdefinierten Domänennamens für Blob-Daten ein einem Azure-Speicherkonto
==================================================================================================

Sie können eine benutzerdefinierte Domäne für den Zugriff auf Blob-Daten in Ihrem Azure-Speicherkonto konfigurieren. Der Standardendpunkt für den Blob-Dienst ist "https://&lt;*mystorageaccount*\>.blob.core.windows.net". Wenn Sie dem Blob-Endpunkt für Ihr Speicherkonto eine benutzerdefinierte Domäne und Unterdomäne wie z. B. **www.contoso.com** zuordnen, können Benutzer auch unter Verwendung dieser Domäne auf Blob-Daten in Ihrem Speicherkonto zugreifen.

**Hinweis**

Die Vorgehensweisen in dieser Aufgabe gelten für Azure-Speicherkonten. Informationen zu Clouddiensten finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst](/de-de/develop/net/common-tasks/custom-dns/); Informationen zu Websites finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website](/de-de/develop/net/common-tasks/custom-dns-web-site/).

Es gibt zwei Methoden, um Ihre benutzerdefinierte Domäne auf den Blob-Endpunkt für Ihr Speicherkonto zu verweisen. Die einfachste Methode besteht darin, einen CNAME-Datensatz zu erstellen, der Ihre benutzerdefinierte Domäne und Unterdomäne dem Blob-Endpunkt zuordnet. Ein CNAME-Datensatz ist eine DNS-Funktion, die eine Quelldomäne einer Zieldomäne zuordnet. In diesem Fall ist die Quelldomäne Ihre benutzerdefinierte Domäne und Unterdomäne (beachten Sie, dass die Unterdomäne immer erforderlich ist). Die Zieldomäne ist der Blob-Dienstendpunkt.

Die Zuordnung der benutzerdefinierten Domäne zum Blob-Endpunkt kann jedoch zu einer kurzzeitigen Downtime für die Domäne führen, während Sie die Domäne im Azure-Verwaltungsportal registrieren. Wenn Ihre benutzerdefinierte Domäne derzeit eine Anwendung mit einer Vereinbarung zum Servicelevel (SLA) unterstützt, die keine Downtime zulässt, können Sie die Azure-Unterdomäne **asverify** verwenden, um einen Registrierungs-Zwischenschritt bereitzustellen, sodass Benutzer auf die Domäne zugreifen können, während die DNS-Zuordnung stattfindet.

Die folgende Tabelle enthält Beispiel-URLs für den Zugriff auf Blob-Daten in einem Speicherkonto namens **mystorageaccount**. Die für das Speicherkonto registrierte benutzerdefinierte Domäne ist **www.contoso.com**:

<table data-morhtml="true" border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody data-morhtml="true">
		<tr data-morhtml="true">
			<td data-morhtml="true" style="width: 100px;"><strong data-morhtml="true">Ressourcentyp</strong></td>
			<td data-morhtml="true"><strong data-morhtml="true">URL-Formate</strong></td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">Speicherkonto</td>
			<td data-morhtml="true"><strong data-morhtml="true">Standard-URL:</strong> http://mystorageaccount.blob.core.windows.net<br data-morhtml="true" /> <strong data-morhtml="true">URL der benutzerdefinierten Dom&auml;ne:</strong> http://www.contoso.com</td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">Blob</td>
			<td data-morhtml="true"><strong data-morhtml="true">Standard-URL:</strong> http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<br data-morhtml="true" /><strong data-morhtml="true">URL der benutzerdefinierten Dom&auml;ne:</strong> http://www.contoso.com/mycontainer/myblob</td>
		</tr>
		<tr data-morhtml="true">
			<td data-morhtml="true">Stammcontainer</td>
			<td data-morhtml="true"><strong data-morhtml="true">Standard-URL:</strong> http://mystorageaccount.blob.core.windows.net/myblob <br data-morhtml="true" />oder<br data-morhtml="true" /> http://mystorageaccount.blob.core.windows.net/$root/myblob<br data-morhtml="true" /> <strong data-morhtml="true">URL der benutzerdefinierten Dom&auml;ne:</strong> http://www.contoso.com/myblob <br data-morhtml="true" />oder<br data-morhtml="true" /> http://www.contoso.com/$root/myblob</td>
		</tr>
	</tbody>
</table>

In dieser Aufgabe werden die folgenden Verfahren erläutert:

-   [Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto](#register-domain)
-   [Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"](#register-asverify)
-   [Überprüfen, ob die benutzerdefinierte Domäne auf den Blob-Dienstendpunkt verweist](#verify-subdomain)

Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto
-------------------------------------------------------------------

Gehen Sie nach diesem Verfahren vor, um Ihre benutzerdefinierte Domäne zu registrieren, wenn es unerheblich ist, ob die Domäne Benutzern kurzzeitig nicht zur Verfügung steht, oder wenn die benutzerdefinierte Domäne derzeit keine Anwendung hostet.

Wenn Ihre benutzerdefinierte Domäne derzeit eine Anwendung unterstützt, die keine Downtime zulässt, gehen Sie nach dem unter [Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"](#register-asverify) beschriebenen Verfahren vor.

Zum Konfigurieren eines benutzerdefinierten Domänennamens müssen Sie einen neuen CNAME-Datensatz bei der Domänenregistrierung erstellen. Der CNAME-Datensatz gibt einen Alias für einen Domänennamen an; in diesem Fall ordnet er die Adresse der benutzerdefinierten Domäne dem Blob-Dienstendpunkt für Ihr Speicherkonto zu.

Die Methoden zum Festlegen eines CNAME-Datensatzes der verschiedenen Registrierungen sind zwar ähnlich, jedoch unterscheiden sie sich auch in einigen Punkten. Das Konzept ist allerdings gleich. Beachten Sie, dass viele Basispakete für die Domänenregistrierung keine DNS-Konfiguration anbieten, daher müssen Sie möglicherweise Ihr Domänenregistrierungspaket aktualisieren, bevor Sie den CNAME-Datensatz erstellen können.

1.  Navigieren Sie im Azure-Verwaltungsportal zur Registerkarte **Speicher**.

2.  Klicken Sie auf der Registerkarte **Speicher** auf den Namen des Speicherkontos, für das Sie die benutzerdefinierte Domäne zuordnen möchten.

3.  Klicken Sie auf die Registerkarte **Konfigurieren**.

4.  Klicken Sie am unteren Rand des Bildschirms auf **Domäne verwalten**, um das Dialogfeld **Benutzerdefinierte Domäne verwalten** anzuzeigen. Oben im Dialogfeld wird ein Text mit Anweisungen zum Erstellen des CNAME-Datensatzes angezeigt. Ignorieren Sie für dieses Verfahren den Text, der sich auf die Unterdomäne **asverify** bezieht.

5.  Melden Sie sich bei der Website Ihrer DNS-Registrierung an, und rufen Sie die Seite zur DNS-Verwaltung auf. Diese finden Sie z. B. in einem Abschnitt mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

6.  Suchen Sie den Abschnitt zur Verwaltung von CNAMEs. Möglicherweise müssen Sie eine Seite mit erweiterten Einstellungen aufrufen und nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen** suchen.

7.  Erstellen Sie einen neuen CNAME-Datensatz, und geben Sie einen Unterdomänenalias wie z. B. **www** oder **photos** an. Geben Sie dann einen Hostnamen (Ihr Blob-Dienstendpunkt) im Format **mystorageaccount.blob.core.windows.net** an (hierbei ist **mystorageaccount** der Name Ihres Speicherkontos). Der zu verwendende Hostname wird im Text des Dialogfelds **Benutzerdefinierte Domäne verwalten** bereitgestellt.

8.  Nachdem Sie den CNAME-Datensatz erstellt haben, kehren Sie zum Dialogfeld **Benutzerdefinierte Domäne verwalten** zurück, und geben Sie den Namen Ihrer benutzerdefinierten Domäne einschließlich Unterdomäne in das Feld **Benutzerdefinierter Domänenname** ein. Beispiel: Wenn die Domäne **contoso.com** und die Unterdomäne **www** lautet, geben Sie **www.contoso.com** ein; wenn die Unterdomäne **photos** lautet, geben Sie **photos.contoso.com** ein. Beachten Sie, dass die Unterdomäne erforderlich ist.

9.  Klicken Sie auf die Schaltfläche **Registrieren**, um die benutzerdefinierte Domäne zu registrieren.

    Bei erfolgreicher Registrierung wird die Meldung **Ihre benutzerdefinierte Domäne ist aktiv** angezeigt. Benutzer können jetzt Blob-Daten in Ihrer benutzerdefinierten Domäne anzeigen, sofern sie über die entsprechenden Berechtigungen verfügen.

Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"
-----------------------------------------------------------------------------------------------------------------------

Gehen Sie nach diesem Verfahren vor, um Ihre benutzerdefinierte Domäne zu registrieren, wenn diese derzeit eine Anwendung mit einer SLA unterstützt, die keine Downtime zulässt. Indem Sie einen CNAME erstellen, der von "asverify.&lt;subdomain\>.&lt;customdomain\>" auf "asverify.&lt;storageaccount\>.blob.core.windows.net" verweist, können Sie Ihre Domäne bei Azure vorregistrieren. Anschließend können Sie einen zweiten CNAME erstellen, der von "&lt;subdomain\>.&lt;customdomain\>" auf "&lt;storageaccount\>.blob.core.windows.net" verweist; an diesem Punkt wird Datenverkehr zu Ihrer benutzerdefinierten Domäne an Ihren Blob-Endpunkt geleitet.

Die Unterdomäne "asverify" ist eine spezielle Unterdomäne, die von Azure erkannt wird. Indem Sie Ihrer eigenen Unterdomäne **asverify** voranstellen, erlauben Sie Azure, Ihre benutzerdefinierte Domäne zu erkennen, ohne den DNS-Datensatz für die Domäne zu ändern. Sobald Sie den DNS-Datensatz für die Domäne ändern, wird diese dem Blob-Endpunkt ohne Downtime zugeordnet.

1.  Navigieren Sie im Azure-Verwaltungsportal zur Registerkarte **Speicher**.

2.  Klicken Sie auf der Registerkarte **Speicher** auf den Namen des Speicherkontos, für das Sie die benutzerdefinierte Domäne zuordnen möchten.

3.  Klicken Sie auf die Registerkarte **Konfigurieren**.

4.  Klicken Sie am unteren Rand des Bildschirms auf **Domäne verwalten**, um das Dialogfeld **Benutzerdefinierte Domäne verwalten** anzuzeigen. Oben im Dialogfeld wird ein Text mit Anweisungen zum Erstellen des CNAME-Datensatzes mit der Unterdomäne **asverify** angezeigt.

5.  Melden Sie sich bei der Website Ihrer DNS-Registrierung an, und rufen Sie die Seite zur DNS-Verwaltung auf. Diese finden Sie z. B. in einem Abschnitt mit der Bezeichnung **Domänenname**, **DNS** oder **Namenserververwaltung**.

6.  Suchen Sie den Abschnitt zur Verwaltung von CNAMEs. Möglicherweise müssen Sie eine Seite mit erweiterten Einstellungen aufrufen und nach den Wörtern **CNAME**, **Alias** oder **Unterdomänen** suchen.

7.  Erstellen Sie einen neuen CNAME-Datensatz, und geben Sie einen Unterdomänenalias an, der die Unterdomäne "asverify" enthält. Die angegebene Unterdomäne kann z. B. das Format **asverify.www** oder **asverify.photos** haben. Geben Sie dann einen Hostnamen (Ihr Blob-Dienstendpunkt) im Format **asverify.mystorageaccount.blob.core.windows.net** an (hierbei ist **mystorageaccount** der Name Ihres Speicherkontos). Der zu verwendende Hostname wird im Text des Dialogfelds **Benutzerdefinierte Domäne verwalten** bereitgestellt.

8.  Nachdem Sie den CNAME-Datensatz erstellt haben, kehren Sie zum Dialogfeld **Benutzerdefinierte Domäne verwalten** zurück, und geben Sie den Namen Ihrer benutzerdefinierten Domäne in das Feld **Benutzerdefinierter Domänenname** ein. Beispiel: Wenn die Domäne **contoso.com** und die Unterdomäne **www** lautet, geben Sie **www.contoso.com** ein; wenn die Unterdomäne **photos** lautet, geben Sie **photos.contoso.com** ein. Beachten Sie, dass die Unterdomäne erforderlich ist.

9.  Aktivieren Sie das Kontrollkästchen **Advanced: Use the 'asverify' subdomain to preregister my custom domain**.

10. Klicken Sie auf die Schaltfläche **Registrieren**, um Ihre benutzerdefinierte Domäne vorzuregistrieren.

    Bei erfolgreicher Vorregistrierung wird die Meldung **Ihre benutzerdefinierte Domäne ist aktiv** angezeigt.

11. An diesem Punkt wurde Ihre benutzerdefinierte Domäne von Azure überprüft, allerdings wird der Datenverkehr zu Ihrer Domäne noch nicht zu Ihrem Speicherkonto geleitet. Um den Prozess abzuschließen, kehren Sie zur Website der DNS-Registrierung zurück, und erstellen Sie einen weiteren CNAME-Datensatz, der Ihre Unterdomäne Ihrem Blob-Dienstendpunkt zuordnet. Geben Sie z. B. die Unterdomäne **www** oder **photos** und den Hostnamen **mystorageaccount.blob.core.windows.net** an (wobei **mystorageaccount** der Name Ihres Speicherkontos ist). Mit diesem Schritt ist die Registrierung Ihrer benutzerdefinierten Domäne abgeschlossen.

12. Abschließend können Sie den CNAME-Datensatz löschen, den Sie mit **asverify** erstellt haben, da er nur als Zwischenschritt benötigt wurde.

Benutzer können jetzt Blob-Daten in Ihrer benutzerdefinierten Domäne anzeigen, sofern sie über die entsprechenden Berechtigungen verfügen.

Überprüfen, ob die benutzerdefinierte Domäne auf den Blob-Dienstendpunkt verweist
---------------------------------------------------------------------------------

Um zu überprüfen, ob Ihre benutzerdefinierte Domäne tatsächlich Ihrem Blob-Dienstendpunkt zugeordnet ist, erstellen Sie einen Blob in einem öffentlichen Container in Ihrem Speicherkonto. Greifen Sie anschließend in einem Webbrowser mit einem URI im folgenden Format auf den Blob zu:

-   http://&lt;*subdomain.customdomain*\>/&lt;*mycontainer*\>/&lt;*myblob*\>

Sie können z. B. den folgenden URI verwenden, um über eine benutzerdefinierte Unterdomäne **photos.contoso.com**, die einem Blob im Container **myforms** zugeordnet ist, auf ein Webformular zuzugreifen:

-   http://photos.contoso.com/myforms/applicationform.htm

Zusätzliche Ressourcen
----------------------

-   [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne](http://msdn.microsoft.com/de-de/library/windowsazure/gg680307.aspx)

