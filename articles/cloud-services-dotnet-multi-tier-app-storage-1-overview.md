<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" pageTitle="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" metaKeywords="Azure tutorial, Azure storage tutorial, Azure multi-tier tutorial, MVC Web Role tutorial, Azure worker role tutorial, Azure blobs tutorial, Azure tables tutorial, Azure queues tutorial" description="Learn how to create a multi-tier app using ASP.NET MVC and Azure. The app runs in a cloud service, with web role and worker roles, and uses Azure storage tables, queues, and blobs." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Azure Cloud Service Tutorial: ASP.NET MVC Web Role, Worker Role, Azure Storage Tables, Queues, and Blobs" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="cloud-services" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tdykstra,riande" />

# Tutorial do Serviço de Nuvem do Azure: Função Web do ASP.NET, Função de Trabalho e Tabelas de Armazenamento do Azure, Filas e Blobs - 1 de 5

Esta série de tutoriais mostra como criar e implantar um aplicativo Web MVC do ASP.NET multicamadas que é executado em um serviço de nuvem do Azure e usa tabelas de armazenamento, filas e blobs do Azure. Você pode baixar [o aplicativo completo][o aplicativo completo] a partir da Galeria de código do MSDN ou um [livro eletrônico][livro eletrônico] de uma versão mais recente a partir da Galeria de livro eletrônico TechNet.

Veja a seguir este diagrama que mostra como as partes de um aplicativo interagem:

![Processamento de mensagem de email][Processamento de mensagem de email]

Esta é uma série do tutorial de 5 partes. Se quiser uma introdução mais rápida e simples aos serviços de nuvem, consultas e blobs, consulte [Introdução aos serviços de nuvem do Azure e ao ASP.NET][Introdução aos serviços de nuvem do Azure e ao ASP.NET]. Como alternativa, você pode executar um aplicativo de multi-camada em sites e Trabalhos Web para obter mais informações, consulte [Introdução ao SDK de Trabalhos Web do Azure][Introdução ao SDK de Trabalhos Web do Azure].

Nesta série de tutoriais você aprenderá:

-   Como habilitar seu computador para desenvolvimento do Azure ao instalar o SDK do Azure.
-   Como criar um projeto de nuvem do Visual Studio com uma função web MVC do ASP.NET e duas funções de trabalho.
-   Como publicar o projeto de nuvem em um serviço de nuvem do Azure.
-   Como usar o serviço de armazenamento da fila do Azure na comunicação entre níveis ou funções de trabalho.
-   Como usar o serviço de armazenamento de tabela do Azure como um armazenamento de dados altamente dimensionável para dados estruturados, não relacionais.
-   Como usar o serviço Blob do Azure para armazenar arquivos na nuvem.
-   Como exibir e editar tabelas, filas e blobs do Azure usando o Gerenciador de Servidores do Visual Studio.
-   Como usar o SendGrid para enviar emails.
-   Como configurar o rastreamento e exibir dados de rastreamento.
-   Como dimensionar um aplicativo aumentando o número de instâncias da função de trabalho.

## <a name="toc"></a>Tutoriais da série

Existem cinco tutoriais das séries:

1.  **Introdução ao aplicativo de serviço de email do Azure** (este tutorial). Uma visão em profundidade do aplicativo e sua arquitetura. Você pode pular isso, se quiser apenas ver como implantar ou se quiser ver o código, e você pode voltar aqui depois para compreender melhor a arquitetura.
2.  [Configurando e implantando o aplicativo de serviço de email do Azure][Configurando e implantando o aplicativo de serviço de email do Azure]. Como baixar o aplicativo de exemplo, configurar, testar localmente, implantar e testar na nuvem.
3.  [Compilando a função web para o aplicativo de serviço de email do Azure][Compilando a função web para o aplicativo de serviço de email do Azure]. Como compilar os componentes MVC do aplicativo e testá-los localmente.
4.  [Compilando a função de trabalho A (agendador de email) para o aplicativo de serviço de email do Azure][Compilando a função de trabalho A (agendador de email) para o aplicativo de serviço de email do Azure]. Como criar o componente de back-end que cria a fila de itens de trabalho para o envio de emails e testá-lo localmente.
5.  [Compilando a função de trabalho B (remetente de email) para o aplicativo de serviço de email do Azure][Compilando a função de trabalho B (remetente de email) para o aplicativo de serviço de email do Azure]. Como criar o componente de back-end que processa a fila de itens de trabalho para o envio de emails e testá-lo localmente.

## Segmentos deste tutorial

-   [Pré-requisitos][Pré-requisitos]
-   [Visão geral do front-end][Visão geral do front-end]
-   [Visão geral do back-end][Visão geral do back-end]
-   [Tabelas do Azure][Tabelas do Azure]
-   [Filas do Azure][Filas do Azure]
-   [Diagrama de dados][Diagrama de dados]
-   [Blobs do Azure][Blobs do Azure]
-   [Serviço de Nuvem do Azure versus Site do Azure][Serviço de Nuvem do Azure versus Site do Azure]
-   [Custo][Custo]
-   [Autenticação e autorização][Autenticação e autorização]
-   [Próximas etapas][Próximas etapas]

## Pré-requisitos

As instruções nestes tutoriais funcionam para os seguintes produtos:

-   Visual Studio 2013 com Atualização 2
-   Visual Studio 2013 Express para Web com Atualização 2

Você também precisa de uma assinatura do Azure. Você pode criar uma [conta de avaliação gratuita][conta de avaliação gratuita] ou [ativar os benefícios de assinante MSDN][ativar os benefícios de assinante MSDN].

