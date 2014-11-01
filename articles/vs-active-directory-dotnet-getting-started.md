<properties title="Erste Schritte mit Active Directory-Authentifizierung" pageTitle="" metaKeywords="Azure, Getting Started, Active Directory" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

> [AZURE.SELECTOR]
>
> -   [Erste Schritte][Erste Schritte]
> -   [Was ist passiert?][Was ist passiert?]

## Erste Schritte mit Azure Active Directory (.NET-Projekte)

##### Erfordern von Authentifizierung für den Zugriff auf Controller

Alle Controller in Ihrem Projekt wurden mit dem Attribut **Authorize** versehen. Dieses Attribut erfordert, dass der Benutzer vor dem Zugriff auf diese Controller authentifiziert werden muss. Wenn Sie anonymen Zugriff auf diesen Controller erlauben möchten, entfernen Sie dieses Attribut vom Controller. Wenn Sie die Berechtigungen präziser festlegen möchten, wenden Sie das Attribut auf jede Methode an, die Autorisierung erfordert, anstatt es auf die Controllerklasse anzuwenden.

##### Hinzufügen von SignIn-/SignOut-Steuerelementen

Wenn Sie Ihrer Ansicht SignIn-/SignOut-Steuerelemente hinzufügen möchten, können Sie die Teilansicht **\_LoginPartial.cshtml** zum Hinzufügen der Funktion zu einer Ihrer Ansichten verwenden. Das folgende Beispiel zeigt die Funktion, die der Standardansicht **\_Layout.cshtml** hinzugefügt wurde. (Beachten Sie das letzte Element in **div** mit der Klasse **navbar-collapse**):

<pre class="prettyprint">
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset=&quot;utf-8&quot; /&gt; 
        &lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render(&quot;~/Content/css&quot;) 
        @Scripts.Render(&quot;~/bundles/modernizr&quot;) 
    &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class=&quot;navbar navbar-inverse navbar-fixed-top&quot;&gt; 
            &lt;div class=&quot;container&quot;&gt; 
                &lt;div class=&quot;navbar-header&quot;&gt; 
                    &lt;button type=&quot;button&quot; class=&quot;navbar-toggle&quot; data-toggle=&quot;collapse&quot; data-target=&quot;.navbar-collapse&quot;&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                        &lt;span class=&quot;icon-bar&quot;&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink(&quot;Application name&quot;, &quot;Index&quot;, &quot;Home&quot;, new { area = &quot;&quot; }, new { @class = &quot;navbar-brand&quot; }) 
                &lt;/div&gt; 
                &lt;div class=&quot;navbar-collapse collapse&quot;&gt; 
                    &lt;ul class=&quot;nav navbar-nav&quot;&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Home&quot;, &quot;Index&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;About&quot;, &quot;About&quot;, &quot;Home&quot;)&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink(&quot;Contact&quot;, &quot;Contact&quot;, &quot;Home&quot;)&lt;/li&gt; 
                    &lt;/ul&gt; 
                    @Html.Partial(&quot;_LoginPartial&quot;) 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class=&quot;container body-content&quot;&gt; 
            @RenderBody() 
            &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;© @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render(&quot;~/bundles/jquery&quot;) 
        @Scripts.Render(&quot;~/bundles/bootstrap&quot;) 
        @RenderSection(&quot;scripts&quot;, required: false) 
    &lt;/body&gt; 
    &lt;/html&gt;
</pre>

[Weitere Informationen zu Azure Active Directory][Weitere Informationen zu Azure Active Directory]

  [Erste Schritte]: /documentation/articles/vs-active-directory-dotnet-getting-started/
  [Was ist passiert?]: /documentation/articles/vs-active-directory-dotnet-what-happened/
  [Weitere Informationen zu Azure Active Directory]: http://azure.microsoft.com/services/active-directory/
