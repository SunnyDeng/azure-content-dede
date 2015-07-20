<properties 
	pageTitle="Erstellen von ContentKeys mit .NET" 
	description="Erfahren Sie, wie Sie Inhaltsschlüssel erstellen, die den sicheren Zugriff auf Medienobjekte ermöglichen." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#Erstellen von ContentKeys mit .NET

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md) und [Media Services: Livestreaming-Workflow](media-services-live-streaming-workflow.md).

Media Services ermöglicht das Erstellen und Übermitteln verschlüsselter Medienobjekte. Ein **ContentKey** ermöglicht den sicheren Zugriff auf Ihre **Medienobjekte**.

Beim Erstellen eines neuen Medienobjekts (z. B. vor dem [Hochladen von Dateien](media-services-dotnet-upload-files.md)) können Sie die folgenden Verschlüsselungsoptionen angeben: **StorageEncrypted**, **CommonEncryptionProtected** oder **EnvelopeEncryptionProtected**.

Wenn Sie Medienobjekte an Ihre Clients übermitteln, können Sie mithilfe einer der beiden folgenden Verschlüsselungen die [dynamische Verschlüsselung von Medienobjekten konfigurieren](media-services-dotnet-configure-asset-delivery-policy.md): **DynamicEnvelopeEncryption** oder **DynamicCommonEncryption**.

Verschlüsselte Medienobjekte müssen **ContentKeys** zugeordnet werden. In diesem Artikel wird beschrieben, wie ein Inhaltsschlüssel erstellt wird.

>[AZURE.NOTE]Beim Erstellen eines neuen **StorageEncrypted**-Medienobjekts mithilfe des Media Services .NET SDKs wird der **ContentKey** automatisch erstellt und mit dem Medienobjekt verknüpft.

##ContentKeyType

Einer der Werte, die Sie beim Erstellen eines Inhaltsschlüssels festlegen müssen, ist der Typ des Inhaltsschlüssels. Wählen Sie unter folgenden Werten aus.

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,
    }

##<a id="envelope_contentkey"></a>Erstellen eines ContentKey vom Typ "Umschlagverschlüsselung"

Im folgenden Codeausschnitt wird ein Inhaltsschlüssel vom Typ „Umschlagverschlüsselung“ erstellt. Anschließend wird der Schlüssel dem angegebenen Medienobjekt zugeordnet.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

Aufruf

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



##<a id="common_contentkey"></a>Erstellen eines ContentKey vom Typ "Allgemeine Verschlüsselung"    

Im folgenden Codeausschnitt wird ein Inhaltsschlüssel vom Typ „Allgemeine Verschlüsselung“ erstellt. Anschließend wird der Schlüssel dem angegebenen Medienobjekt zugeordnet.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int length)
    {
        var returnValue = new byte[length];

        using (var rng =
            new System.Security.Cryptography.RNGCryptoServiceProvider())
        {
            rng.GetBytes(returnValue);
        }

        return returnValue;
    }
Aufruf

	IContentKey key = CreateCommonTypeContentKey(encryptedsset); 

<!---HONumber=July15_HO2-->