## <a name="frontend"></a>Visão geral do front-end

O aplicativo é um serviço da lista de email. O front-end inclui páginas da Web que os administradores do serviço usam para gerenciar listas de email.

(As capturas de tela mostram o estilo do modelo do Visual Studio 2012; o conteúdo é o mesmo para o Visual Studio 2013, mas o estilo é diferente.)

![Página de índice da lista de endereçamento][Página de índice da lista de endereçamento]

![Página do índice do assinante][Página do índice do assinante]

Há também um conjunto de páginas que os administradores usam para criar as mensagens para serem enviadas para uma lista de emails.

![Página de índice de mensagem][Página de índice de mensagem]

![Página de criação de mensagem][Página de criação de mensagem]

Os clientes do serviço são empresas que oferecem aos clientes a oportunidade de inscrever-se para obter uma lista de distribuição no site do cliente. Por exemplo, um administrador configura uma lista para anúncios do Departamento de História da Contoso University. Quando um aluno interessado em anúncios do Departamento de História clica em um link no site da Contoso University, a Contoso University faz uma chamada de serviço Web para o aplicativo de serviço de email do Azure. O método de serviço faz com que um email seja enviado para o cliente. Esse email contém um hiperlink, e quando o destinatário clica no link, é exibida uma página de boas-vindas ao cliente para a lista de anúncios do Departamento de História.

![Email de confirmação][Email de confirmação]

![Bem-vindo à página de lista][Bem-vindo à página de lista]

Cada email enviado (exceto a confirmação de inscrição) inclui um hiperlink que pode ser usado para cancelar a inscrição. Se um destinatário clica no link, uma página da Web solicita a confirmação da intenção do cancelamento da inscrição.

![Página de confirmação do cancelamento da inscrição][Página de confirmação do cancelamento da inscrição]

Se o destinatário clicar no botão **Confirmar**, uma página é exibida confirmando que a pessoa foi removida da lista.

![Página de confirmação do cancelamento da inscrição][1]

## <a name="backend"></a>Visão geral do back-end

O front-end armazena listas e mensagens de email a serem enviadas para eles nas tabelas do Azure. Quando um administrador agenda uma mensagem a ser enviada, uma linha da tabela contendo a data agendada e outros dados, como a linha de assunto, é adicionada à tabela `message`. Uma função de trabalho verifica periodicamente a tabela `message` procurando mensagens que precisam ser enviadas (chamaremos isso de função de trabalho A).

Quando a função do trabalho A encontra uma mensagem que precisa ser enviada, ela faz as seguintes tarefas:

-   Obtém todos os endereços de email na lista de email de destino.
-   Coloca as informações necessárias para enviar cada email na tabela `message`.
-   Cria um item de fila de trabalho para cada email que precisa ser enviado.

Uma segunda função de trabalho (função de trabalho B) controla a fila de itens de trabalho. Quando a função de trabalho B encontra um item de trabalho, processa o item enviando o email e, em seguida, exclui o item de trabalho da fila. O diagrama a seguir mostra essas relações.

![Processamento de mensagem de email][Processamento de mensagem de email]

Não há emails perdidos se a função de trabalho B falhar e precisar ser reiniciada, porque um item da fila de trabalho para um email não é excluído até depois que o email for enviado. O aplicativo também impede que vários emails sejam enviados no caso de uma função de trabalho A falhar e precisar ser reiniciada.

![Impedindo emails duplicados][Impedindo emails duplicados]

A função de trabalho B controla uma fila de inscrição para itens de trabalho inseridos pelo método de serviço Web API para novas assinaturas. Quando encontrar uma, ele envia um email de confirmação.

![Processamento de mensagens da fila de assinatura][Processamento de mensagens da fila de assinatura]

## <a name="tables"></a>Tabelas do Azure

O aplicativo de serviço de email do Azure armazena dados em tabelas de armazenamento do Azure. As tabelas do Azure são um armazenamento de dados NoSQL, e não um banco de dados relacional como [Banco de Dados SQL do Azure][Banco de Dados SQL do Azure]. As tabelas do Azure são uma boa opção quando a eficiência e a dimensionabilidade são mais importantes do que a normalização de dados e a integridade relacional. Por exemplo, neste aplicativo, uma função de trabalho cria uma linha toda vez que um item de fila de trabalho é criado, e outra função recupera e atualiza uma linha sempre que um email é enviado, o que pode criar uma redução de desempenho se fosse usado um banco de dados relacional. Além disso, as tabelas do Azure são mais baratas do que o SQL do Azure. Para obter mais informações sobre blobs do Azure, consulte [o último tutorial desta série][Compilando a função de trabalho B (remetente de email) para o aplicativo de serviço de email do Azure].

As seções a seguir descrevem o conteúdo das tabelas do Azure usadas pelo aplicativo de serviço de email do Azure. Para um diagrama que mostra as tabelas e seus relacionamentos, consulte o [diagrama de dados de serviço de email do Azure][Diagrama de dados] mais adiante nesta página.

### tabela mailinglist

A tabela `mailinglist` armazena informações sobre listas de endereçamento e os assinantes das listas de endereçamento. (Uma melhor prática da convenção de nomenclatura da tabela do Azure é usar todas as letras minúsculas.)

Nas tabelas do Azure, diferentes linhas podem ter diferentes esquemas e essa flexibilidade é normalmente usada para criar dados de armazenamento da tabela que exigem várias tabelas em um banco de dados relacional. Por exemplo, para armazenar dados da lista de endereçamento no Banco de Dados SQL, você pode usar três tabelas: uma tabela `mailinglist`, que armazena informações sobre a lista, uma tabela `subscriber`, que armazena informações sobre assinantes e uma tabela `mailinglistsubscriber`, que associa listas de endereçamento com assinantes e vice-versa. Na tabela NoSQL deste aplicativo, todas essas funções são distribuídas em uma tabela chamada `mailinglist`.

