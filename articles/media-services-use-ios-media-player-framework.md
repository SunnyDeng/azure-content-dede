<properties urlDisplayName="iOS Media Player Framework" pageTitle="Verwenden des iOS Media Player Framework mit Azure Media Services" metaKeywords="" description="Erfahren Sie, wie Sie die iOS Media Player Framework-Bibliothek von Media Services zum Erstellen umfangreicher, dynamischer Apps verwenden." metaCanonical="" services="media-services" documentationCenter="" title="How to use the Azure Media Services iOS Media Player Framework" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="mobile-ios" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />



# Verwenden des Azure Media Services iOS Media Player Framework

Mit der Azure Media Services iOS Media Player Framework-Bibliothek können Entwickler für iPod, iPhone und iPad reichhaltig ausgestattete, dynamische Clientanwendungen erstellen, die Video- und Audiostreams spontan erzeugen und mischen. Beispielsweise können in Anwendungen, die Sportinhalte zeigen, ohne weiteres an beliebiger Stelle Werbespots eingefügt werden, und Sie können festlegen, wie oft diese Werbespots angezeigt werden, auch wenn der Hauptinhalt zurückgespult wird. Bildungsanwendungen können mit den gleichen Funktionen z. B. Inhalte erstellen, bei denen neben dem Hauptvortrag Randleisten dargestellt werden, bevor der Hauptinhalt fortgesetzt wird.

In der Regel ist es recht kompliziert, eine Anwendung zu erstellen, die Inhaltsdatenströme erzeugen kann, die durch die Interaktion zwischen Anwendung und Benutzer entstehen. Normalerweise müssen Sie den gesamten Datenstrom von Grund auf neu erstellen und vorab auf dem Server speichern. Mit dem iOS Media Player Framework können Sie Clientanwendungen erstellen, die all dies ermöglichen, ohne dass die Anwendungen Einfluss auf den Datenstrom des Hauptinhalts haben. Sie können:

- Inhaltsdatenströme vorab auf dem Clientgerät planen;
- Pre-Roll-Werbespots oder -Inserts planen;
- Post-Roll-Werbespots oder -Inserts planen;
- Mid-Roll-Werbespots oder -Inserts planen und Werbespot-Pods erzeugen;
- steuern, ob die Mid-Roll-Werbespots oder -Inserts jedes Mal wiedergegeben werden, wenn die Inhaltszeitleiste zurückgespult wird, oder ob sie nur einmal wiedergegeben und dann automatisch aus der Zeitleiste entfernt werden;
- Inhalte dynamisch und ereignisgesteuert direkt in die Zeitleiste einfügen; mögliche Ereignisse sind beispielsweise ein Tastendruck oder eine Benachrichtigung der Anwendung durch einen Dienst - ein Nachrichtenprogramm kann z. B. Benachrichtigungen zu aktuellen Neuigkeiten senden, und die Anwendung kann den Hauptinhalt "anhalten", um einen Datenstrom mit aktuellen Neuigkeiten dynamische zu laden. 

Werden diese Funktionen mit den Medienwiedergabemöglichkeiten von iOS-Geräten kombiniert, können in sehr kurzer Zeit mit geringem Ressourceneinsatz reichhaltige multimediale Darbietungen erzeugt werden.

Das SDK enthält die Anwendung SamplePlayer als Beispiel für eine iOS-Anwendung, die viele dieser Funktionen verwendet, um spontan einen Inhaltsdatenstrom zu erstellen und dem Benutzer die Möglichkeit zu geben, ein Insert durch Drücken einer Taste dynamisch auszulösen. In diesem Lernprogramm werden die Hauptkomponenten der Anwendung SamplePlayer dargestellt, und es wird erläutert, wie Sie davon ausgehend Ihre Anwendung erstellen können.

## Erste Schritte mit der Anwendung SamplePlayer
Im Folgenden wird beschrieben, wie Sie die Anwendung erhalten, und es werden die Bereiche der Anwendung vorgestellt, in denen das Framework eingesetzt wird. 

1. Klonen Sie das Repository "git". 

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2. Öffnen Sie das Projekt im Verzeichnis "azure-media-player-framework/src/iOS/HLSClient/": **SamplePlayer.xcodeproj**.

 
3. Hier sehen Sie die Struktur von SamplePlayer:

![HLS Sample Code structure](http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png)

4. Der iPad-Ordner enthält zwei XIB-Dateien: **SeekbarViewController** und **SamplePlayerViewController**. Damit wird das Layout der Benutzeroberfläche der iPad-Anwendung erzeugt. Dementsprechend enthält der iPhone-Ordner zwei XIB-Dateien, mit denen die Suchleiste und die Steuerung definiert werden. 

6. Die Logik der Hauptanwendung befindet sich in **SamplePlayerViewController.m** im Ordner "Shared". Die meisten der unten erläuterten Codeausschnitte sind in dieser Datei enthalten. 

