<properties title="Lista de verifica&ccedil;&atilde;o de desempenho e escalabilidade do armazenamento do Microsoft Azure" pageTitle="Lista de verifica&ccedil;&atilde;o de desempenho e escalabilidade do armazenamento do Microsoft Azure" description="Obrigat&oacute;rio" metaKeywords="Optional" services="Optional" solutions="Optional" documentationCenter="Optional" authors="Tamra Myers" videoId="Optional" scriptId="Optional" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/08/2014" ms.author="tamram" />

# Lista de verificação de desempenho e escalabilidade do armazenamento do Microsoft Azure

## Introdução

Desde os serviços de armazenamento do Microsoft Azure, a Microsoft desenvolveu diversas práticas comprovadas para usar esses serviços de modo a obter muito rendimento. Este artigo consolida as práticas mais importantes na forma de uma "lista de verificação". A finalidade deste artigo é ajudar os desenvolvedores de aplicativos a verificar se eles estão usando as práticas comprovadas com o armazenamento do Azure, bem como ajudá-lo a identificar outras práticas comprovadas que eles podem adotar. Este artigo não tem como objetivo cobrir todas as possibilidades de otimização de desempenho e escalabilidade. Aqui, não abordamos as práticas que apresentam pouco impacto ou que não se aplicam em larga escala. Na medida em que é possível prever o comportamento do aplicativo durante a criação, é útil seguir essas práticas desde o início para evitar problemas de desempenho.

Todos os desenvolvedores de aplicativos que usam o armazenamento do Azure devem ler este artigo e verificar se seu aplicativo segue as práticas comprovadas listadas abaixo.

## Lista de verificação

Este artigo organiza as práticas comprovadas nos grupos a seguir. As práticas comprovadas aplicam-se a:

-   Todos os serviços de armazenamento do Azure (blobs, tabelas, filas e arquivos)
-   Blobs
-   Tabelas
-   Filas

O serviço Arquivos do Azure está na fase de visualização. Por isso, as práticas comprovadas dele serão adicionadas ao artigo posteriormente.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Concluído</th>
<th align="left">Área</th>
<th align="left">Categoria</th>
<th align="left">Pergunta</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Metas de escalabilidade</td>
<td align="left"><a href="#subheading1">Seu aplicativo foi criado para evitar a abordagem de metas de escalabilidade?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Rede</td>
<td align="left"><a href="#subheading2">Os dispositivos cliente têm largura de banda suficiente e baixa latência para alcançar o desempenho necessário?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Rede</td>
<td align="left"><a href="#subheading3">Os dispositivos cliente têm um link de alta qualidade?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Rede</td>
<td align="left"><a href="#subheading4">O aplicativo cliente está &quot;próximo&quot; à conta de armazenamento?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Distribuição de conteúdo</td>
<td align="left"><a href="#subheadin5">Você usa um CDN para distribuir conteúdo?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Acesso direto do cliente</td>
<td align="left"><a href="#subheading6">Você usa SAS e CORS para permitir o acesso direto ao armazenamento, em vez de usar um proxy?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Caching</td>
<td align="left"><a href="#subheading7">Seu aplicativo armazena em cache os dados que são usados com frequência e que raramente mudam?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Caching</td>
<td align="left"><a href="#subheading8">Seu aplicativo compila atualizações, armazenando-as em cache no cliente e carregando-as em grandes conjuntos?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Configuração .NET</td>
<td align="left"><a href="#subheading9">Você configurou seu cliente para usar uma quantidade suficiente de conexões simultâneas?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Configuração .NET</td>
<td align="left"><a href="#subheading10">Você configurou o .NET para usar uma quantidade suficiente de threads?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Configuração .NET</td>
<td align="left"><a href="#subheading11">Você usa o .NET 4.5 ou posterior, versões com recurso aprimorado de coleta de lixo?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Paralelismo</td>
<td align="left"><a href="#subheading12">Você garantiu a associação adequada do paralelismo para não carregar as funcionalidades do cliente nem as metas de escalabilidade?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Ferramentas</td>
<td align="left"><a href="#subheading13">Você está usando a última versão das bibliotecas e ferramentas fornecidas pela Microsoft?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Novas tentativas</td>
<td align="left"><a href="#subheading14">Você usa uma política de nova tentativa de retirada exponencial para diminuir os erros e a ocorrência de tempos limite?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Todos os serviços</td>
<td align="left">Novas tentativas</td>
<td align="left"><a href="#subheading15">Seu aplicativo evita novas tentativas para erros que não admitem novas tentativas?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Metas de escalabilidade</td>
<td align="left"><a href="#subheading16">Seu aplicativo segue a meta de largura de banda ou escalabilidade operacional para um único blob?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Cópia de blobs</td>
<td align="left"><a href="#subheading17">Seu método de cópia de blobs é eficiente?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Cópia de blobs</td>
<td align="left"><a href="#subheading18">Você usa o AzCopy para copiar blobs em massa?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Cópia de blobs</td>
<td align="left"><a href="#subheading19">Você usa a função de importação/exportação do Azure para transferir grandes volumes de dados?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Uso de metadados</td>
<td align="left"><a href="#subheading20">Você armazena os metadados sobre blobs usados com frequência?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Carregamento rápido</td>
<td align="left"><a href="#subheading21">Ao tentar carregar um blob rapidamente, você carrega blocos paralelamente?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Carregamento rápido</td>
<td align="left"><a href="#subheading22">Ao tentar carregar muitos blobs rapidamente, você carrega blocos paralelamente?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Blobs</td>
<td align="left">Tipo de blob correto</td>
<td align="left"><a href="#subheading23">Você usa blobs de página ou de bloco quando necessário?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Metas de escalabilidade</td>
<td align="left"><a href="#subheading24">Você leva as metas de escalabilidade em consideração para entidades por segundo?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Configuração</td>
<td align="left"><a href="#subheading25">Você usa JSON para suas solicitações de tabela?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Configuração</td>
<td align="left"><a href="#subheading26">Você desativou o Nagle para melhorar o desempenho de pequenas solicitações?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Tabelas e partições</td>
<td align="left"><a href="#subheading27">Você particionou seus dados corretamente?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Partições mais acessadas</td>
<td align="left"><a href="#subheading28">Você evita padrões do tipo &quot;somente anexar&quot; e &quot;somente incluir&quot;?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Partições mais acessadas</td>
<td align="left"><a href="#subheading29">Suas inserções/atualizações valem para diversas partições?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Escopo da consulta</td>
<td align="left"><a href="#subheading30">Você criou seu esquema para permitir o uso de consultas pontuais na maioria dos casos e de consultas de tabelas raramente?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Densidade da consulta</td>
<td align="left"><a href="#subheading31">As suas consultas geralmente verificam e indicam quais linhas o aplicativo usará?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Limitação dos dados retornados</td>
<td align="left"><a href="#subheading32">Você usa a filtragem para evitar o retorno de entidades que não são necessárias?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Limitação dos dados retornados</td>
<td align="left"><a href="#subheading33">Você usa a projeção para evitar o retorno de propriedades que não são necessárias?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Desnormalização</td>
<td align="left"><a href="#subheading34">Você desnormalizou seus dados a fim de evitar consultas ineficientes sou diversas solicitações de leitura ao tentar obter dados?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Inserção/atualização/exclusão</td>
<td align="left"><a href="#subheading35">Você compila as solicitações que devem ser transacionais ou que podem ser executadas ao mesmo tempo para diminuir a quantidade de viagens de ida e volta?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Inserção/atualização/exclusão</td>
<td align="left"><a href="#subheading36">Você evita novas tentativas nas entidades apenas para determinar se a chamada deve ser inserida ou atualizada?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Inserção/atualização/exclusão</td>
<td align="left"><a href="#subheading37">Você já pensou em armazenar séries de dados que serão recuperados em conjunto frequentemente, em uma única entidade e como propriedades, em vez de serem recuperados como diversas entidades?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Tabelas</td>
<td align="left">Inserção/atualização/exclusão</td>
<td align="left"><a href="#subheading38">Você já pensou em usar blobs no lugar das tabelas para as entidades que serão recuperadas em conjunto e que podem ser escritas em lotes (por exemplo, dados de séries temporais)?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Filas</td>
<td align="left">Metas de escalabilidade</td>
<td align="left"><a href="#subheading39">Você leva as metas de escalabilidade em consideração para mensagens por segundo?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Filas</td>
<td align="left">Configuração</td>
<td align="left"><a href="#subheading40">Você desativou o Nagle para melhorar o desempenho de pequenas solicitações?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Filas</td>
<td align="left">Tamanho da mensagem</td>
<td align="left"><a href="#subheading41">Suas mensagens são compactas para melhorar o desempenho da fila?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Filas</td>
<td align="left">Recuperação em massa</td>
<td align="left"><a href="#subheading41">Você recupera diversas mensagens com uma única operação &quot;Obter&quot;?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Filas</td>
<td align="left">Frequência de votação</td>
<td align="left"><a href="#subheading42">As votações ocorrem com frequência suficiente para reduzir a latência notável do aplicativo?</a></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Filas</td>
<td align="left">Atualização de mensagem</td>
<td align="left"><a href="#subheading43">Você usa a função de atualização de mensagem para armazenar o progresso do processamento de mensagens, evitando a necessidade de reprocessar toda a mensagem em caso de erro?</a></td>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Filas</td>
<td align="left">Arquitetura</td>
<td align="left"><a href="#subheading44">Você usa filas para melhorar a escalabilidade de todo o aplicativo ao manter cargas de trabalho demoradas fora do caminho crítico e escalá-las independentemente?</a></td>
</tr>
</tbody>
</table>