Em uma tabela do Azure, cada linha tem uma *chave de partição* e uma *chave de linha* que identifica a linha com exclusividade. A chave da partição divide a tabela logicamente em partições. Dentro de uma partição, a chave de linha identifica exclusivamente uma linha. Não há nenhum índice secundário; portanto, para certificar-se de que o aplicativo seja dimensionável, é importante criar suas tabelas de forma que você sempre possa consultar por valores da chave de partição e valores da chave de linha.

A chave da partição da tabela `mailinglist` é o nome da lista de endereçamento.

A chave da linha da tabela `mailinglist` pode ser uma das duas coisas: a "mailinglist" constante ou o endereço de email do assinante. Linhas que possuem a chave de linha "mailinglist" incluem informações sobre a lista de endereçamento. Linhas com endereço de email como a chave de linha têm informações sobre os assinantes para a lista.

Em outras palavras, linhas com chave de linha "mailinglist" equivalem a uma tabela `mailinglist` em um banco de dados relacional. Linhas com chave da linha = endereços de email equivalem a uma tabela `subscriber` e uma tabela de associação `mailinglistsubscriber` em um banco de dados relacional.

Fazer uma tabela ter várias finalidades facilita a obtenção de um melhor desempenho. Em um banco de dados relacional três tabelas precisarão ser lidas e três conjuntos de linhas precisarão ser classificados e combinados em relação uns aos outros, o que leva tempo. Aqui apenas uma tabela é lida e suas linhas são retornadas automaticamente na ordem chave de partição e chave de linha.

A grade a seguir mostra as propriedades de linha para as linhas que contêm informações de lista de email (chave de linha = "MailingList").


<table border="1">

<tr bgcolor="lightgray">
<th>Propriedade</th>
<th>Tipo de Dados</th>
<th>Descri&ccedil;&atilde;o</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>Cadeia de caracteres</td>
<td>ListName:  Um identificador exclusivo para a lista de email, por exemplo: contoso1. O uso t&iacute;pico da tabela &eacute; para recuperar todas as informa&ccedil;&otilde;es para uma lista de email espec&iacute;fica, portanto, usar o nome da lista &eacute; uma maneira eficiente para a dividir a tabela.</td>
</tr>

<tr>
<td>RowKey</td>
<td>Cadeia de caracteres</td>
<td>A constante &quot;mailinglist&quot;.</td>
</tr>

<tr>
<td>Descri&ccedil;&atilde;o</td>
<td>Cadeia de caracteres</td>
<td>Descri&ccedil;&atilde;o de uma lista de email, por exemplo: &quot;An&uacute;ncios do Departamento de Hist&oacute;ria da Contoso University&quot;.</td>
</tr>

<tr>
<td>FromEmailAddress</td>
<td>Cadeia de caracteres</td>
<td>O endere&ccedil;o de email &quot;De&quot; em emails enviados a esta lista, por exemplo: donotreply@contoso.edu.</td>
</tr>

</table>

A grade a seguir mostra as propriedades de linha para as linhas que contêm informações do assinante para a lista (chave de linha = endereço de email).

<table border="1">

<tr bgcolor="lightgray">
<th>Propriedade</th>
<th>Tipo de Dados</th>
<th>Descri&ccedil;&atilde;o</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>Cadeia de caracteres</td>
<td>ListName:  O nome (identificador exclusivo) da lista de email, por exemplo: contoso1.</td>
</tr>

<tr>
<td>RowKey</td>
<td>Cadeia de caracteres</td>
<td>Endere&ccedil;o de Email:  O endere&ccedil;o de email do assinante, por exemplo: student1@contoso.edu.</td>
</tr>

<tr>
<td>SubscriberGUID</td>
<td>Cadeia de caracteres</td>
<td>Gerado quando o endere&ccedil;o de email &eacute; adicionado a uma lista. Usado nos links inscrever e cancelar inscri&ccedil;&atilde;o para que seja dif&iacute;cil inscrever ou cancelar a inscri&ccedil;&atilde;o do endere&ccedil;o de email de outra pessoa. 
<br/><br/>
Algumas consultas para p&aacute;ginas da Web Inscrever e Cancelar inscri&ccedil;&atilde;o especificam apenas o PartitionKey e essa propriedade. Consultar uma parti&ccedil;&atilde;o sem usar a RowKey limita a dimensionabilidade do aplicativo, pois as consultas levar&atilde;o mais tempo conforme o tamanho da lista de email aumenta. Uma op&ccedil;&atilde;o para melhorar a dimensionabilidade &eacute; adicionar linhas de pesquisa com o SubscriberGUID na propriedade RowKey. Por exemplo, para cada endere&ccedil;o de email, uma linha pode ter &quot;email:student1@domain.com&quot; na RowKey e outra linha para o mesmo assinante poderia ter &quot;guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a&quot; na RowKey. Isso &eacute; simples de implementar, pois as transa&ccedil;&otilde;es de lote at&ocirc;micas em linhas dentro de uma parti&ccedil;&atilde;o s&atilde;o f&aacute;ceis para codificar. Para obter mais informa&ccedil;&otilde;es, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/hh508997.aspx">Mundo Real: Criando uma Estrat&eacute;gia de Particionamento Escalon&aacute;vel para Armazenamento de Tabela do Azure</a>
</td>
</tr>

