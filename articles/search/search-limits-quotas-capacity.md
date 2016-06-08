<properties
	pageTitle="Limites do serviço na Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Os limites do serviço usados para o planejamento de capacidade e os limites máximos de solicitações e respostas para a Pesquisa do Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.date="05/19/2016"
	ms.author="heidist"/>

# Limites de serviço na Pesquisa do Azure

Os limites máximos de armazenamento, cargas de trabalho, quantidades de índices, documentos e outros objetos dependem do tipo de preço ao qual a Pesquisa do Azure é adicionada, que pode ser **Gratuito**, **Básico** ou **Standard**.

- **Gratuito** é um serviço compartilhado multilocatário fornecido com sua assinatura do Azure. É uma opção sem custo adicional para assinantes existentes que permite experimentar o serviço antes de se inscrever para obter recursos dedicados. 
- **Básico (Preview)** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor. Atualmente, esse tipo está em Preview e é oferecido por uma [taxa 50% reduzida durante o período de Preview](https://azure.microsoft.com/pricing/details/search/).
- **Standard** é executado em máquinas dedicados, com mais capacidade de armazenamento e processamento em cada nível, incluindo a configuração mínima. Standard vem em dois níveis (S1 e S2). 

Todos os tipos podem ser [provisionados no portal](search-create-service-portal.md), com exceção do S2, que exige um tíquete de suporte. Envie um e-mail para azuresearch_contact@microsoft.com a fim de começar a usar o S2.

## Limites de camada

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

## Limites de chave de API

As chaves de API são usadas para autenticação de serviço. Há dois tipos. Chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. Chaves de consulta são somente leitura, especificadas na URL, e, geralmente, são distribuídas para aplicativos cliente.

- Máximo de duas chaves de administração por serviço
- Máximo de 50 chaves de consulta por serviço

## Limites de solicitação

- Máximo de 16 MB por solicitação <sup>1</sup>
- Comprimento máximo da URL de 8 KB
- Máximo de 1000 documentos por lote de carregamentos, mesclagens ou exclusões de índice
- Máximo de 32 campos na cláusula $orderby
- O tamanho do termo de pesquisa é de 32766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

## Limites de resposta

- Máximo de 1000 documentos retornados por página de resultados da pesquisa
- Máximo de 100 sugestões retornadas por solicitação de Sugerir API

<sup>1</sup> na Pesquisa do Azure, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático no conteúdo de campos individuais ou Coleções que, caso contrário, não são limitadas por limites teóricos (consulte [Tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter mais informações sobre restrições e composição de campos).

## Consultas por segundo

Embora essas estimativas aproximadas sejam fornecidas na página de preços e no gráfico [Limites do Tipo](#TierLimits) fornecido acima, é difícil determinar a QPS (consultas por segundo) real, especialmente no serviço Gratuito compartilhado, no qual a taxa de transferência tem base na largura de banda disponível na competição por recursos do sistema. Os recursos de computação e armazenamento que apoiam o serviço compartilhado são compartilhados por vários assinantes; portanto, o QPS de sua solução sempre vai variar conforme o número de outras cargas de trabalho em execução ao mesmo tempo.

No nível padrão, é possível estimar melhor o QPS, porque você tem controle sobre mais parâmetros. Veja a seção de práticas recomendadas em [Gerenciar sua solução de pesquisa](search-manage.md) para obter orientação sobre como calcular o QPS para suas cargas de trabalho.

<!---HONumber=AcomDC_0525_2016-->