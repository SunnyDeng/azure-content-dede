<properties
   pageTitle="Problembehandlung bei nicht gestarteten Rollen | Microsoft Azure"
   description="Hier finden Sie einige der häufigsten Gründe, aus denen eine Clouddienstrolle nicht gestartet wird. Es werden auch Lösungen für diese Probleme bereitgestellt."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Allgemeine Schritte zur Problembehandlung bei nicht gestarteten Clouddienstrollen

In diesem Artikel finden Sie Informationen zu allgemeinen Problemen in Zusammenhang mit nicht gestarteten Azure-Clouddienstrollen sowie zur Lösung dieser Probleme.

## Kontaktieren des Azure-Kundensupports

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten.

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).


## Fehlende DLLs oder Abhängigkeiten
Nicht reagierende Rollen oder Rollen, die sich in einer Schleife zwischen den Status **Wird initialisiert**, **Ausgelastet** und **Wird beendet** befinden, können durch fehlende DLLs oder Assemblys verursacht werden.

**Symptom:** Folgende Symptome können auf fehlende DLLs oder Assemblys hinweisen:

- Die Rolleninstanz befindet sich in einer Schleife zwischen **Wird initialisiert** / **Ausgelastet** / **Wird beendet**.
- Die Rolleninstanz befindet sich im Status **Bereit**, wenn Sie jedoch zu Ihrer Webanwendung navigieren, wird die Seite nicht geöffnet.

Lösung: Zur Untersuchung dieser Probleme gibt es drei empfohlene Methoden.

## Diagnostizieren von Problemen mit fehlenden DLLs in einer Webrolle

Sie navigieren zu einer Website, die in einer Webrolle bereitgestellt ist, und der Browser zeigt einen Serverfehler ähnlich dem folgenden an:

![Serverfehler in Anwendung '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## Diagnostizieren von Problemen durch Deaktivieren von benutzerdefinierten Fehlern

Sie können umfassendere Fehlerinformationen anzeigen, indem Sie die in der web.config-Datei für die Webrolle den Modus für benutzerdefinierte Fehler deaktivieren und den Dienst erneut bereitstellen.

So zeigen Sie umfassendere Fehlerinformationen an, ohne Remotedesktop zu verwenden:

1. Öffnen Sie die Projektmappe in Visual Studio.

2. Suchen Sie im **Projektmappen-Explorer** die web.config-Datei, und öffnen Sie sie.

3. Suchen Sie in der web.config-Datei den Abschnitt „system.web“, und fügen Sie folgende Zeile hinzu:

    ```xml
    <customErrors mode="Off" />
    ```

4. Speichern Sie die Datei.

5. Packen Sie den Dienst neu, und stellen Sie ihn erneut bereit.

Nachdem der Dienst neu bereitgestellt wurde, sehen Sie den unten gezeigten Fehler mit dem Namen der fehlenden Assembly oder DLL.

## Diagnostizieren von Problemen durch Remoteanzeige des Fehlers

Sie können Remotedesktop verwenden, um remote auf die Rolle zuzugreifen und vollständige Fehlerinformationen anzuzeigen. Gehen Sie folgendermaßen vor, um Fehler mithilfe von Remotedesktop anzuzeigen:

1. Stellen Sie sicher, dass Azure SDK 1.3 oder höher installiert ist.

2. Wählen Sie während der Bereitstellung der Projektmappe mithilfe von Visual Studio die Option zum Konfigurieren von Remotedesktopverbindungen. Weitere Informationen hierzu finden Sie unter [Verwenden von Remotedesktop mit Azure-Rollen](https://msdn.microsoft.com/library/gg443832.aspx).

3. Wenn die Instanz im klassischen Microsoft Azure-Portal den Status **Bereit** zeigt, klicken Sie auf eine der Rolleninstanzen.

4. Klicken Sie im Menübandbereich **Remotezugriff** auf das Symbol **Verbinden**.

5. Melden Sie sich mit den Anmeldeinformationen, die Sie während der Remotedesktopkonfiguration angegeben haben, beim virtuellen Computer an.

6. Öffnen Sie eine Eingabeaufforderung.

7. Geben Sie `IPconfig` ein.

8. Notieren Sie sich den Wert der IPV4-Adresse.

9. Öffnen Sie Internet Explorer.

10. Geben Sie die Adresse und den Namen der Webanwendung ein. Beispiel: `http://<IPV4 Address>/default.aspx`.

Wenn Sie jetzt zu der Website navigieren, werden ausführlichere Fehlermeldungen zurückgegeben.

* Serverfehler in Anwendung '/'

* Beschreibung: Unbehandelte Ausnahme beim Ausführen der aktuellen Webanforderung. Überprüfen Sie die Stapelüberwachung, um weitere Informationen über diesen Fehler anzuzeigen und festzustellen, wo der Fehler im Code verursacht wurde.

* Ausnahmedetails: System.IO.FIleNotFoundException: Die Datei oder Assembly "Microsoft.WindowsAzure.StorageClient, Version=1.1.0.0, Culture=neutral, PublicKeyToken=31bf856ad364e35" oder eine Abhängigkeit davon wurde nicht gefunden. Die angegebene Datei wurde nicht gefunden.

Beispiel:

![Expliziter Serverfehler in Anwendung '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## Diagnostizieren von Problemen mit dem Serveremulator

Sie können den Microsoft Azure-Serveremulator verwenden, um Probleme mit fehlenden Abhängigkeiten sowie web.config-Fehler zu diagnostizieren und zu beheben.

Bei dieser Diagnosemethode erzielen Sie die besten Ergebnisse, wenn Sie einen physischen oder virtuellen Computer mit einer neuen Windows-Installation verwenden. Um die Azure-Umgebung möglichst optimal zu simulieren, sollten Sie Windows Server 2008 R2 x64 verwenden.

1. Installieren Sie die eigenständige Version des [Azure SDK](https://azure.microsoft.com/downloads/).

2. Erstellen Sie das Clouddienstprojekt auf dem Entwicklungscomputer.

3. Navigieren Sie im Windows Explorer zum Ordner „bin\\debug“ des Clouddienstprojekts.

4. Kopieren Sie den CSX-Ordner und die CSCFG-Datei auf den Computer, den Sie zum Debuggen von Problemen verwenden.

5. Öffnen Sie auf dem neu installierten Computer eine Azure SDK-Eingabeaufforderung, und geben Sie `csrun.exe /devstore:start` ein.

6. Geben Sie an der Eingabeaufforderung `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser` ein.

7. Wenn die Rolle gestartet wird, werden detaillierte Fehlerinformationen in Internet Explorer angezeigt. Für eine eingehendere Problemdiagnose können Sie auch standardmäßige Windows-Tools zur Problembehandlung verwenden.

## Diagnostizieren von Problemen mit IntelliTrace

Für Worker- und Webrollen, die .NET Framework 4 verwenden, können Sie [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx) nutzen, das in [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs) zur Verfügung steht.

Gehen Sie folgendermaßen vor, um den Dienst mit aktiviertem IntelliTrace bereitzustellen:

1. Vergewissern Sie sich, dass Azure SDK 1.3 oder höher installiert ist.

2. Stellen Sie die Projektmappe mithilfe von Visual Studio bereit. Aktivieren Sie bei der Bereitstellung das Kontrollkästchen **IntelliTrace für .NET 4-Rollen aktivieren**.

3. Wenn die Instanz gestartet wurde, öffnen Sie den **Server-Explorer**.

4. Erweitern Sie den Knoten **Azure\\Clouddienste**, und suchen Sie die Bereitstellung.

5. Erweitern Sie die Bereitstellung, bis die Rolleninstanzen angezeigt werden. Klicken Sie mit der rechten Maustaste auf eine der Instanzen.

6. Wählen Sie **IntelliTrace-Protokolle anzeigen** aus. Die **IntelliTrace-Zusammenfassung** wird geöffnet.

7. Suchen Sie in der Zusammenfassung den Abschnitt mit den Ausnahmen. Wenn Ausnahmen vorhanden sind, sind sie als **Ausnahmedaten** gekennzeichnet.

8. Erweitern Sie **Ausnahmedaten**, und suchen Sie nach **System.IO.FileNotFoundException**-Fehlern ähnlich dem folgenden:

![Ausnahmedaten, fehlende Datei oder Assembly](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## Behandeln von Problemen mit fehlenden DLLs und Assemblys

Um Fehler mit fehlenden DLLs und Assemblys zu beheben, gehen Sie folgendermaßen vor:

1. Öffnen Sie die Projektmappe in Visual Studio.

2. Öffnen Sie im **Projektmappen-Explorer** den Ordner **Verweise**.

3. Klicken Sie auf die im Fehler identifizierte Assembly.

4. Suchen Sie im Bereich **Eigenschaften** die Eigenschaft „Lokale Kopie“, und legen Sie den Wert auf **True** fest.

5. Stellen Sie den gehosteten Dienst erneut bereit.

Nachdem Sie sichergestellt haben, dass alle Fehler behoben wurden, können Sie den Dienst ohne aktiviertes Kontrollkästchen **IntelliTrace für .NET 4-Rollen aktivieren** bereitstellen.

## Nächste Schritte

Sehen Sie sich weitere [Artikel zur Problembehandlung](..\?tag=top-support-issue&service=cloud-services) für Clouddienste an.

Erfahren Sie in der [Blogreihe von Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx), wie Sie Probleme bei Clouddienstrollen mithilfe der Computerdiagnosedaten von Azure-PaaS beheben.

<!---HONumber=AcomDC_0128_2016-->