<tr>
<td>Verificado</td>
<td>Booliano</td>
<td>Quando a linha &eacute; criada inicialmente para um novo assinante, o valor &eacute; false. Muda para true somente depois que o novo assinante clicar no hyperlink Confirmar no email de boas-vindas ou um administrador definir como true. Se uma mensagem &eacute; enviada a uma lista enquanto o valor Verificado para um dos seus assinantes for false, nenhum email &eacute; enviado para esse assinante.</td>
</tr>

</table>


A lista a seguir mostra um exemplo da aparência dos dados na tabela.

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Descrição

</th>
<td>
Anúncios do Departamento de História da Contoso University

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
student1@Domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
6f32b03b-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verificado

</th>
<td>
verdadeiro

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
student2@domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
01234567-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verificado

</th>
<td>
false

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
mailinglist

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Descrição

</th>
<td>
Vagas abertas pela Fabrikam Engineering

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@Fabrikam.com

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
fabrikam1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
applicant1@Domain.com

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Verificado

</th>
<td>
verdadeiro

</td>
</tr>
</table>
### tabela de mensagens

A tabela `message` armazena informações sobre mensagens agendadas para serem enviadas a uma lista de endereçamento. Os administradores criam e editam linhas nessa tabela usando páginas da Web e as funções de trabalho usa para passar informações sobre cada email da função de trabalho A para a função de trabalho B.

A chave da partição para a tabela de mensagens é a data em que o email está programado para ser enviado no formato aaaa-mm-dd. Ela otimiza a tabela para a consulta executada com mais frequência em relação a essa tabela, que seleciona linhas com `ScheduledDate` de hoje ou anterior. Porém, ela cria um gargalo de desempenho em potencial, porque as tabelas de armazenamento do Azure têm uma taxa de transferência máxima de 500 entidades por segundo para uma partição. Para cada email a ser enviado, o aplicativo grava uma linha da tabela `message`, lê uma linha e exclui uma linha. Por isso, o menor tempo possível para o processamento de 1.000.000 emails programados para um único dia é de quase duas horas, independentemente de quantas funções de trabalho sejam adicionadas para identificar cargas maiores.

A chave da linha da tabela `message` pode ser uma das duas coisas: a constante "message" mais uma chave exclusiva para a mensagem chamada `MessageRef` ou o valor `MessageRef` mais o endereço de email do assinante. Linhas que possuem a chave de linha que começa com "message" incluem informações sobre a mensagem, como a lista de email para enviar e quando deve ser enviada. As linhas com `MessageRef` e o endereço de email como a chave da linha têm todas as informações necessárias para enviar um email a esse endereço de email.

Em termos de banco de dados relacional, as linhas com a chave de linha começando com "message" equivalem a uma tabela `message`. Linhas com chave de linha = `MessageRef` mais os endereços de email equivalem a uma exibição de consulta de junção que contém as informações `mailinglist`, `message` e `subscriber`.

A grade a seguir mostra as propriedades da linha para as linhas da tabela `message` com informações sobre a mensagem propriamente dita.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Propriedade</th>
<th align="left">Tipo de Dados</th>
<th align="left">Descrição</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">PartitionKey</td>
<td align="left">Cadeia de caracteres</td>
<td align="left">A data em que a mensagem está programada para ser enviada no formato aaaa-mm-dd.</td>
</tr>
<tr class="even">
<td align="left">RowKey</td>
<td align="left">Cadeia de caracteres</td>
<td align="left">A constante &quot;message&quot; concatenada com o valor <code data-inline="1">MessageRef</code>. O <code data-inline="1">MessageRef</code> é um valor exclusivo criado obtendo-se o valor <code data-inline="1">Ticks</code> de <code data-inline="1">DateTime.Now</code> quando a linha é criada.<br /><br />Observação: Um alto volume de aplicativos multithread de várias instâncias deve estar preparado para lidar com exceções RowKey duplicadas ao usar Ticks. Ticks não são garantidos como exclusivos.</td>
</tr>
<tr class="odd">
<td align="left">ScheduledDate</td>
<td align="left">Data</td>
<td align="left">A data em que a mensagem está agendada para ser enviada. (Igual ao <code data-inline="1">PartitionKey</code>, mas em formato de data.)</td>
</tr>
<tr class="even">
<td align="left">SubjectLine</td>
<td align="left">Cadeia de caracteres</td>
<td align="left">A linha de assunto do email.</td>
</tr>
<tr class="odd">
<td align="left">ListName</td>
<td align="left">Cadeia de caracteres</td>
<td align="left">A lista para qual esta mensagem deve ser enviada.</td>
</tr>
<tr class="even">
<td align="left">Status</td>
<td align="left">Cadeia de caracteres</td>
<td align="left"><ul>
<li>&quot;Pendente&quot; - A função de trabalho A ainda não começou a criar mensagens de fila para agendar emails.</li>
<li>&quot;Em Fila&quot; - A função de trabalho A começou a criar mensagens de fila para agendar emails.</li>
<li>&quot;Processamento&quot; - A função de trabalho A criou itens de trabalho da fila para todos os emails na lista, mas nem todos os emails foram enviados.</li>
<li>&quot;Concluído&quot; - A função de trabalho B terminou de processar todos os itens de trabalho da fila (todos os emails foram enviados). As linhas concluídas são arquivadas na tabela <code data-inline="1">messagearchive</code>, conforme explicado posteriormente. Esperamos tornar esta propriedade um <code data-inline="1">enum</code> na próxima versão.</li>
</ul></td>
</tr>
</tbody>
</table>

