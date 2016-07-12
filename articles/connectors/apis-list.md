<properties
	pageTitle="Lista de conectores gerenciados pela Microsoft para uso em aplicativos Lógicos do Microsoft Azure | Serviço de Aplicativo do Microsoft Azure"
	description="Obter uma lista completa dos conectores Gerenciados pela Microsoft que você pode usar para compilar os Aplicativos lógicos no Serviço de Aplicativo do Azure"
	services="app-service\logic"
	documentationCenter=""
	authors="MSFTMAN"
	manager="erikre"
	editor=""
    tags="connectors"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/31/2016"
	ms.author="deonhe"/>

# Lista de conectores

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos Lógicos. Para obter a versão do esquema 2014-12-01-preview, clique em [Lista de Conectores](../app-service-logic/app-service-logic-connectors-list.md).

Para obter informações de preço e uma lista do que é incluído com cada camada de serviço, consulte [Preços do Serviço de Aplicativo do Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Se você quiser começar a usar os Aplicativos Lógicos do Azure antes de se inscrever em uma conta do Azure, acesse [Experimentar o aplicativo lógico](https://tryappservice.azure.com/?appservice=logic). Você pode criar instantaneamente um aplicativo lógico de inicialização e de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## Conectores padrão

Selecione um ícone para saber como aproveitar rapidamente esses conectores para compilar aplicativos que chamam esses serviços. Esses conectores podem ser usados para compilar aplicativos Lógicos, PowerApps e Fluxos.

|Conectores||||
|-----------|-----------|-----------|-----------|
|[![Ícone de API][blobicon]<br/>**Blob do Azure**][azureblobdoc]|[![Ícone de API][boxicon]<br/>**Box**][boxDoc]|[![Ícone de API][crmonlineicon]<br/>**CRM Online**][crmonlinedoc]|[![Ícone de API][dropboxicon]<br/>**Dropbox**][dropboxdoc]|
|[![Ícone de API][facebookicon]<br/>**Facebook**][facebookdoc]|[![Ícone de API][ftpicon]<br/>**FTP**][ftpdoc]|[![Ícone de API][githubicon]<br/>**GitHub**][githubdoc]|[![Ícone de API][googledriveicon]<br/>**Google Drive**][googledrivedoc]|
|[![Ícone de API][mailchimpicon]<br/>**MailChimp**][mailchimpdoc]|[![Ícone de API][microsofttranslatoricon]<br/>**Translator**][microsofttranslatordoc]|[![Ícone de API][office365icon]<br/>**Office 365**<br/>**Outlook**][office365outlookdoc]|[![Ícone de API][office365icon]<br/>**Office 365**<br/>**Usuários**][office365usersdoc]|
|[![Ícone de API][office365icon]<br/>**Office 365**<br/>**Vídeo**][office365videodoc]|[![Ícone de API][onedriveicon]<br/>**OneDrive**][onedrivedoc]|[![Ícone de API][onedriveicon]<br/>**OneDrive<br/>for Business**][onedriveforbusinessdoc]|[![Ícone de API][outlookicon]<br/>**Outlook**][outlookdoc]|
|[![Ícone de API][projectonlineicon]<br/>**Project<br/>Online**][projectonlinedoc]|[![Ícone de API][rssicon]<br/>**RSS**][rssdoc]|[![Ícone de API][salesforceicon]<br/>**Salesforce**][salesforcedoc]|[![Ícone de API][sendgridicon]<br/>**SendGrid**][sendgriddoc]|
|[![Ícone de API][servicebusicon]<br/>**Barramento de Serviço**][servicebusdoc]|[![Ícone de API][sftpicon]<br/>**SFTP**][sftpdoc]|[![Ícone de API][sharepointicon]<br/>**SharePoint**<br/>**Online**][sharepointdoc]|[![Ícone de API][slackicon]<br/>**Slack**<br/>][slackdoc]|
|[![Ícone de API][smtpicon]<br/>**SMTP**][smtpdoc]|[![Ícone de API][sqlicon]<br/>**SQL Azure**][sqldoc]|[![Ícone de API][trelloicon]<br/>**Trello**][trellodoc]|[![Ícone de API][twilioicon]<br/>**Twilio**][twiliodoc]|
|[![Ícone de API][twittericon]<br/>**Twitter**][twitterdoc]|[![Ícone de API][wunderlisticon]<br/>**Wunderlist**][wunderlistdoc]|[![Ícone de API][yammericon]<br/>**Yammer**][yammerdoc] | |

## Conectores do EIP (Enterprise Integration Pack)
Use os conectores do EIP para criar aplicativos Lógicos para cenários B2B que incluem EAI e EDI.
 
|Conectores EIP ||||
|-----------|-----------|-----------|-----------|
|[![Ícone de API][as2icon]<br/>**AS2</br>codificar/decodificar**][as2doc]|[![Ícone de API][x12icon]<br/>**X12</br>codificar/decodificar**][x12Doc]|[![Ícone de API][xmlvalidateicon]<br/>**XML <br/>validação**][xmlvalidatedoc]|[![Ícone de API][xmltransformicon]<br/>**XML<br/> transformar**][xmltransformdoc]|
|[![Ícone de API][flatfileicon]<br/>**Arquivo simples</br>codificar**][flatfiledoc]|[![Ícone de API][flatfiledecodeicon]<br/>**Arquivo simples</br>decodificar**][flatfiledecodedoc]|||


### Os conectores podem ser gatilhos
Vários conectores fornecem gatilhos que podem notificar seu aplicativo quando eventos específicos ocorrem. Por exemplo, o conector do FTP tem o gatilho OnUpdatedFile. É possível compilar um Aplicativo lógico, PowerApp ou Fluxo que escuta esse gatilho e executa uma ação sempre que ele é acionado.

Há dois tipos de gatilhos:

* Gatilhos de Pesquisa: esses gatilhos pesquisam seu serviço de interesse a uma frequência especificada para verificar se há novos dados. Quando novos dados estão disponíveis, uma nova instância do seu aplicativo é executada com os dados como entrada. O gatilho pode limpar os dados já lidos e transmitidos para seu aplicativo para impedir que os mesmos dados sejam consumidos várias vezes.
* Gatilhos Push: esses gatilhos escutam em busca de dados em um ponto de extremidade ou da ocorrência de eventos. Em seguida, ele dispara uma nova instância do seu aplicativo. O conector do twitter é um exemplo.


### Os conectores podem ser ações
Os conectores também podem ser usados como ações em seus aplicativos. Ações são úteis para pesquisar dados no que podem então ser usados na execução do seu aplicativo. Por exemplo, talvez seja necessário pesquisar dados de um cliente de um banco de dados SQL durante o processamento de um pedido. Ou então, talvez seja necessário escrever, atualizar ou excluir dados em uma tabela de destino. Você pode fazer isso usando as ações fornecidas pelos conectores. As ações são mapeadas para as operações que são definidas nos metadados do Swagger.


[Novidades](../app-service-logic/app-service-logic-schema-2015-08-01.md) [Compilar um aplicativo Lógico agora](../app-service-logic/app-service-logic-create-a-logic-app.md) [Introdução aos PowerApps agora](../power-apps/powerapps-get-started-azure-portal.md) [Migrar Aplicativos lógicos para a versão mais recente do esquema](connectors-schema-migration.md)

<!--Connectors Documentation-->
[azureblobdoc]: ./connectors-create-api-azureblobstorage.md "Conecte-se ao Blob do Azure para gerenciar arquivos no seu contêiner de blob."
[bingsearchDoc]: ./connectors-create-api-bingsearch.md "Pesquisa do Bing para Web, imagens, notícias e vídeo."
[boxDoc]: ./connectors-create-api-box.md "Conecta-se a caixa e pode carregar, obter, excluir, listar e mais tarefas de arquivo."
[crmonlinedoc]: ./connectors-create-api-crmonline.md "Conecte-se ao Dynamics CRM Online e faça mais com os dados do CRM Online."
[dropboxdoc]: ./connectors-create-api-dropbox.md "Conecte-se ao Dropbox e pode obter, excluir, listar e mais tarefas de arquivo."
[exceldoc]: ./connectors-create-api-excel.md "Conecte-se ao Excel."
[facebookdoc]: ./connectors-create-api-facebook.md "Conecte-se ao Facebook para postar em uma linha do tempo, receber um feed de página e muito mais."
[ftpdoc]: ./connectors-create-api-ftp.md "Conecta-se a um servidor FTP / FTPS e executa tarefas FTP diferentes, incluindo carregar, obter, excluir arquivos e muito mais."
[googledrivedoc]: ./connectors-create-api-googledrive.md "Conecte-se ao GoogleDrive e interaja com os dados."
[microsofttranslatordoc]: ./connectors-create-api-microsofttranslator.md
[office365outlookdoc]: ./connectors-create-api-office365-outlook.md "O Conector do Office 365 pode enviar e receber emails, gerenciar seu calendário e gerenciar seus contatos usando sua conta do Office 365."
[officeunifieddoc]: ./connectors-create-api-bingsearch.md
[office365usersdoc]: ./connectors-create-api-office365-users.md
[office365videodoc]: ./connectors-create-api-office365-video.md
[onedrivedoc]: ./connectors-create-api-onedrive.md "Se conecta ao seu Microsoft OneDrive pessoal e carrega, exclui, lista arquivos e muito mais."
[onedriveforbusinessdoc]: ./connectors-create-api-onedriveforbusiness.md "Conecta seu Microsoft OneDrive corporativo e carrega, exclui, lista os arquivos e muito mais."
[outlookdoc]: ./connectors-create-api-outlook.md "Conecte sua caixa de correio do Outlook e acesse seu email e muito mais."
[projectonlinedoc]: ./connectors-create-api-projectonline.md "Conecta o Microsoft Project Online."
[rssdoc]: ./connectors-create-api-rss.md "O conector RSS permite que os usuários publiquem e recuperem itens de feed. Também permite que os usuários inicializem operações quando um novo item é publicado no feed."
[salesforcedoc]: ./connectors-create-api-salesforce.md "Conecte-se à sua conta da equipe de vendas e gerencia contas, clientes potenciais, oportunidades e muito mais."
[sendgriddoc]: ./connectors-create-api-sendgrid.md "Conecta o Microsoft Project Online."
[servicebusdoc]: ./connectors-create-api-servicebus.md "Pode enviar mensagens de tópicos e filas do Barramento de Serviço e receber mensagens de assinaturas e filas do Barramento de Serviço."
[sharepointdoc]: ./connectors-create-api-sharepointonline.md "Conecta-se ao SharePoint Online para gerenciar documentos e itens de lista."
[slackdoc]: ./connectors-create-api-slack.md "Conecte-se à margem de atraso e poste mensagens em canais de margem de atraso."
[sftpdoc]: ./connectors-create-api-sftp.md "Conecta-se ao SFTP e pode carregar, obter, excluir arquivos e muito mais."
[githubdoc]: ./connectors-create-api-github.md "Conecta o GitHub e pode acompanhar os problemas."
[mailchimpdoc]: ./connectors-create-api-mailchimp.md "Envie Email Melhor."
[smtpdoc]: ./connectors-create-api-smtp.md "Conecta-se a um servidor SMTP e pode enviar emails com anexos."
[sqldoc]: ./connectors-create-api-sqlazure.md "Conecte-se ao Banco de Dados SQL Azure. Você pode criar, atualizar, obter e excluir entradas em uma tabela de banco de dados SQL."
[trellodoc]: ./connectors-create-api-trello.md "Trello é a maneira gratuita, flexível e visual de organizar qualquer coisa com qualquer pessoa."
[twiliodoc]: ./connectors-create-api-twilio.md "Conecta-se a Twilio e pode enviar e receber mensagens, obter os números disponíveis, gerenciar de números de telefone de entrada e muito mais."
[twitterdoc]: ./connectors-create-api-twitter.md "Se conecta ao Twitter e obtém cronogramas, posta tweets e muito mais."
[wunderlistdoc]: ./connectors-create-api-wunderlist.md "Mantenha sua vida sincronizada."
[yammerdoc]: ./connectors-create-api-yammer.md "Conecta-se no Yammer para postar mensagens e obter novas mensagens."
[as2doc]: ../app-service-logic/app-service-logic-enterprise-integration-as2.md "Saiba mais sobre a integração corporativa do AS2."
[x12doc]: ../app-service-logic/app-service-logic-enterprise-integration-x12.md "Saiba mais sobre a integração corporativa do X12"
[flatfiledoc]: ../app-service-logic/app-service-logic-enterprise-integration-flatfile.md "Saiba mais sobre o arquivo simples de integração corporativa."
[flatfiledecodedoc]: ../app-service-logic/app-service-logic-enterprise-integration-flatfile.md "Saiba mais sobre o arquivo simples de integração corporativa."
[xmlvalidatedoc]: ../app-service-logic/app-service-logic-enterprise-integration-xml.md "Saiba mais sobre a validação de XML de integração corporativa."
[xmltransformdoc]: ../app-service-logic/app-service-logic-enterprise-integration-transform.md "Saiba mais sobre as transformações de integração corporativa."

<!--Icon references-->
[blobicon]: ./media/apis-list/blobicon.png
[bingsearchicon]: ./media/apis-list/bingsearchicon.png
[boxicon]: ./media/apis-list/boxicon.png
[ftpicon]: ./media/apis-list/ftpicon.png
[githubicon]: ./media/apis-list/githubicon.png
[crmonlineicon]: ./media/apis-list/dynamicscrmicon.png
[dropboxicon]: ./media/apis-list/dropboxicon.png
[excelicon]: ./media/apis-list/excelicon.png
[facebookicon]: ./media/apis-list/facebookicon.png
[googledriveicon]: ./media/apis-list/googledriveicon.png
[mailchimpicon]: ./media/apis-list/mailchimpicon.png
[microsofttranslatoricon]: ./media/apis-list/translatoricon.png
[office365icon]: ./media/apis-list/office365icon.png
[onedriveicon]: ./media/apis-list/onedriveicon.png
[onedriveforbusinessicon]: ./media/apis-list/onedriveforbusinessicon.png
[outlookicon]: ./media/apis-list/outlookicon.png
[projectonlineicon]: ./media/apis-list/projectonlineicon.png
[rssicon]: ./media/apis-list/rssicon.png
[salesforceicon]: ./media/apis-list/salesforceicon.png
[sendgridicon]: ./media/apis-list/sendgridicon.png
[servicebusicon]: ./media/apis-list/servicebusicon.png
[sftpicon]: ./media/apis-list/sftpicon.png
[sharepointicon]: ./media/apis-list/sharepointicon.png
[slackicon]: ./media/apis-list/slackicon.png
[smtpicon]: ./media/apis-list/smtpicon.png
[sqlicon]: ./media/apis-list/sqlicon.png
[trelloicon]: ./media/apis-list/trelloicon.png
[twilioicon]: ./media/apis-list/twilioicon.png
[twittericon]: ./media/apis-list/twittericon.png
[wunderlisticon]: ./media/apis-list/wunderlisticon.png
[yammericon]: ./media/apis-list/yammericon.png
[as2icon]: ./media/apis-list/as2new.png
[x12icon]: ./media/apis-list/x12new.png
[flatfileicon]: ./media/apis-list/flatfileencoding.png
[flatfiledecodeicon]: ./media/apis-list/flatfiledecoding.png
[xmlvalidateicon]: ./media/apis-list/xmlvalidation.png
[xmltransformicon]: ./media/apis-list/xsltransform.png

<!---HONumber=AcomDC_0706_2016-->