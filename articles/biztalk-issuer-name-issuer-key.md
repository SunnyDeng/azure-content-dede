<properties linkid="manage-services-biztalk-services-issuer-name-issuer-key" urlDisplayName="Issuer name and issuer key" pageTitle="Issuer Name and Issuer Key in BizTalk Services | Azure" metaKeywords="BizTalk Services, BizTalk, issuer name, issuer key, Azure" description="Learn how to retrieve Issuer Name and Issuer Key for either Service Bus or Access Control (ACS) in BizTalk Services." metaCanonical="" services="biztalk-services" documentationCenter="" title="BizTalk Services: Issuer Name and Issuer Key" authors="mandia" solutions="" manager="paulettm" editor="susanjo" />

BizTalk Services: Name und Schlüssel des Ausstellers
====================================================

Azure BizTalk Services verwendet Ausstellername und -schlüssel von Service Bus und Access Control. Dies gilt insbesondere in folgenden Fällen:

<table border="1">
<tr bgcolor="FAF9F9">
<td><strong>Aufgabe</strong></td>
<td><strong>Welcher Ausstellername und -schlüssel?</strong></td>
</tr>
<tr>
<td>Bereitstellen einer Anwendung in Visual Studio</td>
<td>Access Control-Ausstellername und -schlüssel</td>
</tr>
<tr>
<td>Konfigurieren des Azure BizTalk Services-Portals</td>
<td>Access Control-Ausstellername und -schlüssel</td>
</tr>
<tr>
<td>Erstellen von LOB-Relays mit den BizTalk Adapter-Diensten in Visual Studio</td>
<td>Service Bus-Ausstellername und -schlüssel</td>
</tr>
</table>

Dieses Thema beschreibt die notwendigen Schritte zum Abrufen von Ausstellername und -schlüssel.

Access Control-Ausstellername und -schlüssel
--------------------------------------------

Access Control-Ausstellername und -schlüssel werden von den folgenden Komponenten verwendet:

-   Ihre Azure BizTalk Service-Anwendung, die Sie in Visual Studio erstellt haben: Um Ihre BizTalk Service-Anwendung in Visual Studio für Azure bereitzustellen, müssen Sie Access Control-Ausstellername und -schlüssel eingeben.
-   Das Azure BizTalk Services-Portal: Bei Ihrer ersten Anmeldung am BizTalk Services-Portal geben Sie Ihren BizTalk-Servicenamen als Dienstanbieter sowie Access Control-Ausstellername und -schlüssel ein.

### Abrufen von Access Control-Ausstellername und -schlüssel

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2.  Klicken Sie im linken Navigationsbereich auf **BizTalk Services**.
3.  Wählen Sie Ihren BizTalk Service aus.
4.  Wählen Sie **Connection Information** in der Taskleiste aus. Dort sind Access Control-Namespace, -Standardaussteller (Ausstellername) und -Standardschlüssel (Ausstellerschlüssel) aufgelistet. Sie können diese Werte kopieren und einfügen.<br/><br/>
Zusammenfassung:<br/>
Ausstellername = Standardaussteller<br/>
Ausstellerschlüssel = Standardschlüssel

Alternativ können Sie auf **Open ACS Management Portal** klicken, um die Access Control-Werte abzurufen:

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2.  Klicken Sie im linken Navigationsbereich auf **BizTalk Services**.
3.  Wählen Sie Ihren BizTalk Service aus.
4.  Klicken Sie auf die Connection Information-Schaltfläche und wählen Sie **Open ACS Management Portal** aus.
5.  Klicken Sie im Portal unter **Service Settings** auf **Service Identities**. Dort wird Ihre Dienstidentität angezeigt. Dies ist gleichzeitig Ihr Access Control Ausstellername. Klicken Sie auf den Service Identity-Link, um das Kennwort anzuzeigen, das gleichzeitig Ihr Ausstellerschlüssel ist. Diese Werte können kopiert werden.<br/><br/>
Unter **Service Identities** sehen Sie z. B. "owner". "Owner" ist Ihr Access Control Ausstellername. Wenn Sie auf den "owner"-Link klicken, wird **Password** angezeigt. Wenn Sie auf den "Password"-Link klicken, wird der Wert angezeigt. Dieser Password-Wert ist Ihr Access Control Ausstellerschlüssel.<br/><br/>
Zusammenfassung:<br/>
Issuer Name = Dienstidentitäts-Name<br/>
Issuer Key = Password-Wert

