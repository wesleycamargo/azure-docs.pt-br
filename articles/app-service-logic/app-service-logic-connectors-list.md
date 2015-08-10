<properties
	pageTitle="Lista de Conectores e Aplicativos de API | Azure"
	description="Leia sobre conectores e aplicativos de API no serviço de aplicativo do Azure; arquitetura de microsserviços"
	services="app-service\logic"
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
	ms.date="07/12/2015"
	ms.author="mandia"/>


# Lista de conectores e aplicativos de API no Serviço de Aplicativo do Microsoft Azure
Este tópico lista todos os conectores e aplicativos de API disponíveis, criados pela Microsoft.

Para obter informações de preço e uma lista do que é incluído com cada camada de serviço, consulte [Preços do Serviço de Aplicativo do Azure](http://azure.microsoft.com/pricing/details/app-service/).


## Conectores padrão
A tabela a seguir lista todos os conectores e aplicativos de API criados pela Microsoft, que estão disponíveis com os conectores Padrão:

Nome | Descrição
--- | ---
[Azure HDInsight](app-service-logic-connector-hdinsight.md) | Use esse conector para criar um cluster do Hadoop no Azure e enviar diferentes trabalhos do Hadoop, como Pig, Hive, MapReduce e Streaming MapReduce. Usando esse conector, você também pode criar um cluster, enviar um trabalho e aguardar sua conclusão.
[Barramento de Serviço do Azure](app-service-logic-connector-azureservicebus.md) | Usando esse conector, você pode enviar mensagens de entidades do Barramento de Serviço, como Filas e Tópicos, e receber mensagens de entidades do Barramento de Serviço, como Filas e Assinaturas.
[Blob de Armazenamento do Azure](app-service-logic-connector-azurestorageblob.md) | Conecta-se ao armazenamento de Blob e pode carregar, obter e excluir Blobs, listar Blobs no contêiner, fazer instantâneos dos Blobs, copiar Blobs e usar um gatilho para recuperar Blobs.
[Box](app-service-logic-connector-box.md) | Conecta-se ao Box e pode carregar, obter, excluir e listar arquivos e usa um gatilho para recuperar arquivos.
[Chatter](app-service-logic-connector-chatter.md) | Conecta ao Chatter e pode postar mensagens, pesquisar e usar um gatilho para recuperar novas mensagens.
[Dropbox](app-service-logic-connector-dropbox.md) | Conecta-se ao Dropbox e pode carregar, obter, excluir e listar arquivos e usar um gatilho para recuperar arquivos.
[Facebook](app-service-logic-connector-facebook.md) | Conecta-se ao Facebook e pode publicar mensagens, vídeos, fotos, ofertas e assim por diante. Você também pode recuperar mensagens, comentários, eventos, feeds de usuários e diferentes informações do usuário, como livros, jogos, filmes e assim por diante. Esse conector também inclui um gatilho para recuperar novas mensagens postadas em uma página.
[Arquivo](app-service-logic-connector-file.md) | Permite aos clientes enviar ou carregar arquivos em um sistema de arquivos ou rede e receber ou baixar arquivos de um sistema de arquivos ou rede. Usando este conector, você pode se conectar ao servidor de arquivos local e carregar, obter, excluir e listar arquivos e também usar um gatilho para recuperar arquivos.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Conecta-se a um servidor FTP/FTPS e pode carregar, obter, excluir e listar arquivos e usar um gatilho para recuperar arquivos.
[HTTP](app-service-logic-connector-http.md) | O Ouvinte HTTP abre um ponto de extremidade que atua como servidor HTTP e ouve solicitações HTTP de entrada. Você pode usar os comandos POST, GET, DELETE e PUT. A ação de HTTP não exige um aplicativo de API e há suporte nativo para esse tipo de ação nos Aplicativos Lógicos. Pode usar o protocolo HTTP ou HTTPS.
[Microsoft Office 365](app-service-logic-connector-office365.md) | O Conector do Office 365 pode enviar e receber emails, gerenciar seu calendário e gerenciar seus contatos usando sua conta do Office 365. Você pode também enviar, receber e obter emails, criar e excluir eventos de seu calendário e criar, atualizar, obter e excluir seus contatos.
[Microsoft OneDrive](app-service-logic-connector-onedrive.md) | O Microsoft OneDrive é um serviço de hospedagem de arquivos. O conector do OneDrive conecta ao seu Microsoft OneDrive pessoal e pode carregar, obter, excluir e listar arquivos e usar um gatilho para recuperar arquivos.
[Microsoft SharePoint](app-service-logic-connector-sharepoint.md) | Conecta-se ao SharePoint Online ou Microsoft SharePoint Server local para gerenciar documentos e itens de lista. Você também pode criar, atualizar, obter e excluir documentos e itens de lista. Há suporte para diferentes métodos de autenticação, como credenciais padrão, OAuth 2.0, autenticação do Windows e autenticação baseada em formulário.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Conecta-se ao SQL Server local ou a um Banco de Dados SQL do Azure. Você pode criar, atualizar, obter e excluir entradas em uma tabela de banco de dados SQL.
[Microsoft Yammer](app-service-logic-connector-yammer.md) | Conecta-se ao Yammer. Inclui a ação Postar Mensagem e um gatilho para recuperar novas mensagens.
[POP3](app-service-logic-connector-pop3.md) (Protocolo POP3)| Conecta-se a um servidor POP3 e inclui um gatilho para recuperar emails com anexos.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Usando este conector, você pode criar, atualizar, ler, excluir e consultar entidades diferentes do Intuit QuickBooks, como clientes, itens, notas fiscais e assim por diante.
[SFTP](app-service-logic-connector-sftp.md) (SSH File Transfer Protocol)| Conecta-se a SFTP e pode carregar, obter, excluir e listar arquivos e usar um gatilho para recuperar arquivos.
[SMTP](app-service-logic-connector-smtp.md) (Simple Mail Transfer Protocol) | Conecta-se a um servidor SMTP e pode enviar emails com anexos.
[Margem de atraso](app-service-logic-connector-slack.md) | Conecte-se à margem de atraso e poste mensagens em canais de margem de atraso.
[Salesforce](app-service-logic-connector-salesforce.md) | O conector do Salesforce gerencia entidades diferentes, como contas, clientes potenciais, oportunidades, casos e assim por diante na sua conta do Salesforce. Você também pode criar, atualizar, inserir, consultar e excluir diferentes entidades.
[SugarCRM](app-service-logic-connector-sugarcrm.md) | Conecta-se ao SugarCRM Online e pode criar, atualizar, ler e excluir diferentes tipos de módulos, como contas, contatos, produtos e assim por diante.
[Twilio](app-service-logic-connector-twilio.md) | Conecta-se ao Twilio e pode enviar, obter e listar mensagens, listar o uso, obter números de telefone gratuitos, números de celular, números locais e números de chamadas recebidas disponíveis, números de chamadas recebidas e adicionar números de chamadas recebidas.
[Twitter](app-service-logic-connector-twitter.md) | Conecta-se ao Twitter e pode obter a linha do tempo dos usuários, pesquisar tweets, obter seguidores, obter amigos, pesquisar usuários, obter a linha do tempo da página inicial, obter a linha do tempo de referências, postar tweets, postar tweets para um usuário e enviar mensagens diretas. O Conector do Twitter também usa gatilhos, como Obter tweets por palavra-chave, Obter tweets por usuário e Obter tweets por hashtag.
[Aguarde](app-service-logic-connector-wait.md) | Use este conector para atrasar a execução do aplicativo. Você pode atrasar o aplicativo por uma duração específica ou até uma ocorrência em um momento específico.


## Conectores Premium
A tabela a seguir lista todos os conectores e Aplicativos de API criados pela Microsoft que estão disponíveis em Conectores Premium:

Nome | Descrição
------------- | -------------
[Conector AS2](app-service-logic-connector-as2.md) | O Conector AS2 pode receber e enviar mensagens usando o protocolo de transporte AS2 em comunicações entre empresas. Os dados são transportados de maneira segura e confiável pela Internet usando criptografia e certificados digitais.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | O aplicativo de API EDIFACT recebe e envia mensagens usando o protocolo EDIFACT em comunicações entre empresas.
[BizTalk X12](app-service-logic-connector-x12.md) | O aplicativo de API X12 recebe e envia mensagens usando o protocolo X12 em comunicações entre empresas.
[Gerenciamento de parceiros comerciais do BizTalk](app-service-logic-connector-tpm.md) | O aplicativo de API de Gerenciamento de Parceiros Comerciais define e persiste relações entre empresas usando parceiros, contratos e esquemas e certificados usados em contratos. Essas relações são impostas usando AS2, EDIFACT e aplicativos de API X12.
[Codificador JSON do BizTalk](app-service-logic-connector-jsonencoder.md) | Um codificador e um decodificador que ajuda a interoperabilidade do aplicativo entre dados JSON e XML. Pode converter uma determinada instância JSON para XML e vice-versa.
[Regras do BizTalk](app-service-logic-use-biztalk-rules.md) | As Regras do BizTalk definem e controlam a estrutura, a operação e a estratégia de uma organização. Políticas de negócios podem ser atualizadas sem recompilar e reimplantar os aplicativos associados.
Conector do DB2 | Conecta-se a um banco de dados IBM DB2, local e em uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix. <br/><br/>Sem gatilhos. As ações incluem as instruções de tabela selecionar, inserir, atualizar, excluir e personalizar<br/><br/>Esse conector também inclui o Microsoft Client para DRDA para conectar a um servidor Informix em uma rede TCP/IP.
Informix | Conecta-se a um banco de dados IBM Informix, local e em uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix.<br/><br/>Sem gatilhos. As ações incluem as instruções de tabela selecionar, inserir, atualizar, excluir e personalizar.<br/><br/>Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. Esse conector também inclui um Microsoft Client para DRDAA para conectar a um servidor do Informix em uma rede TCP/IP.
MQ | Conecta-se ao IBM WebSphere MQ Server versão 8, localmente e em uma máquina virtual do Azure executando um sistema operacional Windows. Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. O conector também inclui o Microsoft Client para MQ.<br/><br/>Sem gatilhos. Sem ações.<br/><br/><strong>Observação</strong> Atualmente não pode ser usado com Aplicativos Lógicos.
[Banco de Dados do Oracle](app-service-logic-connector-oracle.md) | O conector de banco de dados Oracle se conecta ao banco de dados da Oracle local e pode criar, atualizar, obter e excluir as entradas em uma tabela de banco de dados.
[SAP](app-service-logic-connector-sap.md) | O conector do SAP se conecta a um servidor SAP local e invoca RFCs, BAPIs e tRFCs e envia IDOCs.
[Codificador de Arquivo Simples do BizTalk](app-service-logic-flatfile-encoder.md) | Oferece interoperabilidade entre dados de arquivo simples (como Excel e csv) e dados XML. Este aplicativo de API pode converter uma instância de arquivo simples em XML e vice-versa.
[Serviço de Transformação do BizTalk](app-service-logic-transform-xml-documents.md) | O Aplicativo de API de Transformação do BizTalk converte dados de um formato para outro. Você também pode carregar um mapa existente (arquivo. trfm), exibir a representação gráfica do mapa que mostra os links entre os esquemas de origem e de destino e usar a funcionalidade de "Teste" com um conteúdo XML de entrada de exemplo. Funções internas diferentes também estão disponíveis, incluindo manipulações de cadeia de caracteres, atribuições condicionais, expressões aritméticas, formatadores de data e hora, looping e assim por diante.
[Validador de XML do BizTalk](app-service-logic-xml-validator.md) | O Aplicativo de API do Validador valida dados XML com relação a esquemas XML predefinidos. Você pode usar esquemas existentes ou gerar esquemas com base em uma instância de arquivo simples, instância JSON ou em conectores existentes.
[Extrator de XPath do BizTalk](app-service-logic-xpath-extract.md) | O aplicativo de API do extrator procura e extrai dados de conteúdo XML com base em determinado XPath.

## Conectores como gatilhos
Vários conectores fornecem gatilhos para aplicativos lógicos. Os gatilhos são de dois tipos:

1. Gatilhos de Pesquisa: esses gatilhos pesquisam seu serviço de interesse a uma frequência especificada para verificar se há novos dados. Quando novos dados estão disponíveis, uma nova instância do Aplicativo Lógico é executada com os dados como entrada. O gatilho pode executar tarefas adicionais, como limpeza dos dados lidos e transmitidos para o Aplicativo Lógico para impedir que os mesmos dados sejam consumidos várias vezes. Exemplos de tais conectores são Arquivo, SQL e Armazenamento do Azure.
2. Gatilhos Push: esses gatilhos escutam em busca de dados em um ponto de extremidade ou da ocorrência de eventos. Em seguida, disparam uma nova instância de um Aplicativo Lógico. Exemplos de tais conectores são HTTP Listener e o Twitter.

## Conectores como ações
Os conectores também podem ser usados como ações dentro do Aplicativo Lógico. Ações são úteis para pesquisar dados no Aplicativo Lógico que podem, em seguida, ser usado na execução. Por exemplo, talvez seja necessário pesquisar dados de um banco de dados SQL para obter informações adicionais sobre um cliente durante o processamento de um pedido. Ou então, talvez seja necessário escrever, atualizar ou excluir dados em um destino. Você pode fazer isso usando as ações fornecidas pelos conectores. As ações são mapeadas para operações em aplicativos de API (conforme definido pelos seus metadados Swagger).

## Criar seus próprios conectores e aplicativos de AP
[Connectors and API Apps Reference (Referência a aplicativos de API e conectores)](http://aka.ms/appservicesconnectorreference)<br/> [Gatilhos de aplicativo de API do Serviço de Aplicativo do Azure](../app-service-api/app-service-api-dotnet-triggers.md)<br/> [Logic App Reference (Referência de aplicativos lógicos)](https://msdn.microsoft.com/library/azure/dn948510.aspx)


## Mais informações sobre conectores e aplicativos de API

[O que são aplicativos de API do BizTalk e conectores](app-service-logic-what-are-biztalk-api-apps.md)<br/> [Usando o Gerenciador de Conexão Híbrida no Serviço de Aplicativo do Azure](app-service-logic-hybrid-connection-manager.md)<br/> [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md)

<!---HONumber=July15_HO5-->