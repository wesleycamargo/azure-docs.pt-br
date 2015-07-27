<properties
	pageTitle="Proteja sua API com limites de taxa usando o Gerenciamento de API do Azure"
	description="Saiba como proteger sua API com cotas e políticas de limitação (limite de taxa)."
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/18/2015"
	ms.author="sdanie"/>

# Proteja sua API com limites de taxa usando o Gerenciamento de API do Azure

Este guia mostra como é fácil adicionar proteção à API de back-end configurando políticas de cota e limite de taxa com o Gerenciamento de API do Azure.

Neste tutorial, você criará um produto de API de 'Avaliação gratuita' que permite aos desenvolvedores fazer até 10 chamadas por minuto e um máximo de 200 chamadas por semana para sua API. Depois você publicará a API e testará a política de limite de taxa.

>[AZURE.NOTE]Se já tiver um produto configurado e desejar usá-lo para este tutorial, você pode pular para [Configurar políticas de cota e de limite de taxa de chamada][] e seguir o tutorial a partir daí, usando seu produto em vez do produto de **Avaliação Gratuita**.

## <a name="create-product"> </a>Criar um produto

Nesta etapa você criará um produto de avaliação gratuita que não requer aprovação de assinatura.

Para começar, clique em **Gerenciar** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal do editor de Gerenciamento de API.

![Portal do editor][api-management-management-console]

>Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][] no tutorial [Introdução ao Gerenciamento de API do Azure][].

Clique em **Produtos** no menu **Gerenciamento de API** à esquerda para exibir a página **Produtos**.

![Adicionar produto][api-management-add-product]

Clique em **Adicionar Produto** para exibir a janela pop-uo **Adicionar novo produto**.

![Adicionar novo produto][api-management-new-product-window]

Digite **Avaliação Gratuita** na caixa de texto **Título**.

Digite **Assinantes poderão fazer 10 chamadas/minuto, no máximo 200 chamadas/semana e após isso o acesso será negado.** na caixa de texto **Descrição**.

Produtos de Gerenciamento de API podem ser **Livre** ou **Protegido**. Produtos protegidos devem ser assinados antes que possam ser usados, enquanto produtos abertos podem ser usados sem uma assinatura. Certifique-se de que **Exigir assinatura** esteja marcado para criar um produto protegido que requer uma assinatura. Esta é a configuração padrão.

Se deseja que um administrador revise e aceite ou rejeite as tentativas de assinatura para este produto, marque **Requerer aprovação de assinatura**. Se a caixa não estiver marcada, as tentativas de assinatura serão aprovadas automaticamente. Neste exemplo, as assinaturas são aprovadas automaticamente; por isso, não marque a caixa.

Para permitir que contas de desenvolvedor assinem várias vezes o novo produto, marque a caixa de seleção **Permitir várias assinaturas simultâneas**. Este tópico não utiliza várias inscrições simultâneas, por isso deixe-o desmarcado.

Após inserir todos os valores, clique em **Salvar** para criar o produto.

![Produto adicionado][api-management-product-added]

Por padrão, os novos produtos ficam visíveis para os usuários no grupo **Administradores**. Vamos adicionar o grupo **Desenvolvedores**. Clique em **Avaliação Gratuita** e selecione a guia **Visibilidade**.

>No Gerenciamento de API, os grupos são usados para gerenciar a visibilidade dos produtos para desenvolvedores. Os produtos dão visibilidade aos grupos e os desenvolvedores podem exibir e assinar os produtos visíveis para os grupos aos quais pertencem. Para obter mais informações, consulte [Como criar e usar grupos no Gerenciamento de API do Azure][].

![Adicionar um grupo de desenvolvedores][api-management-add-developers-group]

Marque o grupo **Desenvolvedores** e clique em **Salvar**.

## <a name="add-api"> </a>Adicionar uma API ao produto

Nesta etapa do tutorial você adicionará a API de ECO a uma produto de avaliação gratuita.

>Cada instância de serviço de Gerenciamento de API vem pré-configurada com uma API de ECO que pode ser usada para experimentar e aprender sobre o Gerenciamento de API. Para obter mais informações, consulte [Introdução ao Gerenciamento de API do Azure][].

Clique em **Produtos** no menu **Gerenciamento de API** à esquerda, depois clique em **Avaliação Gratuita** para configurar o produto.

![Configurar produto][api-management-configure-product]

Clique em **Adicionar API ao produto**.