Quando a função de trabalho A cria uma mensagem de fila para um email a ser enviado para uma lista, ela cria uma linha de email na tabela `message`. Quando a função de trabalho B envia o email, ela move a linha de email para a tabela `messagearchive` e atualiza a propriedade `EmailSent` como `true`. Quando todas as linhas de email de uma mensagem no status Em processamento forem arquivadas, a função de trabalho A definirá o status como Concluído e moverá a linha `message` para a tabela `messagearchive`.

A grade a seguir mostra as propriedades das linhas de email na tabela `message`.

| Propriedade     | Tipo de Dados        | Descrição                                                                                                    |
|-----------------|----------------------|--------------------------------------------------------------------------------------------------------------|
| PartitionKey    | Cadeia de caracteres | A data em que a mensagem está programada para ser enviada no formato aaaa-mm-dd.                             |
| RowKey          | Cadeia de caracteres | O valor `MessageRef` e o endereço de email de destino de uma linha `subscriber` da tabela `mailinglist`.     |
| MessageRef      | Longa                | Mesmo que o componente `MessageRef` do `RowKey`.                                                             |
| ScheduledDate   | Data                 | A data programada da linha `message` da tabela `message`. (Igual ao `PartitionKey`, mas em formato de data.) |
| SubjectLine     | Cadeia de caracteres | A linha de assunto do email da linha `message` da tabela `message`.                                          |
| ListName        | Cadeia de caracteres | O nome da lista de email da tabela `mailinglist`.                                                            |
| De EmailAddress | Cadeia de caracteres | O endereço de email "de" da linha `mailinglist` da tabela `mailinglist`.                                     |
| EmailAddress    | Cadeia de caracteres | O endereço de email da linha `subscriber` da tabela `mailinglist`.                                           |
| SubscriberGUID  | Cadeia de caracteres | A GUID do assinante da linha `subscriber` da tabela `mailinglist`.                                           |
| EmailSent       | Booliano             | False significa o email ainda não foi enviado; true significa que o email foi enviado.                       |

Há dados redundantes nestas linhas, que você normalmente evitaria no banco de dados relacional. Mas nesse caso você está trocando algumas das desvantagens dos dados redundantes para o benefício de maior eficiência de processamento e dimensionabilidade. Como todos os dados necessários para um email estão presente em uma dessas linhas, a função de trabalho B só precisa ler uma linha para enviar um email quando ele obtém um item de trabalho da fila.

Você deve estar imaginando de onde vem o corpo do email. Essas linhas não tem referências de blob para os arquivos que contêm o corpo do email, porque o valor deriva do valor `MessageRef`. Por exemplo, se o `MessageRef` for 634852858215726983, os blobs serão chamados de 634852858215726983.htm e 634852858215726983.txt.

A lista a seguir mostra um exemplo da aparência dos dados na tabela.

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
message634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
Nova série de aulas

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Status

</th>
<td>
Processando

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
634852858215726983student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
Nova série de aulas

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student1@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
76543210-90ed-41a9-b8ac-c1310c67b66a

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
verdadeiro

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
634852858215726983student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
634852858215726983

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-10-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
Nova série de aulas

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
contoso1

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
FromEmailAddress

</th>
<td>
donotreply@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailAddress

</th>
<td>
student2@contoso.edu

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubscriberGUID

</th>
<td>
12345678-90ed-41a9-b8ac-c1310c679876

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
EmailSent

</th>
<td>
verdadeiro

</td>
</tr>
</table>

------------------------------------------------------------------------

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">
Chave de partição

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Chave de linha

</th>
<td>
message124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
MessageRef

</th>
<td>
124852858215726999

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ScheduledDate

</th>
<td>
2012-11-15

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
SubjectLine

</th>
<td>
Novos posts de trabalho

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
ListName

</th>
<td>
fabrikam

</td>
</tr>
<tr>
<th align="right" bgcolor="lightgray">
Status

</th>
<td>
Pendente

</td>
</tr>
</table>

### tabela messagearchive

Uma estratégia para garantir que as consultas sejam executadas com eficiência, especialmente se você precisar procurar em campos que não sejam `PartitionKey` e `RowKey`, é limitar o tamanho da tabela. A consulta em uma função de trabalho A que verifica se todos os emails foram enviados para uma mensagem precisa localizar linhas de email na tabela `message` que possui `EmailSent` = falso. Como o valor `EmailSent` não está em PartitionKey ou RowKey, essa não seria uma consulta eficiente para uma mensagem com um grande número de linhas de email. Por isso, o aplicativo move linhas de email para a tabela `messagearchive` conforme os emails são enviados. Assim, a consulta verifica se todos os emails para uma mensagem foram enviados apenas para consultar a tabela de mensagens em `PartitionKey` e `RowKey`, porque se localizar alguma linha de email para uma mensagem, isso significará que há mensagens não enviadas e a mensagem não poderá ser marcada como `Complete`.

O esquema de linhas na tabela `messagearchive` é idêntico ao da tabela `message`. Dependendo do que você deseja fazer com esses dados de arquivamento, é possível limitar seu tamanho e as despesas, reduzindo o número de propriedades armazenadas para cada linha e excluindo linhas mais antigas que uma determinada idade.

## <a name="queues"></a>Filas do Azure