Im linken Navigationsbereich können Sie außerdem **Active Directory** auswählen, um die Access Control-Werte abzurufen.

**Wichtig**

Wenn Sie einen Access Control-Namespace mit **Active Directory** erstellen, wird die Dienstidentität **nicht** automatisch erstellt. Wenn Sie einen BizTalk Service einrichten, werden Access Control-Namespace, eine Dienstidentität mit dem Namen "owner" (Ausstellername), Kennwort (Ausstellerschlüssel) und ein symmetrischer Schlüssel automatisch erstellt.

[Gewusst wie: Verwenden des ACS Management Service zum Konfigurieren von Dienstidentitäten](http://go.microsoft.com/fwlink/p/?LinkID=303942) enthält weitere Informationen zu Access Control-Dienstidentitäten.

Service Bus-Ausstellername und -schlüssel
-----------------------------------------

Service Bus-Ausstellername und -schlüssel werden von BizTalk-Adapterdiensten verwendet. In Ihrem BizTalk Services-Projekt in Visual Studio können Sie die BizTalk-Adapterdienste verwenden, um sich mit einem lokalen Line-of-Business (LOB)-System zu verbinden. Erstellen Sie dazu das LOB-Relay und geben Sie Ihre LOB-Systemdetails ein. Bei diesem Prozess geben Sie ebenfalls Ihren Service Bus-Ausstellernamen und -schlüssel ein.

### Abrufen von Service Bus-Ausstellername und -schlüssel

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=213885) an.
2.  Klicken Sie im linken Navigationsbereich auf **Service Bus**.
3.  Klicken Sie auf Ihren Namespace. Klicken Sie in der Taskleiste auf **Verbindungsinformationen**. Daraufhin werden **Standardaussteller** (Ausstellername) und **Standardschlüssel** (Ausstellerschlüssel) angezeigt. Diese Werte können kopiert werden.<br/><br/>
Zusammenfassung:<br/>
Ausstellername = Standardaussteller<br/>
Ausstellerschlüssel = Standardschlüssel

Nächste Schritte
----------------

Zusätzliche Azure BizTalk Services-Themen:

-   [Installieren des Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)
-   [Lernprogramme: Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=236944)
-   [Wie verwende ich das Azure BizTalk Services SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)
-   [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=303664)

Weitere Informationen
---------------------

-   [Gewusst wie: Verwenden des ACS Management Service zum Konfigurieren von Dienstidentitäten](http://go.microsoft.com/fwlink/p/?LinkID=303942)
-   [BizTalk Services: Übersicht über Developer, Basic, Standard und Premium Edition](http://go.microsoft.com/fwlink/p/?LinkID=302279)
-   [BizTalk Services: Bereitstellen mit dem Azure-Verwaltungsportal](http://go.microsoft.com/fwlink/p/?LinkID=302280)
-   [BizTalk Services: Bereitstellungsstatusübersicht](http://go.microsoft.com/fwlink/p/?LinkID=329870)
-   [BizTalk Services: Registerkarten "Dashboard", "Überwachen" und "Skalieren"](http://go.microsoft.com/fwlink/p/?LinkID=302281)
-   [BizTalk Services: Sichern und Wiederherstellen](http://go.microsoft.com/fwlink/p/?LinkID=329873)
-   [BizTalk Services: Drosselung](http://go.microsoft.com/fwlink/p/?LinkID=302282)