## <a name="allservices"></a>Todos os serviços

Esta seção apresenta as práticas comprovadas aplicáveis ao uso dos serviços de armazenamento do Azure (blobs, tabelas, filas ou arquivos).

### <a name="subheading1"></a>Metas de escalabilidade

Cada serviço de armazenamento do Azure tem metas de escalabilidade para capacidade (GB), taxa de transações e largura de banda. Se o seu aplicativo se aproximar ou ultrapassar alguma das metas de escalabilidade, pode haver aumento da latência e restrição das transações. Quando um serviço de armazenamento restringe seu aplicativo, o serviço começa a apresentar os códigos de erro "503 Server busy" (Servidor ocupado) e "500 Operation timeout" (Tempo limite da operação) para algumas das transações de armazenamento. Esta seção trata da abordagem geral para lidar com as metas de escalabilidade e as metas de escalabilidade da largura de banda. As outras seções que tratam de cada serviço de armazenamento tratam das metas de escalabilidade no contexto de cada serviço:

-   [Largura de banda do blob e solicitações por segundo][Seu aplicativo segue a meta de largura de banda ou escalabilidade operacional para um único blob?]
-   [Entidades de tabela por segundo][Você leva as metas de escalabilidade em consideração para entidades por segundo?]
-   [Mensagens em fila por segundo][Você leva as metas de escalabilidade em consideração para mensagens por segundo?]

#### <a name="sub1bandwidth"></a>Meta de escalabilidade da largura de banda para todos os serviços

No momento da edição, as metas de largura de banda nos EUA para uma conta de GRS (armazenamento com redundância geográfica) são de 10 gigabits por segundo (Gbps) para entrada (dados enviados à conta de armazenamento) e 20 Gbps para saída (dados enviados pela conta de armazenamento). Os limites das contas de LRS (armazenamento com redundância local) são mais altos — 20 Gbps para entrada e 30 Gbps para saída. Os limites de largura de banda internacional podem ser menores e constam na nossa [página de metas de escalabilidade][página de metas de escalabilidade]. Para obter mais informações sobre as opções de redundância em armazenamento, confira a seção [Recursos úteis][Recursos úteis] abaixo.

#### O que fazer ao lidar com uma meta de escalabilidade

Se seu aplicativo estiver lidando com metas de escalabilidade de uma única conta de armazenamento, você pode adotar uma destas abordagens:

-   Repensar a carga de trabalho que faz com que o aplicativo se aproxime da meta de escalabilidade ou a ultrapasse. Você pode alterar o aplicativo para que ele use menos largura de banda, menos capacidade ou menos transações?
-   Se o aplicativo ultrapassar uma das metas de escalabilidade propositalmente, você deve criar diversas contas de armazenamento e particionar os dados do seu aplicativo nessas contas. Se você usar esse padrão, crie o aplicativo de forma que seja possível adicionar mais contas de armazenamento posteriormente, para balancear a carga. No momento da edição, cada assinatura do Azure pode ter até 100 contas de armazenamento. O único custo das contas de armazenamento é o uso dos dados armazenados, das transações feitas ou dos dados transferidos.
-   Se o aplicativo alcançar as metas de largura de banda, você pode compactar os dados no cliente para reduzir a largura de banda necessária para enviar os dados ao serviço de armazenamento. Apesar de economizar a largura de banda e melhorar o desempenho da rede, isso também pode ter impactos negativos. Você deve avaliar o impacto no desempenho causado por essa alteração, devido aos requisitos de processamento adicionais para compactar e descompactar dados no cliente. Além disso, o armazenamento de dados compactados pode dificultar a solução de problemas, pois pode ser mais difícil visualizar os dados armazenados por meio de ferramentas padrão.
-   Se seu aplicativo alcançar as metas de escalabilidade, você deve usar a retirada exponencial para novas tentativas (confira [Novas tentativas][Você usa uma política de nova tentativa de retirada exponencial para diminuir os erros e a ocorrência de tempos limite?]). O mais recomendado é nunca alcançar as metas de escalabilidade, o que é possível garantir por meio de um dos métodos acima. Porém, isso garante que o aplicativo não faça novas tentativas rapidamente, piorando o problema de restrição.

#### Recursos úteis

Os links a seguir apresentam mais detalhes sobre as metas de escalabilidade:

-   Para ver as metas de escalabilidade atuais, acesse a página [Escalabilidade e Metas de Desempenho do Armazenamento do Azure][página de metas de escalabilidade].
-   Você pode aprender mais sobre as opções de redundância de armazenamento na postagem do blog [Windows Azure Storage Redundancy Options and Read Access Geo Redundant Storage][Windows Azure Storage Redundancy Options and Read Access Geo Redundant Storage]– consulte a próxima seção para obter mais detalhes.
-   Para ver informações atualizadas sobre os preços dos serviços Azure, confira [Preços do Azure][Preços do Azure].

### Rede

