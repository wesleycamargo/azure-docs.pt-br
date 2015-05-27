<properties 
	pageTitle="Conectores de dados de aplicativos de API do Microsoft Azure |Microsserviços de aplicativos de API" 
	description="Saiba como criar aplicativos de API de conector de dados do Microsoft Azure e adicionar aplicativo de API ao seu aplicativo lógico; microsserviços" 
	services="app-service\logic" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="dwrede" 
	editor=""/>

<tags
	ms.service="app-service-logic" 
	ms.workload="data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="deonhe"/>


# Conectores de dados no Serviço de Aplicativo do Microsoft Azure


## O que é um Conector de aplicativos de API de aplicativos + serviços de dados
Conectores de aplicativos e serviços de dados são “Aplicativos de API" que conectam a uma ampla gama de aplicativos de serviços de dados e executam diversas *Ações* em nome do usuário autenticado. A maioria desses conectores também pode ser configurada com um *Gatilho*. Gatilhos são eventos (semelhantes a eventos no .NET Framework) que podem ser configurados em alguns conectores para iniciar um fluxo de trabalho.

Por exemplo, uma instância do conector do Dropbox pode ser configurada com um gatilho *novo arquivo* em que o gatilho é executado sempre que um novo arquivo é adicionado à conta do Dropbox que está sendo monitorada. Esse gatilho pode, em seguida, ser configurado para iniciar uma *Ação* que vai *Obter* o arquivo e carregá-lo em uma lista do SharePoint local.


Eis uma breve visão geral de cada um dos conectores de aplicativos e serviços de dados que estão disponíveis na Galeria do Azure:

<table>
<tr>
<th> Nome</th>
<th> Descrição</th>
<th> Gatilhos</th>
<th>Ações</th>

<tr>
<td>Serviços de Mídia do Azure
<td>O conector do Serviços de Mídia do Azure permite que você crie fluxos de trabalho de mídia completos, com codificação, empacotamento e distribuição flexíveis e escalonáveis. Você também pode carregar, armazenar, codificar e transmitir conteúdo de áudio ou vídeo com segurança para entrega de streaming sob demanda e ao vivo para uma grande variedade de programas de TV, PC e pontos de extremidade de dispositivo móvel.
<td>Nenhum
<td>Nenhum 
</tr>

<tr>
<td>Service Bus do Azure
<td>O conector do Barramento de Serviço do Azure permite enviar mensagens de entidades do Barramento de Serviço como filas e tópicos e receber mensagens de entidades do Barramento de Serviço como filas e assinaturas.
<td>Novas mensagens
<td>Enviar mensagem
</tr>
<tr>
<td>Tabela de armazenamento do Azure
<td>O conector de Tabela de Armazenamento do Azure permite que você se conecte à Tabela de Armazenamento do Azure e execute várias ações, como Obter Entidade, Consultar Entidades, Inserir Entidades, Atualizar Entidade, Excluir Entidade e um gatilho para recuperar dados.
<td>Nova entidade
<td><li>Obter Entidade
	<li>Inserir entidade
	<li>Excluir entidade
	<li>Atualizar entidade
	<li>Consulta

</tr>

<tr>
<td>Box
<td>O Conector do Box permite que você se conecte ao Box e execute várias ações em seus arquivos. 
<td>Novos arquivos adicionados
<td><li>Carregar Arquivo
<li>Obter Arquivo
<li>Excluir Arquivo
<li>Listar Arquivos
</tr>

<tr>
<td>DB2
<td>O Conector do DB2 permite que você se conecte a um banco de dados IBM DB2, local e em uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix. Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. Esse conector também inclui o Microsoft Client para DRDA para conectar a um servidor Informix em uma rede TCP/IP.
<td>Nenhum
<td><li>Seleção de tabela
<li>Inserir
<li>Atualizar
<li>Excluir
<li>Instrução personalizada
</tr>

<tr>
<td>Dropbox
<td>O Conector do Dropbox permite que você se conecte ao Dropbox e execute várias ações, como Carregar Arquivo, Obter Arquivo, Excluir Arquivo, Listar Arquivos e um gatilho para recuperar arquivos.
<td>Novos arquivos adicionados
<td><li>Carregar Arquivo
<li>Obter Arquivo
<li>Excluir Arquivo
<li>Listar Arquivos
</tr>

<tr>
<td>HDInsight
<td>Um conector do HDInsight permite que você crie um cluster Hadoop no Azure e envie vários trabalhos do Hadoop, como Hive, Pig, MapReduce e MapReduce de Streaming. Com esse conector, você pode criar um cluster, enviar um trabalho e aguardar a conclusão do trabalho.
<td>Nenhum
<td><li>Criar Cluster
		<li>Aguardar a Criação do Cluster
		<li>Enviar Trabalho do Pig
		<li>Enviar Trabalho do Hive
		<li>Enviar Trabalho do MapReduce
		<li>Aguardar a Conclusão do Trabalho
		<li>Excluir Cluster
