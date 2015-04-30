<properties 
    pageTitle="Hochladen benutzerdefinierter Images"
    description="Erfahren Sie, wie Sie ein benutzerdefiniertes Image für RemoteApp hochladen." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="ericor" />



# Hochladen benutzerdefinierter Images

Nachdem Sie Ihr benutzerdefiniertes Vorlagenimage erstellt oder aktualisiert haben, müssen Sie es in Ihre Azure RemoteApp-Imagebibliothek hochladen. Gehen Sie dazu folgendermaßen vor: 


## Vorbereitung

1.      Vergewissern Sie sich, dass Ihr benutzerdefiniertes Image den [Anforderungen an Images](..\remoteapp-imagereqs) und den [Anforderungen an Anwendungen](..\remoteapp-appreqs) entspricht.
2.      Installieren Sie das [Azure PowerShell-Modul](install-configure-powershell.md).

## So laden Sie ein benutzerdefiniertes Image hoch

1.      Öffnen Sie das Azure-Verwaltungsportal, und navigieren Sie zur RemoteApp-Seite.
2.      Klicken Sie unten auf der Registerkarte **Template images** auf **Hochladen**.
4.      Geben Sie einen aussagekräftigen Namen für Ihr Image und einen Speicherort im Speicherkonto an. Achten Sie darauf, dass der Speicherort mit dem Ihrer RemoteApp-Sammlung übereinstimmt (oder an dem Sie planen, sie zu erstellen). 
5.      Laden Sie, wenn Sie dazu aufgefordert werden, das Skript auf Ihren lokalen Computer herunter.
6.      Kopieren Sie die Befehlsparameter im Textfeld in die Zwischenablage.
7.      Öffnen Sie ein Windows PowerShell-Fenster mit erhöhten Rechten.  
8.      Navigieren Sie im Windows PowerShell-Fenster mit erhöhten Rechten zu dem Verzeichnis, in das Sie das Skript heruntergeladen haben.
9.      Fügen Sie den kopierten Befehl ein, und drücken Sie die **Eingabetaste:**

	Der Upload wird gestartet. Die Dauer hängt von verschiedenen Faktoren wie der Netzwerkgeschwindigkeit und der Imagegröße ab.

11.    Wenn der Upload aufgrund einer Netzwerkstörung oder einem ähnlichen Problem nicht abgeschlossen wird, können Sie den Vorgang jederzeit fortsetzen. Zum Fortsetzen eines Uploads führen Sie das Skript erneut mit derselben Befehlszeile aus.

**Warnung:** Nehmen Sie keine Änderungen am Upload-Skript vor. Es werden verschiedene Tests durchgeführt, ob das Image den Anforderungen an Images und Anwendungen entspricht. 

## Häufiger auftretende Probleme

- Vergewissern Sie sich, dass Sie Windows PowerShell, nicht Azure PowerShell verwenden.  Sie müssen das Azure PowerShell-Modul installieren, da bestimmte Module während des Uploads erforderlich sind. 
- Nehmen Sie keine Änderungen am Skript vor, die Überprüfungen sollen Ihnen helfen.
- Wenn die VHD-Datei beim Upload ausgesperrt wird, kopieren oder verschieben Sie sie an den neuen Speicherort, und wiederholen Sie den Upload. Möglicherweise verhindern bestimmte Windows-Prozesse den Upload.  

<!--HONumber=52-->