Embora as chamadas de API sejam importantes, muitas vezes as limitações físicas da rede do aplicativo têm impacto considerável no desempenho. A seção a seguir descreve algumas das limitações que os usuários podem enfrentar.

#### Funcionalidade da rede do cliente

##### <a name="subheading2"></a>Taxa de transferência

No caso da largura de banda, muitas vezes o problema está relacionado às funcionalidades do cliente. Por exemplo, embora uma única conta de armazenamento possa receber um fluxo de 10 Gbps ou mais (confira as [metas de escalabilidade da largura de banda][metas de escalabilidade da largura de banda]), a velocidade da rede em uma instância de função de trabalho "pequena" do Azure só consegue lidar com cerca de 100 Mbps. As instâncias maiores do Azure têm NICs com mais capacidade. Por isso, você pode usar uma instância maior ou mais máquinas virtuais se precisar de limites de rede mais altos em uma única máquina. Se você estiver acessando um serviço de armazenamento em um aplicativo local, a mesma regra é válida: conheça as funcionalidades da rede do dispositivo cliente e a conectividade de rede com o local do armazenamento do Azure e aprimore-as conforme necessário ou desenvolva seu aplicativo para que ele funcione de acordo com suas funcionalidades.

##### <a name="subheading3"></a>Qualidade do vínculo

Como é necessário com qualquer uso de rede, as condições de rede que resultam em erros e na perda de pacote desaceleram a taxa de transferência. Usar WireShark ou NetMon pode ajudar a identificar esse problema.

##### Recursos úteis

Para obter mais informações sobre o tamanho das máquinas virtuais e a largura de banda alocada, confira [Práticas recomendadas para a criação de serviços em larga escala nos serviços de nuvem do Azure][Práticas recomendadas para a criação de serviços em larga escala nos serviços de nuvem do Azure] no MSDN.

#### <a name="subheading4"></a>Location

Em todos os ambientes, colocar o cliente próximo ao servidor proporciona o melhor desempenho. Para acessar o armazenamento do Azure com o mínimo de latência, o melhor local para o cliente é a região na qual o Azure se encontra. Por exemplo, se você tem um site do Azure que usa o armazenamento do Azure, ambos devem estar na mesma região (por exemplo, no oeste dos EUA ou no sudeste asiático). Isso diminui a latência e o custo. No momento da edição, o uso da largura de banda em uma única região é gratuito.

Se os aplicativos do seu cliente não estiverem hospedados no Azure (como no caso de aplicativos para dispositivos móveis ou serviços corporativos), colocar a conta de armazenamento próximo aos dispositivos que a conta acessará geralmente diminui a latência. Se seus clientes estão espalhados por vários lugares (por exemplo, pela América do Norte e pela Europa), você pode usar mais de uma conta de armazenamento: uma localizada na América do Norte e outra na Europa. Isso ajuda a diminuir a latência nas duas regiões. A implementação dessa solução geralmente é mais fácil se os dados armazenados pelo aplicativo são específicos aos usuários e se não é necessário replicar os dados entre as contas de armazenamento. Para uma ampla distribuição de conteúdo, recomendamos o uso de uma CDN. Confira a seção a seguir para saber mais detalhes.

### <a name="subheading5"></a>Distribuição de conteúdo

É possível que um aplicativo precise apresentar o mesmo conteúdo a muitos usuários (por exemplo, um vídeo para demonstrar um produto, apresentado na página inicial de um site) espalhados na mesma região ou em diversas regiões. Nesse cenário, você deve usar uma CDN (rede de distribuição de conteúdo), como o Azure CDN. Por sua vez, o CDN deve usar o armazenamento do Azure como fonte dos dados. Diferente das contas de armazenamento do Azure que existem em uma única região e não podem fornecer conteúdos com baixa latência a outras regiões, a CDN do Azure usa servidores em diversos data centers pelo mundo. Além disso, a CDN geralmente tem limites de entrada muito maiores do que as contas de armazenamento individuais.

Para obter mais informações, confira [CDN do Azure][CDN do Azure].

### <a name="subheading6"></a>Usando SAS e CORS

Quando precisar autorizar código como JavaScript no navegador da Web ou aplicativo móvel de um usuário a fim de acessar os dados no armazenamento do Azure, uma das saídas é usar um aplicativo com função web de proxy: o dispositivo do usuário autentica com a função web que, por sua vez, autentica com o serviço de armazenamento. Dessa forma, você pode evitar a exposição das chaves de conta de armazenamento em dispositivos que não são seguros. No entanto, isso gera uma grande sobrecarga na função web porque todos os dados transferidos entre os dispositivos do usuário e o serviço de armazenamento devem ser transmitidos por meio de uma função web. Você pode evitar o uso da função web como um proxy para o serviço de armazenamento. Para isso, use SAS (assinaturas de acesso compartilhado), que podem ser combinadas a cabeçalhos de CORS (compartilhamento de recursos entre origens). Por meio das SAS, você pode permitir que o dispositivo do usuário faça solicitações diretamente a um serviço de armazenamento por meio de um token de acesso limitado. Por exemplo, se o usuário quiser carregar uma foto no seu aplicativo, sua função web pode gerar e enviar um token SAS que conceda permissão de edição a um blob ou contêiner específico pela próxima meia hora ao dispositivo do usuário. Após esse período, o token expira.

Normalmente, o navegador não permite o uso de JavaScript em páginas hospedadas por um site em um domínio para executar operações especificas, como um "PUT" em outro domínio. Por exemplo, se você hospedar uma função web em "contosomarketing.cloudapp.net" e quiser usar o JavaScript do cliente para carregar um blob em sua conta de armazenamento, em "contosoproducts.blob.core.windows.net", a "política de mesma origem” proibirá a operação. O CORS é um recurso do navegador que permite que o domínio alvo (neste caso, a conta de armazenamento) comunique-se com o navegador ao qual confia as solicitações que se originam no domínio de origem (neste caso, a função web).

As duas tecnologias podem ajudar você a evitar cargas e gargalos desnecessários no seu aplicativo Web.

#### Recursos úteis

Para obter mais informações sobre SAS, confira [Assinaturas de acesso compartilhado, Parte 1: Noções básicas sobre o modelo SAS][Assinaturas de acesso compartilhado, Parte 1: Noções básicas sobre o modelo SAS].

Para obter mais informações sobre o CORS, confira [Suporte a CORS (Compartilhamento de recursos entre origens) para os serviços de armazenamento do Azure][Suporte a CORS (Compartilhamento de recursos entre origens) para os serviços de armazenamento do Azure] no MSDN.

### Caching

#### <a name="subheading7"></a>Obtenção de dados

Em geral, obter os dados de um serviço uma única vez é melhor do que obtê-los duas vezes. Tome como exemplo um aplicativo Web MVC em execução em uma função web que já recuperou um blob de 50 MB do serviço de armazenamento para apresentar ao usuário como conteúdo. O aplicativo pode recuperar esse mesmo blob sempre que o usuário o solicitar, ou pode armazená-lo em cache localmente e reutilizar esse conteúdo em outras solicitações dos usuários. Além disso, sempre que um usuário solicitar os dados, o aplicativo pode emitir um GET com um cabeçalho condicional para tempo de modificação, o que evita obter todo o bob se ele não foi modificado. Você pode aplicar esse mesmo padrão ao trabalho com entidades em tabela.

