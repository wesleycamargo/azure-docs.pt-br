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
	ms.date="05/25/2016"
	ms.author="heidist"/>

# Limites de serviço na Pesquisa do Azure

Os limites máximos de armazenamento, cargas de trabalho, quantidades de índices, documentos e outros objetos dependem do tipo de preço ao qual a Pesquisa do Azure é adicionada, que pode ser **Gratuito**, **Básico** ou **Standard**.

- **Gratuito** é um serviço compartilhado multilocatário fornecido com sua assinatura do Azure. É uma opção sem custo adicional para assinantes existentes que permite experimentar o serviço antes de se inscrever para obter recursos dedicados. 
- **Básico (Preview)** fornece recursos de computação dedicados para cargas de trabalho de produção em uma escala menor. Atualmente, esse tipo está em Preview e é oferecido por uma [taxa 50% reduzida durante o período de Preview](https://azure.microsoft.com/pricing/details/search/).
- **Standard** é executado em máquinas dedicados, com mais capacidade de armazenamento e processamento em cada nível, incluindo a configuração mínima. Standard vem em dois níveis (S1 e S2). 

Todos os tipos podem ser [provisionados no portal](search-create-service-portal.md), com exceção do S2, que exige um tíquete de suporte. Entre em contato com Suporte ou azuresearch_contact@microsoft.com para começar a usar S2.

## Limites de camada

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-tier.md)]

## Limites de armazenamento ##

[AZURE.INCLUDE [azure-search-limits](../../includes/azure-search-limits-storage.md)]

## Limites de dados ##

Recurso|Grátis|Básico (Preview) |S1|S2
---|---|---|---|----
Índice: campos máximos por índice|1000|100 <sup>1</sup>|1000|1000
Índice: máximo de perfis de pontuação por índice|16|16|16|16
Índice: funções máximas por perfil|8|8|8|8
Tamanho do documento por Índice de API<sup>2</sup> em MB|<16|<16|<16|<16
Indexadores: carga de indexação máxima por invocação|10\.000 documentos|Limitado apenas pelo máximo de documentos|Limitado apenas pelo máximo de documentos|Limitado apenas pelo máximo de documentos
Indexadores: tempo de execução máximo|3 minutos|24 horas|24 horas|24 horas
Indexador de blob: tamanho máximo do blob, MB|16|16|128|256
Indexador de blob: número máximo de caracteres de conteúdo extraído de um blob|32\.000|64\.000|4 milhões|4 milhões

<sup>1</sup> O tipo Básico é o único que tem limite inferior de 100 campos por índice.

<sup>2</sup> Tamanho máximo do documento ao chamar uma API do Índice. O tamanho do documento é realmente um limite ao tamanho do corpo da solicitação de API do Índice. Como você pode passar um lote de vários documentos para uma API de uma só vez, o limite de tamanho dependerá de quantos documentos estão no lote. Para um lote com um único documento, o tamanho máximo de documentos será de 16 MB de JSON.

Para reduzir o tamanho do documento, lembre-se de excluir dados não consultáveis da solicitação. Imagens e outros dados binários não podem ser diretamente consultados e não devem ser armazenados no índice. Para integrar dados que não podem ser consultados aos resultados da pesquisa, defina um campo não pesquisável que armazene uma referência uma URL para o recurso.

## Limites de carga de trabalho (consultas por segundo) ##

Recurso|Grátis|Básico (Preview) |S1|S2
---|---|---|---|----
QPS|N/D|~3 por réplica|~15 por réplica|~60 por réplica

QPS (consultas por segundo) é uma aproximação baseada em heurística, usando as cargas de trabalho de cliente real e simulada para derivar os valores estimados. A taxa de transferência de QPS exata irá variar dependendo dos dados e da natureza da consulta.

Embora essas estimativas aproximadas sejam apresentadas acima, é difícil determinar a QPS (consultas por segundo) real, especialmente no serviço Gratuito compartilhado, no qual a taxa de transferência tem base na largura de banda disponível na competição por recursos do sistema. Os recursos de computação e armazenamento que apoiam o serviço compartilhado são compartilhados por vários assinantes; portanto, o QPS de sua solução sempre vai variar conforme o número de outras cargas de trabalho em execução ao mesmo tempo.

No nível padrão, é possível estimar melhor o QPS, porque você tem controle sobre mais parâmetros. Veja a seção de práticas recomendadas em [Gerenciar sua solução de pesquisa](search-manage.md) para obter diretrizes sobre como calcular o QPS para suas cargas de trabalho.

## Limites de Solicitação da API

- Máximo de 16 MB por solicitação <sup>1</sup>
- Comprimento máximo da URL de 8 KB
- Máximo de 1000 documentos por lote de carregamentos, mesclagens ou exclusões de índice
- Máximo de 32 campos na cláusula $orderby
- O tamanho do termo de pesquisa é de 32766 bytes (32 KB menos 2 bytes) de texto codificado em UTF-8

<sup>1</sup> na Pesquisa do Azure, o corpo de uma solicitação está sujeito a um limite superior de 16 MB, impondo um limite prático no conteúdo de campos individuais ou Coleções que, caso contrário, não são limitadas por limites teóricos (consulte [Tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) para obter mais informações sobre restrições e composição de campos).

## Limites de resposta da API

- Máximo de 1000 documentos retornados por página de resultados da pesquisa
- Máximo de 100 sugestões retornadas por solicitação de Sugerir API

## Limites de chave de API

As chaves de API são usadas para autenticação de serviço. Há dois tipos. Chaves de administração são especificadas no cabeçalho da solicitação e concedem acesso completo de leitura/gravação ao serviço. Chaves de consulta são somente leitura, especificadas na URL, e, geralmente, são distribuídas para aplicativos cliente.

- Máximo de duas chaves de administração por serviço
- Máximo de 50 chaves de consulta por serviço

<!---HONumber=AcomDC_0601_2016-->