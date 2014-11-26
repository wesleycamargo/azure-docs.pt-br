<properties title="Search Service: workflow for developers" pageTitle="Search Service: workflow for developers" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Pesquisa do Azure: fluxo de trabalho de desenvolvimento

[WACOM.INCLUDE [Este artigo usa o Portal de Visualização do Azure](../includes/preview-portal-note.md)]

Este artigo oferece um roteiro e algumas das melhores práticas para criar e manter o serviço de pesquisa e seus índices.

Nós presumimos que você já tenha provisionado o serviço. Caso ainda não o tenha feito, consulte [Configurar a pesquisa no Portal de Visualização do Azure][Configurar a pesquisa no Portal de Visualização do Azure] para começar.

-   [Etapa 1: Criar o índice][Etapa 1: Criar o índice]
-   [Etapa 2: Adicionar documentos][Etapa 2: Adicionar documentos]
-   [Etapa 3: Consultar um índice][Etapa 3: Consultar um índice]
-   [Etapa 4: Atualizar ou excluir índices e documentos][Etapa 4: Atualizar ou excluir índices e documentos]
-   [Considerações sobre o design do armazenamento][Considerações sobre o design do armazenamento]

## Etapa 1: Criar o índice

As consultas (pelo menos consultas não sistemáticas) têm como alvo um índice de pesquisa que contenha atributos e dados de pesquisa. Nesta etapa, você define o esquema do índice no formato JSON e executa uma solução PUT HTTPS para que o índice seja criado no serviço.

Normalmente, os índices são codificados no seu ambiente de desenvolvimento local. Não há ferramentas ou editores incorporados para a definição de índice. Para obter mais informações sobre a criação do índice, consulte [Criar índice (API de pesquisa do Azure)][Criar índice (API de pesquisa do Azure)] no MSDN.

## Etapa 2: Adicionar documentos

Após o índice de pesquisa ter sido criado, você pode adicionar documentos a ele POSTando-os no formato JSON. Cada documento deve ter uma chave única e uma coleção de campos contendo dados pesquisáveis e não pesquisáveis. Os dados dos documentos são representados como um conjunto de pares de chaves/valores.

Recomendamos adicionar documentos em lotes para melhorar a produtividade. Você pode criar lotes de até 10.000 documentos, presumindo um tamanho médio de documento de cerca de 1 a 2 KB.

Há um código de status geral para a solicitação POST. Os códigos de status são HTTP 200 (sucesso) ou HTTP 207 (multi-status), caso haja uma cominação de documentos bem-sucedidos em com falha. Além do código de status para a solicitação POST, a Pesquisa do Azure mantém um campo de status para cada documento. Diante do carregamento de um lote, você precisa de um modo de obter o status por documento que indica se a inserção de cada documento foi bem-sucedida ou falhou. O campo de status fornece essas informações. Ele será definido como falso caso haja falha no carregamento do documento.

Diante de grandes cargas, não é incomum que haja algumas falhas no carregamento. Caso isto ocorra, o código de status geral será 207, indicando um sucesso parcial, e os documentos que não forem indexados terão a propriedade de “status” definida como falsa.

> [WACOM.NOTE] Quando o serviço recebe os documentos, eles ficam em fila para indexação e podem não ser incluídos imediatamente nos resultados da pesquisa. Quando não há uma grande carga, normalmente, os documentos são indexados dentro de alguns segundos.

## Etapa 3: Consultar um índice

Após os documentos terem sido indexados, você pode executar consultas de pesquisa. Você pode consultar um índice por vez, usando OData ou uma sintaxe de consulta simples:

-   [Sintaxe de expressão OData para a Pesquisa do Azure][Sintaxe de expressão OData para a Pesquisa do Azure]
-   [Sintaxe de consulta simples na Pesquisa do Azure][Sintaxe de consulta simples na Pesquisa do Azure]

## Etapa 4: Atualizar ou excluir índices e documentos

Você também pode fazer alterações de esquema no índice de pesquisa, atualizar ou excluir documentos de dentro do índice e excluir índices.

Ao atualizar um índice, você pode combinar múltiplas ações (inserir, mesclar, excluir) no mesmo lote, eliminando a necessidade de diversos processos. Atualmente, a Pesquisa do Azure não dá suporte a atualizações parciais (HTTP PATCH), de modo que, se precisar atualizar um índice, você precisará reenviar a definição do índice.

## Considerações sobre o design do armazenamento

Muitos aplicativos de pesquisa usam diversos formatos de armazenamento para as necessidades de diferentes aplicativos. O armazenamento interno que vem com a Pesquisa do Azure deve ser usado para armazenar índices e documentos. A análise de texto depende de ter todos os campos pesquisáveis e os tributos associados prontamente disponíveis.

Nem todos os campos em um documento serão pesquisáveis. Por exemplo, se o seu aplicativo for um catálogo online de música ou vídeos, recomendamos armazenar arquivos binários no Blob ou algum outro armazenamento. Os arquivos binários em si não são pesquisáveis e, portanto, não há necessidade de persistir com eles no armazenamento da Pesquisa do Azure. Você deve armazenar arquivos de imagens, vídeos e áudios em outros serviços ou locais, com um campo no documento armazenando a URL do local do arquivo.

Para obter mais informações sobre a criação de índices ou documentos, consulte [API REST da Pesquisa do Azure][API REST da Pesquisa do Azure].

<!--Anchors--> 
<!--Image references--> 
<!--Link references-->

  [Configurar a pesquisa no Portal de Visualização do Azure]: ../search-configure/
  [Etapa 1: Criar o índice]: #sub-1
  [Etapa 2: Adicionar documentos]: #sub-2
  [Etapa 3: Consultar um índice]: #sub-3
  [Etapa 4: Atualizar ou excluir índices e documentos]: #sub-4
  [Considerações sobre o design do armazenamento]: #sub-5
  [Criar índice (API de pesquisa do Azure)]: http://msdn.microsoft.com/pt-br/library/dn798941.aspx
  [Sintaxe de expressão OData para a Pesquisa do Azure]: http://msdn.microsoft.com/pt-br/library/dn798921.aspx
  [Sintaxe de consulta simples na Pesquisa do Azure]: http://msdn.microsoft.com/pt-br/library/dn798920.aspx
  [API REST da Pesquisa do Azure]: http://msdn.microsoft.com/pt-br/library/dn798935.aspx
