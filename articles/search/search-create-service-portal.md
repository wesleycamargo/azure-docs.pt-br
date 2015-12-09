<properties
	pageTitle="Criar um serviço Pesquisa do Azure no portal | Microsoft Azure | Serviço de pesquisa em nuvem hospedado"
	description="Adicione uma Pesquisa do Azure gratuita ou padrão a uma assinatura existente usando o Portal Clássico do Azure. A Pesquisa do Azure é um serviço de pesquisa hospedado na nuvem para aplicativos personalizados."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Criar um serviço Pesquisa do Azure no Portal Clássico do Azure

A Pesquisa do Microsoft Azure é um novo serviço de pesquisa de nuvem hospedado que permite que você insira a funcionalidade de pesquisa em aplicativos personalizados. Ele fornece um mecanismo de pesquisa e armazenamento para seus dados de pesquisa, os quais você pode acessar e gerenciar por meio do Portal Clássico do Azure, um SDK do .NET ou uma API REST. Os principais recursos incluem consultas de preenchimento automático, correspondência difusa, realce de ocorrências, navegação facetada, perfis de pontuação e suporte a vários idiomas. Para saber mais sobre o que a Pesquisa do Azure faz, confira [O que é a Pesquisa do Azure](seach-what-is-search.md).

## Adicionar a Pesquisa do Azure à sua assinatura gratuitamente

Como administrador, você pode adicionar a Pesquisa do Azure a uma assinatura do Azure existente sem custos adicionais ao selecionar o serviço compartilhado, ou por um valor padrão ao optar por recursos dedicados.

