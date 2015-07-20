<properties 
	pageTitle="Configurar Política de Autorização de Chave de Conteúdo usando o Portal" 
	description="Saiba como configurar uma política de autorização para uma chave de conteúdo." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>



#Configurar a Política de Autorização de Chave de Conteúdo 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../../includes/media-services-selector-content-key-auth-policy.md)]

Este artigo faz parte das séries de [Vídeo de serviços de mídia no fluxo de trabalho sob demanda](media-services-video-on-demand-workflow.md) e [Fluxo de trabalho da transmissão ao vivo dos serviços de mídia](media-services-live-streaming-workflow.md).


##Visão geral

Os serviços de mídia do Microsoft Azure permitem distribuir o conteúdo criptografado com criptografia AES (padrão avançado) (usando chaves de criptografia de 128 bits) e PlayReady DRM. Os serviços de mídia também fornecem um **serviço de entrega de Chave/Licença** no qual os clientes podem obter uma chave ou uma licença para reproduzir o conteúdo criptografado.

Este tópico mostra como usar o **Portal de gerenciamento do Azure** para configurar a política de autorização de chave de conteúdo. A chave pode ser usada posteriormente para criptografar dinamicamente o conteúdo. Observe que, no momento, você pode criptografar o seguintes formatos de streaming: HLS, MPEG DASH, e Smooth Streaming. Você não pode criptografar o formato de streaming HDSou fazer o download progressivo.
 
Quando um player solicita um fluxo que é definido para ser criptografado dinamicamente, os serviços de mídia usam a chave configurada para criptografar dinamicamente seu conteúdo usando a criptografia AES ou PlayReady. Para descriptografar o fluxo, o player solicitará a chave do serviço de distribuição de chaves. Para decidir se o usuário está autorizado para obter a chave ou não, o serviço avalia as políticas de autorização que você especificou para a chave.


Se você planeja ter várias chaves de conteúdo ou deseja especificar uma URL de **serviço de entrega de Chave/Licença** diferente do serviço de distribuição de chaves dos serviços de mídia, use as APIs SDK ou REST do .NET dos serviços de mídia.

[Configurar política de autorização de chave de conteúdo usando o SDK do .NET dos Serviços de Mídia](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurar política de autorização de chave de conteúdo usando a API REST dos Serviços de Mídia](media-services-rest-configure-content-key-auth-policy.md)

###Algumas considerações se aplicam:

- Para poder usar o empacotamento dinâmico e a criptografia dinâmica, verifique se você tem pelo menos uma unidade de streaming reservada. Para obter mais informações, consulte [Como dimensionar um serviço de mídia](media-services-manage-origins.md#scale_streaming_endpoints). 
- O ativo deve conter um conjunto de MP4s de taxa de bits adaptável ou arquivos de Smooth Streaming de taxa de bits adaptável. Para obter mais informações, consulte [Codificar um ativo](media-services-encode-asset.md).  
- O serviço de entrega de chave armazena em cache ContentKeyAuthorizationPolicy e seus objetos relacionados (opções e restrições da política) por 15 minutos. Se você criar um ContentKeyAuthorizationPolicy e optar por usar uma restrição "Token", testá-lo e, em seguida, atualizar a política de restrição "Aberta", levará aproximadamente 15 minutos antes da política alternar para a versão "Aberta" da política.


##Como: configurar a política de autorização da chave

Para configurar a política de autorização da chave, selecione a página **PROTEÇÃO DE CONTEÚDO**.
	
Os serviços de mídia oferecem suporte a várias maneiras de autenticar os usuários que fazem solicitações de chave. A política de autorização de chave de conteúdo pode ter restrições de autorização **abertas**, de **token**, ou de **IP** (**IP** pode ser configurado com REST ou SDK do .NET).

###Restrição aberta

A restrição **aberta** significa que o sistema fornecerá a chave para qualquer pessoa que fizer uma solicitação de chave. Essa restrição pode ser útil para fins de teste.

![OpenPolicy][open_policy]

###Restrição de token

Para escolher a política de token restrito, pressione o botão **TOKEN**.

A política restrita do **token** deve ser acompanhada por um token emitido por um **Secure Token Service** (STS). Os Serviços de Mídia dão suporte a tokens no formato **SWT** ([Simple Web Tokens](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) e no formato JWT (**JSON Web Token**). Para obter informações, consulte [Autenticação do token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Os serviços de mídia não fornecem **Secure Token Services**. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos serviços de mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem aos configurados para a chave de conteúdo. Para obter mais informações, consulte [Uso do Azure ACS para emitir tokens](http://mingfeiy.com/acs-with-key-services).

Ao configurar a política restrita do **TOKEN**, você deve definir valores para **chave de verificação**, **emissor** e **audiência**. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

###PlayReady

Ao proteger o conteúdo com **PlayReady**, uma das coisas que você precisa especificar na sua política de autorização é uma cadeia de caracteres XML que define o modelo de licença do PlayReady. Por padrão, a seguinte política é definida:
		
	<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
	  <LicenseTemplates>
	    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
	      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
	      <LicenseType>Nonpersistent</LicenseType>
	      <PlayRight>
	        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
	      </PlayRight>
	    </PlayReadyLicenseTemplate>
	  </LicenseTemplates>
	</PlayReadyLicenseResponseTemplate>

Você pode clicar no botão **Importar a política XML** e fornecer um XML diferente que está em conformidade com o esquema XML definido [aqui](https://msdn.microsoft.com/library/azure/dn783459.aspx).

##Próximas etapas
Agora que você configurou a política de autorização da chave de conteúdo, vá para o tópico [Como: Usar o Portal de Gerenciamento do Azure para habilitar a criptografia](../media-services-manage-content#encrypt/).


[open_policy]: ./media/media-services-portal-configure-content-key-auth-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png

 

<!---HONumber=July15_HO2-->