Em alguns casos, você pode determinar que seu aplicativo parta do pressuposto de que o blob permanece válido por um curto período após a recuperação e que, durante esse período, o aplicativo não precisa verificar se o blob foi modificado.

O armazenamento em cache é ótimo para configurações, pesquisas e outros dados que são sempre usados pelo aplicativo.

Para ver como obter as propriedades de um blob e descobrir a data da última modificação por meio de .NET, confira [Definição e recuperação de propriedades e metadados][Definição e recuperação de propriedades e metadados] no MSDN. Para obter mais informações sobre downloads condicionais, confira [Atualização condicional de uma cópia local de um blob][Atualização condicional de uma cópia local de um blob] no MSDN.

#### <a name="subheading8"></a>Carregamento de dados em lotes

Em algumas situações nos aplicativos, é possível agregar dados localmente e carregá-los em lotes em vez de carregar cada dado imediatamente. Por exemplo, um aplicativo web por manter um log das atividades: o aplicativo pode enviar os detalhes de cada atividade assim que elas acontecem, como uma entidade de tabela (que requer muitas operações de armazenamento), ou pode salvar os detalhes da atividade em um arquivo de log local para carregar os detalhes de todas as atividades periodicamente, como um arquivo delimitado, em um blob. Se cada entrada do log tiver 1 KB, você pode carrega milhares de transações de uma só vez (é possível carregar um blob para transações com até 64 MB). Se a máquina local falhar antes do carregamento, você provavelmente perderá parte dos dados do log: o desenvolvedor do aplicativo deve precaver-se para a possibilidade de solicitações do cliente ou falhas no carregamento. Se for necessário carregar os dados das atividades para timespans (não apenas por atividade), os blobs são mais indicados do que as tabelas.

### Configuração .NET

Esta seção é útil para quem usa o .NET Framework, pois lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho. Se estiver usando outras linguagens, verifique se conceitos parecidos aplicam-se à linguagem escolhida.

#### <a name="subheading9"></a>Aumentar o limite da conexão padrão

No .NET, o código a seguir aumenta o limite da conexão padrão (que geralmente é de 2 em um ambiente cliente ou 10 em um ambiente servidor) ara 100. Você deve definir o valor para aproximar-se à quantidade de threads usada pelo seu aplicativo.

    ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  

Você deve definir o limite da conexão antes de abrir as conexões.

No caso de outras linguagens de programação, confira a documentação da linguagem para verificar como definir o limite da conexão.

Para obter mais informações, confira a postagem [Web Services: Concurrent Connections][Web Services: Concurrent Connections] (Serviços Web: Conexões simultâneas) no MSDN.

#### <a name="subheading10"></a>Aumentar o mínimo de threads ThreadPool Min Threads em caso de uso de código síncrono por meio do Async Tasks

Este código aumenta o mínimo de threads ThreadPool:

    ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  

Para obter mais informações, confira [Método ThreadPool.SetMinThreads][Método ThreadPool.SetMinThreads] no MSDN.

#### <a name="subheading11"></a>Aproveite o recurso Coleta de lixo do .NET 4.5

Use a versão 4.5 ou posterior do .NET para que o aplicativo cliente aproveite as melhorias de desempenho na coleta de lixo do servidor.

Para obter mais informações, confira o artigo [Uma visão geral dos aprimoramentos de desempenho no .NET 4.5][Uma visão geral dos aprimoramentos de desempenho no .NET 4.5] no MSDN.

### <a name="subheading12"></a>Paralelismo não associado

Embora o paralelismo possa ser ótimo para o desempenho, tenha cuidado ao usar o paralelismo não associado (no qual não há limites para a quantidade de threads e/ou de solicitações paralelas) para carregar ou baixar dados, usando diversas funções de trabalho para acessar diversas partições (contêineres, filas ou tabelas) na mesma conta de armazenamento ou para acessar diversos itens na mesma partição. Se o paralelismo não estiver associado, o aplicativo pode ultrapassar as funcionalidades do dispositivo cliente ou as metas de escalabilidade da conta de armazenamento, o que resulta em latências mais longas e restrições.

### <a name="subheading13"></a>Ferramentas e bibliotecas cliente para armazenamento

Sempre use a última versão das bibliotecas e ferramentas fornecidas pela Microsoft. No momento da edição, é possível usar bibliotecas RTM disponíveis para .NET, Windows Phone, Windows Runtime e Java, além de bibliotecas de visualização em outras linguagens, como node.js e C++. Além disso, a Microsoft liberou cmdlets do PowerShell e ferramentas de linha de comando de plataforma cruzada desenvolvidas no Node.js para garantir a compatibilidade com o armazenamento do Azure. A Microsoft desenvolve essas ferramentas pesando no desempenho, além de mantê-las atualizadas com as últimas versões do serviço e garantir que elas gerenciem muitas das práticas de desempenho comprovadas internamente.

### Novas tentativas

#### <a name="subheading14"></a>Restrição/servidor ocupado

Em alguns casos, o serviço de armazenamento pode restringir seu aplicativo ou simplesmente não conseguir atender uma solicitação devido a alguma condição transitória, e retornar uma mensagem "503 Server busy" ou "500 Timeout". Isso pode acontecer se o aplicativo estiver alcançando alguma das metas de escalabilidade ou se o sistema estiver balanceando os dados particionados a fim de aumentar a taxa de transferência. O aplicativo cliente geralmente deve tentar executar novamente a operação que causa o erro em questão: uma nova tentativa da mesma solicitação pode obter sucesso. No entanto, se o serviço de armazenamento estiver restringindo o aplicativo porque ele ultrapassou as metas de escalabilidade ou porque o serviço não pôde atender a solicitação por algum motivo, novas tentativas geralmente piorarão o problema. Por esse motivo, você deve usar uma retirada exponencial (as bibliotecas cliente são o padrão para esse comportamento). Por exemplo, o aplicativo pode fazer uma nova tentativa em 2 segundos, 4 segundos, 10 segundos e 30 segundos para então abandonar o processo. Esse comportamento faz com que o aplicativo diminua consideravelmente a sua carga no serviço, em vez de agravar qualquer problema.

Pode haver novas tentativas imediatas em caso de erros de conectividade porque esses erros não são resultado de restrições e devem ser temporários.

#### <a name="subheading15"></a>Erros que não admitem novas tentativas

As bibliotecas cliente sabem quais erros admitem novas tentativas ou não. No entanto, se você está escrevendo o seu próprio código com base na API REST de armazenamento, lembre-se de que alguns erros não devem ter novas tentativas: Por exemplo, uma resposta 400 (Solicitação incorreta) indica que o aplicativo cliente enviou uma solicitação que não pôde ser processada porque não tinha o formato esperado. O reenvio dessa solicitação sempre resultará na mesma resposta, por isso as novas tentativas não são úteis. Se você está escrevendo o seu próprio código com base na API REST de armazenamento, conheça o significado de alguns erros e saiba o método adequado de fazer uma nova tentativa ou não para cada um deles.

#### Recursos úteis

Para obter mais informações sobre os códigos de erro de armazenamento, confira [Status e códigos de erro][Status e códigos de erro] no site do Microsoft Azure.

## Blobs

Além das práticas comprovadas para [todos os serviços][todos os serviços] descritos, as práticas comprovadas a seguir aplicam-se especificamente ao serviço de blob.

### Metas de escalabilidade específicas do blob

