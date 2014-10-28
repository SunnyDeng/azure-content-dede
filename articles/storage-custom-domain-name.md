<properties linkid="manage-services-storage-custom-dns-storage" urlDisplayName="custom dns storage" pageTitle="Configure a domain name for blob data in a storage account | Microsoft Azure" metaKeywords="" description="Learn how to configure a custom domain for accessing blob data in an Azure storage account." metaCanonical="" services="storage" documentationCenter="" title="Configure a custom domain name for blob data in a storage account" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Konfigurieren eines benutzerdefinierten Domänennamens für Blob-Daten ein einem Azure-Speicherkonto

Sie können eine benutzerdefinierte Domäne für den Zugriff auf Blob-Daten in Ihrem Azure-Speicherkonto konfigurieren. Der Standardendpunkt für den Blob-Dienst ist https://<*mystorageaccount*>.blob.core.windows.net. Wenn Sie dem Blob-Endpunkt für Ihr Speicherkonto eine benutzerdefinierte Domäne und Unterdomäne wie z. B. **www.contoso.com** zuordnen, können Benutzer auch unter Verwendung dieser Domäne auf Blob-Daten in Ihrem Speicherkonto zugreifen.

<div class="dev-callout"> 
<b>Hinweis</b> 
    <p>Die Vorgehensweisen in dieser Aufgabe gelten f&uuml;r Azure-Speicherkonten. Informationen zu Cloud-Diensten finden Sie unter <a href = "/de-de/develop/net/common-tasks/custom-dns/">Konfigurieren eines benutzerdefinierten Dom&auml;nennamens f&uuml;r einen Azure-Cloud-Dienst</a>, und zu Webseiten unter <a href="/de-de/develop/net/common-tasks/custom-dns-web-site/">Konfigurieren eines benutzerdefinierten Dom&auml;nennamens f&uuml;r eine Azure-Website</a>.</p> 
</div>

Es gibt zwei Methoden, um Ihre benutzerdefinierte Domäne auf den Blob-Endpunkt für Ihr Speicherkonto zu verweisen. Die einfachste Methode besteht darin, einen CNAME-Datensatz zu erstellen, der Ihre benutzerdefinierte Domäne und Unterdomäne dem Blob-Endpunkt zuordnet. Ein CNAME-Datensatz ist eine DNS-Funktion, die eine Quelldomäne einer Zieldomäne zuordnet. In diesem Fall ist die Quelldomäne Ihre benutzerdefinierte Domäne und Unterdomäne (beachten Sie, dass die Unterdomäne immer erforderlich ist). Die Zieldomäne ist der Blob-Dienstendpunkt.

Die Zuordnung der benutzerdefinierten Domäne zum Blob-Endpunkt kann jedoch zu einer kurzzeitigen Downtime für die Domäne führen, während Sie die Domäne im Azure-Verwaltungsportal registrieren. Wenn Ihre benutzerdefinierte Domäne derzeit eine Anwendung mit einer Vereinbarung zum Servicelevel (SLA) unterstützt, die keine Downtime zulässt, können Sie die Azure-Unterdomäne **asverify** verwenden, um einen Registrierungs-Zwischenschritt bereitzustellen, sodass Benutzer auf die Domäne zugreifen können, während die DNS-Zuordnung stattfindet.

Die folgende Tabelle enthält Beispiel-URLs für den Zugriff auf Blob-Daten in einem Speicherkonto namens **mystorageaccount**. Die für das Speicherkonto registrierte benutzerdefinierte Domäne ist **www.contoso.com**:





