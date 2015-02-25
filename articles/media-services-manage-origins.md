<properties pageTitle="Como gerenciar origens em uma conta de Serviços de Mídia" description="" services="media-services" documentationCenter="" authors="juliako" writer="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/29/2015" ms.author="juliako"/>


# <a id="managemediaservicesorigins"></a>Como gerenciar pontos de extremidade de streaming em uma conta de Serviços de Mídia

Os Serviços de Mídia permitem adicionar vários pontos de extremidade de streaming à sua conta e configurá-los. Cada conta dos Serviços de Mídia tem, pelo menos, um ponto de extremidade de streaming **padrão** associado a ela.

>[AZURE.NOTE] Pontos de extremidade de streaming costumavam ser conhecidos como Origens. 


## Adicionando e excluindo pontos de extremidade de streaming 

1. No [Portal de Gerenciamento](https://manage.windowsazure.com/), clique em **Serviços de Mídia**. Em seguida, clique no nome do serviço de mídia.
2. Selecione a página de PONTOS DE EXTREMIDADE DE STREAMING. 
3. Clique no botão ADICIONAR ou EXCLUIR na parte inferior da página. Observe que o ponto de extremidade de streaming não pode ser excluído. 
4. Clique no botão INICIAR para iniciar o ponto de extremidade de streaming. 
5. Clique no nome do ponto de extremidade de streaming para configurá-lo.   

	![Origin page][origin-page]

## Dimensionar o ponto de extremidade de Streaming

Unidades de streaming fornecem a você tanto capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps quanto  funcionalidade adicional que atualmente inclui [recursos de empacotamento dinâmico](http://go.microsoft.com/fwlink/?LinkId=276874). Por padrão, o streaming por demanda é configurado em um modelo de instância compartilhada para a qual os recursos do servidor (por exemplo, computação, capacidade de egresso etc.) são compartilhados com todos os outros usuários. Para melhorar a taxa de transferência de um streaming por demanda, é recomendável adquirir unidades de Streaming. 

Para alterar o número de unidades de streaming, faça o seguinte:

1. Para especificar o número de unidades de streaming, selecione a guia ESCALA e mova o controle deslizante **capacidade reservada**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Pressione o botão SALVAR para salvar as alterações.

	A alocação de quaisquer novas unidades de streaming leva cerca de 20 minutos para ser concluída. 

	 
	>[AZURE.NOTE] No momento, mudar de qualquer valor positivo de unidades de streaming de volta para nenhuma pode desabilitar o streaming por demanda por até uma hora.

	>[AZURE.NOTE] O número mais alto de unidades especificadas para o período de 24 horas é usado para calcular o custo. Para obter informações sobre os detalhes de preços, consulte [Detalhes de preços dos Serviços de Mídia](http://go.microsoft.com/fwlink/?LinkId=275107).
	
## Configurando o ponto de extremidade de streaming

A guia CONFIGURAR permite que você realize configurações conforme mostrado nesta imagem. A seguir, a descrição dos campos.

>[AZURE.NOTE] A configuração nesta página se aplica apenas aos pontos de extremidade de streaming que têm pelo menos uma unidade reservada. Para reservar as unidades reservadas de streaming sob demanda.

![Configure origin][configure-origin]
  

1. Defina o período de cache máximo que será especificado no cabeçalho do controle do cache de respostas HTTP. Esse valor não substituirá o valor máximo do cache definido explicitamente no conteúdo do blob.

2. Especifique endereços IP que terão permissão para se conectar ao ponto de extremidade do streaming publicado. Se nenhum endereço IP for especificado, qualquer endereço IP poderá se conectar.

3. Especifique a configuração para autenticação do cabeçalho de assinatura do Akamai.

4. Você pode especificar uma política de acesso entre domínios para clientes do Adobe Flash (para obter mais informações, consulte [Especificação de arquivo de política entre domínios](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html). Além de política de acesso de cliente para clientes do Microsoft Silverlight (para obter mais informações, consulte [Tornando um serviço disponível entre os limites de domínio](https://msdn.microsoft.com/pt-br/library/cc197955(v=vs.95).aspx).  

5. Você também pode configurar nomes de host personalizados clicando no botão **configurar**. Para obter mais informações, consulte a propriedade **CustomHostNames** no tópico [StreamingEndpoint](https://msdn.microsoft.com/pt-br/library/dn783468.aspx).  




[origin-page]: ./media/media-services-manage-origins/media-services-origins-page.png
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png


<!--HONumber=42-->
