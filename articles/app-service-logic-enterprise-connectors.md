<properties 
	pageTitle="Conectores empresariais no Serviço de Aplicativo do Microsoft Azure | Azure" 
	description="Aprenda a criar e configurar um conector empresarial; arquitetura de microsserviços" 
	services="app-service-logic" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mandia"/>

# Conectores empresariais no Serviço de Aplicativo do Microsoft Azure
O Serviço de Aplicativo do Microsoft Azure (ou Serviço de App, para abreviar) inclui vários conectores que podem ser usados com os sistemas corporativos, como SAP e Marketo.  Usando esses conectores, você pode facilmente se conectar a um sistema empresarial e concluir tarefas diferentes.

Esses conectores empresariais oferecem recursos de "Gatilho" ou "Ação".  Um gatilho inicia uma nova instância com base em um evento específico, como atualizar uma entrada no MongoDb.  Uma Ação é o resultado, como após a atualização de uma entrada no MongoDB, então obter uma entrada do MongoDB.


## O que é um conector empresarial
Conectores empresariais são "API de Aplicativo" existentes que podem se conectar a locais e sistemas empresariais baseados em nuvem, incluindo: 

Conector | Descrição
--- | ---
Marketo | Um conector **Ação**.  Conecte-se a Marketo e execute ações diferentes, como Criar/Atualizar Leads, Obter Alterações de Lead, Agendar Campanha, Solicitar Campanha, Obter Leads, Obter Campanhas/Lista, Adicionar Leads à Lista e Remover Leads da Lista.
MongoDB | Um conector de **Gatilho** e **Ação**.  Conecte ao banco de dados do Servidor Mongo ou servidor do Mongo local.  Em uma coleção do MongoDB, execute ações diferentes, como criar, atualizar, obter e excluir entradas. 
QuickBooks | Um conector **Ação**.  Conecte-se com QuickBooks e use seus aplicativos para criar, atualizar, ler, excluir e consultar entidades diferentes de Intuit QuickBooks, como clientes, itens, notas fiscais e assim por diante.
Salesforce |Um conector **Ação**.  Conecte-se ao Salesforce para gerenciar entidades diferentes, como contas, leads, oportunidades, casos e assim por diante, na sua conta do Salesforce.  Você pode executar ações diferentes, como criação, atualização, upsert, consulta e exclusão, nas entidades diferentes. 
SugarCRM | Um conector **Ação**.  Conecte-se com SugarCRM online e use seus aplicativos para criar, atualizar, ler e excluir e diferentes tipos de módulos, como contas, contatos, produtos e assim por diante.
SAP | Um conector **Ação**.  Conecte a um servidor SAP local.  Invoqu RFCs BAPIs e tRFCs e também envie IDOCs.


Usando esses conectores, você pode concluir tarefas diferentes no sistema empresarial.  Por exemplo, usando o conector QuickBooks, você pode fazer as tarefas típicas QuickBooks, como adicionar um novo cliente, atualizar uma nota fiscal ou excluir um item. 

Você pode criar tantos conectores quanto desejar, e criá-los facilmente.  Também é possível reutilizar um único conector em vários cenários ou fluxos de trabalho.

Por exemplo, digamos que você esteja em uma conferência e conheça um cliente que deseje comprar 10 itens de seu produto.  No dispositivo móvel, abra o aplicativo móvel que usa o conector QuickBooks criado, adicione os detalhes do cliente e crie uma nota fiscal. 

Enquanto estiver na conferência, um cliente em seu local de negócios deseja pagar uma fatura existente.  O funcionário abre um aplicativo que usa o mesmo conector QuickBooks que você criou, procura as informações do cliente e atualiza a nota fiscal para refletir o novo saldo. 

Esses são fluxos diferentes e usam o mesmo conector QuickBooks.  Você pode criar e usar esses conectores sem escrever nenhum código.  Vamos começar. 


## Requisitos para começar
Quando você cria um conector que usa uma empresa en, há alguns recursos necessários.  Esses itens devem ser criados por você antes que possam ser usados dentro do conector.  Esses requisitos incluem: 

