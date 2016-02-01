
<properties 
    pageTitle="Sichern des Zugriffs auf Azure RemoteApp und darüber hinaus | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Zugriff auf Azure RemoteApp mithilfe des bedingten Zugriffs in Azure Active Directory sichern."
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="01/12/2016" 
    ms.author="elizapo" />

# Sichern des Zugriffs auf Azure RemoteApp und darüber hinaus

In diesem Artikel erhalten Sie einen Überblick darüber, wie ein Administrator einen sicheren Zugriffskanal vom Endbenutzer über Azure RemoteApp bis hin zu einer sicheren Ressource einrichten kann, z. B. einer SQL-Datenbank oder einem anderen Anwendungs-Back-End. Ziel ist es, sicherzustellen, dass nur autorisierte Benutzer, die die gewünschten Bedingungen erfüllen, auf Remoteanwendungen zugreifen können und dass das sichere Back-End nur von der kontrollierten Azure RemoteApp-Umgebung und nicht von anderen Stellen aus zugänglich ist.

Es gibt drei Hauptbereiche, die der Administrator in Augenschein nehmen muss:

![Überlegungen zum bedingten Zugriff auf Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalenvironment.png)

Im Folgenden finden Sie weitere Informationen und Antworten auf diese Fragen.

## Wer kann auf die Sammlung zugreifen?
Der Administrator wählt die Benutzer, die auf Remoteanwendungen in der Sammlung zugreifen können. Sie können Geschäfts- oder Schulkonten (früher als „Organisationskonten“ bezeichnet) aus Azure Active Directory (Azure AD) oder Microsoft-Konten (z. B. @outlook.com) verwenden. In den meisten Unternehmensszenarien werden Azure AD-Konten verwendet. Sie ermöglichen den Einsatz von Features für den bedingten Zugriff, die weiter unten erläutert werden, und sind außerdem die einzige Wahl für in Domänen eingebundene Sammlungen. Im weiteren Verlauf dieses Artikels wird davon ausgegangen, dass Sie Azure AD-Konten mit Azure RemoteApp verwenden.

**Wir haben Folgendes erreicht:**

Die Verwendung von Azure AD-Konten zum Steuern des Zugriffs auf Azure RemoteApp bietet zweierlei:

1.	Wir wissen jederzeit, wer auf die von uns veröffentlichten Anwendungen und auf alle Back-Ends zugreifen kann, mit denen diese Anwendungen eine Verbindung herstellen.
2.	Wir steuern das zugrunde liegende Azure AD, sodass wir Benutzerkonten erstellen und löschen, Richtlinien für Kennwörter festlegen, die mehrstufige Authentifizierung verwenden können usw. 

## Wie erfolgt der Zugriff auf die Sammlung? Von wo aus?
Im Allgemeinen möchten Administratoren Richtlinien für den Zugriff auf eine öffentliche Umgebung mit Internetzugriff definieren, z. B. Azure RemoteApp. Sie möchten z. B. sicherstellen, dass Benutzer, die von außerhalb des Unternehmensnetzwerks auf die Umgebung zugreifen, die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verwenden müssen, um Zugriff zu erhalten. Vielleicht sollte dieser Zugriff auch vollständig blockiert werden.

Azure RemoteApp-Administratoren können die in Azure AD Premium bereitstehende Funktionalität nutzen, um für ihre Azure RemoteApp-Umgebung Richtlinien für den bedingten Zugriff festzulegen. Sie können auch anhand von umfangreichen Berichts- und Warnfeatures überwachen, wie auf die Umgebung zugegriffen wird.

### Einrichten des bedingten Zugriffs für Azure RemoteApp
Wir durchlaufen ein Beispielszenario: Der Azure RemoteApp-Administrator möchte den Zugriff auf die Umgebung für Benutzer blockieren, die sich außerhalb des Unternehmensnetzwerks befinden.

>[AZURE.NOTE]Angenommen, Sie haben Azure AD auf den Premium-Tarif aktualisiert und Sie haben mindestens eine Azure RemoteApp-Sammlung erstellt.

1.	Klicken Sie im Azure-Portal auf die Registerkarte **Active Directory**. Klicken Sie dann auf das Verzeichnis, das Sie konfigurieren möchten.

	Denken Sie daran: Der bedingte Zugriff ist eine Eigenschaft des Verzeichnisses und nicht von Azure RemoteApp, daher erfolgt die gesamte Konfiguration auf der Verzeichnisebene. Dies bedeutet auch, dass Sie der Verzeichnisadministrator sein müssen, um diese Änderungen vorzunehmen.