<li>Enviar Trabalho do MapReduce
<li>Enviar Trabalho de MapReduce de Streaming
</tr>

<tr>
<td>Informix
<td>O conector do Informix conecta a um banco de dados IBM Informix local e a uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix. Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. Esse conector também inclui um Microsoft Client para DRDAA para conectar a um servidor do Informix em uma rede TCP/IP.
<td>Nenhum
<td><li>Seleção de tabela
<li>Inserir
<li>Atualizar
<li>Excluir
<li>Instrução personalizada
</tr>

<tr>
<td>Microsoft SQL
<td>Um conector do Microsoft SQL permite criar e modificar entradas no Microsoft SQL Server e tabelas de banco de dados SQL do Azure.
<td>Sondar dados
<td><li>Inserir na Tabela
	<li>Atualizar Tabela
	<li>Selecionar da Tabela
	<li>Excluir da Tabela
	<li>Chamar Procedimento Armazenado
</tr>

<tr>
<td>MongoDB
<td>Um conector do MongoDB permite criar, atualizar, excluir e obter documentos de uma coleção MongoDB.
<td>Novo documento
<td>	<li>Adicionar Documento
		<li>Atualizar Documento
		<li>Obter Documento
		<li>Inserir Documento
		<li>Excluir Documento
</tr>


<tr>
<td>MQ
<td>O conector do MQ se conecta ao IBM WebSphere MQ Server versão 8, localmente e em uma máquina virtual do Azure executando um sistema operacional Windows. Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. O conector também inclui o Microsoft Client para MQ.<br/><br/><strong>Observação</strong> Atualmente não pode ser usado com aplicativos lógicos.
<td>Nenhum
<td>Nenhum
</tr>

<tr>
<td>Office 365
<td>Um conector do Office 365 permite enviar e receber emails e gerenciar seu calendário e contatos.
<td>Nova mensagem
<td>	<li>Enviar Email
		<li>Responder Email
		<li>Enviar Evento
		<li>Adicionar Contato
</tr>

<tr>
<td>OneDrive
<td>O conector do OneDrive permite que você se conecte à sua conta de armazenamento pessoal do Microsoft OneDrive e execute várias ações, como carregar, obter, excluir, listar arquivos.
<td>Novo arquivo
<td><li>Arquivos: carregar, excluir, listar, baixar
</tr>

<tr>
<td>Oracle
<td>Um conector de banco de dados Oracle permite criar e modificar entradas em uma tabela de banco de dados Oracle.
<td>Novos dados, com base em consulta
<td><li>Tabela: Inserir, atualizar, selecionar, excluir de
<li>Chamar Procedimento Armazenado
</tr>

<tr>
<td>SharePoint Online
<td>Um conector do Microsoft SharePoint permite criar e modificar documentos e itens de lista no SharePoint Server ou Microsoft SharePoint Online.
<td><li>Novo documento
<li>Novo item de lista
<td><li>Biblioteca de documentos: carregar, excluir e obter
<li>Lista: Inserir item

</tr>

<tr>
<td>SharePoint Server
<td>O conector do SharePoint Server permite gerenciar documentos e listar itens em seu servidor do SharePoint. Há suporte para credenciais padrão, autenticação do Windows e autenticação baseada em formulários. Você precisa fornecer uma cadeia de conexão do Barramento de Serviço e instalar o agente ouvinte local antes de se conectar ao servidor.
<td><li>Novo documento
<li>Novo item de lista
<td><li>Biblioteca de documentos: carregar, excluir e obter
<li>Lista: Inserir item
</tr>
</table>


## Por que usar conectores?

Os conectores aceleram o desenvolvimento de aplicativos e até mesmo permitem que não desenvolvedores criem aplicativos totalmente funcionais de nível empresarial sem necessidade de aprender uma linguagem de programação ou escrever qualquer código.

Agora que você tem uma ideia do que os conectores de aplicativos + serviços de dados podem fazer, vamos examinar alguns casos de uso simples para esses conectores.

### Monitorando o Dropbox e atualizando o SharePoint
Imagine que sua empresa é uma firma de construção que recebe arquivos muito grandes contendo planos gráficos. Esses arquivos normalmente são muito grandes para serem manipulados por email, assim sua empresa configura uma conta do Dropbox e solicita aos clientes para colocar os planos gráficos no Dropbox. Em seguida, você pode pedir que cada funcionário verifique constantemente as pastas do Dropbox quanto a novos planos e os carregue no servidor do SharePoint. No entanto, você está convencido de que deve haver uma maneira melhor! Felizmente, você descobriu que a Microsoft lançou recentemente seu conector de aplicativo + serviços de dados para Dropbox, Sharepoint e outros serviços de dados. Você pode, facilmente, criar conectores para o Dropbox e SharePoint, adicioná-los a um aplicativo lógico e configurá-los para carregar cada novo arquivo de sua conta Dropbox na lista do SharePoint. Como o conector do Dropbox tem um gatilho de *nova mensagem*, ele pode ser usado para notificar o aplicativo lógico que há um novo arquivo disponível. O conector do Dropbox pode, então, baixar o arquivo. O conector do Sharepoint, em seguida, pode ser configurado para carregar o arquivo em uma lista do SharePoint usando a ação *Carregar* do SharePoint. Você pode fazer tudo isso sem escrever uma única linha de código.

