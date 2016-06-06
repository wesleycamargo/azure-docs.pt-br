<properties
	pageTitle="Como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure"
	description="Saiba como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure." 
	services="api-management"
	documentationCenter=""
	authors="steved0x"
	manager="erikre"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/25/2016"
	ms.author="sdanie"/>

# Como implantar uma instância do serviço de Gerenciamento de API do Azure em múltiplas regiões do Azure

O Gerenciamento de API dá suporte à implantação multirregião, que permite a editores de API distribuir um único serviço de gerenciamento de API por qualquer número desejado de regiões do Azure. Isso ajuda a reduzir a solicitação de latência percebida pelos consumidores de API distribuídos geograficamente e também melhora a disponibilidade do serviço se uma região ficar offline.

Quando um serviço de Gerenciamento de API é inicialmente criado, ele contém apenas uma [unidade][] e reside em uma única região do Azure, que é designada como a Região Primária. Regiões adicionais podem ser facilmente acrescentadas por meio do Portal clássico do Azure. Um servidor de gateway do Gerenciamento de API é implantado em cada região e o tráfego de chamada será encaminhado para o gateway mais próximo. Se uma região fica offline, o tráfego é automaticamente redirecionado para o gateway mais próximo entre os demais.

> [AZURE.IMPORTANT] A implantação multirregião só está disponível na camada **[Premium][]**.

## <a name="add-region"> </a>Implantar uma instância do serviço de Gerenciamento de API em uma nova região

Para começar, clique em **Gerenciar** no Portal Clássico do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal do editor de Gerenciamento de API.

![Portal do editor][api-management-management-console]

>Se você ainda não criou uma instância de serviço de Gerenciamento de API, consulte [Criar uma instância de serviço de Gerenciamento de API][] no tutorial [Introdução ao Gerenciamento de API do Azure][].

Navegue até a guia **Escala** no Portal Clássico do Azure para sua instância do serviço de Gerenciamento de API.

![Guia Escala][api-management-scale-service]

Para implantar uma nova região, clique na lista suspensa abaixo da região principal e selecione uma região na lista.

![Adicionar região][api-management-add-region]

Após ter selecionado esta região, selecione o número de unidades para a nova região na lista suspensa da unidade.

![Especificar unidades][api-management-select-units]

Após configurar as regiões e unidades desejadas, clique em **Salvar**.

## <a name="remove-region"> </a>Excluir uma instância do serviço de Gerenciamento de API de uma região

Para remover uma instância do serviço de Gerenciamento de API de uma região, navegue até a guia **Escala** no Portal Clássico do Azure para sua instância do serviço de Gerenciamento de API.

![Guia Escala][api-management-scale-service]

Clique no **X** à direita da região desejada a ser removido.

![Remover região][api-management-remove-region]

Depois que as regiões desejadas forem removidas, clique em **Salvar**.


[api-management-management-console]: ./media/api-management-howto-deploy-multi-region/api-management-management-console.png

[api-management-scale-service]: ./media/api-management-howto-deploy-multi-region/api-management-scale-service.png
[api-management-add-region]: ./media/api-management-howto-deploy-multi-region/api-management-add-region.png
[api-management-select-units]: ./media/api-management-howto-deploy-multi-region/api-management-select-units.png
[api-management-remove-region]: ./media/api-management-howto-deploy-multi-region/api-management-remove-region.png

[Criar uma instância de serviço de Gerenciamento de API]: api-management-get-started.md#create-service-instance
[Introdução ao Gerenciamento de API do Azure]: api-management-get-started.md

[Deploy an API Management service instance to a new region]: #add-region
[Delete an API Management service instance from a region]: #remove-region

[unidade]: http://azure.microsoft.com/pricing/details/api-management/
[Premium]: http://azure.microsoft.com/pricing/details/api-management/

<!---HONumber=AcomDC_0525_2016-->