<properties 
	pageTitle="Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia" 
	description="Este tópico mostra como gerenciar Pontos de Extremidade da Transmissão usando o Portal Clássico do Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	writer="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016"
	ms.author="juliako"/>


#<a id="managemediaservicesorigins"></a>Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia

> [AZURE.SELECTOR]
- [Portal](media-services-manage-origins.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)


Nos Serviços de Mídia do Microsoft Azure, um **Ponto de Extremidade de Streaming** representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player de cliente ou à CDN (Rede de Distribuição de Conteúdo) para a distribuição posterior. Os Serviços de Mídia também fornecem integração perfeita da CDN do Azure. O fluxo de saída do serviço StreamingEndpoint pode ser um fluxo ao vivo ou um ativo de vídeo por demanda na sua conta dos Serviços de Mídia.

Além disso, você pode controlar a capacidade do serviço de ponto de extremidade de Streaming para lidar com crescentes necessidades de largura de banda ajustando as unidades de dimensionamento (também conhecido como unidades de streaming). É recomendável alocar uma ou mais unidades de escala para aplicativos no ambiente de produção. As unidades de dimensionamento fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que inclui: [empacotamento dinâmico](media-services-dynamic-packaging-overview.md), Integração de CDN e configuração avançada.

Observe que você será cobrado apenas quando seu StreamingEndpoint estiver em estado de execução.

Este tópico fornece uma visão geral das principais funcionalidades fornecidas pelos Pontos de Extremidade de Streaming. O tópico também mostra como usar o Portal Clássico do Azure para gerenciar pontos de extremidade de streaming.


##Adicionando e excluindo pontos de extremidade de streaming

Você pode adicionar ou remover pontos de extremidade da transmissão usando o SDK do .NET, a API REST ou o Portal Clássico do Azure.

Para adicionar ou excluir um ponto de extremidade da transmissão usando o Portal Clássico do Azure, faça o seguinte:

1. No [Portal Clássico do Azure](https://manage.windowsazure.com/), clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.
2. Selecione a página de **PONTOS DE EXTREMIDADE DE STREAMING**.
3. Clique no botão ADICIONAR ou EXCLUIR na parte inferior da página. Observe que o ponto de extremidade de streaming não pode ser excluído.
4. Clique no botão INICIAR para iniciar o ponto de extremidade de streaming.
5. Clique no nome do ponto de extremidade de streaming para configurá-lo.

![Página de ponto de extremidade de streaming][streaming-endpoint]


Por padrão, você pode ter até dois pontos de extremidade de streaming. Se você precisar solicitar mais, consulte [Cotas e limitações](media-services-quotas-and-limitations.md).

##<a id="scale_streaming_endpoints"></a>Dimensionar o ponto de extremidade de streaming

As unidades para Streaming fornecem capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps e funcionalidade adicional que, atualmente, inclui [recursos de empacotamento dinâmico](media-services-dynamic-packaging-overview.md). Por padrão, o streaming é configurado em um modelo de instância compartilhada para a qual os recursos do servidor (por exemplo, computação, capacidade de egresso, etc.) são compartilhados com todos os outros usuários. Para melhorar uma taxa de transferência de streaming, é recomendável adquirir unidades de Streaming.

Você pode dimensionar usando o SDK do .NET, a API REST ou o Portal Clássico do Azure.

Para alterar o número de unidades de streaming usando o Portal, faça o seguinte:

1. Para especificar o número de unidades de streaming, selecione a guia ESCALA e mova o controle deslizante **capacidade reservada**.

	![Página Escala](./media/media-services-manage-origins/media-services-origin-scale.png)

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

Você pode configurar essas propriedades usando o SDK do .NET, a API REST ou o Portal Clássico do Azure.

Para alterar o número de unidades de streaming usando o Portal, faça o seguinte:

1. Selecione o ponto de extremidade de streaming que você deseja configurar.
1. Selecione a guia CONFIGURAR.
  
A seguir há uma breve descrição dos campos.

![Configurar a origem][configure-origin]
  

1. Defina o período de cache máximo que será especificado no cabeçalho do controle de cache de respostas HTTP. Esse valor não substituirá o valor máximo do cache que foi definido explicitamente no conteúdo do blob.

2. Especificar endereços IP que terão permissão para se conectar ao ponto de extremidade do streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar.

3. Especifique a configuração para autenticação do cabeçalho de assinatura do Akamai.

4. Você pode especificar uma política de acesso entre domínios para clientes do Adobe Flash (para obter mais informações, consulte [Especificação de arquivo de política entre domínios](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). Além de política de acesso de cliente para clientes do Microsoft Silverlight (para obter mais informações, consulte [Tornando um serviço disponível entre os limites de domínio](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).

5. Você também pode configurar nomes de host personalizados clicando no botão **configurar**. Para obter mais informações, consulte a propriedade **CustomHostNames** no tópico [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).


##<a id="enable_cdn"></a>Habilitar a integração da CDN do Azure

Você pode especificar a habilitação da integração da CDN do Azure para um Ponto de Extremidade de Streaming (isso é desabilitado por padrão).

Para configurar a integração da CDN do Azure como true:

- O ponto de extremidade de streaming deve ter pelo menos uma unidade de streaming (escala). Se, posteriormente, você desejar definir unidades de escala como 0, primeiro deverá desabilitar a integração da CDN. Por padrão, quando você cria um novo ponto de extremidade de streaming, uma unidade de streaming é automaticamente definida.

- O ponto de extremidade de streaming deve estar no estado parado. Quando a CDN for habilitada, você poderá iniciar o ponto de extremidade de streaming.

Pode levar até 90 minutos para que a integração da CDN do Azure seja habilitada. Leva até duas horas para que as alterações estejam ativas em todos os POPS do CDN.


A Integração CDN é habilitada em todos os data centers do Azure: Oeste dos EUA, Leste dos EUA, Norte da Europa, Europa Ocidental, Oeste do Japão, Leste do Japão, Sudeste da Ásia e Ásia Oriental.

Depois que ela for habilitada, as seguintes configurações serão desabilitadas: **Nomes de host personalizados** e **Controle de Acesso**.

![CDN habilitado para ponto de extremidade de streaming][streaming-endpoint-enable-cdn]

>[AZURE.IMPORTANT] Integração dos Serviços de Mídia do Azure com a CDN do Azure é implementada na **CDN do Azure da Verizon**. Para usar a **CDN do Azure da Akamai** para Serviços de Mídia do Azure, é preciso [configurar o ponto de extremidade manualmente](../cdn/cdn-create-new-endpoint.md). Para saber mais sobre os recursos da CDN do Azure, veja [Visão geral da CDN](../cdn/cdn-overview.md).

###Considerações adicionais

- Quando CDN está habilitado para um ponto de extremidade de streaming, os clientes não podem solicitar o conteúdo diretamente a partir da origem. Se você precisar da capacidade de testar seu conteúdo com ou sem CDN pode criar outro ponto de extremidade de streaming que não seja o CDN habilitado.
- O nome de host do ponto de extremidade de streaming permanece o mesmo depois de habilitar o CDN. Você não precisa fazer nenhuma alteração ao seu fluxo de trabalho de serviços de mídia depois que o CDN for habilitado. Por exemplo, se o nome de host do ponto de extremidade de streaming for strasbourg.streaming.mediaservices.windows.net, depois de habilitar o CDN, o mesmo nome de host é usado.
- Para novos pontos de extremidade de streaming, é possível habilitar o CDN simplesmente criando um novo ponto de extremidade; para pontos de extremidade de streaming existentes, será necessário primeiro parar o ponto de extremidade e, em seguida, habilitar o CDN.
 

Para obter mais informações, consulte [Anunciando a integração dos Serviços de Mídia do Azure com o CDN do Azure (Rede de Distribuição de Conteúdo)](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/).


##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png
 

<!---HONumber=AcomDC_0629_2016-->