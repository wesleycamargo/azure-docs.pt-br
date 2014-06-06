<properties linkid="develop-net-tutorials-multi-tier-web-site-1-overview" urlDisplayName="Etapa 1: Visão geral" pageTitle="Aplicativo Web multicamadas do ASP.NET com o Azure - Etapa 1: Visão geral" metaKeywords="tutorial do Azure, aplicativo de serviço de lista de emails, arquitetura de serviço de emails, visão geral do tutorial do Azure, Azure multicamadas, armazenamento do Azure, blobs do Azure, tabelas do Azure, filas do Azure" description="Saiba mais sobre o tutorial do aplicativo Web Azure multicamadas em cinco partes." metaCanonical="" services="cloud-services,storage" documentationCenter=".NET" title="Tutorial do site MVC do ASP.NET multicamadas - Etapa 1: Visão geral" authors="tdykstra,riande" solutions="" manager="wpickett" editor="mollybos" />

# Aplicativo Web multicamadas do ASP.NET usando tabelas de armazenamento, filas e blobs do Azure - 1 de 5

Esta série de tutoriais mostra como criar um aplicativo Web MVC do ASP.NET multicamadas que usa tabelas de armazenamento, filas e blobs do Azure e como implantar o aplicativo em um serviço de nuvem do Azure. Este tutorial pressupõe que você não tenha experiência anterior com o Azure. Em Concluindo a série, você saberá como criar um aplicativo da Web orientado a dados flexíveis e dimensionável e implantá-lo na nuvem.

