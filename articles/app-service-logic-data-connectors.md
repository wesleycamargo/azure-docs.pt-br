<properties 
	pageTitle="Conectores de Dados dos Aplicativos de API do Microsoft Azure | Microsserviço de Aplicativos de API" 
	description="Saiba como criar Aplicativos de API de Conector de Dados do Microsoft Azure e adicionar o aplicativo de API ao seu aplicativo lógico; microsserviço" 
	services="app-service-logic" 
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
	ms.date="03/24/2015" 
	ms.author="deonhe"/>


# Conectores de Dados no Serviço de Aplicativo do Microsoft Azure


## O que é um Conector de Aplicativos de API de Adicionar Dados + Serviços
Conectores de Aplicativos e Serviços de Dados são "Aplicativos de API" que podem se conectar a uma ampla gama de aplicativos de serviços de dados e executar diversos *Actions* em nome do usuário autenticado.  A maioria desses conectores também pode ser configurada com um *Trigger*. Gatilhos são eventos (semelhantes a eventos no .NET Framework) que podem ser configurados em alguns conectores para iniciar um fluxo de trabalho.  Por exemplo, uma instância do conector do Dropbox pode ser configurada com um gatilho *new file* e o gatilho será executado sempre que um novo arquivo for adicionado à conta do Dropbox que está sendo monitorada.  Esse gatilho pode, em seguida, ser configurado para iniciar um *Action* que vai *Get* o arquivo e carregá-lo em uma lista do SharePoint local.


Esta é uma breve visão geral de cada um dos conectores de Aplicativos e Serviços de Dados que estão disponíveis na Galeria do Azure. 

<table>
<tr>
<th> Nome</th>
<th> Descrição</th>
<th> Gatilhos</th>
<th>Ações</th>

<tr>
<td>Serviços de Mídia do Azure
<td>O conector dos Serviços de Mídia do Azure permitem que você crie fluxos de trabalho de mídia de ponta a ponta com codificação, empacotamento e distribuição flexíveis e dimensionáveis.  Você também pode carregar, armazenar, codificar e fazer streaming de conteúdos de vídeo ou áudio para fornecimento de streaming sob demanda e em tempo real para uma ampla gama de pontos de extremidade de dispositivos móveis, TV e PC.
<td>Nenhum
<td>Nenhum 
</tr>

<tr>
<td>Serviços Móveis do Azure
<td>Um conector dos Serviços Móveis do Azure permite criar e modificar dados e chamar APIs personalizadas.
<td>Nenhum
<td><li>Consultar
<li>Inserir
<li>Excluir
<li>Atualizar
<li>Chamada à API personalizada

</tr>

<tr>
<td>Barramento de Serviço do Azure
<td>O conector do Barramento de Serviço do Azure permite enviar mensagens de entidades do Barramento de Serviço, como Filas e Tópicos, e receber mensagens de entidades do Barramento de Serviço, como Filas e Assinaturas.
<td>Novas mensagens
<td>Enviar mensagem
</tr>

<tr>
<td>Blob de Armazenamento do Azure
<td>O Blob de Armazenamento do Azure permite se conectar ao armazenamento de blob e executar várias ações.
<td>Novo arquivo adicionado
<td><li>Carregar <li>Obter <li>Excluir <li>Listar <li>Copiar <li>Instantâneo
</tr>

<tr>
<td>Tabela de Armazenamento do Azure
<td>O Conector da Tabela de Armazenamento do Azure permite se conectar à Tabela de Armazenamento do Azure e executar várias ações, como obter, consultar, inserir, atualizar e excluir entidades e um gatilho para recuperar dados.
<td>Nova entidade
<td><li>Obter Entidade
	<li>Inserir entidade
	<li>Excluir entidade
	<li>Atualizar entidade
	<li>Consultar

</tr>

<tr>
<td>Box
<td>O Conector do Box permite se conectar ao Box e executar diversas ações em seus arquivos. 
<td>Novos arquivos adicionados
<td><li>Carregar arquivo
<li>Obter arquivo
<li>Excluir arquivo
<li>Listar arquivos
</tr>

<tr>
<td>Dropbox
<td>O Conector do Dropbox permite se conectar ao Dropbox e executar diversas ações, como carregar, obter, excluir e listar arquivos e um gatilho para recuperar arquivos.
<td>Novos arquivos adicionados
<td><li>Carregar arquivo
<li>Obter arquivo
<li>Excluir arquivo
<li>Listar arquivos
</tr>

