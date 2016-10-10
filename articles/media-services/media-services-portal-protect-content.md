<properties 
	pageTitle="Configurando as políticas de proteção de conteúdo com o portal do Azure | Microsoft Azure" 
	description="Este artigo demonstra como usar o portal do Azure para configurar as políticas de proteção de conteúdo. O artigo também mostra como habilitar a criptografia dinâmica para seus ativos." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2016"    
	ms.author="juliako"/>

# Configurando as políticas de proteção de conteúdo com o portal do Azure

> [AZURE.NOTE] Para concluir este tutorial, você precisa de uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

## Visão geral

Os Serviços de Mídia do Microsoft Azure (AMS) permitem proteger a mídia do momento em que ela deixa o computador e durante o armazenamento, processamento e entrega. Os Serviços de Mídia permitem entregar o conteúdo criptografado dinamicamente com a criptografia AES (usando chaves de criptografia de 128 bits) e a CENC (criptografia comum) usando o PlayReady e/ou Widevine DRM e Apple FairPlay.

O AMS fornece um serviço para entregar as licenças DRM e as chaves de limpeza AES aos clientes autorizados. O portal do Azure permite que você crie uma **política de autorização de chave/licença** para todos os tipos de criptografias.

Este artigo demonstra como configurar as políticas de proteção de conteúdo com o portal do Azure. O artigo também mostra como aplicar a criptografia dinâmica em seus ativos.

> [AZURE.NOTE]  Se você usou o portal clássico do Azure para criar políticas de proteção, as políticas poderão não aparecer no [portal do Azure](https://portal.azure.com/). No entanto, todas as antigas políticas ainda existirão. Você pode examiná-las usando o SDK do .NET dos Serviços de Mídia do Azure ou a ferramenta [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases) (para ver as políticas, clique com o botão direito no ativo -> Exibir informações (F4) -> clique na guia Chaves de conteúdo -> clique na chave).
> 
> Se você quiser criptografar seu ativo usando as novas políticas, configure-as com o portal do Azure, clique em Salvar e reaplique a criptografia dinâmica.

## Iniciar a configuração da proteção de conteúdo

Para usar o portal para começar a configurar a proteção de conteúdo, global para sua conta AMS, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Configurações** > **Proteção de conteúdo**.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection001.png)
 

## Diretiva de autorização da chave/licença

O AMS oferece várias maneiras de autenticar os usuários que fazem solicitações de licença ou chave. A política de autorização da chave de conteúdo deve ser configurada por você e atendida pelo cliente para que a chave/licença seja entregue ao cliente. A política de autorização de chave de conteúdo pode ter uma ou mais restrições de autorização: **aberta** ou **restrição** de token.

O portal do Azure permite que você crie uma **política de autorização de chave/licença** para todos os tipos de criptografias.

###Aberto 

Restrição aberta significa que o sistema entregará a chave para qualquer pessoa que fizer uma solicitação de chave. Essa restrição pode ser útil para o teste.

### Token

A política restrita do token deve ser acompanhada por um token emitido por um Secure Token Service (STS). Os serviços de mídia oferecem suporte a tokens no formato Simple Web Tokens (SWT) e no formato JSON Web Token (JWT). Os serviços de mídia não fornecem Secure Token Services. Você pode criar um STS personalizado ou usar o Microsoft Azure ACS para emitir tokens. O STS deve ser configurado para criar um token assinado com as a chave especificada e declarações de emissão que você especificou na configuração de restrição do token. O serviço de distribuição de chaves dos Serviços de Mídia retornará a chave de criptografia para o cliente se o token for válido e as declarações no token corresponderem àquelas configuradas para a chave (ou licença).

Ao configurar a política restrita do token, você deve especificar os parâmetros da chave de verificação primária, do emissor e do público-alvo. A chave de verificação primária contém a chave que o token foi assinado, o emissor é o serviço de token seguro que emite o token. A audiência (às vezes chamada de escopo) descreve a intenção do token ou o recurso que o token autoriza o acesso. O serviço de distribuição de chaves dos serviços de mídia valida que esses valores no token correspondem aos valores no modelo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection002.png)