Este conteúdo está disponível como um livro eletrônico gratuito na 
[Galeria de livros eletrônicos do TechNet](http://social.technet.microsoft.com/wiki/contents/articles/11608.e-book-gallery-for-microsoft-technologies.aspx#ASPNETMultiTierWindowsAzureApplicationUsingStorageTablesQueuesandBlobs).

<h2><a name="whatyoulllearn"></a><span class="short-header">O que você aprenderá</span>o que você aprenderá</h2>

Nesta série de tutoriais você aprenderá:

* Como habilitar a máquina para desenvolvimento do Azure instalando o SDK do Azure.
* Como criar um projeto de nuvem do Visual Studio com uma função web MVC do ASP.NET e duas funções de trabalho.
* Como publicar o projeto de nuvem em um serviço de nuvem do Azure.
* Como publicar o projeto MVC em um site do Azure se você preferir e ainda usar as funções de trabalho em um serviço de nuvem.
* Como usar o serviço de armazenamento da fila do Azure na comunicação entre níveis ou funções de trabalho.
* Como usar o serviço de armazenamento de tabela do Azure como um armazenamento de dados altamente dimensionável para dados estruturados, não relacionais.
* Como usar o serviço Blob do Azure para armazenar arquivos na nuvem.
* Como exibir e editar blobs, filas e tabelas do Azure usando o Visual Studio ou o Azure Storage Explorer.
* Como usar o SendGrid para enviar emails.
* Como configurar o rastreamento e exibir dados de rastreamento.
* Como dimensionar um aplicativo aumentando o número de instâncias da função de trabalho.

<h2><a name="frontendoverview"></a><span class="short-header">Visão geral do front-end</span>Visão geral do front-end</h2>

O aplicativo que você criará é um serviço da lista de email. O front-end do aplicativo multicamada inclui páginas da Web que os administradores do serviço usam para gerenciar listas de email.

![Página de índice da lista de endereçamento][mtas-mailing-list-index-page]

![Página do índice do assinante][mtas-subscribers-index-page]

Há também um conjunto de páginas que os administradores usam para criar as mensagens para serem enviadas para uma lista de emails.

![Página de índice de mensagem][mtas-message-index-page]

![Página de criação de mensagem][mtas-message-create-page]

Os clientes do serviço são empresas que oferecem aos clientes a oportunidade de inscrever-se para obter uma lista de distribuição no site do cliente. Por exemplo, um administrador configura uma lista para anúncios do Departamento de História da Contoso University. Quando um aluno interessado em anúncios do Departamento de História clica em um link no site da Contoso University, a Contoso University faz uma chamada de serviço Web para o aplicativo de serviço de email do Azure. O método de serviço faz com que um email seja enviado para o cliente. Esse email contém um hiperlink, e quando o destinatário clica no link, é exibida uma página de boas-vindas ao cliente para a lista de anúncios do Departamento de História.

![Email de confirmação][mtas-subscribe-email]

![Bem-vindo à página de lista][mtas-subscribe-confirmation-page]

Cada email enviado pelo serviço (exceto a confirmação de inscrição) inclui um hiperlink que pode ser usado para cancelar a inscrição. Se um destinatário clica no link, uma página da Web solicita a confirmação da intenção do cancelamento da inscrição. 

![Página de confirmação do cancelamento da inscrição][mtas-unsubscribe-query-page]

Se o destinatário clicar no botão **Confirmar**, uma página é exibida confirmando que a pessoa foi removida da lista.

![Página de confirmação do cancelamento da inscrição][mtas-unsubscribe-confirmation-page]




<h2><a name="whyanemaillistapp"></a><span class="short-header">Tutoriais</span>Tutoriais da série</h2>

Aqui está uma lista dos tutoriais com um resumo do seu conteúdo:

1. **Introdução ao aplicativo de serviço de email do Azure** (este tutorial). Uma visão geral do aplicativo e sua arquitetura.
2. [Configurando e implantando o aplicativo de serviço de email do Azure][tut2]. Como baixar o aplicativo de exemplo, configurar, testar localmente, implantar e testar na nuvem.  
3. [Compilando a função web para o aplicativo de serviço de email do Azure][tut3]. Como compilar os componentes MVC 4 do aplicativo e testá-los localmente.
4. [Compilando a função de trabalho A (agendador de email) para o aplicativo de serviço de email do Azure][tut4]. Como criar o componente de back-end que cria a fila de itens de trabalho para o envio de emails e testá-lo localmente.
5. [Compilando a função de trabalho B (remetente de email) para o aplicativo de serviço de email do Azure][tut5]. Como criar o componente de back-end que processa a fila de itens de trabalho para o envio de emails e testá-lo localmente.

Se você apenas deseja baixar o aplicativo e experimentar, tudo o que você precisa é os dois primeiros tutoriais.  Se você deseja ver todas as etapas para a criação de um aplicativo como este a partir do zero, veja os três últimos tutoriais depois de passar pelos dois primeiros.



<h2><a name="whyanemaillistapp"></a><span class="short-header">Por que esse aplicativo</span>Por um aplicativo de serviço de lista de email</h2>

Escolhemos um serviço da lista de emails para esse aplicativo de exemplo porque ele é o tipo de aplicativo que precisa ser resiliente e escalonável, duas características que o tornam especialmente apropriado para o Azure.  

### Resiliente 

Se um servidor falhar ao enviar emails para uma lista grande, você deseja ser capaz de criar um novo servidor rapidamente e facilmente e deseja que o aplicativo reinicie de onde parou sem perder ou duplicar qualquer email. Uma instância da função de trabalho ou web do serviço de nuvem do Azure (uma máquina virtual) será substituída automaticamente se falhar. E tabelas e filas de armazenamento do Azure oferecem um meio para implementar uma comunicação entre servidores que possa sobreviver a uma falha sem perder o trabalho.

### Escalonável

Um serviço de email também deve ser capaz de lidar com picos de carga de trabalho, desde que, às vezes, você esteja enviando emails para listas pequenas e às vezes listas muito grandes.  Em muitos ambientes de hospedagem, você precisa adquirir e manter hardware suficiente para lidar com picos de carga de trabalho e está pagando por toda essa capacidade 100% do tempo, embora só possa usá-lo 5% do tempo.  Com o Azure, você paga apenas pelo volume de computação de que realmente precisa enquanto precisar.  Para dimensionar um endereçamento grande, basta alterar uma configuração para aumentar o número de servidores disponíveis para processar a carga de trabalho. Isso pode ser feito por meio de programação.  Por exemplo, você poderia configurar o aplicativo para que, se o número de itens de trabalho aguardando na fila excedesse um determinado número, o Azure criaria automaticamente instâncias adicionais da função de trabalho que processa esses itens de trabalho.




<h2><a name="backendoverview"></a><span class="short-header">Visão geral de back-end</span>Visão geral de back-end</h2>

O front-end armazena listas e mensagens de email a serem enviadas para eles nas tabelas do Azure. Quando um administrador agenda uma mensagem a ser enviada, uma linha da tabela contendo a data agendada e outros dados, como a linha de assunto, é adicionada à tabela `message`. Uma função de trabalho verifica periodicamente a tabela `message` procurando mensagens que precisam ser enviadas (chamaremos isso de função de trabalho A). 

Quando a função do trabalho A encontra uma mensagem que precisa ser enviada, ela faz as seguintes tarefas:

* Obtém todos os endereços de email na lista de email de destino.
* Coloca as informações necessárias para enviar cada email na tabela `message`.
* Cria um item de fila de trabalho para cada email que precisa ser enviado. 

Uma segunda função de trabalho (função de trabalho B) controla a fila de itens de trabalho. Quando a função de trabalho B encontra um item de trabalho, processa o item enviando o email e, em seguida, exclui o item de trabalho da fila. O diagrama a seguir mostra essas relações.

![Processamento de mensagem de email][mtas-worker-roles-a-and-b]

Não há emails perdidos se a função de trabalho B falhar e precisar ser reiniciada, porque um item da fila de trabalho para um email não é excluído até depois que o email for enviado. O back-end também implementa o processamento da tabela que impede que vários emails sejam enviados no caso de uma função de trabalho A falhar e precisar ser reiniciada. Nesse caso, vários itens de trabalho da fila podem ser gerados para um endereço de email de destino específico. Mas, para cada endereço de email de destino, uma linha na tabela `message` controla se o email foi enviado. Dependendo do momento do reinício e processamento de email, a função de trabalho A usa esta linha para evitar a criação de um segundo item da fila de trabalho ou a função de trabalho B usa esta linha para evitar o envio de um segundo email.

![Impedindo emails duplicados][mtas-message-processing]

A função de trabalho B também controla uma fila de inscrição para itens de trabalho inseridos pelo método de serviço Web API para novas assinaturas. Quando encontrar uma, ele envia um email de confirmação. 

![Processamento de mensagens da fila de assinatura][mtas-subscribe-diagram]





<h2><a name="tables"></a><span class="short-header">Tabelas</span>Tabelas do Azure</h2>

O aplicativo de serviço de email do Azure armazena dados em tabelas de armazenamento do Azure. As tabelas do Azure são um armazenamento de dados NoSQL, e não um banco de dados relacional como [Banco de Dados SQL do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/ee336279.aspx). Isso as torna uma boa opção quando a eficiência e a dimensionabilidade são mais importantes do que a normalização de dados e a integridade relacional. Por exemplo, neste aplicativo, uma função de trabalho cria uma linha toda vez que um item de fila de trabalho é criado, e outra função recupera e atualiza uma linha sempre que um email é enviado, o que pode criar uma redução de desempenho se fosse usado um banco de dados relacional. Além disso, as tabelas do Azure são mais baratas do que o SQL do Azure.  Para obter mais informações sobre as tabelas do Azure, consulte os recursos listados ao final do [último tutorial desta série][tut5].

As seções a seguir descrevem o conteúdo das tabelas do Azure usadas pelo aplicativo de serviço de email do Azure. Para um diagrama que mostra as tabelas e seus relacionamentos, consulte o [diagrama de dados de serviço de email do Azure](#datadiagram) mais adiante nesta página.

### tabela mailinglist ###

A tabela `mailinglist` armazena informações sobre listas de endereçamento e os assinantes das listas de endereçamento. (A melhor prática da convenção de nomenclatura da tabela do Azure é usar todas as letras minúsculas.) Os administradores usam páginas da Web para criar e editar listas de email e clientes e assinantes usam um conjunto de páginas da Web e um método de serviço para se inscrever e cancelar a inscrição. 

Nas tabelas NoSQL, diferentes linhas podem ter diferentes esquemas e essa flexibilidade é normalmente usada para criar dados de armazenamento da tabela que exigem várias tabelas em um banco de dados relacional. Por exemplo, para armazenar dados da lista de endereçamento no Banco de Dados SQL, você pode usar três tabelas: uma tabela `mailinglist`, que armazena informações sobre a lista, uma tabela `subscriber`, que armazena informações sobre assinantes e uma tabela `mailinglistsubscriber`, que associa listas de endereçamento com assinantes e vice-versa. Na tabela NoSQL deste aplicativo, todas essas funções são distribuídas em uma tabela chamada `mailinglist`. 

Em uma tabela do Azure, cada linha tem uma *chave de partição* e uma *chave de linha* que identifica a linha com exclusividade. A chave da partição divide a tabela logicamente em partições. Dentro de uma partição, a chave de linha identifica exclusivamente uma linha. Não há nenhum índice secundário; portanto, para certificar-se de que o aplicativo seja dimensionável, é importante criar suas tabelas de forma que você sempre possa especificar valores da chave de partição e valores da chave de linha na cláusula de consultas Onde.

A chave da partição da tabela `mailinglist` é o nome da lista de endereçamento. 

A chave da linha da tabela `mailinglist` pode ser uma das duas coisas: a "mailinglist" constante ou o endereço de email do assinante. Linhas que possuem a chave de linha "mailinglist" incluem informações sobre a lista de endereçamento. Linhas com endereço de email como a chave de linha têm informações sobre os assinantes para a lista.

Em outras palavras, linhas com chave de linha "mailinglist" equivalem a uma tabela `mailinglist` em um banco de dados relacional. Linhas com chave da linha = endereços de email equivalem a uma tabela `subscriber` e uma tabela de associação `mailinglistsubscriber` em um banco de dados relacional.

Fazer uma tabela ter várias finalidades facilita a obtenção de um melhor desempenho. Em um banco de dados relacional três tabelas precisarão ser lidas e três conjuntos de linhas precisarão ser classificados e combinados em relação uns aos outros, o que leva tempo. Aqui apenas uma tabela é lida e suas linhas são retornadas automaticamente na ordem chave de partição e chave de linha.

A grade a seguir mostra as propriedades de linha para as linhas que contêm informações de lista de email (chave de linha = "MailingList").

<table border="1">

<tr bgcolor="lightgray">
<th>Propriedade</th>
<th>Tipo de Dados</th>
<th>Descrição</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>Cadeia de caracteres</td>
<td>ListName: Um identificador exclusivo para a lista de email, por exemplo: contoso1. O uso típico da tabela é para recuperar todas as informações para uma lista de email específica, portanto, usar o nome da lista é uma maneira eficiente para a dividir a tabela.</td>
</tr>

<tr>
<td>RowKey</td>
<td>Cadeia de caracteres</td>
<td>A constante "mailinglist".</td>
</tr>

<tr>
<td>Descrição</td>
<td>Cadeia de caracteres</td>
<td>Descrição de uma lista de email, por exemplo: "Anúncios do Departamento de História da Contoso University".</td>
</tr>

<tr>
<td>FromEmailAddress</td>
<td>Cadeia de caracteres</td>
<td>O endereço de email "De" nos emails enviados para essa lista, por exemplo: donotreply@contoso.edu.</td>
</tr>

</table>

A grade a seguir mostra as propriedades de linha para as linhas que contêm informações do assinante para a lista (chave de linha = endereço de email).

<table border="1">

<tr bgcolor="lightgray">
<th>Propriedade</th>
<th>Tipo de Dados</th>
<th>Descrição</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>Cadeia de caracteres</td>
<td>ListName: O nome (identificador exclusivo) da lista de endereçamento, por exemplo: contoso1.</td>
</tr>

<tr>
<td>RowKey</td>
<td>Cadeia de caracteres</td>
<td>EmailAddress: O endereço de email do assinante, por exemplo: student1@contoso.edu.</td>
</tr>

<tr>
<td>SubscriberGUID</td>
<td>Cadeia de caracteres</td>
<td>Gerado quando o endereço de email é adicionado a uma lista. Usado nos links inscrever e cancelar inscrição para que seja difícil inscrever ou cancelar a inscrição do endereço de email de outra pessoa. 
<br/><br/>
Algumas consultas para páginas da Web Inscrever e Cancelar inscrição especificam apenas o PartitionKey e essa propriedade. Consultar uma partição sem usar a RowKey limita a dimensionabilidade do aplicativo, pois as consultas levarão mais tempo conforme o tamanho da lista de email aumenta. Uma opção para melhorar a dimensionabilidade é adicionar linhas de pesquisa com o SubscriberGUID na propriedade RowKey. Por exemplo, para cada endereço de email, uma linha pode ter "email:student1@domain.com" na RowKey e outra linha para o mesmo assinante poderia ter "guid:6f32b03b-90ed-41a9-b8ac-c1310c67b66a" na RowKey. Isso é simples de implementar, pois as transações de lote atômicas em linhas dentro de uma partição são fáceis para codificar. Esperamos implementar isso na próxima versão do aplicativo de exemplo. Para obter mais informações, consulte <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/hh508997.aspx">Mundo real: criando uma estratégia de particionamento escalonável para o armazenamento de tabela do Azure</a>
</td>
</tr>

<tr>
<td>Verificado</td>
<td>Booliano</td>
<td>Quando a linha é criada inicialmente para um novo assinante, o valor é false. Muda para true somente depois que o novo assinante clicar no hyperlink Confirmar no email de boas-vindas ou um administrador definir como true. Se uma mensagem é enviada a uma lista enquanto o valor Verificado para um dos seus assinantes for false, nenhum email é enviado para esse assinante.</td>
</tr>

</table>

A lista a seguir mostra um exemplo da aparência dos dados na tabela.

<table border="1">
<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>mailinglist</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Descrição</th>
<td>Anúncios do Departamento de História da Contoso University</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>student1@Domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>6f32b03b-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verificado</th>
<td>verdadeiro</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>student2@domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>01234567-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verificado</th>
<td>false</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>fabrikam1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>mailinglist</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Descrição</th>
<td>Vagas abertas pela Fabrikam Engineering</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@Fabrikam.com</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>fabrikam1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>applicant1@Domain.com</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Verificado</th>
<td>verdadeiro</td>
</tr>

</table>

### tabela de mensagens ###

A tabela `message` armazena informações sobre mensagens agendadas para serem enviadas a uma lista de endereçamento. Os administradores criam e editam linhas nessa tabela usando páginas da Web e as funções de trabalho usa para passar informações sobre cada email da função de trabalho A para a função de trabalho B.

A chave da partição para a tabela de mensagens é a data em que o email está programado para ser enviado no formato aaaa-mm-dd. Ela otimiza a tabela para a consulta executada com mais frequência em relação a essa tabela, que seleciona linhas com `ScheduledDate` de hoje ou anterior. Porém, ela cria um gargalo de desempenho em potencial, porque as tabelas de armazenamento do Azure têm uma taxa de transferência máxima de 500 entidades por segundo para uma partição.  Para cada email a ser enviado, o aplicativo grava uma linha da tabela `message`, lê uma linha e exclui uma linha. Por isso, o menor tempo possível para o processamento de 1.000.000 emails programados para um único dia é de quase duas horas, independentemente de quantas funções de trabalho sejam adicionadas para identificar cargas maiores. 

A chave da linha da tabela `message` pode ser uma das duas coisas: a constante "message" mais uma chave exclusiva para a mensagem chamada `MessageRef` ou o valor `MessageRef` mais o endereço de email do assinante. Linhas que possuem a chave de linha que começa com "message" incluem informações sobre a mensagem, como a lista de email para enviar e quando deve ser enviada. As linhas com `MessageRef` e o endereço de email como a chave da linha têm todas as informações necessárias para enviar um email a esse endereço de email.

Em termos de banco de dados relacional, as linhas com a chave de linha começando com "message" equivalem a uma tabela `message`. Linhas com chave de linha = `MessageRef` mais os endereços de email equivalem a uma exibição de consulta de junção que contém as informações `mailinglist`, `message` e `subscriber`. 

A grade a seguir mostra as propriedades das linhas da tabela `message` com informações sobre a mensagem propriamente dita.

<table border="1">

<tr bgcolor="lightgray">
<th>Propriedade</th>
<th>Tipo de Dados</th>
<th>Descrição</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>Cadeia de caracteres</td>
<td>A data em que a mensagem está programada para ser enviada no formato aaaa-mm-dd.</td>
</tr>

<tr>
<td>RowKey</td>
<td>Cadeia de caracteres</td>
<td>A constante "message" concatenada com o valor <code>MessageRef</code>. O <code>MessageRef</code> é um valor exclusivo criado obtendo-se o valor <code>Ticks</code> de <code>DateTime.Now</code> quando a linha é criada. <br/><br/>Observação: Um alto volume de aplicativos multithread de várias instâncias deve estar preparado para lidar com exceções RowKey duplicadas ao usar Ticks. Ticks não são garantidos como exclusivos.</td>
</tr>

<tr>
<td>ScheduledDate</td>
<td>Data</td>
<td>A data em que a mensagem está agendada para ser enviada. (Igual ao <code>PartitionKey</code> , mas em formato de data.)</td>
</tr>

<tr>
<td>SubjectLine</td>
<td>Cadeia de caracteres</td>
<td>A linha de assunto do email.</td>
</tr>

<tr>
<td>ListName</td>
<td>Cadeia de caracteres</td>
<td>A lista para qual esta mensagem deve ser enviada.</td>
</tr>

<tr>
<td>Status</td>
<td>Cadeia de caracteres</td>
<td><ul>
<li>"Pendente" - A função de trabalho A ainda não começou a criar mensagens de fila para agendar emails.</li>
<li>"Em Fila" - A função de trabalho A começou a criar mensagens de fila para agendar emails.</li>
<li>"Processamento" - A função de trabalho A criou itens de trabalho da fila para todos os emails na lista, mas nem todos os emails foram enviados.</li>
<li>"Concluído" - A função de trabalho B terminou de processar todos os itens de trabalho da fila (todos os emails foram enviados). As linhas concluídas são arquivadas na tabela <code>messagearchive</code>, conforme explicado posteriormente. Esperamos tornar esta propriedade um <code>enum</code> na próxima versão.</li></ul></td>
</tr>

</table>

Quando a função de trabalho A cria uma mensagem de fila para um email a ser enviado para uma lista, ela cria uma linha de email na tabela `message`. Quando a função de trabalho B envia o email, ela move a linha de email para a tabela `messagearchive` e atualiza a propriedade `EmailSent` como `true`. Quando todas as linhas de email de uma mensagem no status Em processamento forem arquivadas, a função de trabalho A definirá o status como Concluído e moverá a linha `message` para a tabela `messagearchive`.

A grade a seguir mostra as propriedades das linhas de email na tabela `message`.  

<table border="1">

<tr bgcolor="lightgray">
<th>Propriedade</th>
<th>Tipo de Dados</th>
<th>Descrição</th>
</tr>

<tr>
<td>PartitionKey</td>
<td>Cadeia de caracteres</td>
<td>A data em que a mensagem está programada para ser enviada no formato aaaa-mm-dd.</td>
</tr>

<tr>
<td>RowKey</td>
<td>Cadeia de caracteres</td>
<td>O valor <code>MessageRef</code> e o endereço de email de destino de uma linha <code>subscriber</code> da tabela <code>mailinglist</code>.
</td>
</tr>

<tr>
<td>MessageRef</td>
<td>Longa</td>
<td>Mesmo que o componente <code>MessageRef</code> do <code>RowKey</code>.</td>
</tr>

<tr>
<td>ScheduledDate</td>
<td>Data</td>
<td>A data programada da linha <code>message</code> da tabela <code>message</code>. (Igual ao <code>PartitionKey</code> , mas em formato de data.)</td>
</tr>

<tr>
<td>SubjectLine</td>
<td>Cadeia de caracteres</td>
<td>A linha de assunto do email da linha <code>message</code> da tabela <code>message</code>.</td>
</tr>

<tr>
<td>ListName</td>
<td>Cadeia de caracteres</td>
<td>O nome da lista de email da tabela <code>mailinglist</code>.</td>
</tr>

<tr>
<td>De EmailAddress</td>
<td>Cadeia de caracteres</td>
<td>O endereço de email "de" da linha <code>mailinglist</code> da tabela <code>mailinglist</code>.</td>
</tr>

<tr>
<td>EmailAddress</td>
<td>Cadeia de caracteres</td>
<td>O endereço de email da linha <code>subscriber</code> da tabela <code>mailinglist</code>.</td>
</tr>

<tr>
<td>SubscriberGUID</td>
<td>Cadeia de caracteres</td>
<td>A GUID do assinante da linha <code>subscriber</code> da tabela <code>mailinglist</code>.</td>
</tr>

<tr>
<td>EmailSent</td>
<td>Booliano</td>
<td>False significa o email ainda não foi enviado; true significa que o email foi enviado.</td>
</tr>

</table>

Há dados redundantes nestas linhas, que você normalmente evitaria no banco de dados relacional. Mas nesse caso você está trocando algumas das desvantagens dos dados redundantes para o benefício de maior eficiência de processamento e dimensionabilidade.  Como todos os dados necessários para um email estão presente em uma dessas linhas, a função de trabalho B só precisa ler uma linha para enviar um email quando ele obtém um item de trabalho da fila.

Você deve estar imaginando de onde vem o corpo do email. Essas linhas não tem referências de blob para os arquivos que contêm o corpo do email, porque o valor deriva do valor `MessageRef`. Por exemplo, se o `MessageRef` for 634852858215726983, os blobs serão chamados de 634852858215726983.htm e 634852858215726983.txt.

A lista a seguir mostra um exemplo da aparência dos dados na tabela.

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>message634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>Nova série de aulas</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Status</th>
<td>Processando</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>634852858215726983student1@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>Nova série de aulas</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailAddress</th>
<td>student1@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>76543210-90ed-41a9-b8ac-c1310c67b66a</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailSent</th>
<td>verdadeiro</td>
</tr>

</table>

<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>634852858215726983student2@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>634852858215726983</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-10-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>Nova série de aulas</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>contoso1</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">FromEmailAddress</th>
<td>donotreply@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailAddress</th>
<td>student2@contoso.edu</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubscriberGUID</th>
<td>12345678-90ed-41a9-b8ac-c1310c679876</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">EmailSent</th>
<td>verdadeiro</td>
</tr>

</table>


<hr/>

<table border="1">

<tr>
<th width="200" align="right" bgcolor="lightgray">Chave de partição</th>
<td>2012-11-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Chave de linha</th>
<td>message124852858215726999</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">MessageRef</th>
<td>124852858215726999</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ScheduledDate</th>
<td>2012-11-15</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">SubjectLine</th>
<td>Novos posts de trabalho</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">ListName</th>
<td>fabrikam</td>
</tr>

<tr>
<th align="right" bgcolor="lightgray">Status</th>
<td>Pendente</td>
</tr>

</table>

<br/>
<br/>

### tabela messagearchive ###

Uma estratégia para garantir que as consultas sejam executadas com eficiência, especialmente se você precisar procurar em campos que não sejam `PartitionKey` e `RowKey`, é limitar o tamanho da tabela. A consulta em uma função de trabalho A que verifica se todos os emails foram enviados para uma mensagem precisa localizar linhas de email na tabela `message` que possui `EmailSent` = falso. Como o valor `EmailSent` não está em PartitionKey ou RowKey, essa não seria uma consulta eficiente para uma mensagem com um grande número de linhas de email. Por isso, o aplicativo move linhas de email para a tabela `messagearchive` conforme os emails são enviados. Assim, a consulta verifica se todos os emails para uma mensagem foram enviados apenas para consultar a tabela de mensagens em `PartitionKey` e `RowKey`, porque se localizar alguma linha de email para uma mensagem, isso significará que há mensagens não enviadas e a mensagem não poderá ser marcada como `Complete`. 

O esquema de linhas na tabela `messagearchive` é idêntico ao da tabela `message`. Dependendo do que você deseja fazer com esses dados de arquivamento, é possível limitar seu tamanho e as despesas, reduzindo o número de propriedades armazenadas para cada linha e excluindo linhas mais antigas que uma determinada idade.



<h2><a name="queues"></a><span class="short-header">Filas</span>Filas do Azure</h2>

As filas do Azure facilitam a comunicação entre as camadas do aplicativo multicamadas e entre as funções de trabalho na camada do back-end. 
As filas são usadas para a comunicação entre a função de trabalho A e a função de trabalho B para tornar o aplicativo dimensionável. A função de trabalho A poderia criar uma linha na tabela Messages para cada email e função de trabalho B pode examinar a tabela para linhas representando emails que ainda não foram enviados, mas não será possível adicionar outras instâncias da função de trabalho B para dividir o trabalho. O problema com o uso das linhas da tabela para coordenar o trabalho entre a função de trabalho A e B é que não há como garantir que somente uma instância da função de trabalho irá pegar qualquer linha de determinada tabela para processamento. As filas fornecem essa garantia. Quando uma instância da função de trabalho obtém um item de trabalho de uma fila, o serviço da fila certifica-se de que nenhuma outra instância de função do trabalho possa retirar o mesmo item de trabalho. Esse recurso de concessão exclusiva das filas do Azure facilita o compartilhamento de uma carga de trabalho entre várias instâncias de uma função de trabalho.

O Azure também oferece o serviço de fila do Barramento de Serviço. Para obter mais informações sobre as filas de armazenamento do Azure e as filas do Barramento de Serviço, consulte os recursos listados ao final do [último tutorial desta série][tut5].

O aplicativo do serviço de email do Azure usa duas filas, chamadas `AzureMailQueue` e `AzureMailSubscribeQueue`.

### AzureMailQueue ###

A fila `AzureMailQueue` coordena o envio de emails para listas de emails.  A função de trabalho A coloca um item de trabalho na fila para cada email a ser enviado e função de trabalho B retira um item de trabalho da fila e envia o email. 

Um item da fila de trabalho contém uma sequência de caracteres delimitada por vírgula que consiste na data agendada da mensagem (chave de partição da tabela `message`) e nos valores `MessageRef` e `EmailAddress` (chave de linha da tabela `message`), mais um sinalizador indicando se o item foi criado depois que a função de trabalho foi desativada e reiniciada, por exemplo:

      2012-10-15,634852858215726983,student1@contoso.edu,0

A função de trabalho B usa esses valores para pesquisar a linha na tabela `message` que contém todas as informações necessárias para enviar o email. Se o sinalizador de reinicialização indica uma reinicialização, a função de trabalho B verifica se o email já não foi enviado antes de enviar.

Quando houver picos de tráfego, os serviços de nuvem podem ser reconfigurados para que várias instâncias da função de trabalho B sejam instanciadas, e cada um deles independentemente extraiam itens de trabalho da fila.

### AzureMailSubscribeQueue ###

A fila `AzureMailSubscribeQueue` coordena o envio de emails de confirmação de assinatura.  Em resposta a uma chamada de método de serviço, o método de serviço coloca um item de trabalho na fila.  A função de trabalho B retira o item de trabalho da fila e envia o email de confirmação de assinatura. 

Um item de trabalho da fila contém o GUID do assinante.  Esse valor identifica exclusivamente um endereço de email e a lista para inscrever, que é tudo que a função de trabalho V precisa para enviar um email de confirmação. Conforme explicado anteriormente, isso exige uma consulta em um campo que não está em `PartitionKey` ou `RowKey`, que é ineficiente. Para deixar o aplicativo mais escalonável, a tabela `mailinglist` precisaria ser reestruturada para incluir o GUID do assinante na `RowKey`.




<h2><a name="datadiagram"></a><span class="short-header">Diagrama de dados</span>Diagrama de dados do serviço de email do Azure</h2>

O diagrama a seguir mostra as tabelas e as filas e seus relacionamentos.

   ![Diagrama de dados do aplicativo de serviço de email do Azure][mtas-datadiagram]





<h2><a name="blobs"></a><span class="short-header">Blobs</span>Blobs do Azure</h2>

Blobs são "objetos binários grandes". O serviço Blob do Azure oferece um meio de carregar e armazenar arquivos na nuvem. Para obter mais informações sobre blobs do Azure, consulte os recursos listados ao final do [último tutorial desta série][tut5].

Os administradores do serviço de email do Azure colocam o corpo de um email no formato HTML em um arquivo *.htm* e em texto sem formatação em um arquivo *.txt*. Ao agendar um email, eles carregam esses arquivos na página da Web **Criar Mensagem**, e o controlador MVC do ASP.NET da página armazena o arquivo carregado em um blob do Azure.

Blobs são armazenados em contêineres de blob, assim como os arquivos são armazenados em pastas. O aplicativo do serviço de email do Azure usa um contêiner de blob único chamado **azuremailblobcontainer**.  O nome dos blobs no contêiner é derivado concatenando o valor MessageRef com a extensão de arquivo, por exemplo: 
634852858215726983.htm and 634852858215726983.txt.

Como as mensagens em HTML e texto sem formatação são essencialmente cadeias de caracteres, poderíamos ter projetado o aplicativo para armazenar o corpo da mensagem de email em propriedades da cadeia de caracteres na tabela `Message`, em vez de blobs. No entanto, há um limite de 64K sobre o tamanho de uma propriedade em uma linha da tabela, portanto, a utilização de um blob evita essa limitação no tamanho do corpo de email. (64K é o tamanho total máximo da propriedade; depois permitindo a codificação de sobrecarga, o tamanho máximo da cadeia de caracteres que você pode armazenar em uma propriedade é, na verdade, mais perto de 48K).




<h2><a name="wawsvswacs"></a><span class="short-header">Serviço de nuvem x Site</span>Serviço de nuvem do Azure x site do Azure</h2>

Quando você baixa o serviço de email do Azure, ele é configurado de forma que o front-end e o back-end sejam todos executados em um único serviço de nuvem do Azure.

![Visão geral da arquitetura do aplicativo][mtas-architecture-overview]

Uma arquitetura alternativa deve executar o front-end em um site do Azure. 

![Arquitetura do aplicativo alternativa][mtas-alternative-architecture]

Manter todos os componentes em um serviço de nuvem simplifica a configuração e a implantação. Se criar o aplicativo com o front-end do MVC do ASP.NET em um site do Azure, você terá duas implantações: uma no site do Azure e outra no serviço de nuvem do Azure. Além disso, as funções web do serviço de nuvem do Azure oferecem os seguintes recursos que não estão disponíveis em sites do Azure:

- Suporte para certificados curinga e personalizados.
- Controle total sobre como o IIS é configurado. Muitos recursos do IIS não podem ser habilitados em sites do Azure. Com as funções web do Azure, você pode definir um comando de inicialização que executa o programa [AppCmd](http://www.iis.net/learn/get-started/getting-started-with-iis/getting-started-with-appcmdexe "appCmd") para modificar as configurações do IIS que não podem ser definidas no arquivo *Web.config*. Para obter mais informações, consulte [Como configurar componentes do IIS no Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/gg433059.aspx) e [Como evitar que endereços IP específicos acessem uma função web
](http://msdn.microsoft.com/pt-br/library/windowsazure/jj154098.aspx).
- Suporte para dimensionamento automático do seu aplicativo da Web usando o [Bloco de aplicativo de dimensionamento automático][autoscalingappblock].
- A capacidade de executar scripts de inicialização elevados para instalar aplicativos, modificar as configurações do Registro, instalar os contadores de desempenho, etc.
- Isolamento de rede a ser usado com [Connect do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/gg433122.aspx) e [Rede Virtual do Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj156007.aspx)
- Acesso remoto a área de trabalho para diagnóstico avançado e depuração.
- Implantando atualizações com [Troca de IP virtual](http://msdn.microsoft.com/pt-br/library/windowsazure/ee517253.aspx "VIP swap"). Esse recurso troca o conteúdo de suas implantações em etapas e de produção. 

A arquitetura alternativa pode oferecer alguns benefícios em termos de custo, porque um site do Azure pode ser mais barato para capacidade semelhante em comparação com uma função web em execução em um serviço de nuvem. Tutoriais posteriores na série explicam os detalhes da implementação que diferem entre as duas arquiteturas.

Para obter mais informações sobre como escolher entre sites e serviços de nuvem do Azure, consulte [Modelos de execução do Azure](http://www.windowsazure.com/pt-br/manage/windows/fundamentals/compute/).




<h2><a name="cost"></a><span class="short-header">Custo</span>Custo</h2>

Esta seção apresenta uma visão geral resumida dos custos para executar o aplicativo de exemplo no Azure, dada as taxas em vigor quando o tutorial foi publicado em dezembro de 2012. Antes de tomar decisões comerciais com base em custos, não se esqueça de verificar as taxas atuais nas seguintes páginas da Web:

* [Calculadora de preços do Azure](http://www.windowsazure.com/pt-br/pricing/calculator/)
* [SendGrid Azure](http://sendgrid.com/windowsazure.html)

Os custos são afetados pelo número de instâncias da função Web e trabalho que você decidir manter. Para se qualificar para o [SLA (Contrato de Nível de Serviço) em 99,95% do serviço de nuvem do Azure](https://www.windowsazure.com/pt-br/support/legal/sla/ "SLA"), você deve implantar duas ou mais instâncias de cada função. Um dos motivos que você deve executar pelo menos duas instâncias de função é porque as máquinas virtuais que executam seu aplicativo são reiniciadas aproximadamente duas vezes por mês para atualizações do sistema operacional. (Para obter mais informações sobre as Atualizações do SO, consulte [Reinicialização da instância da função devido às atualizações do SO](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx).) 

O trabalho realizado pelas duas funções do trabalho neste exemplo não depende do tempo e, portanto, não precisa do SLA 99,5%. Portanto, executar uma única instância de cada função de trabalho é viável enquanto uma instância possa acompanhar a carga de trabalho. A instância de função Web é sensível ao tempo, ou seja, os usuários esperam que o site não tem nenhum tempo de inatividade para que um aplicativo de produção tenha pelo menos duas instâncias da função Web.

A tabela a seguir mostra os custos da arquitetura padrão do aplicativo de exemplo do serviço de email do Azure considerando uma carga de trabalho mínima. Os custos mostrados são baseados no uso de um máquina virtual extremamente pequena (compartilhada). O tamanho da máquina virtual é padrão ao criar um projeto de nuvem no Visual Studio pequeno, que é aproximadamente seis vezes mais caro do que o tamanho extra pequeno.
  
<table border="1">

<tr bgcolor="lightgray">
<th>Componente ou Serviço</th>
<th>Tarifa</th>
<th>Custo por mês</th>
</tr>

<tr>
<td>Função Web</td>
<td>2 instâncias a $0.02/hora para instâncias super pequenas</td>
<td>$29.00</td>
</tr>

<tr>
<td>Função de trabalho A (agenda o envio de emails)</td>
<td>2 instância a $0,02/hora para uma instância super pequena</td>
<td>$14.50
</td>
</tr>

<tr>
<td>Função de trabalho B (envia emails)</td>
<td>2 instância a $0,02/hora para uma instância super pequena</td>
<td>$14.50</td>
</tr>

<tr>
<td>Transações de armazenamento do Azure</td>
<td>1 milhão de transações por mês a US$ 0,10/milhão (cada consulta será considerada como uma transação; uma função de trabalho A consulta continuamente tabelas para mensagens que precisam ser enviadas. O aplicativo também é configurado para gravar dados de diagnóstico no Armazenamento do Azure e sempre que ele faz isso é uma transação.)</td>
<td>$0.10</td>
</tr>

<tr>
<td>Armazenamento localmente redundante do Azure</td>
<td>US$ 2,33 para 25 GB (inclui armazenamento para tabelas de aplicativos e dados de diagnóstico).</td>
<td>$2.33</td>
</tr>

<tr>
<td>Largura de banda</td>
<td>O Egress de 5 GB é gratuito</td>
<td>Grátis</td>
</tr>

<tr>
<td>SendGrid</td>
<td>Os clientes do Azure podem enviar 25.000 emails por mês gratuitamente</td>
<td>Grátis</td>
</tr>

<tr>
<td colspan="2">Total</td>
<td>$60.43</td>
</tr>

</table>

Como você pode ver, as instâncias de função são um componente importante do custo geral. As instâncias de função têm um custo mesmo se forem interrompidas; você deve excluir uma instância da função para não pagar todos os encargos. Uma abordagem de economia de custo seria mover todo o código da função de trabalho A e da função de trabalho B em uma função de trabalho. Para esses tutoriais, deliberadamente optamos por implementar duas instâncias de trabalho para simplificar o dimensionamento. O trabalho que a função de trabalho B faz é coordenado pelo serviço Fila do Azure, o que significa que você pode escalonar a função de trabalho B simplesmente aumentando o número de instâncias da função. (A função de trabalho B é o fator limitante para condições de carga elevada.) O trabalho executado por uma função de trabalho A não é coordenado por filas, portanto, não é possível executar várias instâncias da função de trabalho A. Se duas funções de trabalho foram combinadas e você deseja habilitar o dimensionamento, é necessário implementar um mecanismo para garantir que tarefas da função de trabalho A sejam executadas em apenas uma instância. (Um mecanismo assim é fornecido por [CloudFx](http://nuget.org/packages/Microsoft.Experience.CloudFx "CloudFX"). Consulte o [Exemplo WorkerRole.cs](http://code.msdn.microsoft.com/windowsazure/CloudFx-Samples-60c3a852/sourcecode?fileId=57087&pathId=528472169).)

Também é possível mover todo o código das duas funções de trabalho para a função web, de forma que tudo seja executado na função web. No entanto, executar tarefas em segundo plano no ASP.NET não é suportado nem considerado robusta e essa arquitetura complicaria a dimensionabilidade. Para obter mais informações, consulte [Os perigos da implementação recorrente de tarefas em segundo plano no ASP.NET](http://haacked.com/archive/2011/10/16/the-dangers-of-implementing-recurring-background-tasks-in-asp-net.aspx). Consulte também [Como combinar uma função de trabalho e web no Azure](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2010/12/how-to-combine-worker-and-web-role-in.html) e [Como combinar várias funções de trabalho do Azure em uma função web do Azure](http://www.31a2ba2a-b718-11dc-8314-0800200c9a66.com/2012/02/combining-multiple-azure-worker-roles.html).


Outra alternativa de arquitetura que reduz o custo é usar o [Bloqueio do Aplicativo de Dimensionamento Automático][autoscalingappblock] para implantar automaticamente funções de trabalho somente durante os períodos programados e excluir quando o trabalho for concluído. Para obter mais informações sobre a autoescala automática, consulte os links ao final do [último tutorial desta série][tut5].

No futuro, o Azure poderá fornecer um mecanismo de notificação para reinicializações agendadas, o que permitiria abrir apenas uma instância de função web extra para a janela de tempo da reinicialização. Você não está qualificado para o SLA 99,95, mas pode reduzir seus custos quase pela metade e garantir que seu aplicativo da Web permaneça disponível durante o intervalo de reinicialização.


<h2><a name="auth"></a><span class="short-header">Autenticação e autorização</span>Autenticação e autorização</h2>

Em um aplicativo de produção, você poderia implementar um mecanismo de autenticação e autorização, como o sistema de associação do ASP.NET, para o front-end da Web ASP.NET MVC, incluindo o método de serviço de API da Web do ASP.NET. Também existem outras opções, como o uso de um segredo compartilhado, para proteger o método de serviço do API da Web. A funcionalidade de autenticação e autorização foi omitida do aplicativo de exemplo para mantê-lo simples de configurar e implantar. (O segundo tutorial da série mostra como implementar restrições de IP para que pessoas não autorizadas não possam usar o aplicativo quando você implantá-lo na nuvem.) 

Para obter mais informações sobre como implementar autenticação e autorização em um projeto da Web do ASP.NET MVC, consulte os seguintes recursos:

* [Autenticação e autorização no API da Web do ASP.NET](http://www.asp.net/web-api/overview/security/authentication-and-authorization/authentication-and-authorization-in-aspnet-web-api)
* [Armazenamento de música parte 7: Associação e a autorização](http://www.asp.net/mvc/tutorials/mvc-music-store/mvc-music-store-part-7)

**Observação**: planejamos incluir um mecanismo para proteger o método de serviço API da Web por meio de um segredo compartilhado, mas que não foi concluído a tempo para o lançamento inicial. Portanto, o terceiro tutorial não mostra como criar o controlador de API da Web para o processo de inscrição. Esperamos incluir instruções para a implementação de um processo de assinatura seguro na próxima versão deste tutorial. Até lá, é possível testar o aplicativo usando as páginas da Web de administrador para inscrever endereços de email nas listas.




<h2><a name="nextsteps"></a><span class="short-header">Próximas etapas</span>Próximas etapas</h2>

No [próximo tutorial][tut2], você vai baixar o projeto de amostra, configurar seu ambiente de desenvolvimento, configurar o projeto para o seu ambiente e testar o projeto localmente e na nuvem.  Nos seguintes tutoriais, você verá como criar o projeto desde o início.

Para obter links para recursos adicionais para trabalhar com tabelas, filas e blobs do Armazenamento do Azure, consulte o final do [último tutorial desta série][tut5].

<div><a href="/pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/" class="site-arrowboxcta download-cta">Tutorial 2</a></div>

[tut2]: /pt-br/develop/net/tutorials/multi-tier-web-site/2-download-and-run/
[tut3]: /pt-br/develop/net/tutorials/multi-tier-web-site/3-web-role/
[tut4]: /pt-br/develop/net/tutorials/multi-tier-web-site/4-worker-role-a/
[tut5]: /pt-br/develop/net/tutorials/multi-tier-web-site/5-worker-role-b/
[autoscalingappblock]: /pt-br/develop/net/how-to-guides/autoscaling/




[mtas-architecture-overview]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-architecture-overview.png
[mtas-alternative-architecture]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-alternative-architecture.png
[mtas-mailing-list-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-mailing-list-index-page.png
[mtas-subscribers-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribers-index-page.png
[mtas-message-index-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-index-page.png
[mtas-message-create-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-create-page.png
[mtas-subscribe-confirmation-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-confirmation-page.png
[mtas-unsubscribe-query-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-query-page.png
[mtas-unsubscribe-confirmation-page]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-unsubscribe-confirmation-page.png
[mtas-worker-roles-a-and-b]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-worker-roles-a-and-b.png
[mtas-message-processing]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-message-processing.png
[mtas-subscribe-email]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-email.png
[mtas-subscribe-diagram]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-subscribe-diagram.png
[mtas-datadiagram]: ./media/cloud-services-dotnet-multi-tier-app-storage-1-overview/mtas-datadiagram.png