<tr>
<td>HDInsight
<td>Um conector do HDInsight permite criar um cluster do Hadoop no Azure e enviar vários trabalhos do Hadoop, como Pig, Hive, MapReduce e Streaming MapReduce.  Usando esse conector, você pode criar um cluster, enviar um trabalho e aguardar sua conclusão.
<td>N/D
<td><li>Criar Cluster
		<li>Aguardar Criação do Cluster
		<li>Enviar Trabalho de Pig
		<li>Enviar Trabalho de Hive
		<li>Enviar Trabalho de MapReduce
		<li>Aguardar Conclusão do Trabalho
		<li>Excluir Cluster
<li>Enviar Trabalho de MapReduce
<li>Enviar o Trabalho de Streaming MapReduce

</tr>

<tr>
<td>Microsoft SQL
<td>Um conector do Microsoft SQL permite criar e modificar entradas no Microsoft SQL Server e tabelas do banco de dados SQL do Azure.
<td>N/D
<td><li>Mensagens: enviar, listar, obter e pesquisar
	<li>Números de telefone: comprar, listar e obter números locais e gratuitos
</tr>

<tr>
<td>MongoDB
<td>Um conector do MongoDB permite criar, atualizar, excluir e obter documentos de uma coleção do MongoDB.
<td>Novo documento
<td>	<li>Adicionar documentos
		<li>Atualizar Documento
		<li>Obter Documentos
		<li>Inserir Documento
		<li>Excluir Documento
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
<td>O Conector do OneDrive permite que você se conecte à sua conta de armazenamento pessoal do Microsoft OneDrive e execute várias ações, como carregar, obter, excluir e listar arquivos.
<td>Novo arquivo
<td><li>Arquivos: carregar, excluir, listar, baixar
</tr>

<tr>
<td>Oracle
<td>Um conector do banco de dados Oracle permite criar e modificar entradas em uma tabela do banco de dados Oracle.
<td>Novos dados, com base em consulta
<td><li>Tabela:  Inserir em, atualizar, selecionar, excluir do
<li>Chamar Procedimento Armazenado
</tr>

<tr>
<td>SharePoint Online
<td>Um conector do Microsoft SharePoint permite criar e modificar documentos e itens de lista no SharePoint Server ou no Microsoft SharePoint Online.
<td><li>Novo documento
<li>Novo item de lista
<td><li>Biblioteca de documentos:  Carregar, excluir e obter
<li>Listar:  Inserir item

</tr>

<tr>
<td>SharePoint Server
<td>O conector do SharePoint Server permite gerenciar documentos e itens de lista em seu SharePoint Server.  Há suporte para credenciais padrão, autenticação do Windows e autenticação baseada em formulários.  Você precisa fornecer uma Cadeia de conexão do Barramento de Serviço e instalar o agente de ouvinte de local antes de se conectar ao servidor.
<td><li>Novo documento
<li>Novo item de lista
<td><li>Biblioteca de documentos:  Carregar, excluir e obter
<li>Listar:  Inserir item
</tr>
</table>


##Por que usar conectores?

Os conectores aceleram o desenvolvimento de aplicativos e até mesmo permitem que não desenvolvedores criem aplicativos totalmente funcionais de nível empresarial sem necessidade de aprender uma linguagem de programação ou escrever qualquer código.

Agora que você tem uma ideia do que nossos conectores de Aplicativos + serviços de dados, vejamos alguns casos de uso simples desses conectores. 

### Monitorando seu Dropbox e atualizando o SharePoint ###
Imagine que sua empresa é uma construtora que recebe arquivos muito grandes que contêm plantas.  Esses arquivos normalmente são muito grandes para serem manipulados por email, então sua empresa configura uma conta do Dropbox e solicita que os clientes coloquem as plantas no Dropbox.  Em seguida, você pode pedir que cada funcionário verifique constantemente as pastas do Dropbox para ver se há novas plantas e carregá-las em seu SharePoint Server. No entanto, você está convencido de que deve haver uma maneira melhor!  Felizmente, você descobriu que a Microsoft lançou recentemente seu conector de Aplicativo + serviços de dados para Dropbox, Sharepoint e outros serviços de dados.  Você pode, facilmente, criar conectores para o Dropbox e o SharePoint, adicioná-los a um aplicativo lógico e configurá-los para carregar cada novo arquivo  de sua conta do Dropbox para a lista do SharePoint.  Como o conector do Dropbox tem um gatilho *new message*, você pode utilizá-lo para notificar seu aplicativo lógico de que há um novo arquivo disponível.  O conector do Dropbox pode, então, baixar o arquivo.  Em seguida, seu conector do Sharepoint pode ser configurado para carregar o arquivo em uma lista do SharePoint usando a ação *upload* do SharePoint.  Você pode fazer tudo isso sem escrever uma única linha de código.  

Vamos começar. 

##Criar um conector

Há duas maneiras de criar conectores:

- usando o portal do Microsoft Azure (sem necessidade de codificação!)
- usando APIs REST. 