![Adicionar API ao produto][api-management-add-api]

Marque a caixa ao lado de **API de Eco** e clique em **Salvar**.

![Adicionar API de Eco][api-management-add-echo-api]

## <a name="policies"> </a>Configurar o limite de taxa de chamada e as políticas de cota

Os limites de taxa e as cotas são configurados no editor de políticas. Clique em **Políticas** no menu **Gerenciamento de API** à esquerda e selecione **Avaliação Gratuita** na lista suspensa **Produto de escopo da política**.

![Política de produtos][api-management-product-policy]

Clique em **Adicionar política** para importar o modelo de política e começar a criar o limite de taxa e a política de cota.

![Adicionar política][api-management-add-policy]

Para inserir políticas, posicione o cursor na seção **entrada** ou **saída** do modelo da política. A taxa de limite e as políticas de cota são políticas de entrada; portanto, posicione o cursor no elemento de entrada.

![Editor de políticas][api-management-policy-editor-inbound]

As duas políticas que estamos adicionando neste tutorial são as políticas [Limitar taxa de chamada][] e [Definir cota de uso][].

![Declarações de políticas][api-management-limit-policies]

Após posicionar o cursor no elemento de política de **entrada**, clique na seta ao lado de **Limitar taxa de chamada** para inserir o modelo de política.

	<rate-limit calls="number" renewal-period="seconds">
	<api name="name" calls="number">
	<operation name="name" calls="number" />
	</api>
	</rate-limit>

A opção **Limitar taxa de chamada** pode ser usada no nível do produto e também nos níveis de nome de operação individual e da API. Neste tutorial, somente serão usadas políticas de nível de produto, por isso exclua os elementos **api** e **operation** do elemento **rate-limit**, de maneira que apenas o elemento **rate-limit** permanece, conforme mostrado no exemplo a seguir.

	<rate-limit calls="number" renewal-period="seconds">
	</rate-limit>

No produto de **Avaliação Gratuita**, a taxa máxima permitida é de 10 chamadas por minuto, por isso digite **10** como o valor do atributo chamadas e **60** para o atributo **período de renovação**.

	<rate-limit calls="10" renewal-period="60">
	</rate-limit>

Para configurar a política **Definir cota de uso**, posicione seu cursor imediatamente abaixo do novo elemento **limite de taxa** que acabou de adicionar dentro do elemento **entrada** e clique na seta à esquerda de **Definir cota de uso**.

	<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
	<api name="name" calls="number" bandwidth="kilobytes">
	<operation name="name" calls="number" bandwidth="kilobytes" />
	</api>
	</quota>

Como essa política também tem a finalidade de ficar no nível do produto, exclua os elementos de nome **api** e **operação**, conforme mostrado no exemplo a seguir.

	<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
	</quota>

As cotas podem ser baseadas no número de chamadas por intervalo, largura de banda ou ambos. Neste tutorial, não estamos limitando com base na largura de banda, por isso, exclua o atributo **largura de banda**.

	<quota calls="number" renewal-period="seconds">
	</quota>

No produto de **Avaliação Gratuita**, a cota é de 200 chamadas por semana. Especifique **200** como o valor para o atributo chamadas e especifique **604800** como o valor do período de renovação.

	<quota calls="200" renewal-period="604800">
	</quota>

>Os intervalos de política são especificados em segundos. Para calcular o intervalo para uma semana, você pode multiplicar o número de dias (7) pelo número de horas em um dia (24), pelo número de minutos em uma hora (60), pelo número de segundos em um minuto (60). 7 * 24 * 60 * 60 = 604800.

Ao finalizar a configuração da política, ela deve corresponder ao exemplo a seguir.

	<policies>
		<inbound>
			<rate-limit calls="10" renewal-period="60">
			</rate-limit>
			<quota calls="200" renewal-period="604800">
			</quota>
			<base />

	</inbound>
	<outbound>

		<base />

		</outbound>
	</policies>

Após configurar as políticas desejadas, clique em **Salvar**.

![Salvar política][api-management-policy-save]

## <a name="publish-product"> </a>Publicar o produto

Agora que as APIs foram adicionadas e as políticas configuradas, o produto está pronto para ser usado pelos desenvolvedores. Antes que o produto possa ser usado pelos desenvolvedores, ele deve ser publicado. Clique em **Produtos** no menu **Gerenciamento de API** à esquerda, depois clique em **Avaliação Gratuita** para configurar o produto.

