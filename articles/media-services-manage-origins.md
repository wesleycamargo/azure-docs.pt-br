<properties 
	pageTitle="Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia" 
	description="Este tópico mostra como gerenciar pontos de extremidade de Streaming usando o Portal de Gerenciamento do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/10/2015" 
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia

Este artigo faz parte das séries de [Vídeo de serviços de mídia no fluxo de trabalho sob demanda](media-services-video-on-demand-workflow.md) e [fluxo de trabalho da transmissão ao vivo dos serviços de mídia](media-services-live-streaming-workflow.md).  


Nos Serviços de Mídia do Microsoft Azure, um **Ponto de Extremidade de Streaming** representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou à CDN (Rede de Entrega de Conteúdo) para a distribuição posterior. Os Serviços de Mídia também fornecem integração perfeita da CDN do Azure. O fluxo de saída do serviço StreamingEndpoint pode ser um fluxo ao vivo ou um ativo de vídeo por demanda na sua conta dos Serviços de Mídia.  

Além disso, você pode controlar a capacidade do serviço de ponto de extremidade de Streaming para lidar com crescentes necessidades de largura de banda ajustando as unidades de dimensionamento (também conhecido como unidades de streaming). É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. As unidades de dimensionamento fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que inclui: [empacotamento dinâmico](https://msdn.microsoft.com/library/azure/jj889436.aspx), integração da CDN e configuração avançada.

Observe que você será cobrado apenas quando seu StreamingEndpoint estiver em estado de execução. 

Este tópico fornece uma visão geral das principais funcionalidades fornecidas pelos Pontos de Extremidade de Streaming. O tópico também mostra como usar o Portal de Gerenciamento do Azure para gerenciar pontos de extremidade de streaming.


##Adicionando e excluindo pontos de extremidade de streaming 

Você pode adicionar ou remover pontos de extremidade de streaming usando o SDK do .NET, a API REST ou o Portal de Gerenciamento do Azure.

Para adicionar\excluir um ponto de extremidade de streaming usando o Portal, faça o seguinte:

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.
2. Selecione a página de **PONTOS DE EXTREMIDADE DE STREAMING**. 
3. Clique no botão ADICIONAR ou EXCLUIR na parte inferior da página. Observe que o ponto de extremidade de streaming não pode ser excluído. 
4. Clique no botão INICIAR para iniciar o ponto de extremidade de streaming. 
5. Clique no nome do ponto de extremidade de streaming para configurá-lo.   

	![Página de Ponto de Extremidade de Streaming][streaming-endpoint]


Por padrão, você pode ter até dois pontos de extremidade de streaming. Se você precisar solicitar mais, consulte [Cotas e limitações](media-services-quotas-and-limitations/).

##<a id="scale_streaming_endpoints"></a>Dimensionar o ponto de extremidade de Streaming

As unidades para Streaming fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que, atualmente, inclui [recursos de empacotamento dinâmico](http://go.microsoft.com/fwlink/?LinkId=276874). Por padrão, o streaming é configurado em um modelo de instância compartilhada para a qual os recursos do servidor (por exemplo, computação, capacidade de egresso, etc.) são compartilhados com todos os outros usuários. Para melhorar uma taxa de transferência de streaming, é recomendável adquirir unidades de Streaming. 

Você pode dimensionar usando o SDK do .NET, a API REST ou o Portal de Gerenciamento do Azure.

Para alterar o número de unidades de streaming usando o Portal, faça o seguinte:

1. Para especificar o número de unidades de streaming, selecione a guia ESCALA e mova o controle deslizante **capacidade reservada**.

	![Página Escala](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Pressione o botão SALVAR para salvar as alterações.

	A alocação de quaisquer novas unidades de streaming leva cerca de 20 minutos para ser concluída. 

	 
>[AZURE.NOTE] No momento, mudar de qualquer valor positivo de unidades de streaming de volta para nenhuma pode desabilitar o streaming por demanda por até uma hora.

>[AZURE.NOTE] O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo. Para obter informações sobre os detalhes de preços, consulte [Detalhes de preços dos Serviços de Mídia](http://go.microsoft.com/fwlink/?LinkId=275107).
	
##<a id="configure_streaming_endpoints"></a>Configurando o ponto de extremidade de streaming

O Ponto de Extremidade de Streaming permite que você configure as seguintes propriedades quando tem pelo menos uma unidade de escala: 

- Controle de acesso
- Nomes de host personalizados
- Controle de cache
- Políticas de acesso entre sites

Para obter informações detalhadas sobre essas propriedades, consulte [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx).

Você pode configurar essas propriedades usando o SDK do .NET, a API REST ou o Portal de Gerenciamento do Azure.

Para alterar o número de unidades de streaming usando o Portal, faça o seguinte:

1. Selecione o ponto de extremidade de streaming que você deseja configurar.
1. Selecione a guia CONFIGURAR.
  
A seguir há uma breve descrição dos campos.

![Configurar origem][configure-origin]
  

1. Defina o período de cache máximo que será especificado no cabeçalho do controle de cache de respostas HTTP. Esse valor não substituirá o valor máximo do cache definido explicitamente no conteúdo do blob.

2. Especifique endereços IP que terão permissão para se conectar ao ponto de extremidade do streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar.

3. Especifique a configuração para autenticação do cabeçalho de assinatura do Akamai.

4. Você pode especificar uma política de acesso entre domínios para clientes do Adobe Flash (para obter mais informações, consulte [Especificação de arquivo de política entre domínios](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). Além de política de acesso de cliente para clientes do Microsoft Silverlight (para obter mais informações, consulte [Tornando um serviço disponível entre os limites de domínio](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).  

5. Você também pode configurar nomes de host personalizados clicando no botão **configurar**. Para obter mais informações, consulte a propriedade **CustomHostNames** no tópico [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).  


##<a id="enable_cdn"></a>Habilitar a integração da CDN do Azure CDN

Você pode especificar a habilitação da integração da CDN do Azure para um Ponto de Extremidade de Streaming (isso é desabilitado por padrão).

Para configurar a integração da CDN do Azure como true:

- O ponto de extremidade de streaming deve ter pelo menos uma unidade de streaming (escala). Se, posteriormente, você desejar definir unidades de escala como 0, primeiro deverá desabilitar a integração da CDN. 

- O ponto de extremidade de streaming deve estar no estado parado. Quando a CDN for habilitada, você poderá iniciar o ponto de extremidade de streaming. 

Pode levar até 90 minutos para que a integração da CDN do Azure seja habilitada.   

Depois que ela for habilitada, as seguintes configurações serão desabilitadas: **Nomes de Host Personalizados** e **Controle de Acesso**.

Nem todos os data centers dão suporte à integração da CDN do Azure. Se estiver usando o Portal de Gerenciamento, você verá a opção **HABILITAR CDN** se ela estiver disponível em seu data center. 

![Habilitar CDN de Ponto de Extremidade de Streaming][streaming-endpoint-enable-cdn]

Se estiver usando o SDK do .NET ou a API REST, você precisará verificar se seu data center tem a integração da CDN do Azure disponível. 

Para verificar, faça o seguinte:

1. Tente definir CdnEnabled como true.
1. Verifique o resultado retornado de um Código de Erro HTTP 412 (PreconditionFailed) com uma mensagem "A propriedade CdnEnabled de ponto de extremidade de streaming não pode ser definida como true, pois o recurso de CDN não está disponível na região atual". 

	Se você receber esse erro, o data center não dá suporte a essa opção. Você deve tentar outro data center.


[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png


<!--HONumber=52-->