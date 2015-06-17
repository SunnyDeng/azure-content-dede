<properties
	pageTitle="Web Apps – Übersicht"
	description="Weitere Informationen zu App Service-Web-Apps"
	services="app-service\web"
	documentationCenter=""
	authors="jaime-espinosa"
	manager="wpickett"
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="jaime.espinosa"/>


#Web-Apps – Übersicht

[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) ist eine vollständig verwaltete Plattform für professionelle Entwickler, die einen umfangreichen Satz von Funktionen für Web-, mobile und Integrationsszenarien bereitstellt. Sie können unternehmenskritische Webanwendungen schnell erstellen, die sich mithilfe von Azure App Service an die Anforderungen Ihres Unternehmens anpassen lassen.

Nutzen Sie die Leistungsstärke von [App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) in den Ihnen vertrauten Programmiersprachen und Frameworks. Stellen Sie Ihre Anwendungen umgehend in der Azure-Cloud bereit, und verbessern Sie kontinuierlich Ihren Code, ohne sich über die Infrastruktur Gedanken machen zu müssen.

![Web-Marketplace:](./media/app-service-web-overview/marketplace.png)

## Mehr als nur Websites##

Moderne Unternehmen interagieren mit ihren Kunden auf immer komplexere Art und Weise. Unternehmen aller Art betrachten ihre Internetpräsenz als kritischen Bestandteil ihres Geschäfts und wichtige Komponente in ihrem Geschäftsplan. Um dieser Bedeutung gerecht zu werden, suchen Unternehmen eine Plattform, die ihnen Flexibilität, Sicherheit und Skalierbarkeit bietet. Darüber hinaus muss es möglich sein, Verknüpfungen mit dem vorhandenen Unternehmenssystem einzurichten, neuen Code schnell bereitzustellen und Instanzen auf der ganzen Welt in Betrieb zu nehmen. Mit Azure App Service-Web-Apps können Organisationen ihre Kunden schnell und kostengünstig begeistern.

## Gründe für Web-Apps ##

Azure App Service-Web-Apps ist eine vollständig verwaltete Plattform, auf der Sie Webanwendungen auf Unternehmensniveau binnen Sekunden erstellen, bereitstellen und skalieren können. Konzentrieren Sie sich auf Ihren Anwendungscode, und überlassen Sie Azure das Skalieren der Infrastruktur und den sicheren Betrieb. Web-Apps ist:

- **Vertraut und schnell** – Nutzen Sie Ihre vorhandenen Kenntnisse, und arbeiten Sie in Ihrer bevorzugten Programmiersprache, Framework- und IDE-Umgebung. Fügen Sie mit nur wenigen Mausklicks Ihren vorhandenen Webanwendungen Versionsverwaltung, Aktualisierungen, einmaliges Anmelden, Identitätsanbieter, isolierten Speicher und Leistungsüberwachung hinzu. Greifen Sie auf einen umfassenden Katalog mit Bausteinen zu, um Ihre Entwicklung zu beschleunigen. Profitieren Sie von unübertroffener Entwicklerproduktivität durch branchenführende Funktionen wie kontinuierliche Integration, Debugging aktiver Websites und der integrierten Entwicklungsumgebung (IDE) von Visual Studio.
- **Für Unternehmen konzipiert** – Web-Apps ist auf das Entwickeln und Hosten sicherer, unternehmenskritischer Anwendungen ausgelegt. Entwickeln Sie in Active Directory integrierte Unternehmensanwendungen, die sich sicher mit lokalen Ressourcen verbinden. Hosten Sie sie anschließend auf einer sicheren Cloudplattform, die ISO-, SOC2- und PCI-konform ist. Darüber hinaus werden gleichzeitig SLAs auf Unternehmensebene erfüllt.
- **Globaler Maßstab** – Web-Apps ist optimiert, um Verfügbarkeit und automatische Skalierung in einer globalen Datencenterinfrastruktur bereitzustellen. Sie können Anwendungen mühelos entsprechend dem Bedarf in beide Richtungen skalieren. Dabei wird hohe Verfügbarkeit innerhalb einer einzelnen und übergreifend zwischen verschiedenen geografischen Regionen geboten. Das Replizieren von Daten und Hosten von Diensten an mehreren Standorten ist schnell und einfach, wodurch eine Expansion in neue Regionen und Länder so einfach wie das Klicken mit der Maus wird.  

## Web-Apps – Konzepte ##

- **Web-Apps-Katalog** – Wählen Sie aus einer stetig wachsenden Liste vorhandener Vorlagen für Webanwendungen aus. Nutzen Sie das Beste aus der OSS-App-Community mit 1-Klick-Installation von Paketen wie z. B. Wordpress, Joomla und Drupal. Finden Sie den richtigen Einstieg in den Anwendungsentwicklungsprozess mit Frameworks wie .NET MVC, Django und CakePHP.
- **Automatische Skalierung** – Web-Apps ermöglicht Ihnen das schnelle Skalieren nach oben oder unten, um eingehende Kundenanforderungen zu bewältigen. Wählen Sie Anzahl und Größe der virtuellen Computer manuell aus, oder richten Sie die automatische Skalierung ein, um Ihre Server basierend auf Last oder Zeitplan zu skalieren.
- **Fortlaufende Integration** – Richten Sie die fortlaufende Integration und Bereitstellungsworkflows mit Visual Studio Online, GitHub, TeamCity, Hudson oder BitBucket ein. Dadurch können Sie bei jedem erfolgreichen Einchecken von Code oder Integrationstest Ihre Webanwendung erstellen, testen und bereitstellen.
- **Bereitstellungsslots** – Implementieren Sie die [Stagingbereitstellung][Slots], um Ihren Code in einer Präproduktionsumgebung zu überprüfen, die mit Ihrer Webanwendung in der Produktion in Azure App Service identisch ist. Wenn Sie mit dem Ergebnis zufrieden sind, geben Sie durch einen Ersetzungsvorgang eine neue Version der Anwendung ohne Ausfallzeiten frei. 
- **Test in der Produktion** – Heben Sie Stagingbereitstellungen auf die nächste Stufe, und führen Sie A/B-Tests aus, um Ihren neuen Code mit einem konfigurierbaren Bruchteil Ihres aktiven Datenverkehrs zu überprüfen. 
- **WebJobs** – Führen Sie Programme oder Skripts auf virtuellen Web-Apps-Computern aus. Führen Sie Aufträge fortlaufend oder gemäß einem Zeitplan aus, und nehmen Sie eine Skalierung zur Ausführung auf mehreren virtuellen Computern vor. Nutzen Sie das Azure [WebJobs SDK][Webjobs] für die Integration in Azure Storage oder Service Bus.

## Erste Schritte ##
Führen Sie zum Einstieg in Web-Apps das Lernprogramm [Erstellen einer ASP.NET-Web-App][create] aus.

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service][appservice].

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

[appservice]: ../app-service/app-service-value-prop-what-is.md
[create]: web-sites-dotnet-get-started.md
[Webjobs]: websites-dotnet-webjobs-sdk-get-started.md
[Slots]: web-sites-staged-publishing.md

 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->