<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tbody>
		<tr>
			<td style="width: 100px;"><strong>Ressourcentyp</strong></td>
			<td><strong>URL-Formate</strong></td>
		</tr>
		<tr>
			<td>Speicherkonto</td>
			<td><strong>Standard-URL:</strong> http://mystorageaccount.blob.core.windows.net<br />
			<strong>URL der benutzerdefinierten Domäne:</strong> http://www.contoso.com</td>
		</tr>
		<tr>
			<td>Blob</td>
			<td><strong>Standard-URL:</strong> http://mystorageaccount.blob.core.windows.net/mycontainer/myblob<br /><strong>URL der benutzerdefinierten Domäne:</strong>
			http://www.contoso.com/mycontainer/myblob</td>
		</tr>
		<tr>
			<td>Stammcontainer </td>
			<td><strong>Standard-URL:</strong> http://mystorageaccount.blob.core.windows.net/myblob 
			<br/>or<br />
			http://mystorageaccount.blob.core.windows.net/$root/myblob<br />
			<strong>URL der benutzerdefinierten Domäne:</strong> http://www.contoso.com/myblob
			<br/>oder<br />
			http://www.contoso.com/$root/myblob</td>
		</tr>
	</tbody>
</table>
In dieser Aufgabe werden die folgenden Verfahren erläutert:

-   [Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto][Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto]
-   [Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"][Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"]
-   <a name="#verify-subdomain">Überprüfen, ob die benutzerdefinierte Domäne auf den Blob-Dienstendpunkt verweist</a>

## <a name="register-domain"></a>Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto

Gehen Sie nach diesem Verfahren vor, um Ihre benutzerdefinierte Domäne zu registrieren, wenn es unerheblich ist, ob die Domäne Benutzern kurzzeitig nicht zur Verfügung steht, oder wenn die benutzerdefinierte Domäne derzeit keine Anwendung hostet.

Wenn Ihre benutzerdefinierte Domäne derzeit eine Anwendung unterstützt, die keine Downtime zulässt, gehen Sie nach dem unter [Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"][Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"] beschriebenen Verfahren vor.

Zum Konfigurieren eines benutzerdefinierten Domänennamens müssen Sie einen neuen CNAME-Datensatz bei der Domänenregistrierung erstellen. Der CNAME-Datensatz gibt einen Alias für einen Domänennamen an; in diesem Fall ordnet er die Adresse der benutzerdefinierten Domäne dem Blob-Dienstendpunkt für Ihr Speicherkonto zu.

Alle Registrierungsstelle haben ähnliche, jedoch leicht unterschiedliche Methoden für die Angabe von CNAME-Einträgen.
Das Konzept ist jedoch dasselbe. Beachten Sie, dass viele Basispakete für die Domänenregistrierung keine DNS-Konfiguration anbieten, daher müssen Sie möglicherweise Ihr Domänenregistrierungspaket aktualisieren, bevor Sie den CNAME-Datensatz erstellen können.

1.  Navigieren Sie im Azure-Verwaltungsportal zur Registerkarte **Speicher**.

2.  Klicken Sie auf der Registerkarte **Speicher** auf den Namen des Speicherkontos, für das Sie die benutzerdefinierte Domäne zuordnen möchten.

3.  Klicken Sie auf die Registerkarte **Configure**.

4.  Klicken Sie am unteren Rand des Bildschirms auf **Domäne verwalten**, um das Dialogfeld **Benutzerdefinierte Domäne verwalten** anzuzeigen. Oben im Dialogfeld wird ein Text mit Anweisungen zum Erstellen des CNAME-Datensatzes angezeigt. Ignorieren Sie für dieses Verfahren den Text, der sich auf die Unterdomäne **asverify** bezieht.

5.  Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an und öffnen Sie die Seite für die
    DNS-Verwaltung. Sie finden diese Option in Bereichen wie z. B. **Domänenname**,
    **DNS** oder **Namenserververwaltung**.

6.  Suchen Sie den Abschnitt zur Verwaltung von CNAMEs. Öffnen Sie ggf. die Seite mit
    erweiterten Einstellungen, und suchen Sie nach **CNAME**, **Alias**
    oder **Unterdomänen**.

7.  Erstellen Sie einen neuen CNAME-Datensatz, und geben Sie einen Unterdomänenalias wie z. B. **www** oder **photos** an. Geben Sie
    dann den Endpunkt Ihres Blob-Diensts als Hostname im folgenden Format ein: **meinSpeicherkonto.blob.core.windows.net** (ersetzen Sie **meinSpeicherkonto** durch den Namen Ihres Speicherkontos). Der zu verwendende Hostname wird im Text des Dialogfelds **Benutzerdefinierte Domäne verwalten** bereitgestellt.