2.	Klicken Sie auf **Anwendungen**, und klicken Sie dann auf **Microsoft Azure RemoteApp**, um den bedingten Zugriff einzurichten. Beachten Sie, dass Sie den bedingten Zugriff für jede SaaS-Anwendung (Software-as-a-Service) in Ihrem Verzeichnis separat einrichten können. ![Einrichten des bedingten Zugriffs für Azure RemoteApp](./media/remoteapp-secureaccess/ra-conditionalaccessscreen.png)
 

3.	Auf der Registerkarte **Konfigurieren** legen Sie **Zugriffsregeln aktivieren** auf EIN fest. ![Aktivieren von Zugriffsregeln für Azure RemoteApp](./media/remoteapp-secureaccess/ra-enableaccessrules.png)
 

4.	Jetzt können Sie verschiedene Regeln konfigurieren und auswählen, auf wen Sie sie anwenden möchten:

	1. Wählen Sie **Zugriff blockieren, wenn nicht gearbeitet wird**, damit Benutzer außerhalb der von Ihnen angegebenen Netzwerkumgebung vollständig am Zugriff auf Azure RemoteApp gehindert werden.
	2. Klicken Sie auf die unten stehende Option, um die IP-Adressbereiche zu definieren, aus denen sich Ihr „vertrauenswürdiges Netzwerk“ zusammensetzt. Alles außerhalb dieser Bereiche wird zurückgewiesen.

5.	Testen Sie Ihre Konfiguration, indem Sie den Azure RemoteApp-Client von einer IP-Adresse außerhalb des angegebenen Bereichs aus starten. Nachdem Sie sich mit Ihren Azure AD-Anmeldeinformationen angemeldet haben, wird eine Meldung wie die folgende angezeigt:

![Verweigerter Zugriff auf Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessdenied.png)
 

### Zukünftige Features für den bedingten Zugriff 
Das Azure Active Directory-Team arbeitet an neuen Funktionen für den bedingten Zugriff. Administratoren werden dann dazu in der Lage sein, neue Typen von Regeln zu erstellen, die über den Netzwerkstandort hinaus gehen. Eine öffentliche Vorschau der neuen Funktionalität sollte in Kürze verfügbar sein.

### Überwachen des Zugriffs auf Azure RemoteApp
Ein sehr nützliches Feature, das zusammen mit dem bedingten Zugriff verwendet werden kann, ist die Berichtfunktionalität in Azure Active Directory Premium. Mithilfe von Berichten können Sie überwachen, wer auf Ihre Umgebung zugreift, und somit verdächtige Aktivitäten erkennen.

Beispielsweise werden Ihnen die Namen der Benutzer angezeigt, die auf Azure RemoteApp zugegriffen haben, und Sie sehen, wie oft und wann ein Zugriff erfolgt ist.

1.	Klicken Sie im Azure-Portal auf **Active Directory** und dann auf Ihr Verzeichnis.

2.	Wechseln Sie zur Registerkarte **Berichte**.

3.	Wählen Sie in der Liste der Berichte unter **Integrierte Anwendungen** den Eintrag **Anwendungsnutzung** aus.

	Sie sehen einige aggregierte Statistiken für Azure RemoteApp. ![Aggregierte Zugriffsstatistiken für Azure RemoteApp](./media/remoteapp-secureaccess/ra-accessstats.png)
 
5.	Klicken Sie auf die Anwendung, um Informationen zu Benutzern anzuzeigen, die auf Azure RemoteApp zugreifen. ![Statistiken zum Benutzerzugriff auf Azure RemoteApp](./media/remoteapp-secureaccess/ra-userstats.png)
 
### Zusammenfassung
Mit Azure Active Directory Premium können Sie Zugriffsregeln für Azure RemoteApp (und andere über Azure AD verfügbare SaaS-Anwendungen) einrichten. Regeln sind zurzeit auf Richtlinien begrenzt, die auf dem Netzwerkstandort basieren, werden jedoch in der Zukunft auf weitere Aspekte der Unternehmensverwaltung erweitert.

Azure AD Premium bietet außerdem Berichts- und Überwachungsfunktionen, die die Kontrollmöglichkeiten des Administrators für die Azure RemoteApp-Umgebung ausweiten.

