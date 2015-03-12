
1. Halten Sie den mobilen Dienst an, wenn dieser aktuell in IIS Express ausgeführt wird. Klicken Sie mit der rechten Maustaste auf das IIS-Express-Taskleistensymbol, und klicken Sie für den mobilen Dienst auf **Beenden**.

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)


2. Führen Sie in einem Eingabeaufforderungsfenster den Befehl **ipconfig** aus, um eine gültige lokale IP-Adresse für Ihre Arbeitsstation zu suchen.

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)


3. Öffnen Sie in Visual Studio die Datei "applicationhost.config" für IIS Express. Diese Datei befindet sich im folgenden Unterverzeichnis Ihres Benutzerprofilverzeichnisses.

        C:\Users\<your profile name>\Documents\IISExpress\config\applicationhost.config

4. Konfigurieren Sie IIS Express so, dass Remoteverbindungsanforderungen für den Dienst möglich sind. Suchen Sie dazu in der Datei "applicationhost.config" das Websiteelement für Ihren mobilen Dienst, und fügen Sie unter Verwendung der weiter oben notierten IP-Adresse ein neues  `binding`-Element für den Port hinzu. Speichern Sie anschließend die Datei "applicationhost.config". 

    Your updated site element should look similar to the following:

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. Öffnen Sie die Windows-Firewall-Konsole, und erstellen Sie eine neue Portregel, sodass Verbindungen mit dem Port hergestellt werden können. Weitere Informationen zum Erstellen einer neuen Windows-Firewall-Portregel finden Sie unter [Hinzufügen einer neuen Windows-Firewall-Portregel].

    >[AZURE.NOTE] Wenn der Test-Computer einer Domäne angehört, werden Firewallausnahmen möglicherweise durch eine Domänenrichtlinie kontrolliert. In diesem Fall müssen Sie sich an Ihren Domänenadministrator wenden, um eine Ausnahme für den Port auf Ihrem Computer zu erhalten.

    Die Konfiguration sollte nun so erfolgt sein, dass Ihr mobiler Dienst mit IIS Express als Host getestet werden kann. 

    >[AZURE.NOTE] Wenn Sie fertig sind, den Dienst lokal zu testen, sollten Sie die Windows-Firewall-Regel, die sie erstellt haben, löschen. 


<!-- URLs. -->
[Hinzufügen einer neuen Windows-Firewall-Portregel]:  http://go.microsoft.com/fwlink/?LinkId=392240
<!--HONumber=42-->
