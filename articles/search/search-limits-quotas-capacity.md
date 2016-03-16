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
	ms.date="02/28/2016"
	ms.author="heidist"/>

# Limites de serviço na Pesquisa do Azure

Os limites máximos de armazenamento, cargas de trabalho, quantidades de índices, documentos e outros objetos dependem do tipo de preço ao qual a Pesquisa do Azure é adicionada, que pode ser **Gratuito**, **Básico** ou **Standard**.

- **Gratuito** é um serviço compartilhado multilocatário fornecido com sua assinatura do Azure. É uma opção sem custo adicional para assinantes existentes que permite experimentar o serviço antes de se inscrever para obter recursos dedicados. 
- **Básico (Preview)** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor. Atualmente, esse tipo está em Preview e é oferecido por uma [taxa reduzida](https://azure.microsoft.com/pricing/details/search/).
- **Standard** é executado em computadores dedicados usados apenas pelo seu serviço, com mais capacidade de armazenamento e processamento em cada nível, incluindo a configuração mínima. Standard vem em dois níveis (S1 e S2). 

## Limites de camada

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-all.md)]

> [AZURE.NOTE] O QPS (consultas por segundo) é variável, especialmente, no serviço compartilhado, pois a taxa de transferência baseia-se na largura de banda disponível e na competição por recursos do sistema. Os recursos de computação e armazenamento do Azure que apoiam o serviço compartilhado são compartilhados por vários assinantes; portanto, o QPS de sua solução sempre vai variar conforme o número de outras cargas de trabalho em execução ao mesmo tempo. No nível padrão, é possível estimar melhor o QPS, porque você tem controle sobre mais parâmetros. Veja a seção de práticas recomendadas em [Gerenciar sua solução de pesquisa](search-manage.md) para obter orientação sobre como calcular o QPS para suas cargas de trabalho.

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

<!---HONumber=AcomDC_0302_2016-->