8.  Nachdem Sie den CNAME-Datensatz erstellt haben, kehren Sie zum Dialogfeld **Benutzerdefinierte Domäne verwalten** zurück, und geben Sie den Namen Ihrer benutzerdefinierten Domäne einschließlich Unterdomäne in das Feld **Benutzerdefinierter Domänenname** ein. Beispiel: Wenn die Domäne **contoso.com** und die Unterdomäne **www** lautet, geben Sie **www.contoso.com** ein; wenn die Unterdomäne **photos** lautet, geben Sie **photos.contoso.com** ein. Beachten Sie, dass die Unterdomäne erforderlich ist.

9.  Klicken Sie auf die Schaltfläche **Registrieren**, um die benutzerdefinierte Domäne zu registrieren.

    Bei erfolgreicher Registrierung wird die Meldung **Ihre benutzerdefinierte Domäne ist aktiv** angezeigt. Benutzer können jetzt Blob-Daten in Ihrer benutzerdefinierten Domäne anzeigen, sofern sie über die entsprechenden Berechtigungen verfügen.

## <a name="register-asverify"></a>Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"

Gehen Sie nach diesem Verfahren vor, um Ihre benutzerdefinierte Domäne zu registrieren, wenn diese derzeit eine Anwendung mit einer SLA unterstützt, die keine Downtime zulässt. Indem Sie einen CNAME erstellen, der von asverify.<subdomain\>.<customdomain\> auf asverify.<storageaccount\>.blob.core.windows.net" verweist, können Sie Ihre Domäne bei Azure vorregistrieren. Anschließend können Sie einen zweiten CNAME erstellen, der von <subdomain\>.<customdomain\> auf <storageaccount\>.blob.core.windows.net" verweist; an diesem Punkt wird Datenverkehr zu Ihrer benutzerdefinierten Domäne an Ihren Blob-Endpunkt geleitet.

Die Unterdomäne "asverify" ist eine spezielle Unterdomäne, die von Azure erkannt wird. Indem Sie Ihrer eigenen Unterdomäne **asverify** voranstellen, erlauben Sie Azure, Ihre benutzerdefinierte Domäne zu erkennen, ohne den DNS-Datensatz für die Domäne zu ändern. Sobald Sie den DNS-Datensatz für die Domäne ändern, wird diese dem Blob-Endpunkt ohne Downtime zugeordnet.

1.  Navigieren Sie im Azure-Verwaltungsportal zur Registerkarte **Speicher**.

2.  Klicken Sie auf der Registerkarte **Speicher** auf den Namen des Speicherkontos, für das Sie die benutzerdefinierte Domäne zuordnen möchten.

3.  Klicken Sie auf die Registerkarte **Configure**.

4.  Klicken Sie am unteren Rand des Bildschirms auf **Domäne verwalten**, um das Dialogfeld **Benutzerdefinierte Domäne verwalten** anzuzeigen. Oben im Dialogfeld wird ein Text mit Anweisungen zum Erstellen des CNAME-Datensatzes mit der Unterdomäne **asverify** angezeigt.

5.  Melden Sie sich bei der Website Ihrer DNS-Registrierungsstelle an und öffnen Sie die Seite für die
    DNS-Verwaltung. Sie finden diese Option in Bereichen wie z. B. **Domänenname**,
    **DNS** oder **Namenserververwaltung**.

6.  Suchen Sie den Abschnitt zur Verwaltung von CNAMEs. Öffnen Sie ggf. die Seite mit
    erweiterten Einstellungen, und suchen Sie nach **CNAME**, **Alias**
    oder **Unterdomänen**.

7.  Erstellen Sie einen neuen CNAME-Datensatz, und geben Sie einen Unterdomänenalias an, der die Unterdomäne "asverify" enthält. Die angegebene Unterdomäne kann z. B. das Format **asverify.www** oder **asverify.photos** haben. Geben Sie
    dann den Endpunkt Ihres Blob-Diensts als Hostname im folgenden Format ein: **asverify.meinSpeicherkonto.blob.core.windows.net** (ersetzen Sie **meinSpeicherkonto** durch den Namen Ihres Speicherkontos). Der zu verwendende Hostname wird im Text des Dialogfelds **Benutzerdefinierte Domäne verwalten** bereitgestellt.

