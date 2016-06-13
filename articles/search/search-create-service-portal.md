<properties
	pageTitle="Criar um serviço de Pesquisa do Azure usando o portal do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Saiba como provisionar um serviço de Pesquisa do Azure usando o Portal do Azure."
	services="search"
	authors="ashmaka"
	documentationCenter=""/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="05/31/2016"
	ms.author="ashmaka"/>

# Criar um serviço de Pesquisa do Azure usando o portal do Azure

Este guia o orientará ao longo do processo de criação (ou de provisionamento) de um serviço de Pesquisa do Azure usando o [Portal do Azure](https://portal.azure.com/).

Este guia pressupõe que você já tem uma assinatura do Azure e pode fazer logon no Portal do Azure.

## I. Encontre a Pesquisa do Azure no Portal do Azure
1. Acesse o [Portal do Azure](https://portal.azure.com/) e faça logon.
1. Clique no sinal de adição ("+") no canto superior esquerdo.
2. Selecione "Dados + Armazenamento"
3. Selecione "Pesquisa do Azure"

![](./media/search-create-service-portal/find-search.png)

## II. Escolha um nome de serviço e o ponto de extremidade de URL para o serviço
1. O nome do serviço fará parte da URL do ponto de extremidade do seu serviço de Pesquisa do Azure no qual você fará suas chamadas à API a fim de gerenciar e usar o serviço de pesquisa.
2. Digite o nome do serviço no campo "URL". O nome do serviço:
  * deve conter apenas letras minúsculas, dígitos ou traços ("-")
  * não pode usar um traço ("-") como os dois primeiros caracteres ou o último caractere
  * não pode conter traços consecutivos ("--")
  * tem limitação de 2 a 60 caracteres


## III. Selecione uma assinatura na qual você manterá seu serviço
Se você tiver mais de uma assinatura, poderá selecionar qual incluirá nesse serviço de Pesquisa do Azure.

## IV. Selecione um grupo de recursos para seu serviço
Crie um novo grupo de recursos ou selecione um existente. Um grupo de recursos é uma coleção de serviços e recursos do Azure que são usados juntos. Por exemplo, se você estiver usando a Pesquisa do Azure para indexar um banco de dados SQL, esses dois serviços deverão fazer parte do mesmo grupo de recursos.

## V. Selecione o local onde o serviço será hospedado
Como um serviço do Azure, a Pesquisa do Azure está disponível para ser hospedado em datacenters em todo o mundo. Observe que os [preços podem variar](https://azure.microsoft.com/pricing/details/search/) de acordo com a geografia.

## VI. Selecione seu tipo de preço
[A Pesquisa do Azure é oferecida atualmente em vários tipos de preço](https://azure.microsoft.com/pricing/details/search/): Gratuito, Básico ou Standard. Cada tipo tem sua própria [capacidade e limites](search-limits-quotas-capacity.md).

Nesse caso, escolhemos o tipo Standard para nosso serviço.

## VII. Selecione o botão "Criar" para provisionar o serviço

![](./media/search-create-service-portal/create-service.png)

## VIII. Dimensione seu serviço

Após a conclusão do provisionamento do serviço, você pode dimensioná-lo para atender às suas necessidades. Se você tiver escolhido o tipo Standard para o serviço de Pesquisa do Azure, você pode dimensionar seu serviço em duas dimensões: réplicas e partições. Se você tiver escolhido o tipo Básico, só poderá adicionar réplicas.

*__Partições__* permitem que seu serviço armazene e pesquise mais documentos.

*__Réplicas__* permitem que seu serviço lide com uma carga maior de consultas de pesquisa - [um serviço exige duas réplicas para alcançar um SLA somente leitura e exige três réplicas para alcançar uma SLA de leitura/gravação](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Acesse a folha de gerenciamento de seu serviço de Pesquisa do Azure no Portal do Azure
2. Na folha "Configurações", selecione "Escala".
3. Você pode dimensionar seu serviço adicionando Réplicas ou Partições.
  * Você não pode dimensionar seu serviço acima de 36 unidades de pesquisa. O número total de unidades de pesquisa é o produto de suas réplicas e partições (Réplicas * Partições = Total de Unidades de Pesquisa).
  * Se você tiver escolhido o tipo Básico, só poderá dimensionar até três réplicas. Serviços básicos são associados a uma única partição.

![](./media/search-create-service-portal/scale-service.png)

## Avançar
Após o provisionamento de um serviço de Pesquisa do Azure, você estará pronto para [definir um índice de Pesquisa do Azure](search-what-is-an-index.md) para que possa carregar e pesquisar os dados.

<!---HONumber=AcomDC_0601_2016-->