Requisito | Descrição
--- | ---
Namespace do Barramento de Serviço e seus valores de chave | Ao usar SAP ou MongoDB local, um Namespace de Barramento de Serviço e seus valores de chave são necessários.  Se você não estiver se conectando a um sistema local, não é necessário um namespace do Barramento de Serviço.<br/><br/>[Criar um namespace do Barramento de Serviço](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Gerenciador de conexões híbridas local | Quando você estiver se conectando a um sistema local, você instala o Gerenciador de Conexões Híbridas no sistema local.  Quando você cria o conector local, o download está disponível nas propriedades ou configurações do conector. 
Propriedades específicas do sistema | Ao usar sistemas empresariais, há propriedades específicas do sistema (como nome de usuário e senha) de que você precisa. 


## Criar um conector
Um conector pode ser criado usando o portal do Azure ou usando APIs REST. 


### Criar um conector usando APIs REST
[Consulte a documentação sobre como usar as APIs REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### Criar um Conector no Portal do Azure
No portal do Azure, você pode criar um conector empresarial durante a criação de aplicativos lógicos, Web ou móveis.  Ou você pode criar um usando sua própria folha.  Ambos os modos são fáceis, assim, depende das suas necessidades ou preferências.  Alguns usuários preferem criar primeiro todos os conectores com suas propriedades específicas.  Em seguida, criar os aplicativos lógicos, Web ou móveis e adicionar o conector criado.  

As etapas a seguir criam um conector empresarial usando a folha de conector:

1. No quadro inicial (a Home page) do portal do Azure, selecione **Marketplace**.  **Aplicativos de API** lista todos os conectores existentes.  Você também pode **Pesquisar** um conector específico.
2. Selecione o conector.  Na folha nova, selecione **Criar**. 
3. Insira as propriedades: 

	Propriedade | Descrição
--- | ---
Nome | Digite um nome para o conector.  Por exemplo, você pode chamá-lo de *SAPConnector**SalesForceGetAccounts* ou *QuickBooksGetItems*.
Configurações do pacote | Insira as configurações do sistema empresarial, como *SAP User Name* ou *SugarCRM Server URL*.  Consulte [Propriedades específicas do sistema empresarial](#AddProperties) neste tópico. 
Plano de Serviço de Aplicativo | Lista seu plano de pagamento.  Você pode alterá-lo se precisar de mais ou menos recursos.
Camada de Preços | Propriedade somente leitura que lista a categoria de preços dentro de sua assinatura do Azure. 
Grupo de Recursos | Crie um novo ou use um grupo existente.  Todos os Aplicativos de API e conectores para aplicativos lógicos, Web e móveis devem estar no mesmo grupo de recursos. <br/><br/>[Usar grupos de recursos](azure-preview-portal-using-resource-groups.md) explica essa propriedade. 
Assinatura | Propriedade somente leitura que lista sua assinatura atual.
Local | A localização geográfica que hospeda o serviço do Azure. 
Adicionar ao quadro inicial | Selecione essa opção para adicionar o conector ao quadro inicial (home page).


	**<a name="AddProperties"></a>Propriedades específicas do sistema empresarial**

> [AZURE.IMPORTANT] Cada conector tem propriedades específicas para esse sistema empresarial.  Ao conectar-se ao SAP, você pode inserir propriedades específicas do SAP.  Ao conectar-se ao Salesforce, insira as propriedades específicas do Salesforce, e assim por diante.		A tabela a seguir lista as propriedades de sistema empresarial necessárias. 
	
	Sistema Empresarial | Propriedades obrigatórias
--- | ---
Marketo | <ul><li>Ponto de extremidade</li><li>Nome do Provedor</li></ul>
MongoDB| <ul><li>Cadeia de conexão</li><li>Host</li><li>Port</li><li>Nome de usuário</li><li>Senha</li><li>Banco de dados</li><li>Usar criptografia SSL</li><li>Local:  Insira False se for baseado em nuvem.  Se o sistema MongoDb for local, insira True e também as seguintes propriedades:<ul><li>Nome de chave de acesso compartilhada</li><li>Namespace do Barramento de Serviço</li><li>Caminho de Retransmissão</li><li>Enviar chave</li></ul></li></ul>
QuickBooks | <ul><li>ID da empresa</li><li>Nome do provedor</li></ul>
SAP | <ul><li>Nome de host</li><li>Linguagem</li><li>Nome de usuário</li><li>Senha</li><li>Número do Sistema</li><li>Cadeia de conexão do Barramento de Serviço</li><li>Nomes RFC</li><li>Nomes TRFC</li><li>Nomes BAPI</li><li>Nome IDOC</li></ul>
Salesforce | <ul><li>Nome do provedor</li><li>Instância</li><li>Versão</li><li>Entidades (valores separados por vírgula)</li></ul>
SugarCRM | <ul><li>URL do servidor</li><li>Nome do provedor</li><li>Nomes do módulo</li></ul>

4. Selecione **Criar**.


## Instalar o Gerenciador de conexões híbridas local
Depois de criar o conector empresarial que se conecta a um sistema local, como SAP, instale o Gerenciador de conexão híbrida: 

1. No sistema corporativo local, abra o portal de Gerenciamento do Azure e selecione seu conector empresarial. 
2. Na folha, selecione **Conexão Híbrida**. 
3. Copie o valor da **Cadeia de Caracteres de Configuração de Gateway Primária**. 
4. Selecione **Baixar e Configurar**.  Durante a instalação, cole a Cadeia de Caracteres de Configuração de Gateway copiada e continue com a instalação. 
5. Para confirmar a conectividade, abra a folha do conector empresarial.  O status deve listar **Conectado**. 

[Integrar com um servidor SAP local](app-service-logic-integrate-with-an-on-premise-SAP-server.md) fornece um exemplo. 


## Monitorar seus Aplicativos de API
No Portal de Gerenciamento do Azure, abra seu Aplicativo de API empresarial.  Na seção **Operações**, você pode exibir as operações de gerenciamento diferentes.  Por exemplo, você pode:

- Visualizar eventos de Informação e Erro
- Exibir a contagem de threads e uso de memória do processo de trabalho (w3wp)
- Exibir o logs\ de servidor Web e do Aplicativo

Mais em [Monitorar seus Aplicativos Lógicos](app-service-logic-monitor-your-logic-apps.md).


## Adicione o Conector ao seu aplicativo 
O Serviço de Aplicativo do Microsoft Azure expõe diferentes tipos de aplicativos que podem usar esses conectores.  Você pode criar um novo conector ou adicionar conectores existentes a aplicativos lógicos, móveis ou Web.  

Dentro de seu aplicativo, simplesmente selecionar seu conector da Galeria automaticamente o adiciona a seu aplicativo. 

As etapas a seguir adicionam um conector empresarial a aplicativos Web, móveis ou lógicos: 

1. No quadro inicial (home page) do Portal do Azure, vá até **Marketplace** e pesquise o aplicativo lógico, móvel ou Web.

	Se você estiver criando um novo aplicativo, pesquise aplicativos lógicos, móveis ou Web.  Selecione o aplicativo e, na nova folha, selecione **Criar**.  [Criar um aplicativo lógico](app-service-logic-create-a-logic-app.md) lista as etapas. 

2. Abra seu aplicativo e selecione **Gatilhos e Ações**. 

3. Na **Galeria**, adicione os conectores criados, o que os adiciona automaticamente a seu aplicativo. 
4. Selecione **OK** para salvar suas alterações. 


## Segurança
Alguns dos conectores empresariais fornecem segurança OAuth.  Quando você adiciona o conector a seu aplicativo, você **Autoriza** o conector conectando-se ao sistema empresarial com sua conta de logon e concordando com os termos.  Quando você faz isso, seu aplicativo e o conector usam a conta de logon para autenticar com o sistema. 


## Mais recursos do Conector Empresarial
[Integrar com um servidor SAP local](app-service-logic-integrate-with-an-on-premise-SAP-server.md)<br/>
[O que são Conectores e Aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## Leia sobre os aplicativos lógicos
[O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md)<br/>
[Sites e Aplicativos Web no Serviço de Aplicativo do Azure](app-service-web-app-azure-portal.md)



## Mais conectores

[Conectores de integração do BizTalk](app-service-logic-integration-connectors.md)<br/>
[Conectores B2B](app-service-logic-b2b-connectors.md)<br/>
[Conectores sociais](app-service-logic-social-connectors.md)<br/>
[Conectores de protocolo](app-service-logic-protocol-connectors.md)<br/>
[Conectores de Aplicativo + Serviços de Dados](app-service-logic-data-connectors.md)<br/>
[Lista de Aplicativos de API e conectores](app-service-logic-connectors-list.md)

<!--HONumber=49-->