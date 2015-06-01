<properties 
	pageTitle="Verwenden von Access Control (.NET) - Azure-Featureleitfaden" 
	description="Erfahren Sie, wie Sie Access Control Service (ACS) in Ihrer Azure-Anwendung zur Authentifizierung von Benutzern beim Zugriff auf eine Webanwendung verwenden können." 
	services="active-directory" 
	documentationCenter=".net" 
	authors="msmbaldwin" 
	manager="mbaldwin" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/04/2014" 
	ms.author="mbaldwin"/>




# Authentifizieren von Webbenutzern mit der Azure Active Directory-Zugriffssteuerung


Diese Anleitung zeigt, wie Sie mit der Azure Active Directory-Zugriffssteuerung (auch Access Control Service oder ACS genannt) Benutzer von Identitätsanbietern wie Microsoft, Google, Yahoo und Facebook authentifizieren, wenn sie versuchen, auf eine Webanwendung zuzugreifen.


## Was ist der ACS?

Die meisten Entwickler sind keine Identitätsexperten und auch nicht bereit, ihre Zeit für die Entwicklung von Authentifizierungs- und Autorisierungsmechanismen für ihre Anwendungen und Dienste zu verschwenden. ACS ist ein Azure-Dienst, der Ihnen einfache Möglichkeiten bietet, Benutzer für den Zugriff auf Ihre Webanwendungen und -dienste zu authentifizieren, ohne eine komplexe Authentifizierungslogik in Ihren Code aufnehmen zu müssen.

Folgende Funktionen stehen im ACS zur Verfügung:

-   Integration mit Windows Identity Foundation (WIF).
-   Unterstützung beliebter Anbieter von Webidentitäten (IPs) wie beispielsweise Microsoft-Konto (vormals Windows Live ID), Google, Yahoo und Facebook.
-   Unterstützung von Active Directory Federation Services (AD FS) 2.0.
-   Ein auf dem Open Data Protocol (OData) basierender Verwaltungsdienst, der den programmgesteuerten Zugriff auf die ACS-Einstellungen ermöglicht.
-   Ein Verwaltungsportal, über das Administratoren auf die ACS-Einstellungen zugreifen können.

Weitere Informationen zu ACS finden Sie unter [Access Control Service 2.0][].

## Konzepte

ACS basiert auf den Prinzipien der anspruchsbasierten Identität -- einem konsistenten Ansatz zur Erstellung von Authentifizierungsmechanismen für Anwendungen, die entweder lokal oder in der Cloud ausgeführt werden. Die anspruchsbasierte Identität ist eine von Anwendungen und Diensten häufig genutzte Möglichkeit, sich benötigte Identitätsinformationen über Benutzer in ihren Unternehmen, in anderen Unternehmen und auf dem Internet zu beschaffen.

Damit Sie die in dieser Anleitung gestellten Aufgaben durchführen können, müssen Sie folgende Begriffe und Konzepte kennen und wissen, wie sie in dieser Anleitung verwendet werden:


**Client** - Ein Browser, der versucht, auf Ihre Webanwendung zuzugreifen.

**Anwendung der vertrauenden Seite (Relying Party, RP)** - Ihre Webanwendung. Eine RP-Anwendung ist eine Website oder ein Dienst, die/der die Authentifizierung an eine externe Stelle auslagert. Im Identitätskontext sagt man, dass die RP der Authentifizierungsstelle vertraut. In dieser Anleitung wird erklärt, wie Sie eine Anwendung so konfigurieren, dass sie dem ACS vertraut.

**Token** - Ein Benutzer erlangt Zugriff auf eine RP-Anwendung, indem er ein gültiges Token vorweist, das von einer Stelle ausgestellt wurde, der die RP-Anwendung vertraut. Eine Sammlung von Sicherheitsdaten, die bei Authentifizierung eines Clients ausgegeben werden. Sie enthält eine Reihe von Ansprüchen, bei denen es sich um Attribute des authentifizierten Benutzers handelt, beispielsweise den Namen oder das Alter eines Benutzers oder einen Bezeichner einer Benutzerrolle. Ein Token ist digital signiert, sodass der Aussteller identifiziert und der Inhalt nicht geändert werden kann.