### Criar um Conector do SharePoint no Portal do Microsoft Azure

1. No portal do Azure, selecione **NOVO** > **Web + móvel** > **Azure Marketplace**
2. **Pesquise** o conector ou selecione-o na lista.  Quando selecionado, uma nova folha ou janela se abre.  Selecione **Criar**. 
3. Insira as seguintes propriedades para o conector: 
	<table>
	    <tr><th>Propriedade</th> <th>Descrição</th> </tr>
	    <tr><td>Nome</td> <td>Digite um nome para seu Aplicativo de API.  Por exemplo, é possível atribuir o nome RulesDiscountTaxCode ou APIAppValidateXML</td> </tr>
	    <tr><td>Plano de Serviço de Aplicativo</td> <td>Lista seu plano de pagamento.  Você pode alterá-lo se precisar de mais ou menos recursos</th> </td>
	    <tr><td>Camada de Preços</td> <td>Propriedade somente leitura que lista a categoria de preços dentro de sua assinatura do Azure.</td> </tr>
	    <tr><td>Grupo de Recursos</td> <td>Crie um novo ou use um grupo existente.  O uso de grupos de recursos explica esta propriedade</td> </tr>
	    <tr><td>Assinatura</td> <td>Propriedade somente leitura que lista sua assinatura atual</td> </tr>
	    <tr><td>Local</td> <td>A localização geográfica que hospeda o serviço do Azure </td></tr>
        <tr><td>Adicionar ao quadro inicial</td> <td>Selecione essa opção para adicionar o Aplicativo de API ao seu quadro inicial</td></tr>
	</table> 
4. Selecione **Criar**.  Seu conector será criado.  Pode levar algum tempo para ser concluído, e a tela inicial será exibida durante a criação do conector.  Use o item de menu Notificações à esquerda para monitorar o status do seu conector.

Agora que você criou seu primeiro conector, considere a criação de um aplicativo Web, móvel ou de lógica com ele. 


### Criar um conector usando APIs REST

[Criar conectores com APIs REST](http://go.microsoft.com/fwlink/p/?LinkId=529766)

## Adicionar seu conector a um aplicativo 
O Serviço de Aplicativo do Microsoft Azure expõe diferentes tipos de aplicativos que podem usar esses conectores.  Por exemplo, você pode criar um aplicativo *Logic* combinando um ou mais dos seus conectores *logically* em um único aplicativo.

Para usar os conectores no seu aplicativo *Logic*, selecione um conector pré-configurado na lista, adicione-o ao fluxo de trabalho de design, faça as alterações de configuração necessárias e ele estará pronto para uso. 

Para executar essas etapas, você precisa de um aplicativo Web, aplicativos móveis ou aplicativo lógico.  Consulte <> para obter as etapas específicas.  Quando seu aplicativo estiver disponível, adicione os conectores.  Faça assim:

Use as etapas a seguir para adicionar um conector a um aplicativo lógico: 

1. No quadro inicial (home page) do Portal do Azure, vá até o **Marketplace** e pesquise o  Aplicativo lógico, móvel ou Web. 

	Se estiver criando um novo aplicativo, pesquise pelo aplicativo lógico, móvel ou Web.  Selecione o aplicativo e, na nova folha, selecione **Criar**.  [Criar um Aplicativo Lógico](app-service-logic-create-a-logic-app.md) lista as etapas. 

2. Abra seu aplicativo e selecione **Gatilhos e Ações**. 
3. Na **Galeria**, selecione o conector.  Ele será adicionado ao seu aplicativo.
4. Configure o conector:
5. Cada conector tem propriedades que são específicas para o serviço e o ambiente ao qual ele está se conectando.  Insira os detalhes das propriedades.  Tenha em mente que algumas propriedades são opcionais.
6. Selecione **OK** para salvar suas alterações.


## Segurança
Conectores usam OAuth ou nomes de usuário e senhas. 


## Leia sobre os aplicativos lógicos
[O que são aplicativos lógicos?](app-service-logic-what-are-logic-apps.md) |
[Sites e Aplicativos Web no Serviço de Aplicativo do Azure](app-service-web-app-azure-portal.md) |



## Mais conectores

[Conectores de integração do BizTalk](app-service-logic-integration-connectors.md) |
[Conectores empresariais](app-service-logic-enterprise-connectors.md) |
[Conectores B2B](app-service-logic-b2b-connectors.md) |
[Conectores sociais](app-service-logic-social-connectors.md) |
[Conectores de protocolo](app-service-logic-protocol-connectors.md) |
[Conectores de aplicativos e serviços de dados](app-service-logic-data-connectors.md) |
[Lista de Aplicativos de API e conectores](app-service-logic-connectors-list.md)

<!--HONumber=49-->