#### <a name="subheading16"></a>Largura de banda e operações por blob

Você pode ler ou editar um único blob a no máximo 60 MB/segundo, o que equivale a 480 Mbps e ultrapassa a capacidade de muitas redes dos clientes, inclusive do NIC físico no dispositivo do cliente. Além disso, um único blob comporta 500 solicitações por segundo. Se vários dos seus clientes precisarem ler os mesmo blob e você talvez ultrapasse esses limites, é possível usar uma CDN para distribuir o blob.

Para obter informações sobre a taxa de transferência alvo para os blobs, confira [Escalabilidade e metas de desempenho do armazenamento do Azure][página de metas de escalabilidade] no MSDN.

### Copiando e movendo blobs

#### <a name="subheading17"></a>Copiar blob

A versão 2012-02-12 da API REST de armazenamento permite copiar os blobs entre as contas: um aplicativo cliente pode instruir o serviço de armazenamento a copiar um blob de outra origem (possivelmente de outra conta de armazenamento) e permitir que o serviço faça a cópia de modo assíncrono. Isso pode diminuir consideravelmente a largura de banda necessária para o aplicativo quando você estiver migrando dados de outras contas de armazenamento, porque você não precisará baixar nem carregar dados.

No entanto, não é possível garantir em quanto tempo a cópia será concluída, ao se realizar cópias entre contas de armazenamento. Se o aplicativo precisar copiar um bob completo rapidamente com a sua supervisão, pode ser melhor baixar o blob para uma máquina virtual e carregá-lo no destino. Para que seja possível prever toda a situação, a cópia deve ser feita por uma máquina virtual em execução na mesma região do Azure, ou as condições da rede podem (e provavelmente irão) afetar o desempenho da cópia. Além disso, você pode monitorar o progresso de uma cópia assíncrona de modo programático.

As cópias da mesma conta de armazenamento geralmente são rápidas.

Para obter mais informações, confira [Copiar blob][Copiar blob] no MSDN.

#### <a name="subheading18"></a>Usar AzCopy

A equipe de armazenamento do Azure lançou a ferramenta de linha de comando "AzCopy", que deve ajudar na transferência em massa de muitos blobs para, de e entre as contas de armazenamento. Essa ferramenta foi otimizada para esse cenário e pode alcançar altas taxas de transferência. Ela deve ser usada para baixar e carregar itens, bem como para copiar cenários, em massa. Você pode saber mais sobre ela e baixá-la [aqui][aqui].

#### <a name="subheading19"></a>Serviço de importação/exportação do Azure

Para grandes volumes de dados (superiores a 1 TB), o armazenamento do Azure oferece o serviço de importação e exportação, que permite o carregamento e o download do armazenamento do blob por meio de discos rígidos. Você pode colocar os dados em um disco rígido e enviá-lo à Microsoft para que possamos carregar esses dados ou enviar um disco rígido vazio para que baixemos os dados. Saiba mais sobre isso [aqui][2]. Essa ação pode ser muito mais eficiente do que o envio/carregamento desse volume de dados pela rede.

### <a name="subheading20"></a>Uso de metadados

O serviço do blob oferece suporte às principais solicitações, o que pode incluir metadados sobre o blob. Por exemplo, se o aplicativo precisar remover os dados EXIF de uma fotografia, é possível recuperá-la e extrair esses dados. Para economizar a largura de banda e melhora o desempenho, seu aplicativo pode armazenar dados EXIF nos metadados do blob quando o aplicativo carregar a foto: Assim você pode recuperar os dados EXIF nos metadados, usando apenas uma solicitação HEAD, poupando a largura de banda e processando o tempo necessário para extrair os dados EXIF sempre que o blob estiver pronto. Isso é útil quando você precisa apenas dos metadados, não do conteúdo completo do bob. Observe que é possível armazenar apenas 8 KB de metadados por blob. O serviço não aceita solicitações para armazenar volumes maiores. Se esse tamanhão não for suficiente, talvez não seja possível usar essa abordagem.

Para ver como obter os metadados de um blob por meio de .NET, confira [Definição e recuperação de propriedades e metadados][Definição e recuperação de propriedades e metadados] no MSDN.

### Carregamento rápido

Para carregar os blobs com rapidez, a primeira pergunta a responder é: Você está carregando ou um diversos blobs? Use as informações abaixo para identificar o método de uso correto de acordo com o seu cenário.

#### <a name="subheading21"></a>Carregamento rápido de um blob grande

Para carregar um único blob grande com rapidez, seu aplicativo cliente deve carregar os blocos ou as páginas em paralelo, levando em consideração as metas de escalabilidade para cada blob e a conta de armazenamento como um todo. As bibliotecas de cliente de armazenamento RTM fornecidas pela Microsoft (.NET e Java) têm a capacidade de fazer isso. Para cada uma das bibliotecas, use a propriedade/o objeto especificado abaixo para definir o nível de concorrência:

-   .NET: Defina ParallelOperationThreadCount em um objeto BlobRequestOptions a ser usado.
-   Java/Android: Use BlobRequestOptions.setConcurrentRequestCount()
-   Node.js: Use parallelOperationThreadCount nas opções da solicitação ou no serviço do blob.
-   C++: Use o método blob\_request\_options::set\_parallelism\_factor.

#### <a name="subheading22"></a>Carregamento rápido de diversos blobs

Para carregar diversos blobs com rapidez, carregue-os paralelamente. Esse tipo de carregamento é mais rápido do que o carregamento de um único blob por vez com carregamento paralelo de blocos porque a carga é dividida em diversas partições do serviço de armazenamento. Cada blob suporta uma taxa de transferência de 60 MB/segundo (aproximadamente, 480 Mbps). No momento da edição, uma conta GRS suporta até 20 Gbps de entrada, valor muito superior à entrada suportada por um único blob. O [AzCopy][Você usa o AzCopy para copiar blobs em massa?] executa os carregamentos em paralelo por padrão e é a opção recomendada nesse caso.

### <a name="subheading23"></a>Escolhendo o tipo de blob certo

O armazenamento do Azure suporta dois tipos de blobs: blobs de *página* e de *bloco*. Em um determinado cenário de uso, o tipo de blob escolhido afeta o desempenho e a escalabilidade da solução. Os blobs de bloco são a escolha certa para carregar grandes quantidades de dados com eficiência: Por exemplo, um aplicativo cliente pode precisar carregar fotos ou vídeo em um armazenamento de blob. Os blobs de página são indicados se o aplicativo precisar fazer edições aleatórias nos dados: Por exemplo, os VHDs do Azure são armazenados como blobs de página.

Para obter mais informações, confira [Noções básicas sobre blobs de bloco e blobs de página][Noções básicas sobre blobs de bloco e blobs de página] no MSDN.

## Tabelas

Além das práticas comprovadas para [todos os serviços][todos os serviços] descritos, as práticas comprovadas a seguir aplicam-se especificamente ao serviço de tabela.

### <a name="subheading24"></a>Metas de escalabilidade específicas da tabela

Além das limitações da largura de banda de toda uma conta de armazenamento, as tabelas têm o limite de escalabilidade descrito a seguir. Observe que o sistema balanceia a carga conforme o tráfego aumento, mas se houver um pico de tráfego repentino, é possível que você não obtenha esse volume de taxa de transferência imediatamente. Se você normalmente presencia esses picos, haverá restrições e/ou eventos de tempo limite durante esses picos, pois o serviço de armazenamento balanceia a carga da tabela automaticamente. Os aumentos graduais geralmente apresentam resultados melhores, pois permitem que o sistema tenha tempo para balancear a carga corretamente.