**Identitätsanbieter (Identity Provider, IP)** - Eine Stelle, die Benutzeridentitäten authentifiziert und Sicherheitstoken ausgibt, beispielsweise Microsoft-Konto (Windows Live ID), Facebook, Google, Twitter und Active Directory. Wenn zwischen ACS und einem IP ein Vertrauensverhältnis konfiguriert worden ist, akzeptiert und validiert ACS die von diesem IP vergebenen Token. Da ACS gleichzeitig mehreren IPs vertrauen kann, kann Ihre Anwendung, sofern sie ACS vertraut, es den Benutzern überlassen, sich mit einem der IPs zu authentifizieren, denen ACS stellvertretend für Sie vertraut.

**Verbundanbieter (Federation Provider, FP)** - Identitätsanbieter (IPs) kennen die Benutzer, authentifizieren Benutzer anhand ihrer Anmeldeinformationen und geben Ansprüche für Benutzer aus. Ein Verbundanbieter (FP) ist eine andere Art von Authentifizierungsstelle. Ein FP authentifiziert Benutzer nicht direkt, sondern fungiert als Authentifizierungsbroker. Er übernimmt die Funktion eines Vermittlers zwischen einer RP-Anwendung und einem oder mehreren IPs. ACS ist ein Verbundanbieter (FP).

**ACS-Regelmodul** - Anspruchstransformationsregeln konvertieren die Ansprüche in Tokens vertrauenswürdiger IPs, sodass diese von einer RP verwendet werden können. ACS verfügt unter anderem über ein Regelmodul, das die Anspruchstransformationsregeln anwendet, die Sie für Ihre RP festlegen.

**Access Control-Namespace** - Bezeichnet einen eindeutigen Bereich, der für die Adressierung von ACS-Ressourcen innerhalb Ihrer Anwendung bereitsteht. Der Namespace enthält Ihre Einstellungen, beispielsweise die IPs, denen Sie vertrauen, die RP-Anwendungen, die Sie bereitstellen wollen, und die Regeln, die Sie auf eingehende Tokens anwenden. Außerdem zeigt er die Endpunkte, über die die Anwendung und der Entwickler mit ACS kommunizieren.

Die folgende Abbildung zeigt, wie die ACS-Authentifizierung bei einer Webanwendung abläuft:

![][0]

1.  Der Client (in diesem Fall ein Browser) fordert von der RP eine Seite an.
2.  Da die Anforderung noch nicht authentifiziert ist, verweist die RP den Benutzer an die Stelle, der sie vertraut, also an den ACS. Der ACS übermittelt dem Benutzer verschiedene IPs, die für diese RP definiert wurden. Der Benutzer wählt einen passenden IP aus.
3.  Der Client geht auf die Authentifizierungsseite des IPs und fordert den Benutzer auf, sich anzumelden.
4.  Wenn der Client authentifiziert ist (beispielsweise die Anmeldeinformationen eingegeben wurden), gibt der IP ein Sicherheitstoken aus.
5.  Nach der Ausgabe des Sicherheitstokens weist der IP den Client an, dieses Token an ACS zu senden.
6.  Der ACS überprüft das vom IP ausgegebene Token, gibt die in diesem Token enthaltenen Identitätsansprüche in das ACS-Regelmodul ein, berechnet die auszugebenden Identitätsansprüche und gibt ein neues Sicherheitstoken aus, das diese Ansprüche enthält.
7.  ACS weist den Client an, das von ACS ausgegebene Token an die RP zu senden. Die RP validiert die Signatur auf dem Sicherheitstoken, extrahiert die für die Geschäftslogik der Anwendung bestimmten Ansprüche und gibt die ursprünglich angeforderte Seite zurück.

## Voraussetzungen


Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, brauchen Sie Folgendes:

-	Azure-Abonnement
-	Microsoft Visual Studio 2012 
-	Identitäts- und Zugriffstool für Visual Studio 2012 (Download siehe [Identitäts- und Zugriffstool][])


## Erstellen eines Access Control-Namespace

