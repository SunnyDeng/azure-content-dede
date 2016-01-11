<properties 
	pageTitle="Konfigurieren von Übermittlungsrichtlinien für Assets mit .NET SDK" 
	description="In diesem Thema wird die Konfiguration verschiedener Übermittlungsrichtlinien für Medienobjekte mit Azure Media Services .NET SDK erläutert." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako,Mingfeiy" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/17/2015"  
	ms.author="juliako"/>

#Konfigurieren von Übermittlungsrichtlinien für Medienobjekte mit .NET SDK
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../../includes/media-services-selector-asset-delivery-policy.md)]

##Übersicht

Wenn Sie verschlüsselte Medienobjekte übermitteln möchten, ist einer der Schritte im Workflow zur Inhaltsübermittlung in Media Services das Konfigurieren von Übermittlungsrichtlinien. Anhand der Übermittlungsrichtlinie für Medienobjekte kann Media Services ermitteln, wie das Medienobjekt übermittelt werden soll, also mit welchem Streamingprotokoll das Medienobjekt dynamisch verpackt werden soll (z. B. MPEG-DASH, HLS, Smooth Streaming oder alle) und ob und wie das Medienobjekt ggf. dynamisch verschlüsselt werden soll (Umschlag- oder allgemeine Verschlüsselung).

In diesem Thema wird erläutert, warum und wie Übermittlungsrichtlinien für Medienobjekte erstellt und konfiguriert werden.

>[AZURE.NOTE]Zur Verwendung der dynamischen Paketerstellung und Verschlüsselung müssen Sie über mindestens eine Skalierungseinheit (auch als Streamingeinheit bezeichnet) verfügen. Weitere Informationen finden Sie unter [Skalieren eines Mediendiensts](media-services-manage-origins.md#scale_streaming_endpoints).
>
>Darüber hinaus muss Ihr Medienobjekt einen MP4-Satz bzw. Smooth Streaming-Dateien mit adaptiver Bitrate enthalten.

Sie können verschiedene Richtlinien auf dasselbe Medienobjekt anwenden. Sie könnten z. B. eine PlayReady-Verschlüsselung auf Smooth Streaming und AES-Umschlagverschlüsselung auf MPEG-DASH und HLS anwenden. Alle Protokolle, die nicht in einer Übermittlungsrichtlinie definiert sind (wenn Sie z. B. eine einzelne Richtlinie hinzufügen, die nur HLS als Protokoll angibt), werden vom Streaming ausgeschlossen. Die einzige Ausnahme besteht darin, wenn Sie überhaupt keine Übermittlungsrichtlinie für Medienobjekte definiert haben. In diesem Fall sind alle Protokolle ohne Verschlüsselung zulässig.

Wenn Sie ein speicherverschlüsseltes Medienobjekt übermitteln möchten, müssen Sie die Übermittlungsrichtlinie für Medienobjekte konfigurieren. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie gestreamt. Wenn Sie ein Medienobjekt für die Übermittlung beispielsweise mit einem Schlüssel für die AES (Advanced Encryption Standard)-Umschlagverschlüsselung verschlüsseln möchten, legen Sie den Richtlinientyp auf **DynamicEnvelopeEncryption** fest. Um die Speicherverschlüsselung zu entfernen und das Medienobjekt unverschlüsselt zu streamen, legen Sie den Richtlinientyp auf **NoDynamicEncryption** fest. In den folgenden Beispielen wird die Konfiguration dieser Richtlinientypen veranschaulicht.

Je nachdem, wie Sie die Übermittlungsrichtlinie für Medienobjekte konfigurieren, können Sie die folgenden Streamingprotokolle dynamisch verpacken, dynamisch verschlüsseln und streamen: Smooth Streaming-, HLS-, MPEG DASH- und HDS-Streams.

Die folgende Liste enthält die Formate, die Sie zum Streamen von Smooth, HLS, DASH und HDS verwenden.

Smooth Streaming:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Anweisungen zum Veröffentlichen eines Medienobjekts und Erstellen einer Streaming-URL finden Sie unter [Erstellen einer Streaming-URL](media-services-deliver-streaming-content.md).

##Überlegungen

- Sie können eine mit einem Asset verknüpfte AssetDeliveryPolicy nicht löschen, solange ein OnDemand-Locator (Streaminglocator) für dieses Asset vorhanden ist. Es wird empfohlen, die Richtlinie aus dem Asset zu entfernen, bevor Sie die Richtlinie löschen.
- Ein Streaminglocator kann nicht auf einem speicherverschlüsselten Asset erstellt werden, wenn keine Übermittlungsrichtlinie für das Asset festgelegt ist. Wenn das Asset nicht speicherverschlüsselt ist, lässt Sie das System einen Locator erstellen und das Asset in Klartext ohne Übermittlungsrichtlinie streamen.
- Sie haben die Möglichkeit, mehrere Übermittlungsrichtlinien für ein einzelnes Asset zu verwenden, aber Sie dürfen nur eine Möglichkeit zum Verarbeiten eines bestimmten AssetDeliveryProtocol angeben. Wenn Sie also versuchen, zwei Übermittlungsrichtlinien zu verknüpfen, die das AssetDeliveryProtocol.SmoothStreaming-Protokoll angeben, führt dies zu einem Fehler, da das System nicht weiß, welche Richtlinie angewendet werden soll, wenn ein Client eine Smooth Streaming-Anforderung sendet.  
- Wenn Sie über ein Asset mit einem vorhandenen Streaminglocator verfügen, können Sie keine neue Richtlinie mit dem Asset verknüpfen (Sie können entweder eine vorhandene Richtlinienverknüpfung des Assets aufheben oder eine Übermittlungsrichtlinie aktualisieren, die dem Asset zugeordnet ist). Sie müssen zuerst den Streaminglocator entfernen, die Richtlinien anpassen und dann den Streaminglocator neu erstellen. Sie können die gleiche Locator-ID verwenden, wenn Sie den Streaminglocator neu erstellen. Allerdings sollten Sie sicherstellen, dass dadurch keine Probleme für Clients auftreten, da Inhalte nach Ursprung oder einem nachgelagerten CDN zwischengespeichert werden können.  


##Löschen einer Übermittlungsrichtlinie für Medienobjekte 

Mit der folgenden **ConfigureClearAssetDeliveryPolicy**-Methode wird angegeben, keine dynamische Verschlüsselung anzuwenden und den Stream unter Verwendung eines der folgenden Protokolle zu übermitteln: MPEG DASH, HLS und Smooth Streaming. Möglicherweise möchten diese Richtlinie auf Ihre im Speicher verschlüsselten Medienobjekte anwenden.
  
Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }

##DynamicCommonEncryption-Übermittlungsrichtlinie für Medienobjekte 


Durch die folgende **CreateAssetDeliveryPolicy**-Methode wird **AssetDeliveryPolicy** erstellt. Diese Richtlinie ist für die Anwendung der dynamischen allgemeinen Verschlüsselung (**DynamicCommonEncryption**) auf ein Smooth Streaming-Protokoll konfiguriert (andere Protokolle werden vom Streaming ausgeschlossen). Die Methode akzeptiert zwei Parameter: **Asset** (das Medienobjekt, auf das die Übermittlungsrichtlinie angewendet werden soll) und **IContentKey** (der Inhaltsschlüssel des **CommonEncryption**-Typs. Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](media-services-dotnet-create-contentkey.md#common_contentkey)).

Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.SmoothStreaming,
            assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " +
            assetDeliveryPolicy.AssetDeliveryPolicyType);
    }

Azure Media Services ermöglicht es Ihnen ebenfalls, Widevine-Verschlüsselung hinzuzufügen. Im folgenden Beispiel wird gezeigt, wie sowohl PlayReady als auch Widevine zur Übermittlungsrichtlinie für Medienobjekte (AssetDeliveryPolicy) hinzugefügt wird.


    static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.PlayReadyLicense);
        Uri widevineURl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.Widevine);

        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
        {
            {AssetDeliveryPolicyConfigurationKey.PlayReadyLicenseAcquisitionUrl, acquisitionUrl.ToString()},
            {AssetDeliveryPolicyConfigurationKey.WidevineLicenseAcquisitionUrl, widevineURl.ToString()},

        };

        var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
                "AssetDeliveryPolicy",
            AssetDeliveryPolicyType.DynamicCommonEncryption,
            AssetDeliveryProtocol.Dash,
            assetDeliveryPolicyConfiguration);


        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

    }