As filas do Azure facilitam a comunicação entre as camadas do aplicativo multicamadas e entre as funções de trabalho na camada do back-end.
As filas são usadas para a comunicação entre a função de trabalho A e a função de trabalho B para tornar o aplicativo dimensionável. A função de trabalho A poderia criar uma linha na tabela Messages para cada email e função de trabalho B pode examinar a tabela para linhas representando emails que ainda não foram enviados, mas não será possível adicionar outras instâncias da função de trabalho B para dividir o trabalho. O problema com o uso das linhas da tabela para coordenar o trabalho entre a função de trabalho A e B é que não há como garantir que somente uma instância da função de trabalho irá pegar qualquer linha de determinada tabela para processamento. As filas fornecem essa garantia. Quando uma instância da função de trabalho obtém um item de trabalho de uma fila, o serviço da fila certifica-se de que nenhuma outra instância de função do trabalho possa retirar o mesmo item de trabalho. Esse recurso de concessão exclusiva das filas do Azure facilita o compartilhamento de uma carga de trabalho entre várias instâncias de uma função de trabalho.

O Azure também oferece o serviço de fila do Barramento de Serviço. Para obter mais informações sobre as filas de armazenamento do Azure e as filas do Barramento de Serviço, consulte [o último tutorial desta série][Compilando a função de trabalho B (remetente de email) para o aplicativo de serviço de email do Azure].

O aplicativo do serviço de email do Azure usa duas filas, chamadas `AzureMailQueue` e `AzureMailSubscribeQueue`.

### AzureMailQueue

A fila `AzureMailQueue` coordena o envio de emails para listas de emails. A função de trabalho A coloca um item de trabalho na fila para cada email a ser enviado e função de trabalho B retira um item de trabalho da fila e envia o email.

Um item da fila de trabalho contém uma sequência de caracteres delimitada por vírgula que consiste na data agendada da mensagem (chave de partição da tabela `message`) e nos valores `MessageRef` e `EmailAddress`(chave de linha da tabela `message`), mais um sinalizador indicando se o item foi criado depois que a função de trabalho foi desativada e reiniciada, por exemplo:

      2012-10-15,634852858215726983,student1@contoso.edu,0

A função de trabalho B usa esses valores para pesquisar a tabela `message` que contém todas as informações necessárias para enviar o email. Se o sinalizador de reinicialização indica uma reinicialização, a função de trabalho B verifica se o email já não foi enviado antes de enviar.

Quando houver picos de tráfego, os serviços de nuvem podem ser reconfigurados para que várias instâncias da função de trabalho B sejam instanciadas, e cada um deles independentemente extraiam itens de trabalho da fila.

### AzureMailSubscribeQueue

A fila `AzureMailSubscribeQueue` coordena o envio de emails de confirmação da assinatura. Em resposta a uma chamada de método de serviço, o método de serviço coloca um item de trabalho na fila. A função de trabalho B retira o item de trabalho da fila e envia o email de confirmação de assinatura.

Um item de trabalho da fila contém o GUID do assinante. Esse valor identifica exclusivamente um endereço de email e a lista para inscrever, que é tudo que a função de trabalho V precisa para enviar um email de confirmação. Conforme explicado anteriormente, isso exige uma consulta em um campo que não está em `PartitionKey` ou `RowKey`, que é ineficiente. Para deixar o aplicativo mais escalonável, a tabela `mailinglist`mailinglist precisaria ser reestruturada para incluir o GUID do assinante na `RowKey`.

## <a name="datadiagram"></a><span class="short-header">Diagrama de dados</span>Diagrama de dados do serviço de email do Azure

O diagrama a seguir mostra as tabelas e as filas e seus relacionamentos.

![Diagrama de dados do aplicativo de serviço de email do Azure][Diagrama de dados do aplicativo de serviço de email do Azure]

## <a name="blobs"></a>Blobs do Azure

Blobs são "objetos binários grandes". O serviço Blob do Azure oferece um meio de carregar e armazenar arquivos na nuvem. Para obter mais informações sobre blobs do Azure, consulte [o último tutorial desta série][Compilando a função de trabalho B (remetente de email) para o aplicativo de serviço de email do Azure].

Os administradores do serviço de email do Azure colocam o corpo de um email no formato HTML em um arquivo *.htm* e em texto sem formatação em um arquivo *.txt*. Ao agendar um email, eles carregam esses arquivos na página da Web **Criar Mensagem**, e o controlador MVC do ASP.NET da página armazena o arquivo carregado em um blob do Azure.

Blobs são armazenados em contêineres de blob, assim como os arquivos são armazenados em pastas. O aplicativo do serviço de email do Azure usa um contêiner de blob único chamado **azuremailblobcontainer**. O nome dos blobs no contêiner é derivado concatenando o valor MessageRef com a extensão de arquivo, por exemplo: 634852858215726983.htm and 634852858215726983.txt.

Como as mensagens em HTML e texto sem formatação são essencialmente cadeias de caracteres, poderíamos ter projetado o aplicativo para armazenar o corpo da mensagem de email em propriedades da cadeia de caracteres na tabela `Message`, em vez de blobs. No entanto, há um limite de 64K sobre o tamanho de uma propriedade em uma linha da tabela, portanto, a utilização de um blob evita essa limitação no tamanho do corpo de email. (64K é o tamanho total máximo da propriedade; depois permitindo a codificação de sobrecarga, o tamanho máximo da cadeia de caracteres que você pode armazenar em uma propriedade é, na verdade, mais perto de 48K).

## <a name="wawsvswacs"></a>Serviço de Nuvem do Azure versus Site do Azure

O serviço de email do Azure é configurado de forma que o front-end e o back-end sejam todos executados em um serviço de nuvem do Azure.

![Visão geral da arquitetura do aplicativo][Visão geral da arquitetura do aplicativo]

Uma arquitetura alternativa deve executar o front-end em um site do Azure.

![Arquitetura do aplicativo alternativa][Arquitetura do aplicativo alternativa]

