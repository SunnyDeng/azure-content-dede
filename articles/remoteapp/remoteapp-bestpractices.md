<properties
    pageTitle="Best Practices für Azure RemoteApp"
    description="Bewährte Methoden zum Konfigurieren und Verwenden von Azure RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2015" 
    ms.author="elizapo" />

# Bewährte Methoden zum Konfigurieren und Verwenden von Azure RemoteApp

Die folgenden Informationen sollen Ihnen bei der Konfiguration und der produktiven Verwendung von Azure RemoteApp behilflich sein.

## Konnektivität


- Verwenden Sie immer die neueste Clientversion. Bei Verwendung älterer Clients können Verbindungsprobleme und andere Beeinträchtigungen auftreten. Durch das Aktivieren automatischer Anwendungsupdates für Ihr Gerät ist sichergestellt, dass immer der neueste Client installiert ist.
- Verwenden Sie immer die stabilste und zuverlässigste Internetverbindung.  
- Verwenden Sie nur unterstützte Proxy-Verbindungen für eine optimale Verbindungsqualität. Der SOCKS-Proxy wird nicht unterstützt.

## Anwendungen


- Speichern und schließen Sie RemoteApp-Anwendungen, wenn Sie die Anwendung nicht mehr benötigen. Eine nicht geschlossene Anwendung kann zu Datenverlusten führen.
- Überprüfen Sie benutzerdefinierte Anwendungen, bevor Sie diese in Azure RemoteApp verwenden. Stellen Sie dabei auch sicher, dass sie auf einer Multisession-Plattform funktionieren, keine unnötigen Ressourcen (Speicher und CPU) belegen und dadurch möglicherweise einen anderen Benutzer derselben Sammlung blockieren. Informationen hierzu finden Sie im Whitepaper [Application Compatibility Best Practices for Remote Desktop Services](http://www.microsoft.com/download/details.aspx?id=18704), das Sie herunterladen können.

## Konfiguration und Verwaltung


- Halten Sie die Vorlagenimages auf dem neuesten Stand, und installieren Sie nach Bedarf Softwareupdates und andere wichtige Hotfixes und Patches. Dadurch ist gewährleistet, dass bei der automatischen Anpassung von Azure RemoteApp an Ihre Kapazität jede Instanz gepatcht wird.  
- Sorgen Sie für eine sichere und zuverlässige Bereitstellung von Active Directory Federation Services (AD FS). Sonst treten bei Clientauthentifizierungen möglicherweise Fehler auf, und die Benutzer können nicht auf Azure RemoteApp zugreifen.
- Achten Sie beim Konfigurieren von Vorlagenimages mit installierten Anwendungen, Rollen oder Features darauf, dass sie keinen Status aufweisen. Sie sollten sich nicht auf eine Instanz der virtuellen Computer in einem RemoteApp-Dienst, der sich in einem permanenten Status befindet, stützen.
	- Speichern Sie alle Benutzerdaten in Benutzerprofilen oder an anderen externen Speicherorten (außerhalb des Diensts), wie z. B. in lokalen Dateifreigaben oder auf OneDrive.
	- Speichern Sie freigegebene Daten an externen Speicherorten (außerhalb des Diensts), wie z. B. in lokalen Dateifreigaben oder auf OneDrive.
	- Konfigurieren Sie systemweite Einstellungen im Vorlagenimage und nicht auf einzelnen virtuellen Computern in einem Dienst.
	- Deaktivieren Sie automatische Softwareupdates für veröffentlichte Anwendungen – wenden Sie Updates stattdessen manuell auf das Vorlagenimage an, und testen Sie sie, bevor Sie sie über die Vorlage bereitstellen.
 

<!---HONumber=August15_HO7-->