Vamos começar.

## Criar um conector
Conector de aplicativos de API podem ser criados usando o Portal do Azure

### Criar um conector do SharePoint no Portal do Microsoft Azure

1. No portal do Azure, selecione **NOVO** > **Web + Móvel** > **Azure Marketplace**
2. **Pesquise** o conector ou selecione-o na lista. Quando selecionada, uma nova folha ou janela é aberta. Selecione **Criar**. 
3. Insira as seguintes propriedades para o conector: <table> <tr><th>Propriedade</th> <th>Descrição</th> </tr> <tr><td>Nome</td> <td>Insira qualquer nome para seu aplicativo de API. Por exemplo, você pode chamá-lo de RulesDiscountTaxCode ou APIAppValidateXML</td> </tr> <tr><td>Plano do Serviço de Aplicativo</td> <td>Lista seu plano de pagamento. Você pode alterá-lo se precisar de mais ou menos recursos</th> </td> <tr><td>Camada de Preços</td> <td>Propriedade somente leitura que lista a categoria de preço na assinatura do Azure.</td> </tr> <tr><td>Grupo de Recursos</td> <td>Crie um novo ou use um grupo existente. Usando grupos de recursos explica essa propriedade</td> </tr> <tr><td>Assinatura</td> <td>Propriedade somente leitura que lista sua assinatura atual</td> </tr> <tr><td>Local</td> <td>O local geográfico que hospeda o serviço do Azure </td></tr> <tr><td>Adicionar ao Quadro Inicial</td> <td>Selecione esta opção para adicionar o aplicativo de API ao seu quadro inicial (a home page)</td></tr> </table> 
4. Selecione **Criar**. O conector será criado. Pode levar algum tempo para ser concluído, e a tela inicial será exibida durante a criação do conector. Use o item de menu Notificações à esquerda para monitorar o status do conector.

Agora que você criou seu primeiro conector, considere a criação de um aplicativo Web, móvel ou lógico com ele.


### Acessar o conector usando APIs REST

[Acessar conectores com as APIs REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Adicionar seu conector a um aplicativo 
O Serviço de Aplicativo do Microsoft Azure expõe os diferentes tipos de aplicativos que podem usar esses conectores. Por exemplo, você pode criar um aplicativo *lógico* combinando *logicamente* um ou mais de seus conectores em um único aplicativo.

Para usar os conectores no aplicativo *Lógico*, selecione um conector configurado na lista, adicione-o ao seu fluxo de trabalho de design, faça as alterações de configuração necessárias e estará pronto para uso.

Para executar essas etapas, você precisa de um aplicativo Web, móvel ou lógico. Consulte <> para conhecer as etapas específicas. Quando seu aplicativo estiver disponível, adicione os conectores. Faça assim:

Use as etapas a seguir para adicionar um conector a um aplicativo lógico:

1. No quadro inicial do portal do Azure (home page), vá até o **Marketplace**, e procure seu aplicativo Web, móvel ou lógico. 

	Se você estiver criando um novo aplicativo, procure por aplicativo Web, móvel ou lógico. Selecione o aplicativo e, na nova folha, selecione **Criar**. [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md) lista as etapas.

2. Abra seu aplicativo e selecione **Gatilhos e Ações**.
3. Na **Galeria**, selecione o conector. Ele será adicionado ao seu aplicativo.
4. Configure o conector:
5. Cada conector tem propriedades específicas ao serviço e ao ambiente com o qual ele está se conectando. Insira os detalhes das propriedades. Lembre-se de que algumas propriedades são opcionais.
6. Selecione **OK** para salvar suas alterações.


## Segurança
Conectores usam nomes OAuth ou nomes e senhas de usuário.


## Leia sobre aplicativos lógicos e aplicativos Web
[O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md) | [Sites e aplicativos Web no Serviço de Aplicativo do Azure](app-service-web-app-azure-portal.md) |



## Mais conectores
[Conectores de integração do BizTalk](app-service-logic-integration-connectors.md) | [Conectores empresariais](app-service-logic-enterprise-connectors.md) | [Conectores entre empresas](app-service-logic-b2b-connectors.md) | [Conectores sociais](app-service-logic-social-connectors.md) | [Conectores de protocolo](app-service-logic-protocol-connectors.md) | [Conectores de aplicativos e serviços de dados](app-service-logic-data-connectors.md) | [Conectores e lista de aplicativos de API](app-service-logic-connectors-list.md)<br/><br/> [O que são conectores e aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->