---
title: 'Exemplo: Introdução – API do Serviço de Exploração de Conhecimento'
titlesuffix: Azure Cognitive Services
description: Use a API do KES (Serviço de Exploração de Conhecimento) para criar um mecanismo para uma experiência de pesquisa interativa em publicações acadêmicas.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: sample
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: e2678200e2f8c55111e53ab0a341804fd17623a3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57994946"
---
# <a name="get-started-with-the-knowledge-exploration-service"></a>Introdução ao Serviço de Exploração de Conhecimento

Neste passo a passo, você usa o KES (Serviço de Exploração de Conhecimento) para criar o mecanismo para uma experiência de pesquisa interativa para publicações acadêmicas. É possível instalar a ferramenta de linha de comando, [`kes.exe`](CommandLine.md), e todos os arquivos de exemplo do [SKD do Serviço de Exploração de Conhecimento](https://www.microsoft.com/en-us/download/details.aspx?id=51488).

O exemplo de publicações acadêmicas contém uma amostra de 1000 artigos acadêmicos publicados por pesquisadores da Microsoft.  Cada artigo está associado a um título, ano de publicação, autores e palavras-chave. Cada autor é representado por uma ID, nome e afiliação no momento da publicação. Cada palavra-chave pode ser associada a um conjunto de sinônimos (por exemplo, a palavra-chave "máquina de vetores de suporte" pode ser associada ao sinônimo "svm").

## <a name="define-the-schema"></a>Definir o esquema

O esquema descreve a estrutura de atributos dos objetos no domínio. Isso especifica o nome e o tipo de dados para cada atributo em um formato de arquivo JSON. O exemplo a seguir é o conteúdo do arquivo *Academic.schema*.

```json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Aqui, você define *Título*, *Ano* e *Palavra-chave* como cadeia de caracteres, inteiro e atributo de cadeia de caracteres, respectivamente. Como os autores são representados por ID, nome e afiliação, você define *Autor* como um atributo de composição com três subatributos: *Author.Id*, *Author.Name* e *Author.Affiliation*.

Por padrão, os atributos dão suporte a todas as operações disponíveis para o tipo de dados, incluindo *equals*, *starts_with* e *is_between*. Como a ID do autor é usada apenas internamente como um identificador, substitua o padrão e especifique *equals* como somente operação indexada.

Para o atributo *Palavra-chave* permita que os sinônimos correspondam aos valores da palavra-chave canônica especificando o arquivo de sinônimo *Keyword.syn* na definição de atributo. Este arquivo contém uma lista de pares de valores canônicos e sinônimos:

```json
...
["support vector machine","support vector machines"]
["support vector machine","support vector networks"]
["support vector machine","support vector regression"]
["support vector machine","support vector"]
["support vector machine","svm machine learning"]
["support vector machine","svm"]
["support vector machine","svms"]
["support vector machine","vector machine"]
...
```

Para obter informações adicionais sobre a definição do esquema, consulte [Formato de Esquema](SchemaFormat.md).

## <a name="generate-data"></a>Gerar dados

O arquivo de dados descreve a lista de publicações a serem indexadas, com cada linha especificando os valores de atributos de um papel no [formato JSON](https://json.org/).  O exemplo a seguir é uma única linha do arquivo de dados *Academic.data*, formatado para legibilidade:

```
...
{
    "logprob": -16.714,
    "Title": "the world wide telescope",
    "Year": 2001,
    "Author": [
        {
            "Id": 717694024,
            "Name": "alexander s szalay",
            "Affiliation": "microsoft"
        },
        {
            "Id": 2131537204,
            "Name": "jim gray",
            "Affiliation": "microsoft"
        }
    ]
}
...
```

Nesse snippet de código, você especifica o atributo *Título* e *Ano* do artigo como uma cadeia de caracteres e um número JSON, respectivamente. Vários valores são representados usando matrizes JSON. Como *Autor* é um atributo composto, cada valor é representado usando um objeto JSON que consiste em seus subatributos. Atributos com valores não encontrados, como *palavra-chave* nesse caso, podem ser excluídos da representação JSON.

Para diferenciar a probabilidade de artigos diferentes, especifique a probabilidade relativa de log, usando o atributo *logprob* interno. Dada uma probabilidade *p* entre 0 e 1, você calcula a probabilidade de log como log(*p*), onde log() é a função de log natural.

Para obter mais informações, consulte [Formato de Dados](DataFormat.md).

## <a name="build-a-compressed-binary-index"></a>Compilar um índice binário compactado

Após ter um arquivo de esquema e um arquivo de dados, você poderá compilar um índice binário compactado dos objetos de dados, usando [`kes.exe build_index`](CommandLine.md#build_index-command). Neste exemplo, você compila o arquivo de índice*Academic.index* a partir do arquivo de esquema de entrada *Academic.schema* e arquivo de dados *Academic.data*. Use o seguinte comando:

`kes.exe build_index Academic.schema Academic.data Academic.index`

Para prototipagem rápida fora do Azure, [`kes.exe build_index`](CommandLine.md#build_index-command) é possível compilar pequenos índices localmente, a partir de arquivos de dados contendo até 10.000 objetos. Para arquivos de dados maiores, é possível executar o comando de dentro de uma [VM do Windows no Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) ou executar uma compilação remota no Azure. Para obter detalhes, confira Como escalar verticalmente.

## <a name="use-an-xml-grammar-specification"></a>Use uma especificação de gramática XML

A gramática especifica o conjunto de consultas de linguagem natural que o serviço pode interpretar, bem como a forma como essas consultas de linguagem natural são traduzidas em expressões de consulta semântica. Neste exemplo, você usa a gramática especificada em *academic.xml*:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="Academic.schema" name="academic"/>

  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>

    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>

    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>

      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>

        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>

        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>

  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="lt" name="year"/>
          </item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item>
            <attrref uri="academic#Year" op="gt" name="year"/>
          </item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```

Para obter mais informações sobre a sintaxe de especificação gramatical, consulte [Formato de Gramática](GrammarFormat.md).

## <a name="compile-the-grammar"></a>Compilar a gramática

Após ter uma especificação de gramática XML, será possível compilá-la em uma gramática binária, usando [`kes.exe build_grammar`](CommandLine.md#build_grammar-command). Observe que, se a gramática importar um esquema, o arquivo de esquema precisará estar localizado no mesmo caminho que o XML de gramática. Neste exemplo, você compila o arquivo de gramática binária *Academic.grammar* a partir do arquivo de gramática XML de entrada *Academic.xml*. Use o seguinte comando:

`kes.exe build_grammar Academic.xml Academic.grammar`

## <a name="host-the-grammar-and-index-in-a-web-service"></a>Hospede a gramática e o índice em um serviço Web

Para prototipagem rápida, é possível hospedar a gramática e o índice em um serviço Web no computador local, usando [`kes.exe host_service`](CommandLine.md#host_service-command). É possível acessar o serviço por meio de [APIs da Web](WebAPI.md) para validar a correção de dados e o design da gramática. Neste exemplo, você hospeda o arquivo de gramática *Academic.grammar* e o arquivo de índice *Academic.index* em `http://localhost:8000/`. Use o seguinte comando:

`kes.exe host_service Academic.grammar Academic.index --port 8000`

Isso inicia uma instância local do serviço Web. É possível testar o serviço de forma interativa, visitando `http::localhost:<port>` de um navegador. Para obter mais informações, confira Testando o serviço.

Você também pode chamar diretamente várias [APIs da Web](WebAPI.md) para testar a interpretação de linguagem natural, a conclusão de consultas, a avaliação de consultas estruturadas e a computação de histogramas. Para parar o serviço, digite "quit" no prompt de comando `kes.exe host_service` ou pressione Ctrl+C. Estes são alguns exemplos:

* [http://localhost:8000/interpret?query=papers by susan t dumais](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20dumais)
* [http://localhost:8000/interpret?query=papers by susan t d&complete=1](http://localhost:8000/interpret?query=papers%20by%20susan%20t%20d&complete=1)
* [http://localhost:8000/evaluate?expr=Composite(Author.Name=='susan t dumais')&attributes=Title,Year,Author.Name,Author.Id&count=2](http://localhost:8000/evaluate?expr=Composite%28Author.Name==%27susan%20t%20dumais%27%29&attributes=Title,Year,Author.Name,Author.Id&count=2)
* [http://localhost:8000/calchistogram?expr=And(Composite(Author.Name=='susan t dumais'),Year>=2013)&attributes=Year,Keyword&count=4](http://localhost:8000/calchistogram?expr=And%28Composite%28Author.Name=='susan%20t%20dumais'%29,Year>=2013%29&attributes=Year,Keyword&count=4)

Fora do Azure, [`kes.exe host_service`](CommandLine.md#host_service-command) é limitado a índices de até 10.000 objetos. Outros limites incluem uma taxa de API de 10 solicitações por segundo e um total de 1.000 solicitações antes do término automático do processo. Para ignorar essas restrições, execute o comando em uma [VM do Windows no Azure](../../../articles/virtual-machines/windows/quick-create-portal.md) ou implante em um serviço de nuvem do Azure usando o comando [`kes.exe deploy_service`](CommandLine.md#deploy_service-command). Para obter detalhes, confira Implantando o serviço.

## <a name="scale-up-to-host-larger-indices"></a>Escalar verticalmente para hospedar índices maiores

Quando estiver executando `kes.exe` fora do Azure, o índice será limitado a 10.000 objetos. É possível compilar e hospedar índices maiores usando o Azure. Inscreva-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). Como alternativa, se você for um assinante do Visual Studio ou MSDN, poderá [ativar os benefícios do assinante](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Eles oferecem alguns créditos do Azure por mês.

Para permitir o acesso `kes.exe` a uma conta do Azure, [baixe o arquivo de Configurações de Publicação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) do portal do Azure. Se solicitado, entre na conta do Azure. Salve o arquivo como *AzurePublishSettings.xml* no diretório de trabalho de onde `kes.exe` está em execução.

Há duas maneiras de compilar e hospedar grandes índices. A primeira é preparar os arquivos de esquema e dados em uma VM do Windows no Azure. Em seguida, execute `kes.exe build_index` para compilar o índice localmente na VM, sem restrições de tamanho. O índice resultante pode ser hospedado localmente na VM usando `kes.exe host_service` para prototipagem rápida, novamente sem restrições. Para obter etapas detalhadas, consulte o [tutorial da VM do Azure](../../../articles/virtual-machines/windows/quick-create-portal.md).

O segundo método é executar uma compilação remota do Azure, usando [`kes.exe build_index`](CommandLine.md#build_index-command) com o parâmetro `--remote`. Isso especifica um tamanho de VM do Azure. Quando o parâmetro `--remote` é especificado, o comando cria uma VM do Azure temporária desse tamanho. Em seguida, ele cria o índice na VM, carrega o índice no armazenamento de blobs de destino e exclui a VM após a conclusão. Sua assinatura do Azure será cobrada pelo custo da VM, enquanto o índice estiver sendo criado.

Esse recurso de compilação remota do Azure permite que [`kes.exe build_index`](CommandLine.md#build_index-command) seja executado em qualquer ambiente. Quando você estiver executando uma compilação remota, o esquema de entrada e os argumentos de dados poderão ser caminhos de arquivos locais ou URLs de [armazenamento de blob do Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). O argumento do índice de saída deve ser uma URL de armazenamento de blob. Para criar uma conta de armazenamento do Azure, consulte [Sobre as contas de armazenamento do Azure](../../storage/common/storage-create-storage-account.md). Para copiar arquivos eficientemente para o armazenamento de blobs, use o utilitário [AzCopy](../../storage/common/storage-use-azcopy.md).

Neste exemplo, você pode assumir que o seguinte contêiner de armazenamento de blob já foi criado:http://&lt;*account*&gt;.blob.core.windows.net/&lt;*container*&gt;/. Ele contém o esquema *Academic.schema*, o arquivo de sinônimos referenciado *Keywords.syn* e o arquivo de dados em grande escala *Academic.full.data*. É possível compilar o índice completo remotamente, usando o seguinte comando:

`kes.exe build_index http://<account>.blob.core.windows.net/<container>/Academic.schema http://<account>.blob.core.windows.net/<container>/Academic.full.data http://<account>.blob.core.windows.net/<container>/Academic.full.index --remote <vm_size>`

Observe que pode levar de 5 a 10 minutos para provisionar uma VM temporária para build de índice. Para prototipagem rápida, é possível:
- Desenvolva com um conjunto de dados menor localmente em qualquer computador.
- Manualmente [crie uma VM do Azure](../../../articles/virtual-machines/windows/quick-create-portal.md), [conecte-a ](../../../articles/virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) via Área de Trabalho Remota, instale o [SDK de Serviço de Exploração de Conhecimento](https://www.microsoft.com/en-us/download/details.aspx?id=51488) e execute [`kes.exe`](CommandLine.md) dentro da VM.

A paginação diminui o processo de compilação. Para evitar a paginação, use uma VM com três vezes a quantidade de RAM que o tamanho do arquivo de dados de entrada para a compilação do índice. Use uma VM com 1 GB a mais de RAM que o tamanho do índice para hospedagem. Para obter uma lista de tamanhos de VM disponíveis, consulte [Tamanhos para máquinas virtuais](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

## <a name="deploy-the-service"></a>Implantar o serviço

Após ter uma gramática e um índice, você estará pronto para implantar o serviço em um serviço de nuvem do Azure. Para criar um novo serviço de nuvem do Azure, consulte [Como criar e implantar um serviço de nuvem](../../../articles/cloud-services/cloud-services-how-to-create-deploy-portal.md). Não especifique um pacote de implantação neste momento.  

Ao criar o serviço de nuvem, será possível usar [`kes.exe deploy_service`](CommandLine.md#deploy_service-command) para implantar o serviço. Um serviço de nuvem do Azure tem dois slots de implantação: produção e armazenamento processo de preparo. Para um serviço que recebe tráfego de usuário ativo, você deve inicialmente implantar no slot de teste. Aguarde o serviço iniciar e autoinicializar. Em seguida, será possível enviar algumas solicitações para validar a implantação e verificar se passa nos testes básicos.

[Alterne](../../../articles/cloud-services/cloud-services-nodejs-stage-application.md) o conteúdo do slot de teste com o slot de produção, para que o tráfego dinâmico seja direcionado agora para o serviço implantado recentemente. É possível repetir esse processo ao implantar uma versão atualizada do serviço com novos dados. Como ocorrer com todos os outros serviços de nuvem do Azure, você pode usar o portal do Azure para configurar o [dimensionamento automático](../../../articles/cloud-services/cloud-services-how-to-scale-portal.md).

Neste exemplo, você implanta o índice *Academic* no slot de teste de um serviço de nuvem existente com VMs *<vm_size>* VMs. Use o seguinte comando:

`kes.exe deploy_service http://<account>.blob.core.windows.net/<container>/Academic.grammar http://<account>.blob.core.windows.net/<container>/Academic.index <serviceName> <vm_size> --slot Staging`

Para obter uma lista de tamanhos de VM disponíveis, consulte [Tamanhos para máquinas virtuais](../../../articles/virtual-machines/virtual-machines-windows-sizes.md).

Após implantar o serviço, você poderá chamar as várias [APIs da Web](WebAPI.md) para testar a interpretação de linguagem natural, a conclusão de consultas, a avaliação de consultas estruturadas e a computação de histogramas.  

## <a name="test-the-service"></a>Teste o serviço

Para depurar um serviço dinâmico, navegue até o computador host a partir de um navegador da Web. Para obter um serviço local implantado por meio de host_service, visite `http://localhost:<port>/`.  Para obter um serviço de nuvem do Azure implantado por meio de deploy_service, visite `http://<serviceName>.cloudapp.net/`.

Esta página contém um link para informações sobre estatísticas básicas de chamadas à API, bem como a gramática e o índice hospedados nesse serviço. Esta página também contém uma interface de pesquisa interativa que demonstra o uso das APIs da Web. Insira as consultas na caixa de pesquisa para ver os resultados das chamadas à API [interpret](interpretMethod.md), [evaluate](evaluateMethod.md) e [calchistogram](calchistogramMethod.md). A fonte HTML subjacente desta página também serve como um exemplo de como integrar as APIs da Web em um aplicativo, para criar uma experiência de pesquisa avançada e interativa.