![Configurar produto][api-management-configure-product]

Clique em **Publicar** e depois clique em **Sim, publicar** para confirmar.

![Publicar produto][api-management-publish-product]

## <a name="subscribe-account"> </a>Assinar um produto com uma conta de desenvolvedor

Agora que o produto foi publicado, ele está disponível para inscrição e para ser usado pelos desenvolvedores.

>Os administradores de uma instância de Gerenciamento de API são inscritos automaticamente em cada produto. Nesta etapa do tutorial, inscreveremos um das contas de desenvolvedor não administrador no produto de avaliação gratuita. Se sua conta de desenvolvedor fizer parte da função Administradores, você pode prosseguir com esta etapa, embora já esteja inscrito.

Clique em **Usuários** no menu **Gerenciamento de API** à esquerda e clique no nome da sua conta de desenvolvedor. Neste exemplo, estamos usando a conta de desenvolvedor **Clayton Gragg**.

![Configurar desenvolvedor][api-management-configure-developer]

Clique em **Adicionar assinatura**.

![Adicionar assinatura][api-management-add-subscription-menu]

Marque a caixa ao lado de **Avaliação Gratuita** e clique em **Assinar**.

![Adicionar assinatura][api-management-add-subscription]

>[AZURE.NOTE]Neste tutorial, várias inscrições simultâneas não estão habilitadas para o produto de **Avaliação Gratuita**. Se elas estivessem, você seria solicitado a nomear a assinatura, conforme mostrado no exemplo a seguir.

![Adicionar assinatura][api-management-add-subscription-multiple]

Depois de clicar em **Assinar**, o produto é exibido na lista **Assinatura** do usuário.

![Assinatura adicionada][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Chamar uma operação e testar o limite de taxa

Agora que o produto de Avaliação Gratuita está configurado e publicado, podemos chamar algumas operações e testar a política da taxa de limite. Alterne para o portal do desenvolvedor clicando em **Portal do desenvolvedor** no menu superior à direita.

![Portal do desenvolvedor][api-management-developer-portal-menu]

Clique em **APIs** no menu superior e selecione **API de Eco**.

![Portal do desenvolvedor][api-management-developer-portal-api-menu]

Selecione a operação **Recurso GET** e clique em **Abri Console**.

![Abrir console][api-management-open-console]

Mantenha os valores de parâmetros padrão e selecione a chave de assinatura para o produto de **Avaliação Gratuita**.

![Chave de assinatura][api-management-select-key]

>[AZURE.NOTE]Se você tem várias inscrições certifique-se de selecionar a chave de **Avaliação Gratuita** ou as políticas que foram configuradas nas etapas anteriores não entrarão em vigor.

Clique em **HTTP Get** e visualize a resposta. Observe o **Status de resposta** de **200 OK**.

![Resultados da operação][api-management-http-get-results]

Clique em **HTTP Get** a uma taxa maior que a política de limite de taxa de 10 chamadas por minuto. Uma vez excedida a política de limite de taxa, um status de resposta de **429 muitas solicitações** será retornado.

![Resultados da operação][api-management-http-get-429]

Os **Cabeçalhos de resposta** e o **Conteúdo da resposta** indicam o intervalo restante antes que as recuperações obtenham êxito.

Quando a política de limite de taxa de 10 chamadas por minuto estiver em vigor, as chamadas subsequentes falharão até que 60 segundos tenham se passado a partir da primeira das 10 chamadas com êxito para o produto, antes que o limite de taxa tenha sido excedido. Neste exemplo, o intervalo restante é de 43 segundos.

## <a name="next-steps"> </a>Próximas etapas

-	Verifique os outros tópicos no tutorial [Introdução à configuração avançada de API][].
-	Para obter mais informações e ver uma demonstração da definição de limites de taxa e cotas, consulte o vídeo a seguir.

> [AZURE.VIDEO rate-limits-and-quotas]


[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[Como criar e usar grupos no Gerenciamento de API do Azure]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Introdução ao Gerenciamento de API do Azure]: api-management-get-started.md
[Criar uma instância de serviço de Gerenciamento de API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configurar políticas de cota e de limite de taxa de chamada]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit
[Introdução à configuração avançada de API]: api-management-get-started-advanced.md

[Limitar taxa de chamada]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Definir cota de uso]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota
 

<!---HONumber=July15_HO3-->