Outra alternativa é executar o front-end em um site do Azure e usar o recurso de Trabalhos Web para executar o back-end nos mesmos servidores que executam o front-end. Para obter mais informações, consulte [Comece a usar o SDK de Trabalhos Web do Azure][Introdução ao SDK de Trabalhos Web do Azure].

## <a name="cost"></a>Custo

Esta seção fornece uma breve visão geral dos custos para executar o aplicativo de exemplo no Azure, dada as taxas em vigor quando o tutorial foi publicado originalmente em dezembro de 2012. Antes de tomar decisões comerciais com base nos custos, certifique-se de verificar as taxas atuais nas seguintes páginas da Web:

-   [Calculadora de preços do Azure][Calculadora de preços do Azure]
-   [SendGrid Azure][SendGrid Azure]

Os custos são afetados pelo número de instâncias da função Web e trabalho que você decidir manter. Para se qualificar para o[(Contrato de Nível de Serviço) em 99,95% do serviço de nuvem do Azure][(Contrato de Nível de Serviço) em 99,95% do serviço de nuvem do Azure], você deve implantar duas ou mais instâncias de cada função. Um dos motivos que você deve executar pelo menos duas instâncias de função é porque as máquinas virtuais que executam seu aplicativo são reiniciadas aproximadamente duas vezes por mês para atualizações do sistema operacional. (Para obter mais informações sobre as Atualizações do SO, consulte [Reinicialização da instância da função devido às atualizações do SO][Reinicialização da instância da função devido às atualizações do SO].)

O trabalho realizado pelas duas funções do trabalho neste exemplo não depende do tempo e, portanto, não precisa do SLA 99,5%. Portanto, executar uma única instância de cada função de trabalho é viável enquanto uma instância possa acompanhar a carga de trabalho. A instância de função Web é sensível ao tempo, ou seja, os usuários esperam que o site não tem nenhum tempo de inatividade para que um aplicativo de produção tenha pelo menos duas instâncias da função Web.

A tabela a seguir mostra os custos da arquitetura padrão do aplicativo de exemplo do serviço de email do Azure considerando uma carga de trabalho mínima. Os custos mostrados são baseados no uso de um máquina virtual extremamente pequena (compartilhada). O tamanho da máquina virtual é padrão ao criar um projeto de nuvem no Visual Studio pequeno, que é aproximadamente seis vezes mais caro do que o tamanho extra pequeno.

<table border="1">
<tr bgcolor="lightgray">
<th>
Componente ou Serviço

</th>
<th>
Tarifa

</th>
<th>
Custo por mês

</th>
</tr>
<tr>
<td>
Função Web

</td>
<td>
2 instâncias a $0.02/hora para instâncias super pequenas

</td>
<td>
$29.00

</td>
</tr>
<tr>
<td>
Função de trabalho A (agenda o envio de emails)

</td>
<td>
2 instância a $0,02/hora para uma instância super pequena

</td>
<td>
$14.50

</td>
</tr>
<tr>
<td>
Função de trabalho B (envia emails)

</td>
<td>
2 instância a $0,02/hora para uma instância super pequena

</td>
<td>
$14.50

</td>
</tr>
<tr>
<td>
Transações de armazenamento do Azure

</td>
<td>
1 milhão de transações por mês a US$ 0,10/milhão (cada consulta será considerada como uma transação; uma função de trabalho A consulta continuamente tabelas para mensagens que precisam ser enviadas. O aplicativo também é configurado para gravar dados de diagnóstico no Armazenamento do Azure e sempre que ele faz isso é uma transação.)

</td>
<td>
$0.10

</td>
</tr>
<tr>
<td>
Armazenamento localmente redundante do Azure

</td>
<td>
US$ 2,33 para 25 GB (inclui armazenamento para tabelas de aplicativos e dados de diagnóstico).

</td>
<td>
$2.33

</td>
</tr>
<tr>
<td>
Largura de banda

</td>
<td>
O Egress de 5 GB é gratuito

</td>
<td>
Grátis

</td>
</tr>
<tr>
<td>
SendGrid

</td>
<td>
Os clientes do Azure podem enviar 25.000 emails por mês gratuitamente

</td>
<td>
Grátis

</td>
</tr>
<tr>
<td colspan="2">
Total

</td>
<td>
$60.43

</td>
</tr>
</table>
Como você pode ver, as instâncias de função são um componente importante do custo geral. As instâncias de função têm um custo mesmo se forem interrompidas; você deve excluir uma instância da função para não pagar todos os encargos. Uma abordagem de economia de custo seria mover todo o código da função de trabalho A e da função de trabalho B em uma função de trabalho. Para esses tutoriais, deliberadamente optamos por implementar duas instâncias de trabalho para simplificar o dimensionamento. O trabalho que a função de trabalho B faz é coordenado pelo serviço Fila do Azure, o que significa que você pode escalonar a função de trabalho B simplesmente aumentando o número de instâncias da função. (A função de trabalho B é o fator limitante para condições de carga elevada.) O trabalho executado por uma função de trabalho A não é coordenado por filas, portanto, não é possível executar várias instâncias da função de trabalho A. Se duas funções de trabalho foram combinadas e você deseja habilitar o dimensionamento, é necessário implementar um mecanismo para garantir que tarefas da função de trabalho A sejam executadas em apenas uma instância. (Um mecanismo assim é fornecido por [CloudFx][CloudFx]. Consulte o [Exemplo WorkerRole.cs][Exemplo WorkerRole.cs].)