#### Entidades por segundo (conta)

O limite de escalabilidade para o acesso às tabelas é de até 20 mil entidades (1 KB para cada) por segundo em cada conta. Em geral, cada entidade inserida, atualizada, excluída ou verificada é computada nessa meta. Assim, uma inserção em lote com 100 entidades é computada como 100 entidades. Uma consulta que verifica 1000 entidades e retorna apenas 5 é computada como 1000 entidades.

#### Entidades por segundo (partição)

Em uma única partição, a meta de escalabilidade para o acesso às tabelas é de 2000 entidades (1 KB para cada) por segundo, usando a mesma contagem descrita na seção anterior.

### Configuração

Esta seção lista diversas configurações rápidas que você pode usar para fazer melhorias significativas no desempenho do serviço de tabela:

#### <a name="subheading25"></a>Use JSON

A partir da versão 2013-08-15 do serviço de armazenamento, o serviço de armazenamento é compatível com JSON, em vez do formato AtomPub baseado em XML, para transferir dados de tabela. Isso pode reduzir o tamanho da carga em até 75% e pode melhorar consideravelmente o desempenho do seu aplicativo.

Para obter mais informações, confira a postagem [Tabelas do Microsoft Azure: Introducing JSON][Tabelas do Microsoft Azure: Introducing JSON] (Tabelas do Microsoft Azure: Apresentando JSON) e [Formato de carga para operações do serviço Tabela][Formato de carga para operações do serviço Tabela] no MSDN.

#### <a name="subheading26"></a>Desativação do Nagle

O algoritmo de Nagle é implementado largamente em redes TCP/IP como meio de melhorar o desempeno das redes. No entanto, ele não é ideal em todas as circunstâncias (como em ambientes altamente interativos). No caso do armazenamento do Azure, o algoritmo do Nagle tem um impacto negativo sobre o desempenho das solicitações para a tabela e os serviços de fila. Por isso, desabilite-o, se possível.

Para obter mais informações, confira a postagem [Nagle’s Algorithm is Not Friendly towards Small Requests][Nagle’s Algorithm is Not Friendly towards Small Requests] (O algoritmo de Nagle não é adequado para solicitações pequenas) no nosso blog, que explica por que esse algoritmo tem baixo desempenho na interação com solicitações de tabela e fila, além de mostrar como desabilitá-lo no seu aplicativo cliente.

### Esquema

Como você representa e consulta os seus dados é o maior fator único que afeta o desempenho do serviço de tabela. Embora cada aplicativo seja único, esta seção descreve algumas práticas gerais comprovadas relacionadas:

-   Ao design da tabela
-   A consultas eficientes
-   A atualizações de dados eficientes

#### <a name="subheading27"></a>Tabelas e partições

As tabelas são divididas em partições. Cada entidade armazenada em uma partição compartilha a mesma chave de partição e tem uma chave de linha exclusiva que a identifica dentro da partição em questão. As partições trazem benefícios, mas também criam limites de escalabilidade.

-   Benefícios: Você pode atualizar as entidades em uma única transação atômica e em lote que contenha até 100 operações de armazenamento independentes (limite total de 4 MB). Partindo do pressuposto de que a mesma quantidade de entidades pode ser recuperada, você também pode consultar dados em uma única partição com mais eficiência do que a consulta a dados presentes em diversas partições (consulte as demais recomendações sobre consulta aos dados das tabelas).
-   Limite de escalabilidade: O acesso às entidades armazenadas em uma única partição não pode passar por balanceamento de carga porque as partições suportam transações atômicas em lote. Por esse motivo, a meta de escalabilidade de cada partição da tabela é menor do que a meta do serviço de tabela como um todo.

Devido a essas características, você deve adotar estes princípios de design:

-   Os dados atualizados ou consultados com frequência por seu aplicativo cliente na mesma unidade de trabalho lógica devem constar na mesma partição. Isso pode acontecer porque seu aplicativo agrega edições ou porque você que aproveitar a vantagem das operações atômicas em lote. Além disso, é possível consultar os dados em uma única partição com mais eficiência do que fazer a consulta em diversas partições.
-   Os dados não inseridos/atualizados ou consultados com frequência por seu aplicativo cliente na mesma unidade de trabalho lógica (consulta única ou atualização em lote) devem constar em partições diferentes. Uma observação importante é que não há limite de chaves de partição para uma única tabela. Por isso, ter milhões de chaves de partição não representa um problema e não influencia o desempenho. Por exemplo, se seu aplicativo é um site popular que requer o logon dos usuários, usar a ID do usuário como chave de partição pode ser uma boa opção.

#### Partições mais acessadas

As partições mais acessadas são aquelas que recebem um porcentual desproporcional do tráfego de uma conta e não podem passar pelo balanceamento de carga porque se trata de uma única partição. Em geral, essas partições são criadas de duas formas:

##### <a name="subheading28"></a>Padrões "somente anexar" e "somente incluir"

No padrão "somente anexar", todo (ou praticamente todo) o tráfego atribuído a um determinado PK aumenta e diminui de acordo com o momento. Um exemplo é se o aplicativo usado na data funciona como chave de partição para os dados do log. Isso faz com que todas as inserções vão para a última partição da sua tabela. O sistema não pode balancear a carga porque todas as edições vão em direção ao final da tabela. Se o volume do tráfego para a partição em questão ultrapassar a meta de escalabilidade na partição, teremos restrições como resultado. É melhor garantir que o tráfego seja enviado para diversas partições, a fim de garantir o balanceamento da carga das solicitações na tabela.

##### <a name="subheading29"></a>Dados com alto tráfego

Se você estiver particionando resultados de esquemas em uma única partição que tem dados mais usados do os dados presentes nas demais partições, também pode haver restrições, pois a partição em questão aproxima-se da meta de escalabilidade de uma única partição. É melhor garantir que seu esquema de partição não faça com que nenhuma partição específica aproxime-se às metas de escalabilidade.

#### Consultas

Esta seção descreve as práticas comprovadas de consulta do serviço de tabela.

##### <a name="subheading30"></a>Escopo da consulta

Há diversas maneiras de especificar quais entidades devem ser consultadas. A seguir, tratamos do uso de cada uma delas.

Em geral, evite as verificações (consultas cujo escopo tem mais de uma entidade). No entanto, se a verificação for necessária, tente organizar os dados de modo que as verificações recuperem os dados necessários sem verificar ou retornar grandes quantidades de entidades desnecessárias.

###### Consultas pontuais

A consulta recupera exatamente uma entidade. Ela faz isso por meio da especificação da chave de partição e da chave de linha da entidade a ser recuperada. Essas consultas são muito eficientes e você deve usá-las sempre que possível.

###### Consultas às partições

Esse tipo de consulta recupera um conjunto de dados que tem uma chave de partição em comum. Geralmente, a consulta especifica diversos valores de chave de linha ou valores de propriedade de entidade, além de uma chave de partição. Elas são menos eficientes do que as consultas pontuais e devem ser usados com critério.

###### Consultas às tabelas

