
<properties 
    pageTitle="App-Anforderungen für RemoteApp"
    description="Erfahren Sie die Anforderungen an Apps, die Sie in RemoteApp verwenden möchten." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/11/2015" 
    ms.author="elizapo" />



# App-Anforderungen
RemoteApp unterstützt Streaminganwendungen für 32-Bit- oder 64-Bit-Windows auf Windows Server 2012 R2-Installationen. Die meisten vorhandenen Anwendungen für 32-Bit- oder 64-Bit-Windows können in RemoteApp-Umgebungen (Remote Desktop Services, früher als Terminal Services bezeichnet) ohne Änderungen ausgeführt werden. Es gibt jedoch einen Unterschied zwischen "ausführen" und "gut ausführen" - einige Anwendungen funktionieren ordnungsgemäß und bieten eine gute Leistungen, während dies bei anderen nicht gilt. Die folgenden Informationen dienen als Hilfestellung bei der Entwicklung von Anwendungen in einer Remote Desktop Services-Umgebung und beim Testen zum Sicherstellen der Kompatibilität.

Tipp: Wir arbeiten daran, einige funktionierende Beispiel-Apps für Sie zu erstellen. Bis zum Ende des Monats werden neue Themen angeboten, in denen die Verwendung von Microsoft Access, QuickBooks und App-V in RemoteApp besprochen wird.

## Anforderungen
Die folgenden drei Anforderungen tragen dazu bei, dass Ihre Anwendung in RemoteApp gut ausgeführt werden kann: 

1.	Anwendungen, die alle [Zertifizierungsanforderungen für Windows-Desktop-Apps](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) erfüllen und die [Programmierrichtlinien für Remote Desktop Services](https://msdn.microsoft.com/library/aa383490.aspx) einhalten, sind vollständig mit RemoteApp kompatibel. 
2.	Anwendungen sollten niemals Daten lokal im Image oder in RemoteApp-Instanzen speichern, die verloren gehen können.  Nach dem Erstellen einer RemoteApp-Sammlung werden die Instanzen geklont. Sie weisen dann keinen Zustand auf und sollten ausschließlich Anwendungen enthalten. Speichern Sie Daten in externen Quellen oder im Benutzerprofil. 
3.	Benutzerdefinierte Images sollten niemals Daten enthalten, die verloren gehen können.  

## Testen Ihrer Apps
Gehen Sie folgendermaßen vor, um Anwendungen zu testen:

1.	Installieren Sie Windows Server 2012 R2 und Ihre Anwendung.
2.	Aktivieren Sie Remote Desktop.
3.	Erstellen Sie zwei Benutzerkonten, BenutzerA und BenutzerB, und fügen Sie beide Konten der Remote Desktop-Sicherheitsgruppe hinzu. 
4.	Überprüfen Sie die Kompatibilität mit mehreren Sitzungen, indem Sie zwei simultane RDS-Sitzungen auf dem Computer einrichten, während Sie die Anwendung starten.
5.	Überprüfen Sie das App-Verhalten.

## Anwendungs-Entwicklungsrichtlinien
Gehen Sie nach den folgenden Richtlinien vor, um Anwendungen für RemoteApp zu entwickeln. 

### Mehrere Benutzer
 
- Nach dem Installieren einer [Anwendung für einen einzelnen Benutzer](https://msdn.microsoft.com/library/aa380661.aspx) können in einer Umgebung mit mehreren Benutzern Probleme auftreten. 
- Wendungen sollten [benutzerspezifische Informationen an benutzerspezifischen Speicherorten speichern](https://msdn.microsoft.com/library/aa383452.aspx), die von den globalen, auf alle Benutzer bezogenen Informationen getrennt sind. 
- RemoteApp verwendet mehrere [Namespaces für Kernelobjekte](https://msdn.microsoft.com/library/aa382954.aspx); ein globaler Namespace wird in erster Linie von Diensten in Client-/Server-Anwendungen verwendet. 
- Es darf nicht davon ausgegangen werden, dass der Computername oder die dem Computer zugewiesene [IP-Adresse](https://msdn.microsoft.com/library/aa382942.aspx) mit einem einzelnen Benutzer zusammenhängen, da mehrere Benutzer gleichzeitig an einem Remote Desktop Session Host-Server (RD Session Host) angemeldet sein können. 

### Leistung
- Deaktivieren Sie [Grafikeffekte](https://msdn.microsoft.com/library/aa380822.aspx), bevor Sie Ihre App in RemoteApp einfügen.
- Um die CPU-Verfügbarkeit für alle Benutzer zu maximieren, deaktivieren Sie [Hintergrundaufgaben](https://msdn.microsoft.com/library/aa380665.aspx), oder erstellen Sie effiziente Hintergrundaufgaben, die effektiv mit Ressourcen umgehen. 
- Sie sollten die [Threadnutzung](https://msdn.microsoft.com/library/aa383520.aspx) von Anwendungen für Umgebungen mit mehreren Benutzern und mehreren Prozessoren abstimmen und ausbalancieren.
- Um die Leistung zu optimieren, empfiehlt es sich, der Anwendung eine [Erkennung](https://msdn.microsoft.com/library/aa380798.aspx) hinzuzufügen, ob sie in einer Clientsitzung ausgeführt wird. 

<!--HONumber=52-->