## Modelo de direitos do PlayReady

Para obter informações detalhadas sobre o modelo de direitos do PlayReady, consulte [Visão Geral do Modelo de Licença do PlayReady dos Serviços de Mídia](media-services-playready-license-template-overview.md).

### Não persistente

Se você configurar a licença como não persistente, ela só será mantida na memória enquanto o player estiver usando a licença.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection003.png)

### Persistente

Se você configurar a licença como persistente, ela será salva no armazenamento persistente no cliente.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection004.png)

## Modelo de direitos do Widevine

Para obter informações detalhadas sobre o modelo de direitos do Widevine, consulte [Visão Geral do Modelo de Licença do Widevine](media-services-widevine-license-template-overview.md).

### Basic

Quando você selecionar **Básico**, o modelo será criado com todos os valores padrões.

### Avançado

Para obter uma explicação detalhada sobre a opção avançada das configurações do Widevine, consulte [este](media-services-widevine-license-template-overview.md) tópico.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection005.png)

## Configuração do FairPlay

Para habilitar a criptografia do FairPlay, você precisa fornecer o Certificado do Aplicativo e a Chave de Segredo do Aplicativo (ASK) com a opção Configuração do FairPlay. Para obter informações detalhadas sobre a configuração e os requisitos do FairPlay, consulte [este](media-services-protect-hls-with-fairplay.md) artigo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection006.png)

## Aplique a criptografia dinâmica em seu ativo

Para aproveitar a criptografia dinâmica, você precisa fazer o seguinte:

- Codifique o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável.
- Obtenha pelo menos uma unidade de streaming sob demanda para o ponto de extremidade de streaming por meio do qual você planeja distribuir conteúdo. Para saber mais, consulte [Como dimensionar unidades reservadas para streaming sob demanda](media-services-portal-manage-streaming-endpoints.md).

### Selecionar o ativo que você deseja criptografar

Para ver todos os seus ativos, selecione **Configurações** > **Ativos**.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection007.png)

### Criptografar com AES ou DRM

Depois que você pressionar **Criptografar** em um ativo, verá duas opções: **AES** ou **DRM**.

#### AES

A criptografia da chave de limpeza do AES será habilitada em todos os protocolos de streaming: Smooth Streaming, HLS e MPEG-DASH.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection008.png)

#### DRM

Quando você selecionar a guia DRM, verá diferentes opções de políticas de proteção de conteúdo (que você deve ter configurado agora) + um conjunto de protocolos de streaming.

- **PlayReady e Widevine com o MPEG-DASH** - irão criptografar dinamicamente seu fluxo MPEG DASH com os DRMs do PlayReady e do Widevine.
- **PlayReady e Widevine com o MPEG-DASH+ FairPlay com HLS** - irão criptografar dinamicamente seu fluxo MPEG DASH com os DRMs do PlayReady e do Widevine. Também irão criptografar seus fluxos HLS com o FairPlay.
- **PlayReady somente com Smooth Streaming, HLS e MPEG-DASH** - irão criptografar dinamicamente o Smooth Streaming, HLS e fluxos MPEG-DASH com o DRM do PlayReady.
- **Widevine apenas com MPEG-DASH** - irá criptografar dinamicamente seu MPEG-DASH com o DRM do Widevine.
- **FairPlay apenas com HLS** - irá criptografar dinamicamente seu fluxo HLS com o FairPlay.

Para habilitar a criptografia do FairPlay, você precisa fornecer o Certificado do Aplicativo e a Chave de Segredo do Aplicativo (ASK) com a opção Configuração do FairPlay da folha de configurações Proteção de Conteúdo.

![Proteger conteúdo](./media/media-services-portal-content-protection/media-services-content-protection009.png)

Após fazer a seleção da criptografia, pressione **Aplicar**.

##Próximas etapas

Examine os roteiros de aprendizagem dos Serviços de Mídia.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0928_2016-->