Damit Sie den Active Directory-Zugriffssteuerungsdienst in Azure verwenden können, müssen Sie einen Access Control-Namespace anlegen. Dieser Namespace stellt einen eindeutigen Bereich für die Adressierung von ACS-Ressourcen innerhalb Ihrer Anwendung bereit.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] (https://manage.WindowsAzure.com) an.
    
2.  Klicken Sie auf **Active Directory**.  

	![][1]

3.  Um einen neuen Access Control-Namespace zu erstellen, klicken Sie auf **Neu**. **App-Dienste** und **Zugriffssteuerung** werden ausgewählt. Klicken Sie auf **Schnellerfassung**. 

	![][2]

4.  Geben Sie einen Namen für den Namespace ein. Azure prüft, ob der Name eindeutig ist.

5.  Wählen Sie die Region aus, in der der Namespace verwendet wird. Verwenden Sie zur Optimierung der Leistung die Region, in der Sie Ihre Anwendung bereitstellen, und klicken Sie dann auf **Erstellen**.

Azure erstellt und aktiviert den Namespace.

## Erstellen einer ASP.NET MVC-Anwendung

In diesem Schritt erstellen Sie eine ASP.NET MVC-Anwendung. In nachfolgenden Schritten wird diese einfache Anwendung für Webformulare mit ACS integriert.

1.	Starten Sie Visual Studio 2012 oder Visual Studio Express für Web 2012 (Frühere Versionen von Visual Studio können mit diesem Lernprogramm nicht verwendet werden).
1.	Klicken Sie auf **Datei** und dann auf **Neues Projekt**.
1.	Wählen Sie zuerst die Vorlage "Visual C#/Web" aus und dann **ASP.NET MVC 4-Webanwendung**.

	In dieser Anleitung arbeiten wir mit einer MVC-Anwendung. Sie können für diese Aufgabe allerdings eine beliebige Webanwendung verwenden.

	![][3]

1. Geben Sie in **Name** **MvcACS** ein, und klicken Sie dann auf **OK**.
1. Wählen Sie im nächsten Dialogfeld **Internetanwendung** aus, und klicken Sie dann auf **OK**.
1. Bearbeiten Sie die Datei  *Views\Shared_LoginPartial.cshtml*, und ersetzen Sie den Inhalt durch folgenden Code:

        @if (Request.IsAuthenticated)
        {
            string name = "Null ID";
            if (!String.IsNullOrEmpty(User.Identity.Name))
            {
                name = User.Identity.Name;
            }    
            <text>
            Hello, @Html.ActionLink(name, "Manage", "Account", routeValues: null, htmlAttributes: new { @class = "username", title = "Manage" })!
                    @using (Html.BeginForm("LogOff", "Account", FormMethod.Post, new { id = "logoutForm" }))
                    {
                        @Html.AntiForgeryToken()
                        <a href="javascript:document.getElementById('logoutForm').submit()">Log off</a>
                    }
            </text>
        }
        else
        {
            <ul>
                <li>@Html.ActionLink("Register", "Register", "Account", routeValues: null, htmlAttributes: new { id = "registerLink" })</li>
                <li>@Html.ActionLink("Log in", "Login", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
            </ul>
        }

Derzeit fügt ACS "User.Identity.Name" noch nicht ein, deshalb ist die obige Änderung notwendig.

1. Drücken Sie F5, um die Anwendung auszuführen. Die Standardanwendung ASP.NET MVC erscheint in Ihrem Webbrowser.

## Integrieren der Webanwendung mit ACS

In dieser Aufgabe werden Sie Ihre ASP.NET-Webanwendung mit ACS integrieren.

1.	Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt "MvcACS", und wählen Sie dann **Identität und Zugriff** aus.

	Wenn die Option **Identität und Zugriff** im Kontextmenü nicht angezeigt wird, installieren Sie das Identitäts- und Zugriffstool. Informationen hierzu finden Sie unter [Identitäts- und Zugriffstool]. 

	![][4]

2.	Wählen Sie auf der Registerkarte **Anbieter** die Position **Azure-Zugriffssteuerungsdienst verwenden** aus.

    ![][44]

3.  Klicken Sie auf den Link **Konfigurieren**.

    ![][444]

	Visual Studio fordert Informationen über den Access Control-Namespace an. Geben Sie den Namen des Namespace ein, den Sie zuvor angelegt haben ("Test" in dem Beispiel oben, aber der Name Ihres Namespace wird anders lauten). Wechseln Sie zurück zum Azure-Verwaltungsportal, um den symmetrischen Schlüssel zu beschaffen.

	![][17]

4.  Klicken Sie im Azure-Verwaltungsportal auf den Access Control-Namespace und dann auf **Verwalten**.

	![][8]

5.	Klicken Sie auf **Verwaltungsdienst**, und aktivieren Sie dann **Verwaltungsclient**.

	![][18]

6.	Klicken Sie auf **Symmetrischer Schlüssel**, dann auf **Schlüssel anzeigen**, und kopieren Sie den Schlüsselwert. Klicken Sie abschließend auf **Abbrechen**, um die Seite "Edit Management Client" zu verlassen, ohne Änderungen durchzuführen. 

	![][19]

7.  Fügen Sie den Schlüssel in Visual Studio in das Feld **Enter the Management Key for the namespace** ein, und klicken Sie zuerst auf **Save management key** und dann auf **OK**.

	![][20]

	Visual Studio verwendet die Information über den Namespace, um die Verbindung zum ACS-Verwaltungsportal herzustellen und die Einstellungen für Ihren Namespace abzurufen, unter anderem Identitätsanbieter, Bereich und Rückgabe-URL.

8.	Wählen Sie **Windows Live ID** (Microsoft-Konto) aus, und klicken Sie auf "OK". 

	![][5]

## Testen der Integration mit ACS

In dieser Aufgabe wird erläutert, wie Sie die Integration Ihrer RP-Anwendung mit ACS testen.

-	Drücken Sie in Visual Studio die Taste F5, um die Anwendung auszuführen.

Wenn Ihre Anwendung mit ACS integriert ist und Sie "Windows Live ID" (Microsoft-Konto) ausgewählt haben, wird nicht die standardmäßige ASP.NET Web Forms-Anwendung geöffnet, sondern Ihr Browser auf die Anmeldeseite für Microsoft-Konten umgeleitet. Wenn Sie sich mit einem gültigen Benutzernamen und Kennwort anmelden, werden Sie zu der Anwendung MvcACS weitergeleitet.

![][6]

Glückwunsch! Sie haben ACS mit Ihrer ASP.NET-Webanwendung integriert. ACS authentifiziert Benutzer nun anhand der Anmeldeinformationen ihres Microsoft-Kontos.

## Anzeigen der von ACS gesendeten Ansprüche

In diesem Abschnitt werden wir die Anwendung ändern, sodass die von ACS gesendeten Ansprüche angezeigt werden.  Das Identitäts- und Zugriffstool hat eine Regelgruppe angelegt, die alle Ansprüche vom IP zu Ihrer Anwendung durchleitet.  Beachten Sie, dass unterschiedliche Anbieter unterschiedliche Ansprüche senden.

1. Öffnen Sie die Datei  *Controllers\HomeController.cs*. Fügen Sie eine **using**-Anweisung für **System.Threading** hinzu:

 	using System.Threading;

1. Fügen Sie in der HomeController-Klasse die  *Claims*-Methode hinzu:

    public ActionResult Claims()
    {
        ViewBag.Message = "Your claims page.";

        ViewBag.ClaimsIdentity = Thread.CurrentPrincipal.Identity;

        return View();
    }

1. Klicken Sie mit der rechten Maustaste auf die Methode  *Claims*, und wählen Sie dann **Ansicht hinzufügen** aus.

![][66]

1. Klicken Sie auf **Hinzufügen**.

1. Ersetzen Sie den Inhalt der Datei  *Views\Home\Claims.cshtml* durch den folgenden Code

        @{
            ViewBag.Title = "Claims";
        }
        <hgroup class="title">
            <h1>@ViewBag.Title.</h1>
            <h2>@ViewBag.Message</h2>
        </hgroup>
        <h3>Values from Identity</h3>
        <table>
            <tr>
                <td>
                    IsAuthenticated: 
                </td>
                <td>
                    @ViewBag.ClaimsIdentity.IsAuthenticated 
                </td>
            </tr>
            <tr>
                <td>
                    Name: 
                </td>        
                <td>
                    @ViewBag.ClaimsIdentity.Name
                </td>        
            </tr>
        </table>
        <h3>Claims from ClaimsIdentity</h3>
        <table>
            <tr>
                <th>
                    Claim Type
                </th>
                <th>
                    Claim Value
                </th>
            </tr>
                @foreach (System.Security.Claims.Claim claim in ViewBag.ClaimsIdentity.Claims ) {
            <tr>
                <td>
                    @claim.Type
                </td>
                <td>
                    @claim.Value
                </td>
            </tr>
        }
        </table>

1. Führen Sie die Anwendung aus, und navigieren Sie zu der Methode  *Claims*:

![][666]

Weitere Informationen zur Verwendung von Ansprüchen in Ihrer Anwendung siehe die [Windows Identity Foundation-Bibliothek](http://msdn.microsoft.com/library/hh377151.aspx).

## Anzeigen der Anwendung im ACS-Verwaltungsportal

Das Identitäts- und Zugriffstool in Visual Studio integriert Ihre Anwendung automatisch mit ACS.

Wenn Sie die Option "Use Azure Access Control" auswählen und dann Ihre Anwendung ausführen, fügt das Identitäts- und Zugriffstool Ihre Anwendung als vertrauende Seite (RP) hinzu, konfiguriert sie für die Verwendung der ausgewählten Identitätsanbieter, erzeugt die standardmäßigen Anspruchstransformationsregeln und wählt diese für die Anwendung aus.

Diese Konfigurationseinstellungen können Sie im ACS-Verwaltungsportal prüfen und ändern. Gehen Sie folgendermaßen vor, um die Änderungen im Portal zu überprüfen.

1.	Melden Sie sich auf dem [Azure-Verwaltungsportal](http://manage.WindowsAzure.com) an.

2.	Klicken Sie auf **Active Directory**. 

	![][8]

3.	Wählen Sie einen Access Control-Namespace aus, und klicken Sie dann auf **Verwalten**. Daraufhin öffnet sich das ACS-Verwaltungsportal.

	![][9]


4.	Klicken Sie auf **Relying party applications**.

	Die neue MvcACS-Anwendung wird in der Liste der Anwendungen der vertrauenden Seite aufgeführt. Unter "Bereich" wird automatisch die Hauptseite der Anwendung angezeigt.

	![][10]


5.	Klicken Sie auf **MvcACS**.

	Die Seite "Edit Relying Party Application" enthält Konfigurationseinstellungen für die MvcACS-Webanwendung. Wenn Sie die Einstellungen auf dieser Seite ändern und speichern, werden diese sofort in der Anwendung wirksam.

	![][11]

6.	Führen Sie auf der Seite einen Bildlauf nach unten aus, damit die übrigen Konfigurationseinstellungen für die MvcACS-Anwendung, also unter anderem die Identitätsanbieter und die Anspruchstransformationsregeln, eingeblendet werden.

	![][12]

Im nächsten Abschnitt werden wir mit den Funktionen des ACS-Verwaltungsportals eine Änderung in der Webanwendung vornehmen -- nur, um einmal zu zeigen, wie einfach das ist.

## Hinzufügen eines Identitätsanbieters

Auf dem ACS-Verwaltungsportal ändern wir nun die Authentifizierung der MvcACS-Anwendung. In diesem Beispiel werden wir Google als Identitätsanbieter für MvcACS hinzuzufügen.

1.	Klicken Sie auf **Identitätsanbieter** (im Navigationsmenü) und dann auf **Hinzufügen**.

	![][13]

2.	Klicken Sie auf **Google** und dann auf **Weiter**. Das Kontrollkästchen der MvcACS-App ist standardmäßig aktiviert. 

	![][14]

3. Klicken Sie auf Speichern. 

	![][15]


Das war's! Wenn Sie zu Visual Studio zurückgehen, das Projekt für die MvcACS-App öffnen und auf **Identität und Zugriff** klicken, listet das Tools sowohl Windows Live ID als auch Google als Identitätsanbieter auf.  

![][16]

Wenn Sie dann die Anwendung ausführen, sehen Sie, was sich verändert hat. Wenn eine Anwendung mehr als einen Identitätsanbieter unterstützt, wird der Browser des Benutzers zuerst an eine von ACS gehostete Seite geleitet, die den Benutzer dann auffordert, einen Identitätsanbieter auszuwählen. 

![][7]

Sobald der Benutzer einen Identitätsanbieter ausgewählt hat, geht der Browser auf die Anmeldeseite dieses Anbieters.

## Nächste Schritte

Sie haben nun eine Webanwendung erstellt, die mit ACS integriert ist. Aber das ist erst der Anfang. Dieses Szenario können Sie noch weiter ausführen.
 
Sie können beispielsweise weitere Identitätsanbieter für diese vertrauende Seite hinzufügen oder Benutzern, die in Unternehmensverzeichnissen wie beispielsweise Active Directory-Domänendiensten registriert sind, die Anmeldung bei der Webanwendung gestatten.

Außerdem können Sie mit zusätzlichen Regeln in Ihrem Namespace festlegen, welche Ansprüche an eine Anwendung gesendet und in der Geschäftslogik der Anwendung verarbeitet werden.

Wenn Sie die ACS-Funktionalität genauer erforschen und mit anderen Szenarien experimentieren möchten, finden Sie weitere Informationen unter [Access Control Service 2.0].



  [Was ist der ACS?]: #what-is
  [Konzepte]: #concepts
  [Voraussetzungen]: #pre
  [Erstellen einer ASP.NET MVC-Anwendung]: #create-web-app
  [Erstellen eines Access Control-Namespace]: #create-namespace
  [Integrieren der Webanwendung mit ACS]: #Identity-Access
  [Testen der Integration mit ACS]: #Test-ACS
  [Anzeigen der Anwendung im ACS-Verwaltungsportal]: acs-portal
  [Hinzufügen eines Identitätsanbieters]: #add-IP
  [Nächste Schritte]: #whats-next
  [vcsb]: #bkmk_viewClaims
  [vpp]: #bkmk_VP

  [Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
  [Identitäts- und Zugriffstool]: http://go.microsoft.com/fwlink/?LinkID=245849
  [Azure-Verwaltungsportal]: http://manage.WindowsAzure.com

  [0]: ./media/active-directory-dotnet-how-to-use-access-control/acs-01.png
  [1]: ./media/active-directory-dotnet-how-to-use-access-control/acsCreateNamespace.png
  [2]: ./media/active-directory-dotnet-how-to-use-access-control/acsQuickCreate.png
  [3]: ./media/active-directory-dotnet-how-to-use-access-control/rzMvc.png
  [4]: ./media/active-directory-dotnet-how-to-use-access-control/rzIA.png
[44]: ./media/active-directory-dotnet-how-to-use-access-control/rzPT.png
 [444]: ./media/active-directory-dotnet-how-to-use-access-control/rzC.png
  [5]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndAccess1.png
  [6]: ./media/active-directory-dotnet-how-to-use-access-control/acsMSFTAcct.png
  [66]: ./media/active-directory-dotnet-how-to-use-access-control/rzAv.png
  [666]: ./media/active-directory-dotnet-how-to-use-access-control/rzCl.png
  [7]: ./media/active-directory-dotnet-how-to-use-access-control/acsSignIn.png
  [8]: ./media/active-directory-dotnet-how-to-use-access-control/acsClickManage.png
  [9]: ./media/active-directory-dotnet-how-to-use-access-control/acsACSPortal.png
  [10]: ./media/active-directory-dotnet-how-to-use-access-control/acsRPPage.png
  [11]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP.png
  [12]: ./media/active-directory-dotnet-how-to-use-access-control/acsEdit-RP2.png
  [13]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Idp.png
  [14]: ./media/active-directory-dotnet-how-to-use-access-control/acsAdd-Google.png
  [15]: ./media/active-directory-dotnet-how-to-use-access-control/acsSave-Google.png
  [16]: ./media/active-directory-dotnet-how-to-use-access-control/acsIdAndA-after.png
  [17]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace.png
  [18]: ./media/active-directory-dotnet-how-to-use-access-control/acsManagementService.png
  [19]: ./media/active-directory-dotnet-how-to-use-access-control/acsShowKey.png
  [20]: ./media/active-directory-dotnet-how-to-use-access-control/acsConfigAcsNamespace2.png

<!--HONumber=47-->
