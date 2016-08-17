<properties
	pageTitle="Lista de Aplicativos de API e de Conectores no Serviço de Aplicativo do Microsoft Azure"
	description="Leia sobre os Conectores e os Aplicativos de API no Serviço de Aplicativo do Azure"
	services="logic-apps"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="erikre"
	editor="cgronlun"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/28/2016"
	ms.author="mandia"/>


# Lista de Conectores e de Aplicativos de API a serem usados em seus Aplicativos Lógicos
>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos. Para a versão de GA (Disponibilidade Geral) de aplicativos lógicos, confira [Lista de novos conectores](../connectors/apis-list.md).

Saiba mais sobre todos os conectores e Aplicativos de API disponibilizados pela Microsoft a serem usados em seus Aplicativos Lógicos

Para obter informações de preço e uma lista do que é incluído com cada camada de serviço, consulte [Preços do Serviço de Aplicativo do Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Para começar com Aplicativos Lógicos antes de se inscrever para uma conta do Azure, vá para [Experimentar Aplicativo Lógico](https://tryappservice.azure.com/?appservice=logic). Você pode criar instantaneamente um aplicativo lógico de início e de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Conectores principais
A tabela a seguir lista todos os conectores e aplicativos de API criados pela Microsoft, que estão disponíveis como Conectores principais:

Nome | Descrição
--- | ---
[Barramento de Serviço do Azure](app-service-logic-connector-azureservicebus.md) | Pode enviar mensagens de tópicos e filas do Barramento de Serviço e receber mensagens de assinaturas e filas do Barramento de Serviço.
[Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) | Use o Bing para traduzir textos para outro idioma.
[HTTP](app-service-logic-connector-http.md) | O ouvinte HTTP abre um ponto de extremidade que atua como um servidor HTTP e escuta solicitações HTTP ou HTTPS. A ação de HTTP não exige um aplicativo de API e há suporte nativo para esse tipo de ação nos Aplicativos Lógicos.
[Microsoft Office 365](app-service-logic-connector-office365.md) | O Conector do Office 365 pode enviar e receber emails, gerenciar seu calendário e gerenciar seus contatos usando sua conta do Office 365.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Você pode concluir tarefas diferentes, incluindo criar, atualizar e consultar diferentes entidades da Intuit QuickBooks como clientes, itens, notas fiscais e assim por diante.
[Margem de atraso](app-service-logic-connector-slack.md) | Conecte-se à margem de atraso e poste mensagens em canais de margem de atraso.
[Aguarde](app-service-logic-connector-wait.md) | Use este conector para atrasar a execução do aplicativo. Você pode atrasar o aplicativo por uma duração específica ou até uma ocorrência em um momento específico.


## Conectores de integração Enterprise
A tabela a seguir lista todos os conectores e Aplicativos de API criados pela Microsoft que estão disponíveis como Conectores de integração Enterprise:

Nome | Descrição
------------- | -------------
[Conector AS2](app-service-logic-connector-as2.md) | Pode receber e enviar mensagens usando o protocolo de transporte AS2. Os dados são transportados de maneira segura e confiável usando criptografia e certificados digitais.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | Recebe e envia mensagens usando o protocolo EDIFACT em comunicações entre empresas.
[Codificador de Arquivo Simples do BizTalk](app-service-logic-flatfile-encoder.md) | Oferece interoperabilidade entre dados de arquivo simples (como Excel e csv) e dados XML. Este aplicativo de API pode converter uma instância de arquivo simples em XML e vice-versa.
[Codificador JSON do BizTalk](app-service-logic-connector-jsonencoder.md) | Um codificador e um decodificador que ajuda a interoperabilidade do aplicativo entre dados JSON e XML. Pode converter uma determinada instância JSON para XML e vice-versa.
[Regras do BizTalk](app-service-logic-use-biztalk-rules.md) | Usar as regras do BizTalk para definir e controlar a lógica de negócios em uma organização. Políticas de negócios podem ser atualizadas sem recompilar ou sem reimplantar os aplicativos associados.
[Gerenciamento de parceiros comerciais do BizTalk](app-service-logic-connector-tpm.md) | Define e persiste relações entre empresas usando parceiros, contratos e esquemas e certificados usados em contratos. Essas relações são impostas usando AS2, EDIFACT e aplicativos de API X12.
[Serviço de Transformação do BizTalk](app-service-logic-transform-xml-documents.md) | Converte dados de um formato para outro. Você também pode carregar um mapa existente (arquivo. trfm), exibir os links entre os esquemas de origem e de destino e usar a funcionalidade de "Teste" com o conteúdo XML de entrada de exemplo. Diferentes funções internas também estão disponíveis, incluindo manipulações de cadeia de caracteres, atribuição condicional e muito mais.
[BizTalk X12](app-service-logic-connector-x12.md) | Recebe e envia mensagens usando o protocolo X12 em comunicações entre empresas.
[Validador de XML do BizTalk](app-service-logic-xml-validator.md) | Valida os dados XML com relação a esquemas XML predefinidos. Você pode usar esquemas existentes ou gerar esquemas com base em uma instância de arquivo simples, instância JSON ou em conectores existentes.
[Extrator de XPath do BizTalk](app-service-logic-xpath-extract.md) | Procura e extrai dados de conteúdo XML com base em um XPath que você escolher.
[Conector do DB2](app-service-logic-connector-db2.md) | Conecta-se a um banco de dados IBM DB2, local e em uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix. <br/><br/>Sem gatilhos. As ações incluem as instruções de tabela selecionar, inserir, atualizar, excluir e personalizar<br/><br/>Esse conector também inclui o Microsoft Client para DRDA para conectar a um servidor Informix em uma rede TCP/IP.
[Arquivo](app-service-logic-connector-file.md) | Usando esse conector, você pode se conectar ao sistema de arquivos local ou em rede e concluir diferentes tarefas de arquivo, incluindo carregar, excluir, listar arquivos e muito mais.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Conecta-se a um servidor FTP / FTPS e executa tarefas FTP diferentes, incluindo carregar, obter, excluir arquivos e muito mais.
[Informix](app-service-logic-connector-informix.md) | Conecta-se a um banco de dados IBM Informix, local e em uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix.<br/><br/>Sem gatilhos. As ações incluem as instruções de tabela selecionar, inserir, atualizar, excluir e personalizar.<br/><br/>Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. Esse conector também inclui um Microsoft Client para DRDAA para conectar a um servidor do Informix em uma rede TCP/IP.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Conecta-se ao SQL Server local ou a um Banco de Dados SQL do Azure. Você pode criar, atualizar, obter e excluir entradas em uma tabela de banco de dados SQL.
MQ | Conecta-se ao IBM WebSphere MQ Server versão 8, localmente e em uma máquina virtual do Azure executando um sistema operacional Windows. Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. O conector também inclui o Microsoft Client para MQ.<br/><br/>Sem gatilhos. Sem ações.<br/><br/>**Observação** Atualmente não pode ser usado com Aplicativos Lógicos.
[Banco de Dados do Oracle](app-service-logic-connector-oracle.md) | Conecta-se ao banco de dados do Oracle no local e pode criar, atualizar, obter e excluir as entradas em uma tabela de banco de dados.
[POP3](app-service-logic-connector-pop3.md) (Protocolo POP3)| Conecte-se a um servidor POP3 para recuperar emails com anexos.
[SAP](app-service-logic-connector-sap.md) | Conecta-se a um servidor SAP local e invoca RFCs, BAPIs e tRFCs e envia IDOCs.

## Conectores como gatilhos
Vários conectores fornecem gatilhos para aplicativos lógicos. Os gatilhos são de dois tipos:

1. Gatilhos de Pesquisa: esses gatilhos pesquisam seu serviço de interesse a uma frequência especificada para verificar se há novos dados. Quando novos dados estão disponíveis, uma nova instância do Aplicativo Lógico é executada com os dados como entrada. O gatilho pode executar tarefas adicionais, como limpeza dos dados lidos e transmitidos para o Aplicativo Lógico para impedir que os mesmos dados sejam consumidos várias vezes. Exemplos de tais conectores são Arquivo, SQL e Armazenamento do Azure.
2. Gatilhos Push: esses gatilhos escutam em busca de dados em um ponto de extremidade ou da ocorrência de eventos. Em seguida, disparam uma nova instância de um Aplicativo Lógico. Exemplos de tais conectores são HTTP Listener e o Twitter.

## Conectores como ações
Os conectores também podem ser usados como ações dentro do Aplicativo Lógico. Ações são úteis para pesquisar dados no Aplicativo Lógico que podem, em seguida, ser usado na execução. Por exemplo, talvez seja necessário pesquisar dados de um banco de dados SQL para obter informações adicionais sobre um cliente durante o processamento de um pedido. Ou então, talvez seja necessário escrever, atualizar ou excluir dados em um destino. Você pode fazer isso usando as ações fornecidas pelos conectores. As ações são mapeadas para operações em aplicativos de API (conforme definido pelos seus metadados Swagger).

## Criar seus próprios conectores e aplicativos de AP
[Connectors and API Apps Reference (Referência de conectores e de aplicativos de API)](http://aka.ms/appservicesconnectorreference) [Gatilhos de aplicativo de API do Serviço de Aplicativo do Azure](../app-service-api/app-service-api-dotnet-triggers.md) [Logic App Reference (Referência de aplicativo lógico)](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## Mais informações sobre conectores e aplicativos de API
[O que são aplicativos de API do BizTalk e conectores](app-service-logic-what-are-biztalk-api-apps.md) [Usando o Gerenciador de Conexão Híbrida no Serviço de Aplicativo do Azure](app-service-logic-hybrid-connection-manager.md) [Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md)

<!---HONumber=AcomDC_0810_2016-->