Também é possível mover todo o código das duas funções de trabalho para a função web, de forma que tudo seja executado na função web. No entanto, executar tarefas em segundo plano no ASP.NET não é suportado nem considerado robusta e essa arquitetura complicaria a dimensionabilidade. Para obter mais informações, consulte [Os perigos da implementação recorrente de tarefas em segundo plano no ASP.NET][Os perigos da implementação recorrente de tarefas em segundo plano no ASP.NET]. Consulte também [Como combinar uma função de trabalho e web no Azure][Como combinar uma função de trabalho e web no Azure] e [Como combinar várias funções de trabalho do Azure em uma função web do Azure][Como combinar várias funções de trabalho do Azure em uma função web do Azure]. Se queria ir nessa direção, uma solução melhor seria [executar em um site da web do Azure e usar o recurso de Trabalhos Web para tarefas back-end][executar em um site da web do Azure e usar o recurso de Trabalhos Web para tarefas back-end].

Outra alternativa de arquitetura que reduz o custo é usar o [Bloqueio do Aplicativo de Dimensionamento Automático][Bloqueio do Aplicativo de Dimensionamento Automático] para implantar automaticamente funções de trabalho somente durante os períodos programados e excluir quando o trabalho for concluído. Para obter mais informações sobre autoescala automática, consulte [o último tutorial nesta série][Compilando a função de trabalho B (remetente de email) para o aplicativo de serviço de email do Azure].

No futuro, o Azure poderá fornecer um mecanismo de notificação para reinicializações agendadas, o que permitiria abrir apenas uma instância de função web extra para a janela de tempo da reinicialização. Você não está qualificado para o SLA 99,95, mas pode reduzir seus custos quase pela metade e garantir que seu aplicativo da Web permaneça disponível durante o intervalo de reinicialização.

## <a name="auth"></a>Autenticação e autorização

Em um aplicativo de produção, você poderia implementar um mecanismo de autenticação e autorização, como a [Identidade ASP.NET][Identidade ASP.NET] para o front-end da Web ASP.NET MVC, incluindo o método de serviço de API da Web do ASP.NET. Também existem outras opções, como o uso de um segredo compartilhado, para proteger o método de serviço do API da Web. A funcionalidade de autenticação e autorização foi omitida do aplicativo de exemplo para mantê-lo simples de configurar e implantar.

## <a name="nextsteps"></a>Próximas etapas

No [próximo tutorial][Configurando e implantando o aplicativo de serviço de email do Azure], você vai baixar o projeto de amostra, configurar seu ambiente de desenvolvimento, configurar o projeto para o seu ambiente e testar o projeto localmente e na nuvem. Do tutorial 3 ao 5, você verá como compilar o projeto desde o início.

Para obter links para recursos adicionais a fim de trabalhar com tabelas, filas e blobs do Armazenamento do Azure, consulte [o último tutorial nesta série][o último tutorial nesta série].

<div><a href="/pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">Tutorial 2</a></div>

  [o aplicativo completo]: http://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  [livro eletrônico]: http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs
  [Processamento de mensagem de email]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png
  [Introdução aos serviços de nuvem do Azure e ao ASP.NET]: /pt-br/documentation/articles/cloud-services-dotnet-get-started/
  [Introdução ao SDK de Trabalhos Web do Azure]: /pt-br/documentation/articles/websites-dotnet-webjobs-sdk-get-started/
  [Configurando e implantando o aplicativo de serviço de email do Azure]: /pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
  [Compilando a função web para o aplicativo de serviço de email do Azure]: /pt-br/develop/net/tutorials/multi-tier-web-site/3-web-role/
  [Pré-requisitos]: #prerequisites
  [Visão geral do front-end]: #frontend
  [Visão geral do back-end]: #backend
  [Tabelas do Azure]: #tables
  [Filas do Azure]: #queues
  [Diagrama de dados]: #datadiagram
  [Blobs do Azure]: #blobs
  [Serviço de Nuvem do Azure versus Site do Azure]: #wawsvswacs
  [Custo]: #cost
  [Autenticação e autorização]: #auth
  [Próximas etapas]: #nextsteps
  [conta de avaliação gratuita]: /pt-br/pricing/free-trial/
  [ativar os benefícios de assinante MSDN]: /pt-br/pricing/member-offers/msdn-benefits/
  [Página de índice da lista de endereçamento]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png
  [Página do índice do assinante]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png
  [Página de índice de mensagem]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png
  [Página de criação de mensagem]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png
  [Email de confirmação]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png
  [Bem-vindo à página de lista]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png
  [Página de confirmação do cancelamento da inscrição]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png
  [1]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png
  [Impedindo emails duplicados]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png
  [Processamento de mensagens da fila de assinatura]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png
  [Banco de Dados SQL do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/ee336279.aspx
  [Diagrama de dados do aplicativo de serviço de email do Azure]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png
  [Visão geral da arquitetura do aplicativo]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png
  [Arquitetura do aplicativo alternativa]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png
  [Calculadora de preços do Azure]: http://www.windowsazure.com/pt-br/pricing/calculator/
  [SendGrid Azure]: http://sendgrid.com/windowsazure.html
  [Reinicialização da instância da função devido às atualizações do SO]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
  [CloudFx]: http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"
  [Exemplo WorkerRole.cs]: http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169
  [Os perigos da implementação recorrente de tarefas em segundo plano no ASP.NET]: http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx
  [Como combinar uma função de trabalho e web no Azure]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html
  [Como combinar várias funções de trabalho do Azure em uma função web do Azure]: http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html
  [executar em um site da web do Azure e usar o recurso de Trabalhos Web para tarefas back-end]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Bloqueio do Aplicativo de Dimensionamento Automático]: /pt-br/develop/net/how-to-guides/autoscaling/
  [Identidade ASP.NET]: http://asp.net/identity
  [o último tutorial nesta série]: /pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/#nextsteps