Esse tipo de consulta recupera um conjunto de entidades que não tem uma chave de partição em comum. Essas consultas não são eficientes e você deve evitá-las sempre que possível.

##### <a name="subheading31"></a>Densidade da consulta

Outro fator importante na eficiência da consulta é a quantidade de entidades retornadas, em comparação à quantidade de entidades verificadas para localizar o conjunto retornado. Se o aplicativo ficar uma consulta de tabela filtrando por um valor de propriedade comum a apenas 1% dos dados, a consulta verificará 100 entidades para cada entidade retornada. As metas de escalabilidade da tabela mencionadas anteriormente estão relacionadas à quantidade de entidades verificadas, e não à quantidade de entidades retornadas: uma baixa de densidade de consulta pode fazer com que o serviço de tabela cause restrições no seu aplicativo com facilidade — o que acontece porque é necessário verificar muitas entidades para recuperar a identidade que você está procurando. Confira a seção abaixo sobre [desnormalização][Você desnormalizou seus dados a fim de evitar consultas ineficientes sou diversas solicitações de leitura ao tentar obter dados?] para obter mais informações sobre como evitar isso.

##### Limitação da quantidade de dados retornados

###### <a name="subheading32"></a>Filtragem

Quando souber que uma consulta retornará entidades desnecessárias no aplicativo cliente, você pode usar um filtro para diminui a quantidade de entradas retornadas. Embora as entidades não retornadas para o cliente ainda sejam computadas nos limites de escalabilidade, o desempenho do aplicativo melhora devido à redução no tamanho da carga da rede e na quantidade de entidades que seu aplicativo cliente deve processar. Confira a observação acima sobre a [densidade das consultas][As suas consultas geralmente verificam e indicam quais linhas o aplicativo usará?], mas lembre-se de que as metas de escalabilidade estão relacionadas à quantidade de entidades verificadas. Por isso, as consultas que filtram muitas entidades podem resultar em restrições, mesmo se poucas entidades forem retornadas.

###### <a name="subheading33"></a>Projeção

Se seu aplicativo clientes precisar apenas de um conjunto limitado de propriedades das entidades da sua tabela, você pode usar a projeção para limitar o tamanho do conjunto de dados retornado. Como no caso da filtragem, isso ajuda a diminuir a carga da rede e o processamento do cliente.

##### <a name="subheading34"></a>Desnormalização

Diferente do que acontece com os bancos de dados relacionados, as práticas recomentadas para consultar os dados da tabela com eficiência causam a desnormalização dos dados. Ou seja, a duplicação dos mesmos dados em diversas entidades (uma para cada chave, que você pode usar para localizar os dados) para minimizar a quantidade de entidades a serem verificadas por uma consulta para localizar os dados necessários para o cliente, em vez de precisar verificar muitas entidades para localizar os dados necessários para o aplicativo. Por exemplo, em um site de e-commerce, você pode querer localizar um pedido pela ID do clientes (apresentar os pedidos de um determinado cliente) e por data (apresentar os pedidos de uma determinada data). No armazenamento de tabela, é melhor armazenar a entidade (ou fazer referência a ela) duas vezes, uma com o nome da tabela, PK e RK para facilitar a localização da ID do cliente e outra para facilitar a localização por data.

#### Inserção/atualização/exclusão

Esta seção descreve as práticas comprovadas para modificar as entidades armazenadas no serviço de tabela.

##### <a name="subheading35"></a>Envio em lote

As transações em lote são conhecidas como ETG (transações do grupo de entidades) no armazenamento do Azure. Todas as operações em uma ETG devem constar em uma única partição e em uma única tabela. Quando possível, use as ETGs para inserir, atualizar e excluir em lotes. Isso diminui a quantidade de viagens de ida e volta entre o aplicativo cliente e o servidor, diminui a quantidade de transações computáveis (uma ETG conta como uma única transação e pode conter até 100 operações de armazenamento) e viabiliza atualizações atômicas (nas ETGs, todas as operações têm êxito ou falham). Os ambientes com altos níveis de latência, como os dispositivos móveis, veem muitos benefícios no uso das ETGs.

##### <a name="subheading36"></a>Upsert

Use as operações de tabela **Upsert** sempre que possível. Há dois tipos de **Upsert** e os dois podem ser mais eficientes do que as operações tradicionais **Insert** e **Update**:

-   **InsertOrMerge**: Use essa operação quando quiser carregar um subconjunto de propriedades da entidade, mas não souber se a entidade já existe. Se a entidade existir, essa chamada atualiza as propriedades incluídas na operação **Upsert** e deixa todas as propriedades existente como elas se encontram. Se a entidade não existir, a operação insere a nova entidade. Isso se parece com o uso de projeção em uma consulta, pois você só precisa carregar as propriedades que estão mudando.
-   **InsertOrReplace**: Use essa operação quando quiser carregar uma entidade completamente nova, mas não souber se a entidade já existe. Você só deve usar essa opção quando souber que a entidade carregada está perfeita, pois ela substitui completamente a entidade anterior. Por exemplo, você quer atualizar a entidade que armazena um local atual do usuário independente de o aplicativo já ter armazenado os dados de local do usuário. A nova entidade de localização está completa e você não precisa de informações de nenhuma entidade anterior.

##### <a name="subheading37"></a>Armazenamento de séries de dados em uma única entidade

Às vezes, um aplicativo armazena uma série de dados necessárias para recuperar tudo de uma só vez: Por exemplo, um aplicativo pode acompanhar o uso de CPU para criar um gráfico sobre os dados nas últimas 24 horas. Uma abordagem é ter uma entidade de tabela por hora, com cada uma delas representando uma hora específica e armazenando o uso da CPU para a hora em questão. Para obter esses dados, o aplicativo precisa recuperar as entidades que contêm os dados das últimas 24 horas.

Como alternativa, o aplicativo pode armazenar o uso da CPU para cada hora como uma propriedade independente em uma única entidade: Para atualizar cada hora, seu aplicativo usa uma única chamada **InsertOrMerge Upsert**, a fim de atualizar o valor da última hora. Para obter os dados, o aplicativo precisa recuperar apenas uma entidade, em vez de 24, resultando em uma consulta muito eficiente (confira acima a seção sobre o [escopo da consulta][Você criou seu esquema para permitir o uso de consultas pontuais na maioria dos casos e de consultas de tabelas raramente?]).

##### <a name="subheading38"></a>Armazenando dados estruturados em blobs

Às vezes parece que os dados estruturados devem ser dispostos em tabelas, mas os intervalos das entidades sempre são recuperados em conjunto e podem ser inseridos em lote. Um bom exemplo disso são os arquivos de log. Nesse caso, você pode considerar diversos minutos de log como um lote, inseri-los e recuperar diversos minutos de uma só vez. Nesse caso, para o desempenho, é melhor usar blobs do que tabelas, pois assim é possível diminuir consideravelmente a quantidade de objetos escritos/retornados, além de diminuir a quantidade de solicitações que geralmente devem ser feitas.

## Filas

### <a name="subheading39&quot;"></a>Scalability Limits

A single queue can process approximately 2,000 messages (1KB each) per second (each AddMessage, GetMessage, and DeleteMessage count as a message here). If this is insufficient for your application, you should use multiple queues and spread the messages across them.

You can view the current scalability targets on the page [Azure Storage Scalability and Performance Targets][página de metas de escalabilidade] on MSDN.