8.  Nachdem Sie den CNAME-Datensatz erstellt haben, kehren Sie zum Dialogfeld **Benutzerdefinierte Domäne verwalten** zurück, und geben Sie den Namen Ihrer benutzerdefinierten Domäne in das Feld **Benutzerdefinierter Domänenname** ein. Beispiel: Wenn die Domäne **contoso.com** und die Unterdomäne **www** lautet, geben Sie **www.contoso.com** ein; wenn die Unterdomäne **photos** lautet, geben Sie **photos.contoso.com** ein. Beachten Sie, dass die Unterdomäne erforderlich ist.

9.  Aktivieren Sie das Kontrollkästchen **Advanced: Use the 'asverify' subdomain to preregister my custom domain**.

10. Klicken Sie auf die Schaltfläche **Registrieren**, um Ihre benutzerdefinierte Domäne vorzuregistrieren.

    Bei erfolgreicher Vorregistrierung wird die Meldung **Ihre benutzerdefinierte Domäne ist aktiv** angezeigt.

11. An diesem Punkt wurde Ihre benutzerdefinierte Domäne von Azure überprüft, allerdings wird der Datenverkehr zu Ihrer Domäne noch nicht zu Ihrem Speicherkonto geleitet. Um den Vorgang anzuschließen, kehren Sie zur Website der DNS-Registrierungsstelle zurück und erstellen einen weiteren CNAME-Eintrag, um Ihre Unterdomäne zum Endpunkt Ihres Blob-Diensts zuzuordnen. Geben Sie z. B. die Unterdomäne **www** oder **photos** und den Hostnamen **mystorageaccount.blob.core.windows.net** an (wobei **mystorageaccount** der Name Ihres Speicherkontos ist). Mit diesem Schritt ist die Registrierung Ihrer benutzerdefinierten Domäne abgeschlossen.

12. Abschließend können Sie den CNAME-Datensatz löschen, den Sie mit **asverify** erstellt haben, da er nur als Zwischenschritt benötigt wurde.

Benutzer können jetzt Blob-Daten in Ihrer benutzerdefinierten Domäne anzeigen, sofern sie über die entsprechenden Berechtigungen verfügen.

<a name="verify-subdomain"> </a>

## Überprüfen, ob die benutzerdefinierte Domäne auf den Blob-Dienstendpunkt verweist

Um zu überprüfen, ob Ihre benutzerdefinierte Domäne tatsächlich Ihrem Blob-Dienstendpunkt zugeordnet ist, erstellen Sie einen Blob in einem öffentlichen Container in Ihrem Speicherkonto. Greifen Sie anschließend in einem Webbrowser mit einem URI im folgenden Format auf den Blob zu:

-   <http://<*subdomain.customdomain*\>/<*mycontainer*\>/<*myblob*\>

Sie können z. B. den folgenden URI verwenden, um über eine benutzerdefinierte Unterdomäne
**photos.contoso.com**, die einem Blob im Container
**myforms** zugeordnet ist, auf ein Webformular zuzugreifen:

-   http://photos.contoso.com/myforms/applicationform.htm

## Zusätzliche Ressourcen

-   [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne][Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne]

  [Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Cloud-Dienst]: /de-de/develop/net/common-tasks/custom-dns/
  [Konfigurieren eines benutzerdefinierten Domänennamens für eine Azure-Website]: /de-de/develop/net/common-tasks/custom-dns-web-site/
  [Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto]: #register-domain
  [Registrieren einer benutzerdefinierten Domäne für ein Speicherkonto unter Verwendung der Zwischenunterdomäne "asverify"]: #register-asverify
  [Zuordnen von CDN-Inhalt zu einer benutzerdefinierten Domäne]: http://msdn.microsoft.com/de-de/library/windowsazure/gg680307.aspx