1. Entre no [Portal Clássico do Azure](https://portal.azure.com).

2. Na barra de navegação rápida, clique em **Novo** > **Dados + Armazenamento** > **Pesquisa**.

     ![][1]

3. Configure o nome do serviço, tipo de preço, grupo de recursos, assinatura e local. Essas configurações são necessárias e não podem ser alteradas após o início do fornecimento do serviço.

     ![][2]

	- O **nome do serviço** deve ser exclusivo, com letras minúsculas, menos de 15 caracteres e sem espaços. Esse nome se torna parte do ponto de extremidade de seu serviço de Pesquisa do Azure. Confira [Regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para saber mais sobre as convenções de nomenclatura.

	- A **Camada de preços** determina a capacidade e a cobrança. Ambas as camadas fornecem os mesmos recursos, mas em diferentes níveis de recursos.

		- Um serviço na camada **Gratuito** é executado em clusters compartilhados com outros assinantes. Essa camada oferece capacidade suficiente para testar tutoriais e escrever código de prova de conceito, mas não deve ser utilizada para aplicativos de produção. A implantação de um serviço gratuito geralmente demora apenas alguns minutos.
		- Um serviço na camada **Padrão** é executado em recursos dedicados e é altamente escalonável. Inicialmente, um serviço padrão é configurado com uma réplica e uma partição, mas você pode ajustar a capacidade após o serviço ser criado. A implantação de um serviço padrão demora mais tempo, normalmente cerca de 15 minutos.

	- **Grupos de recursos** são contêineres para serviços e contêineres usados para um fim comum. Por exemplo, se for compilar um aplicativo de pesquisa personalizado baseado na Pesquisa do Azure, o recurso Aplicativos Web no Serviço de Aplicativo do Azure e armazenamento em Blob do Azure, você pode criar um grupo de recursos que mantém esses serviços juntos nas páginas de gerenciamento do portal.

	- **Assinatura** permite que você escolha entre várias assinaturas, se você tiver mais de uma.

	- **Local** é a região do datacenter. Atualmente, todos os recursos devem ser executados no mesmo datacenter. Não há suporte para a distribuição de recursos em vários datacenters.

4. Clique em **Criar** para provisionar o serviço.

Observe as notificações na barra de navegação rápida. Um aviso é exibido quando o serviço fica pronto para uso.

<a id="sub-3"></a>
## Adicionar um serviço de pesquisa de camada Standard para obter recursos dedicados

Muitos clientes começam com o serviço gratuito depois alternam para a camada Standard para acomodar cargas de trabalho maiores. A camada Standard oferece a você recursos dedicados em um data center do Azure que só você pode utilizar.

Operações de Pesquisa do Azure requerem tanto réplicas de serviço quanto de armazenamento. Em contraste com o serviço gratuito, que não tem a opção de adição de recursos, a camada Standard permite dimensionar para adicionar mais armazenamento ou suporte a consultas ao aumentar qualquer recurso que seja mais importante para seu cenário.

Para usar a camada Standard, você precisará criar um novo serviço de pesquisa nesse nível de preço. Você pode repetir as etapas anteriores deste artigo para criar um novo serviço de Pesquisa do Azure. Observe que configurar recursos dedicados pode levar algum tempo, 15 minutos ou mais.

Não há nenhuma atualização in-loco da versão gratuita. A alteração para padrão, com seu potencial de dimensionamento, requer um novo serviço. Você precisará recarregar os índices e documentos usados por seu aplicativo de pesquisa.

Um serviço de Pesquisa do Azure na camada padrão é criado com uma réplica e uma partição, mas pode ser redimensionado facilmente para níveis de recursos maiores.

1.	Após o serviço ser criado, volte para o painel do serviço.

2.	Clique no bloco **Escala**.

3.	Use os controles deslizantes para adicionar réplicas, partições ou ambas.

Réplicas e partições adicionais são cobradas segundo as unidades de pesquisa. O total de unidades de pesquisa necessárias para dar suporte a qualquer configuração de recursos em particular é mostrado na página, conforme você adiciona recursos.

Você pode verificar os [Detalhes de Preços](http://go.microsoft.com/fwlink/p/?LinkID=509792) para obter as informações de cobrança por unidade. Consulte [Limites e restrições](search-limits-quotas-capacity.md) para ajudá-lo a decidir como configurar combinações de partição e de réplica.

<a id="sub-2"></a>
## Localizar o nome do serviço e chaves de Api do serviço de Pesquisa do Azure

Depois que o serviço for criado, você poderá retornar ao Portal Clássico do Azure para obter a URL ou a `api-key`. As conexões com o serviço Pesquisa do Azure exigem que você tenha a URL e a `api-key` para autenticar a chamada.

1. Na barra de navegação rápida, clique em **Página Inicial** e clique no serviço Pesquisa do Azure para abrir o painel do serviço.

2. No painel de serviço, você verá blocos com as informações essenciais e o ícone de chave para acessar as chaves de administrador.

  	![][3]

3. Copie a URL do serviço e uma chave de administrador. Você precisará para a próxima tarefa, [Operações de serviço de teste](#sub-4).


<a id="sub-4"></a>
## Testar operações de serviço

Confirmar que seu serviço está funcionando e pode ser acessado por meio de um aplicativo cliente é a etapa final da configuração da Pesquisa do Azure. Você pode usar qualquer um dos links abaixo para uma abordagem sem código para verificar a disponibilidade do serviço.

- [Como usar o Chrome Postman com a Pesquisa do Azure](search-chrome-postman.md)
- [Como usar o Telerik Fiddler com a Pesquisa do Azure](search-fiddler.md)

<!--Next steps and links -->
<a id="next-steps"></a>
## Próximas etapas

As informações a seguir mostram como compilar e gerenciar aplicativos de pesquisa que usam a Pesquisa do Azure.

- [Como usar a pesquisa do Azure no .NET](search-howto-dotnet-sdk.md)

- [Gerenciar sua solução de pesquisa no Microsoft Azure](search-manage.md)

- [Pesquisa do Azure no MSDN](http://msdn.microsoft.com/library/dn798933.aspx)

- [Vídeo do Canal 9: Introdução à pesquisa do Azure](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to the standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=AcomDC_1203_2015-->