## Wie kann ich sicherstellen, dass meine sichere Ressource nur von meiner Azure RemoteApp-Umgebung aus zugänglich ist?
In den vorherigen Abschnitten dieses Artikels lag der Schwerpunkt darauf, den Zugriff auf die Azure RemoteApp-Umgebung zu sichern. Dies haben wir umgesetzt, indem wir die Benutzer ausgewählt haben, denen der Zugriff erlaubt ist, und indem wir Zugriffsregeln eingerichtet haben, um die Verwendung des Dienstes durch die Benutzer weitergehend zu steuern.

Ein häufiges Szenario für Azure RemoteApp-Bereitstellungen ist, dass die Remoteanwendungen mit einer Back-End-Ressource, z. B. einer SQL-Datenbank, kommunizieren müssen. Diese Ressource wird entweder lokal (z. B. in einem Unternehmensnetzwerk) oder in der Cloud (z. B. in Azure IaaS) gehostet. Oft möchten Administratoren sicherstellen, dass die Back-End-Ressource nur für Anwendungen zugänglich ist, die über Azure RemoteApp bereitgestellt werden, und nicht beispielsweise für eine Anwendung, die direkt auf dem PC eines Benutzers ausgeführt wird und über das öffentliche Internet zugreift. Azure RemoteApp wird häufig als zentral verwaltete und gesicherte Umgebung betrachtet und somit als der einzige Pfad, über den Benutzer mit der Back-End-Ressource interagieren sollten.

Die Lösung besteht darin, sowohl die Azure RemoteApp-Umgebung als auch die sichere Ressource in demselben Azure Virtual Network (VNET) zu platzieren. Wenn die Ressource sich an einem anderen Standort befindet, können Sie eine Standort-zu-Standort-VPN-Verbindung einrichten, um beispielsweise ein VNet zu erstellen, das sich über das Azure-Datencenter und die lokale Kundenumgebung erstreckt.

Azure RemoteApp unterstützt zwei Arten von Sammlungsbereitstellungen, in denen Sie Ihr eigenes VNET bereitstellen können:

-	Nicht in die Domäne eingebunden: Die Anwendungen verfügen über eine „Sichtverbindung“ mit den anderen Ressourcen im VNET. Diese Option kann z. B. zum Verbinden von Anwendungen mit einer SQL-Datenbank verwendet werden, die die SQL-Authentifizierung verwendet (Anwendungen authentifizieren den Benutzer direkt anhand der Datenbank).

-	In die Domäne eingebunden: Die von Azure RemoteApp verwendeten virtuellen Computer werden mit einem Domänencontroller im VNET verknüpft. Dies ist hilfreich, wenn die Anwendungen sich bei einem Windows-Domänencontroller authentifizieren müssen, um Zugriff auf eine Back-End-Ressource zu erhalten. ![Eine in die Domäne eingebundene Sammlung in Azure RemoteApp](./media/remoteapp-secureaccess/ra-domainjoined.png)
 
### Erstellen einer sichere Verbindung zwischen Azure und meiner lokalen Umgebung
Für die Verbindung der Azure-Umgebung mit der lokalen Umgebung gibt es verschiedene Konfigurationsoptionen. Ein guter Überblick über die Optionen steht hier zur Verfügung.

Mit Azure RemoteApp müssen Sie zuerst Ihr VNet konfigurieren und es dann beim Vorgang der Sammlungserstellung verwenden.

## Die vollständige Lösung
Das folgende Diagramm zeigt die vollständige Lösung, in der wir einen sicheren Zugriffskanal vom Endbenutzer über Azure RemoteApp (ARA) bis hin zur Back-End-Ressource erstellt haben. ![Sichern von Azure RemoteApp](./media/remoteapp-secureaccess/ra-secureoverview.png) In Phase 1 wurden die Benutzer ausgewählt und Zugriffsregeln erstellt, die den Zugriff auf ARA steuern. Im folgenden Beispiel gestatten wir nur den Benutzern Zugriff, die vom Unternehmensnetzwerk aus arbeiten. Benutzer, die dieser Vorgabe nicht entsprechen, können überhaupt nicht auf die ARA-Umgebung zugreifen. In Phase 2 haben wir die Back-End-Ressource nur über die Konfiguration von VNet/VPN verfügbar gemacht, die von uns gesteuert wird. Azure RemoteApp wurde im selben VNet platziert. Als Endergebnis ist die Ressource nur über die ARA-Umgebung zugänglich.

<!---HONumber=AcomDC_0121_2016-->