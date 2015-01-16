<properties title="Search Service: workflow for developers" pageTitle="Serviço de pesquisa: fluxo de trabalho para desenvolvedores" description="Serviço de pesquisa: fluxo de trabalho para desenvolvedores" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Pesquisa do Azure: fluxo de trabalho de desenvolvimento

[WACOM.INCLUDE [This article uses the Azure Preview portal](../includes/preview-portal-note.md)]

Este artigo oferece um roteiro e algumas das melhores práticas para criar e manter o serviço de pesquisa e seus índices. 

Nós presumimos que você já tenha provisionado o serviço. Se você ainda não tiver feito isso, consulte [Introdução à Pesquisa do Azure](../search-get-started/) para obter mais instruções.

+ [Etapa 1: Criar o índice](#sub-1)
+ [Etapa 2: Adicionar documentos](#sub-2)
+ [Etapa 3: Consultar um índice](#sub-3)
+ [Etapa 4: Atualizar ou excluir índices e documentos](#sub-4)
+ [Considerações sobre o design do armazenamento](#sub-5)


<h2 id="sub-1">Etapa 1: Criar o índice</h2>

As consultas são voltadas para um índice de pesquisa que contém dados e atributos de pesquisa. Dessa forma, a primeira etapa após o provisionamento do serviço é definir o esquema de índice no formato JSON e executar uma solicitação HTTPS PUT para criar o índice no serviço. 

Os índices são criados pelo código do aplicativo. Não existem ferramentas integradas ou editores para ajudá-lo a definir um índice em uma interface do usuário. Exemplos que demonstram várias maneiras de construir o índice incluem [Criar sua primeira solução de pesquisa usando a Pesquisa do Azure](../search-create-first-solution/), em que o esquema é especificado no arquivo Program.cs, e [Introdução a perfis de pontuação na Pesquisa do Azure](../search-get-started-scoring-profiles), que fornece o índice em um arquivo de esquema JSON independente. Para saber mais sobre a criação do índice, consulte [Criar índice (API de pesquisa do Azure)](http://msdn.microsoft.com/pt-br/library/dn798941.aspx) no MSDN.

<h2 id="sub-2">Etapa 2: Adicionar documentos</h2>

Após o índice de pesquisa ter sido criado, você pode adicionar documentos a ele POSTando-os no formato JSON. Cada documento deve ter uma chave única e uma coleção de campos contendo dados pesquisáveis e não pesquisáveis. Os dados dos documentos são representados como um conjunto de pares de chaves/valores para cada campo.

Recomendamos adicionar documentos em lotes para melhorar a produtividade. Você pode criar lotes de até 1.000 documentos, presumindo um tamanho médio de documento de cerca de 1 a 2 KB.

Há um código de status geral para a solicitação POST. Os códigos de status são HTTP 200 (sucesso) ou HTTP 207 (multi-status), caso haja uma cominação de documentos bem-sucedidos em com falha. Além do código de status para a solicitação POST, a Pesquisa do Azure mantém um campo de status para cada documento. Diante do carregamento de um lote, você precisa de um modo de obter o status por documento que indica se a inserção de cada documento foi bem-sucedida ou falhou. O campo de status fornece essas informações. Ele será definido como falso caso haja falha no carregamento do documento.

Diante de grandes cargas, não é incomum que haja algumas falhas no carregamento. Caso isto ocorra, o código de status geral será 207, indicando um sucesso parcial, e os documentos que não forem indexados terão a propriedade de "status" definida como falsa.

> [WACOM.NOTE] Quando o serviço recebe os documentos, eles ficam em fila para indexação e podem não ser incluídos imediatamente nos resultados da pesquisa. Quando não há uma grande carga, normalmente, os documentos são indexados dentro de alguns segundos.


<h2 id="sub-3">Etapa 3: Consultar um índice</h2>

Após os documentos terem sido indexados, você pode executar consultas de pesquisa. Você pode consultar um índice por vez, usando OData ou uma sintaxe de consulta simples:

+	[Sintaxe de expressão OData para a Pesquisa do Azure](http://msdn.microsoft.com/pt-br/library/dn798921.aspx)
+	[Sintaxe de consulta simples na Pesquisa do Azure](http://msdn.microsoft.com/pt-br/library/dn798920.aspx)

<h2 id="sub-4">Etapa 4: Atualizar ou excluir índices e documentos</h2>

Você também pode fazer alterações de esquema no índice de pesquisa, atualizar ou excluir documentos de dentro do índice e excluir índices.

Ao atualizar um índice, você pode combinar múltiplas ações (inserir, mesclar, excluir) no mesmo lote, eliminando a necessidade de diversos processos. Atualmente, a Pesquisa do Azure não dá suporte a atualizações parciais (HTTP PATCH), de modo que, se precisar atualizar um índice, você precisará reenviar a definição do índice.

<h2 id="sub-5">Considerações sobre o design do armazenamento</h2>

A Pesquisa do Azure usa o armazenamento interno para os índices e documentos usados em operações de pesquisa. A análise de texto e índice depende de ter todos os campos pesquisáveis e os tributos associados prontamente disponíveis.

Nem todos os campos em um documento serão pesquisáveis. Por exemplo, se o seu aplicativo for um catálogo online de música ou vídeos, recomendamos armazenar arquivos binários no serviço BLOB do Azure ou algum outro formato de armazenamento. Os arquivos binários em si não são pesquisáveis e, portanto, não há necessidade de persistir com eles no armazenamento da Pesquisa do Azure. Embora você deva armazenar imagens, vídeos e arquivos de áudio em outros serviços ou locais, deve incluir um campo que faça referência ao URL para o local do arquivo. Dessa forma, você pode retornar os dados externos como parte dos resultados da pesquisa. 

Para obter mais informações sobre a criação de índices ou documentos, consulte [API REST da Pesquisa do Azure](http://msdn.microsoft.com/pt-br/library/dn798935.aspx).


<!--Anchors-->
[Etapa 1: Criar o índice]: #sub-1
[Etapa 2: Adicionar documentos]: #sub-2
[Etapa 3: Consultar um índice]: #sub-3
[Etapa 4: Atualizar ou excluir índices e documentos]: #sub-4
[Escolher um armazenamento de documentos]: #sub-5


<!--Image references-->

<!--Link references-->
[Introdução à Pesquisa do Azure]: ../search-get-started/
[Gerenciar seu serviço de pesquisa no Microsoft Azure]: ../search-manage/
[Crie sua primeira solução de pesquisa usando a Pesquisa do Azure]: ../search-create-first-solution/

