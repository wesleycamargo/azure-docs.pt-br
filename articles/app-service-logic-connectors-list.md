<properties 
	pageTitle="Lista de conectores e aplicativos de API | Azure" 
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
	ms.date="03/31/2015" 
	ms.author="mandia"/>


# Lista de conectores e aplicativos de API no Serviço de Aplicativo do Microsoft Azure
Este tópico lista todos os conectores e aplicativos de API disponíveis, criados pela Microsoft.

Para obter informações de preço e uma lista do que é incluído com cada camada de serviço, consulte [Preços do Serviço de Aplicativo do Azure](http://azure.microsoft.com/pricing/details/app-service/).


## Conectores padrão
A tabela a seguir lista todos os conectores e aplicativos de API criados pela Microsoft, que estão disponíveis com os conectores Padrão:

<table border="1">
<tr bgcolor="FAF9F9">
        <th>Nome</th>
        <th>Descrição</th>
</tr>

<tr>
<td colspan="2"><strong>Aplicativo + conectores de serviços de dados</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-data-connectors/"> Aplicativo + conectores de serviços de dados</a> fornece informações mais detalhadas sobre esses conectores.</td>
</tr>

<tr>
<td>Azure HDInsight</td>
<td>O Azure HDInsight implanta e provisiona clusters do Apache Hadoop na nuvem e fornece uma estrutura de software criada para gerenciar, analisar e relatar Big Data. Esse conector cria um cluster do Hadoop no Azure e envia diferentes trabalhos do Hadoop, como Pig, Hive, MapReduce e Streaming MapReduce. Com esse conector, você pode criar um cluster, enviar um trabalho e aguardar a conclusão do trabalho.</td>
</tr>

<tr>
<td>Service Bus do Azure</td>
<td>O Barramento de Serviço do Azure é um sistema de mensagens genérico, baseado em nuvem, para conectar praticamente qualquer coisa. Ele habilita a troca de mensagens de maneira menos rígida, para melhor dimensionamento e confiabilidade. Usando esse conector, você pode enviar mensagens de entidades do Barramento de Serviço, como Filas e Tópicos, e receber mensagens de entidades do Barramento de Serviço, como Filas e Assinaturas.</td>
</tr>

<tr>
<td>Blob de Armazenamento do Azure</td>
<td>O Blob de Armazenamento do Azure armazena grandes quantidades de dados não estruturados, como dados binários ou texto, que podem ser acessados de qualquer lugar do mundo. Usando esse conector, você pode se conectar ao armazenamento de blob e carregar, obter e excluir blobs, listar blobs no contêiner, fazer instantâneos dos blobs, copiar blobs e usar um gatilho para recuperar blobs.</td>
</tr>

<tr>
<td>Box</td>
<td>O Box é um serviço de compartilhamento de arquivos. O Conector do Box conecta ao Box e pode carregar, obter, excluir e listar arquivos e usa um gatilho para recuperar arquivos.</td>
</tr>

<tr>
<td>Dropbox</td>
<td>O Dropbox é um serviço de hospedagem de arquivos. Usando esse conector, você pode se conectar ao Dropbox e pode carregar, obter, excluir e listar arquivos e usar um gatilho para recuperar arquivos.</td>
</tr>

<tr>
<td>Microsoft Office 365</td>
<td>O Conector do Office 365 pode enviar e receber emails, gerenciar seu calendário e gerenciar seus contatos usando sua conta do Office 365. Você pode também enviar, receber e obter emails, criar e excluir eventos de seu calendário e criar, atualizar, obter e excluir seus contatos.</td>
</tr>

<tr>
<td>Microsoft OneDrive</td>
<td>O Microsoft OneDrive é um serviço de hospedagem de arquivos. O conector do OneDrive conecta ao seu Microsoft OneDrive pessoal e pode carregar, obter, excluir e listar arquivos e usar um gatilho para recuperar arquivos.</td>
</tr>

<tr>
<td>Microsoft SharePoint</td>
<td>O Conector do Microsoft SharePoint conecta ao SharePoint Online ou ao Microsoft SharePoint Server local e pode gerenciar documentos e listar itens. Você também pode criar, atualizar, obter e excluir documentos e itens de lista. Há suporte para diferentes métodos de autenticação, como credenciais padrão, OAuth 2.0, autenticação do Windows e autenticação baseada em formulário.</td>
</tr>

<tr>
<td>Microsoft SQL Server</td>
<td>O conector do Microsoft SQL conecta ao SQL Server local ou ao Banco de Dados SQL do Azure. Você pode criar, atualizar, obter e excluir entradas em uma tabela de banco de dados SQL.</td>
</tr>

<tr>
<td colspan="2"><strong>Conectores sociais</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-social-connectors/">Conectores sociais</a> fornece informações mais detalhadas sobre esses conectores.</td>
</tr>

<tr>
<td>Chatter</td>
<td>O Chatter é uma rede social empresarial. O Conector do Chatter conecta ao Chatter e pode postar mensagens, pesquisar e usar um gatilho para recuperar novas mensagens.</td>
</tr>

<tr>
<td>Facebook</td>
<td>O Facebook é um serviço de rede social. O conector do Facebook conecta ao Facebook e pode publicar mensagens, vídeos, fotos, ofertas e assim por diante. Você também pode recuperar mensagens, comentários, eventos, feeds de usuários e diferentes informações do usuário, como livros, jogos, filmes e assim por diante. Esse conector também inclui um gatilho para recuperar novas mensagens postadas em uma página.</td>
</tr>

<tr>
<td>Microsoft Yammer</td>
<td>O Microsoft Yammer é uma rede social empresarial. O conector do Yammer conecta ao Yammer, inclui uma ação de postagem de mensagem e um gatilho para recuperar novas mensagens.</td>
</tr>

<tr>
<td>Twilio</td>
<td>O Twilio é um serviço SaaS voltado à comunicação. O Conector do Twilio conecta-se ao Twilio e pode enviar, obter e listar mensagens, listar o uso, obter números de telefone gratuitos, números de celular, números locais e números de chamadas recebidas disponíveis, números de chamadas recebidas e adicionar números de chamadas recebidas.</td>
</tr>

<tr>
<td>Twitter</td>
<td>O Twitter é um serviço de rede social online que permite aos usuários enviar e ler mensagens curtas de até 140 caracteres. O Conector do Twitter conecta ao Twitter e pode obter a linha do tempo dos usuários, pesquisar tweets, obter seguidores, obter amigos, pesquisar usuários, obter a linha do tempo da página inicial, obter a linha do tempo de referências, postar tweets, postar tweets para um usuário e enviar mensagens diretas. O Conector do Twitter também usa gatilhos, como Obter tweets por palavra-chave, Obter tweets por usuário e Obter tweets por hashtag.</td>
</tr>

<tr>
<td colspan="2"><strong>Conectores de protocolo</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-protocol-connectors/">Conectores protocolo</a> fornece informações mais detalhadas sobre esses conectores.</td>
</tr>

<tr>
<td>Arquivo</td>
<td>O Conector de Arquivo permite aos clientes enviar ou carregar arquivos em um sistema de arquivos ou rede e receber ou baixar arquivos de um sistema de arquivos ou rede. Usando este conector, você pode se conectar ao servidor de arquivos local e carregar, obter, excluir e listar arquivos e também usar um gatilho para recuperar arquivos.</td>
</tr>

<tr>
<td>FTP<br/>Protocolo FTPS</td>
<td>O FTP (File Transfer Protocol) é um protocolo de rede popular para transferência de arquivos de um host para outro. O Conector de FTP pode carregar, obter, excluir e listar arquivos e usar um gatilho para recuperar arquivos.</td>
</tr>

<tr>
<td>Ouvinte HTTP</td>
<td>O Ouvinte HTTP abre um ponto de extremidade que atua como servidor HTTP e ouve solicitações HTTP de entrada.</td>
</tr>

<tr>
<td>POP3<br/>IMAP</td>
<td>O POP3 (Post Office Protocol) é o protocolo usado por um cliente de email para recuperar emails de um servidor de email. O Conector do POP3 conecta a um servidor POP3 e inclui um gatilho para recuperar emails com anexos.</td>
</tr>

<tr>
<td>SFTP</td>
<td>O SFTP (SSH File Transfer Protocol) é um protocolo comum usado para transferir arquivos com segurança. O Conector de SFTP pode carregar, obter, excluir e listar arquivos e usar um gatilho para recuperar arquivos.</td>
</tr>

<tr>
<td>SMTP</td>
<td>O SMTP (Simple Mail Transfer Protocol) é o protocolo usado entre um cliente e um servidor de email para enviar emails. O Conector de SMTP conecta a um servidor SMTP e envia emails com anexos.</td>
</tr>

<tr>
<td colspan="2"><strong>Conectores empresariais</strong><br/><br/><a HREF="http://azure.microsoft.com/documentation/articles/app-service-logic-enterprise-connectors">Conectores empresariais</a> fornece informações mais detalhadas sobre esses conectores.</td>
</tr>

<tr>
<td>Marketo</td>
<td>O Marketo desenvolve softwares de automação de marketing que fornecem marketing de entrada, marketing social, CRM e outros serviços relacionados. O Conector do Marketo conecta ao Marketo e pode criar/atualizar clientes potenciais, obter alterações de clientes potenciais, agendar e solicitar campanhas, obter clientes potenciais, obter informações sobre campanhas/listas, adicionar e remover clientes potenciais da lista.</td>
</tr>

<tr>
<td>QuickBooks</td>
<td>Usando o conector do QuickBooks, você pode criar, atualizar, ler, excluir e consultar entidades diferentes do Intuit QuickBooks, como clientes, itens, notas fiscais e assim por diante.</td>
</tr>

<tr>
<td>Salesforce</td>
<td>O conector do Salesforce gerencia entidades diferentes, como contas, clientes potenciais, oportunidades, casos e assim por diante na sua conta do Salesforce. Você também pode criar, atualizar, inserir, consultar e excluir diferentes entidades.</td>
</tr>

<tr>
<td>SugarCRM</td>
<td>O conector do SugarCRM conecta ao SugarCRM Online e pode criar, atualizar, ler e excluir diferentes tipos de módulos, como contas, contatos, produtos e assim por diante.</td>
</tr>

</table>


## Aplicativos de API EAI do BizTalk
A tabela a seguir lista todos os conectores e aplicativos de API criados pela Microsoft, disponíveis nos Serviços EAI do BizTalk:

	Name  | Description
------------- | -------------
Codificador de arquivo simples do BizTalk | Oferece interoperabilidade entre dados de arquivo simples (como Excel e csv) e dados XML. Este aplicativo de API pode converter uma instância de arquivo simples em XML e vice-versa.
Serviço de transformação do BizTalk | O aplicativo de API de transformação do BizTalk converte dados de um formato para outro. Você também pode carregar um mapa existente (arquivo. trfm), exibir a representação gráfica do mapa que mostra os links entre os esquemas de origem e de destino e usar a funcionalidade de "Teste" com um conteúdo XML de entrada de exemplo. Funções internas diferentes também estão disponíveis, incluindo manipulações de cadeia de caracteres, atribuições condicionais, expressões aritméticas, formatadores de data e hora, looping e assim por diante.
Validador de XML do BizTalk | O aplicativo de API do Validador valida dados XML com relação a esquemas XML predefinidos. Você pode usar esquemas existentes ou gerar esquemas com base em uma instância de arquivo simples, instância JSON ou em conectores existentes.
Extrator XPath do BizTalk | O aplicativo de API do extrator procura e extrai dados de conteúdo XML com base em determinado XPath.

Consulte [Conectores de integração do BizTalk](app-service-logic-integration-connectors.md) para obter mais informações e detalhes sobre esses aplicativos de API.


## Conectores de EDI e aplicativos de API do BizTalk
A tabela a seguir lista todos os conectores e aplicativos de API criados pela Microsoft, disponíveis nos Serviços de EDI do BizTalk:

	Name  | Description
------------- | -------------
Conector AS2 | O Conector AS2 pode receber e enviar mensagens usando o protocolo de transporte AS2 em comunicações entre empresas. Os dados são transportados de maneira segura e confiável pela Internet usando criptografia e certificados digitais.
BizTalk EDIFACT | O aplicativo de API EDIFACT recebe e envia mensagens usando o protocolo EDIFACT em comunicações entre empresas.
BizTalk X12 | O aplicativo de API X12 recebe e envia mensagens usando o protocolo X12 em comunicações entre empresas.
Gerenciamento de parceiros comerciais do BizTalk | O aplicativo de API de Gerenciamento de Parceiros Comerciais define e persiste relações entre empresas usando parceiros, contratos e esquemas e certificados usados em contratos. Essas relações são impostas usando AS2, EDIFACT e aplicativos de API X12.

Consulte [Conectores entre empresas](app-service-logic-b2b-connectors.md) para obter mais informações e detalhes sobre esses aplicativos de API.


## Aplicativo de API de regras do BizTalk
A tabela a seguir lista todos os conectores e aplicativos de API criados pela Microsoft, disponíveis nas Regras do BizTalk:

	Name  | Description
------------- | -------------
Regras do BizTalk | As Regras do BizTalk definem e controlam a estrutura, a operação e a estratégia de uma organização. Políticas de negócios podem ser atualizadas sem recompilar e reimplantar os aplicativos associados.

Consulte [Conectores de integração do BizTalk](app-service-logic-integration-connectors.md) para obter mais informações e detalhes sobre esse aplicativo de API.


## Conectores Premium
A tabela a seguir lista todos os conectores e Aplicativos de API criados pela Microsoft que estão disponíveis em Conectores Premium:

	Name  | Description
------------- | -------------
Conector do DB2 | O conector do DB2 conecta a um banco de dados IBM DB2, local e em uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix. As ações incluem as instruções de tabela select, insert, update, delete e custom<br/><br/>[Conectores de Aplicativo + Serviços de Dados](app-service-logic-data-connectors.md)
Informix | O conector do Informix conecta a um banco de dados IBM Informix local e a uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix. As ações incluem as instruções de tabela select, insert, update, delete e custom<br/><br/>[Aplicativo + Conectores de serviços de dados](app-service-logic-data-connectors.md)
MQ | O conector do MQ se conecta ao IBM WebSphere MQ Server, localmente e em uma máquina virtual do Azure que executa um sistema operacional Windows.<br/><br/><strong>Observação</strong> Atualmente não pode ser usado com aplicativos lógicos.<br/><br/>[Aplicativo + conectores de serviços de dados](app-service-logic-data-connectors.md)
Banco de dados Oracle | O conector de banco de dados Oracle se conecta ao banco de dados da Oracle local e pode criar, atualizar, obter e excluir as entradas em uma tabela de banco de dados. <br/><br/>[Aplicativo + conectores de serviços de dados](app-service-logic-data-connectors.md)
SAP | O conector do SAP conecta a um servidor SAP local e invoca RFCs, BAPIs e tRFCs e envia IDOCs.<br/><br/>[Conectores empresariais](app-service-logic-enterprise-connectors.md)


## Criar seus próprios conectores e aplicativos de AP
Consulte [Criar conectores com APIs REST](http://go.microsoft.com/fwlink/p/?LinkId=529766).


## Mais conectores

[Conectores de integração do BizTalk](app-service-logic-integration-connectors.md)<br/> [Conectores empresariais](app-service-logic-enterprise-connectors.md)<br/> [Conectores entre empresas](app-service-logic-b2b-connectors.md)<br/> [Conectores sociais](app-service-logic-social-connectors.md)<br/> [Conectores de protocolo](app-service-logic-protocol-connectors.md)<br/> [API+ conectores de serviços de dados](app-service-logic-data-connectors.md)<br/><br/> [O que são conectores e aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)

<!--HONumber=54-->