### <a name="subheading40&quot;"></a>Desativação do Nagle

Consulte a seção de configuração da tabela que fala sobre o algoritmo de Nagle. O uso desse algoritmo geralmente não é uma boa opção para o desempenho das solicitações de fila, e você deve desabilitá-lo.

### <a name="subheading41&quot;"></a>Message Size

Queue performance and scalability decreases as message size increases. You should place only the information the receiver needs in a message.

### <a name="subheading42&quot;"></a>Recuperação em lote

Você pode recuperar até 32 mensagens de uma fila em uma única operação. Isso pode diminuir a quantidade de viagens de ida e volta de um aplicativo cliente, o que é útil principalmente para ambientes com alta latência, como os dispositivos móveis.

### <a name="subheading43&quot;"></a>Queue Polling Interval

Most applications poll for messages from a queue, which can be one of the largest sources of transactions for that application. Select your polling interval wisely: polling too frequently could cause your application to approach the scalability targets for the queue. However, at 200,000 transactions for $0.01 (at the time of writing), a single processor polling once every second for a month would cost less than 15 cents so cost is not typically a factor that affects your choice of polling interval.

For up to date cost information, see [Storage Pricing Details][Storage Pricing Details].

### <a name="subheading44&quot;"></a>UpdateMessage

Você pode usar **UpdateMessage** para aumentar o tempo limite da invisibilidade ou atualizar as informações de estado de uma mensagem. Embora isso seja útil, lembre-se de que a operação **UpdateMessage** é computada na meta de escalabilidade. No entanto, essa abordagem pode ser muito mais eficiente do que ter um fluxo de trabalho que transmite uma tarefa de uma fila para a outra, pois cada etapa da tarefa é concluída. O uso da operação **UpdateMessage** permite que o aplicativo salve o estado da tarefa na mensagem e continue trabalhando, em vez de colocar a mensagem na fila novamente para a próxima etapa a cada etapa concluída.

Para obter mais informações, confira o artigo [Como: Alterar o conteúdo de uma mensagem na fila][Como: Alterar o conteúdo de uma mensagem na fila].

### \<a name=subheading45"\>\</a\>Arquitetura do aplicativo\</h3\> \<p\>Você deve usar filas para que a arquitetura do aplicativo seja escalonável. A seguir temos algumas maneiras de usar filas para que seu aplicativo seja mais escalonável:\</p\> \<ul\> \<li\>Você pode usar as filas para criar listas de pendências de trabalho para processamento e suavização das cargas de trabalho no seu aplicativo. Por exemplo, você pode colocar em fila as solicitações does usuários para execução de trabalhos que requerem muito do processador, como o redimensionamento das imagens carregadas.\</li\> \<li\>Você pode usar as filas para desassociar parte do aplicativo, a fim de poder escaloná-las de forma independente. Por exemplo, um front-end da Web pode colocar os resultados de pesquisa dos usuários em uma fila para análise posterior e armazenamento. Você pode adicionar mais instâncias de função de trabalho para processar os dados da fila conforme necessário.\</li\> \</ul\> \<h2 id="conclusion"\>Conclusão\</h2\> \<p\>Este artigo falou sobre algumas das práticas comprovadas mais comuns para otimizar o desempenho com o uso do armazenamento do Azure. Nós recomendamos que cada desenvolvedor avalie seu aplicativo com base nas práticas descritas acima e considere seguir as recomendações para obter desempenho excelente para seus aplicativos que usam o armazenamento do Azure.\</p\>

  [Você usa uma política de nova tentativa de retirada exponencial para diminuir os erros e a ocorrência de tempos limite?]: #subheading14
  [Seu aplicativo segue a meta de largura de banda ou escalabilidade operacional para um único blob?]: #subheading16
  [Você usa o AzCopy para copiar blobs em massa?]: #subheading18
  [Você leva as metas de escalabilidade em consideração para entidades por segundo?]: #subheading24
  [Você criou seu esquema para permitir o uso de consultas pontuais na maioria dos casos e de consultas de tabelas raramente?]: #subheading30
  [As suas consultas geralmente verificam e indicam quais linhas o aplicativo usará?]: #subheading31
  [Você desnormalizou seus dados a fim de evitar consultas ineficientes sou diversas solicitações de leitura ao tentar obter dados?]: #subheading34
  [Você leva as metas de escalabilidade em consideração para mensagens por segundo?]: #subheading39
  [página de metas de escalabilidade]: http://msdn.microsoft.com/pt-br/library/azure/dn249410.aspx
  [Recursos úteis]: #sub1useful
  [Windows Azure Storage Redundancy Options and Read Access Geo Redundant Storage]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx
  [Preços do Azure]: http://azure.microsoft.com/pt-br/pricing/overview/
  [metas de escalabilidade da largura de banda]: #sub1bandwidth
  [Práticas recomendadas para a criação de serviços em larga escala nos serviços de nuvem do Azure]: http://msdn.microsoft.com/pt-br/library/dn197896.aspx
  [CDN do Azure]: http://azure.microsoft.com/pt-br/services/cdn/
  [Assinaturas de acesso compartilhado, Parte 1: Noções básicas sobre o modelo SAS]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-shared-access-signature-part-1/
  [Suporte a CORS (Compartilhamento de recursos entre origens) para os serviços de armazenamento do Azure]: http://msdn.microsoft.com/library/azure/dn535601.aspx
  [Definição e recuperação de propriedades e metadados]: http://msdn.microsoft.com/pt-br/library/hh225342.aspx
  [Atualização condicional de uma cópia local de um blob]: http://msdn.microsoft.com/pt-br/library/azure/dd179371.aspx
  [Web Services: Concurrent Connections]: http://blogs.msdn.com/b/darrenj/archive/2005/03/07/386655.aspx
  [Método ThreadPool.SetMinThreads]: http://msdn.microsoft.com/pt-br/library/system.threading.threadpool.setminthreads(v=vs.110).aspx
  [Uma visão geral dos aprimoramentos de desempenho no .NET 4.5]: http://msdn.microsoft.com/pt-br/magazine/hh882452.aspx
  [Status e códigos de erro]: http://msdn.microsoft.com/pt-br/library/azure/dd179382.aspx
  [todos os serviços]: #allservices
  [Copiar blob]: http://msdn.microsoft.com/pt-br/library/dd894037.aspx
  [aqui]: http://azure.microsoft.com/pt-br/documentation/articles/storage-use-azcopy/
  [2]: http://azure.microsoft.com/pt-br/documentation/articles/storage-import-export-service/
  [Noções básicas sobre blobs de bloco e blobs de página]: http://msdn.microsoft.com/pt-br/library/azure/ee691964.aspx
  [Tabelas do Microsoft Azure: Introducing JSON]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/05/windows-azure-tables-introducing-json.aspx
  [Formato de carga para operações do serviço Tabela]: http://msdn.microsoft.com/pt-br/library/azure/dn535600.aspx
  [Nagle’s Algorithm is Not Friendly towards Small Requests]: http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx
  [Storage Pricing Details]: http://azure.microsoft.com/pt-br/pricing/details/storage/
  [Como: Alterar o conteúdo de uma mensagem na fila]: http://azure.microsoft.com/pt-br/documentation/articles/storage-dotnet-how-to-use-queues/#change-contents