## Das Layout der Benutzeroberfläche
Die Benutzeroberfläche des Players wird mit zwei XIB-Dateien definiert. (In den folgenden Erläuterungen wird das iPad-Layout als Beispiel verwendet; das iPhone-Layout ist jedoch sehr ähnlich, und die Prinzipien sind gleich.)
### SamplePlayerViewController_iPad.xib
![Sample Player Address Bar](http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png)

* Die **Medien-URL** ist die URL, die zum Laden eines Mediendatenstroms verwendet wird. Die Anwendung enthält eine vordefinierte Liste mit Medien-URLs, die Sie mithilfe der URL-Auswahlschaltflächen verwenden können. Sie haben aber auch die Möglichkeit, Ihre eigene URL für HTTP Live Streaming (HLS)-Inhalte einzugeben. Diese Medieninhalte werden als erster Hauptinhalt verwendet. 
**Hinweis: Lassen Sie diesen URL-Eintrag nicht leer.**

* Mit den **URL-Auswahl**-Schaltflächen können Sie in der Liste der Medien-URLs alternative URLs auswählen.

### SeekbarViewController_iPad.xib
![Seek Bar Controller](http://mingfeiy.com/wp-content/uploads/2013/01/controller.png)
* Mit der **Play-Schaltfläche** können Sie die Medienwiedergabe starten und anhalten.

* Die **Suchleiste** zeigt die gesamte Wiedergabezeitleiste. Halten Sie beim Suchen die Maustaste gedrückt, ziehen Sie die Suchtaste auf die gewünschte Position, lassen Sie dann die Maustaste los. 

**Hinweis**: Wenn Sie im Viewer beim Suchen auf einen Werbespot stoßen, wird eine neue Suchleiste mit der Dauer des Werbespots angezeigt. Die Hauptsuchleiste zeigt nur die Dauer des Hauptinhalts an (d. h., ein Werbespot hat in der Hauptsuchleiste eine Dauer von 0).

* Das Steuerelement **Wiedergabezeit** zeigt zwei Zeiten an ("Label:playerTime"), z. B. 00:23/02:10. In diesem Fall wäre 00:23 die aktuelle Wiedergabezeit und 02:10 wäre die Gesamtdauer des Mediums. 

* **Die Schaltflächen "SkipFroward" und "SkipBackward"** funktionieren derzeit noch nicht wie erwartet. In Kürze wird eine aktualisierte Version herausgegeben.

* Wenn Sie während der Wiedergabe des Hauptinhalts die Schaltfläche **Schedule Now** drücken, wird ein Werbespot eingefügt (Sie können die Quell-URL für den Werbespot in der CodeBehind-Datei definieren.). Hinweis: In der aktuellen Version können Sie keinen Werbespot planen, während ein anderer Werbespot wiedergegeben wird. 

### Planen des Hauptinhalts
Geplant wird ein Inhaltsclip von 0 bis 80 Sekunden:

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Erläuterungen zum obigen Beispielcode:

* Das **MediaTime**-Objekt steuert den Videoclip, den Sie als Hauptinhalt planen möchten. Im obigen Beispiel wird der Videoclip mit einer Dauer von 80 Sekunden (von 0 bis 80 Sekunden) geplant.
* **clipBeginMediaTime** stellt die Startzeit für die Wiedergabe eines Videos dar. Bei "**clipBeginMediaTime** = 5" wird dieser Videoclip nach fünf Sekunden im Videoclip gestartet.
* **clipEndMediaTime** stellt die Endzeit für die Wiedergabe eines Videos dar. Bei "**clipEndMediaTime**=100" endet die Wiedergabe des Videos in der 100. Sekunde des Videoclips.
*Anschließend wird die **MediaTime** geplant. Dazu wird Framework aufgefordert, **appendContentClip** auszuführen. Im obigen Beispiel wird die URL für den Hauptinhalt in "[NSURL URLWithString:url]" angegeben, und die Planung des entsprechenden Mediums wird mit **withMedia** festgelegt:
 "[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])".

**Hinweis:** Planen Sie den Hauptinhalt stets, bevor Sie einen Werbespot planen (einschließlich Pre-Roll-Werbespots). 

### Variante: Wenn Sie zwei Clips mit Hauptinhalt wiedergeben, können Sie den zweiten Clip mit dem folgenden Code auch nach dem ersten planen:

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Wenn dieser Code nach dem vorhergehenden Code ausgeführt wird, werden in der Zeitleiste des Hauptinhalts zwei Inhaltsdatenströme geplant. Der erste Inhalt wird auf Grundlage von "URLWithString:url" und der zweite Inhalt wird auf Grundlage von "URLWithString:secondContent" geplant. Der zweite Inhalt beginnt am Punkt 30 Sekunden im Mediendatenstrom und endet am Punkt 80 Sekunden. 

## Planen von Werbespots 
In der aktuellen Version wird nur ein **pauseTimeline=false**-Werbespot unterstützt. Das bedeutet, dass der Player die Wiedergabe nach einem Werbespot an der Stelle fortsetzt, an der der Hauptinhalt verlassen wurde. 

Im Folgenden wird auf einige wichtige Punkte hingewiesen:
<ul><li> Alle **LinearTime.duration** müssen bei der Planung von Werbeeinblendungen gleich 0 sein.</li>
<li> Wenn **clipEndMediaTime** länger als die Dauer der Werbeeinblendung ist, endet die Werbeeinblendung nach deren Abschluss, und es wird keine Ausnahme ausgelöst. Sie werden darauf hingewiesen zu prüfen, ob die normale Dauer des Werbespots innerhalb der Renderingzeit (**clipEndMediaTime**) liegt, damit Sie keine Werbemöglichkeit verpassen.</li> 
<li> Unterstützt werden Werbeeinblendungen vor, während und nach dem eigentlichen Video. Pre-Roll-Werbespots können nur ganz am Anfang jeden Inhalts geplant werden. Es ist z. B. nicht möglich, einen Pre-Roll-Werbespot für den zweiten Inhalt in einem Schnitteditierungsszenario zu planen. </li>
<li> Sticky- und einmalig abgespielte Werbeeinblendungen werden unterstützt und können in Verbindung mit Werbeeinblendungen vor, während und nach dem eigentlichen Video verwendet werden.</li>
<li> Für die Werbeeinblendungen werden die Formate .Mp4 und HLS unterstützt.</li>
</ul>
### Planen von Pre-Roll-, Mid-Roll-, Post-Roll-Werbespots und Werbespot-Pods

####Planen von Pre-Roll-Werbespots

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Das **AdInfo**-Objekt stellt alle Informationen über den Werbespotclip dar:
* **ClipURL** ist die URL für die Clip-Quelle.
* Die **mediaTime**-Eigenschaft gibt an, wie lange eine Werbeeinblendung abgespielt wird. (**clipBeginMediaTime** gibt die Startzeit und **clipEndMediaTime** die Endzeit des Werbespots an.) Im vorhergehenden Codebeispiel planen wir einen Werbespot für 5 Sekunden, beginnend bei 0 bis zur fünften Sekunde der Werbespotdauer.
* Das **Policy**-Objekt wird momentan nicht vom Framework verwendet.
* Sie müssen den **appendTo**-Wert auf -1 setzen, wenn es sich nicht um einen Ad Pod handelt. 
* Der **type**-Wert kann auf "pre-roll", "mid-roll", "post-roll" oder "ad pod" gesetzt werden. Geben Sie bei Pre-Roll- oder Post-Roll-Werbespots den Typ an, da diesen Typen keine Anzeigedauer zugeordnet ist. 

####Planen von Mid-Roll-Werbespots

Wenn Sie "adLinearTime.startTime = 23;" zum vorherigen Codebeispiel hinzufügen, wird der Werbespot bei 23 Sekunden in der Zeitleiste des Hauptinhalts wiedergegeben.

####Planen von Post-Roll-Werbespots

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Der einzige Unterschied gegenüber der Planung des Pre-Roll-Werbespots ist die Zeile "postAdInfo.type = AdType_Postroll;". Mit dem obigen Code wird ein Fünf-Sekunden-Werbespot als Post-Roll-Werbespot geplant. 

#### Planen von Werbespot-Pods
Werbespot-Pods sind Werbeunterbrechungen mit mehreren Werbespots, die nacheinander wiedergegeben werden. Mit dem folgenden Code werden zwei Werbespots in einem Werbespot-Pod geplant. 

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Hier sind einige Dinge zu beachten:
* Für den ersten Clip entspricht **appendTo** dem Wert -1. Beim Aufruf von "[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex]" erhält "adIndex" einen eindeutigen Wert, der das Ende des ersten Clips im Ad Pod angibt. Richten Sie dann beim zweiten Clip im Werbespot-Pod den Anfang des zweiten Werbespots am Ende des ersten aus, indem Sie **appendTo** auf "adpodInfo2.appendTo = adIndex;" festlegen. Damit wird die Endposition des ersten Clips als Punkt definiert, an dem der zweite beginnt. 
* Anschließend müssen Sie als Typ "AdType_Pod" festlegen, um anzugeben, dass es sich um einen Werbespot-Pod handelt. 

### Planen von einmal wiedergegebenen Werbespots oder Sticky Ads
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

Wie das vorherige Codebeispiel zeigt, wird dieser Werbespot nur einmal wiedergegeben, wenn Sie **deleteAfterPlay** auf **YES** festlegen. Wenn Sie **deleteAfterPlay** hingegen auf **NO** festlegen, wird dieser Werbespot immer weiter wiedergegeben. Ein solcher Werbespot wird als "Sticky Ad" bezeichnet.
### Weitere Informationen finden Sie im [Azure Media Player Framework-Wiki](https://github.com/WindowsAzure/azure-media-player-framework/wiki) .

<!--HONumber=35.1-->