>[AZURE.NOTE]Wenn Sie mit Widevine verschlüsseln, können Sie nur über DASH übermitteln. Geben Sie DASH im Übermittlungsprotokoll für Medienobjekte an.


##DynamicEnvelopeEncryption-Übermittlungsrichtlinie für Medienobjekte 

Durch die folgende **CreateAssetDeliveryPolicy**-Methode wird **AssetDeliveryPolicy** erstellt. Diese Richtlinie ist für die Anwendung der dynamischen Umschlagverschlüsselung (**DynamicEnvelopeEncryption**) auf HLS- und DASH-Protokolle konfiguriert (andere Protokolle werden vom Streaming ausgeschlossen). Die Methode akzeptiert zwei Parameter: **Asset** (das Medienobjekt, auf das die Übermittlungsrichtlinie angewendet werden soll) und **IContentKey** (der Inhaltsschlüssel des **EnvelopeEncryption**-Typs. Weitere Informationen finden Sie unter [Erstellen eines Inhaltsschlüssels](media-services-dotnet-create-contentkey.md#envelope_contentkey)).


Im Abschnitt [Beim Definieren von AssetDeliveryPolicy verwendete Typen](#types) wird erläutert, welche Werte Sie beim Erstellen von AssetDeliveryPolicy angeben

    private static void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
    {
        
        //  Get the Key Delivery Base Url by removing the Query parameter.  The Dynamic Encryption service will
        //  automatically add the correct key identifier to the url when it generates the Envelope encrypted content
        //  manifest.  Omitting the IV will also cause the Dynamice Encryption service to generate a deterministic
        //  IV for the content automatically.  By using the EnvelopeBaseKeyAcquisitionUrl and omitting the IV, this
        //  allows the AssetDelivery policy to be reused by more than one asset.
        //
        Uri keyAcquisitionUri = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.BaselineHttp);
        UriBuilder uriBuilder = new UriBuilder(keyAcquisitionUri);
        uriBuilder.Query = String.Empty;
        keyAcquisitionUri = uriBuilder.Uri;

        // The following policy configuration specifies: 
        //   key url that will have KID=<Guid> appended to the envelope and
        //   the Initialization Vector (IV) to use for the envelope encryption.
        Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
            new Dictionary<AssetDeliveryPolicyConfigurationKey, string> 
        {
            {AssetDeliveryPolicyConfigurationKey.EnvelopeBaseKeyAcquisitionUrl, keyAcquisitionUri.ToString()},
        };

        IAssetDeliveryPolicy assetDeliveryPolicy =
            _context.AssetDeliveryPolicies.Create(
                        "AssetDeliveryPolicy",
                        AssetDeliveryPolicyType.DynamicEnvelopeEncryption,
                        AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.Dash,
                        assetDeliveryPolicyConfiguration);

        // Add AssetDelivery Policy to the asset
        asset.DeliveryPolicies.Add(assetDeliveryPolicy);

        Console.WriteLine();
        Console.WriteLine("Adding Asset Delivery Policy: " + assetDeliveryPolicy.AssetDeliveryPolicyType);
    }


##<a id="types"></a>Beim Definieren von AssetDeliveryPolicy verwendete Typen

###<a id="AssetDeliveryProtocol"></a>AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

###<a id="AssetDeliveryPolicyType"></a>AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

###<a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None = 0,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense = 1,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp = 2,

        /// <summary>
        /// Use Widevine License acquistion protocol
        ///
        </summary>
        Widevine = 3

    }

###<a id="AssetDeliveryPolicyConfigurationKey"></a>AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,

        /// <summary>
        /// Widevine DRM acquisition url
        /// </summary>
        WidevineLicenseAcquisitionUrl
    }

##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_1223_2015-->