<properties 
   pageTitle="Usando conectores" 
   description="Usando conectores" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="prkumar"/>

#Conectores#
Os conectores são aplicativos de API que permitem que você se conecte a dados e serviços em execução na nuvem ou localmente. Conectores facilitam o acesso a dados com uma variedade de gatilhos internos e as ações que podem ser acessadas facilmente em aplicativos lógicos e muito mais.

Os Serviços de Aplicativos do Azure oferecem uma série de conectores prontos para uso:

##Conectores padrão##
* [Conector de barramento de serviço do Azure]
* [Conector do blob de armazenamento do Azure]
* Conector de trabalhos Web do Azure
* [Conector de caixa]
* [Conector do Chatter]
* [Conector do DropBox]
* [Conector do Facebook]
* [Conector de arquivo]
* [Conector de FTP]
* [Conector do HDInsight]
* [Conector de HTTP]
* [Conector do Office 365]
* [Conector do OneDrive]
* [Conector do Oracle]
* [Conector POP3]
* [Conector do QuickBooks]
* [Conector do Salesforce]
* [Conector de SFTP]
* [Usando o conector do Sharepoint]
* [Conector de margem de atraso]
* [Conector de SMTP]
* [Conector do SQL]
* [Conector do SugarCRM]
* [Conector do Twilio]
* [Conector do Twitter]
* Aguarde o conector
* [Conector do Yammer]

##Conectores Premium e aplicativos deAPI##
* Conector AS2
* BizTalk EDIFACT
* Codificador de arquivo simples do BizTalk
* Serviço de transformação do BizTalk
* Regras do BizTalk
* BizTalk X12
* Extrator XPath do BizTalk
* Validador de XML do BizTalk
* Conector do DB2
* Conector do Informix
* Conector do Banco de Dados Oracle
* Conector MQ
* [Conector do SAP]
 
Para obter mais detalhes, consulte a Referência de API do Conector [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).

##Aplicativos lógicos e conectores##
Aplicativos lógicos são compostos por gatilhos e ações. Alguns conectores podem ser usados como gatilhos para criar uma instância de um fluxo de trabalho com base em um evento ou na disponibilidade de alguns dados. Conectores também são usados como ações para ler e gravar dados ou executar outras ações às quais o conector tenha suporte.

###Conectores como gatilhos###
Vários conectores fornecem gatilhos para aplicativos lógicos. Os gatilhos são de dois tipos:

1. Gatilhos pull: esses gatilhos pesquisarão seu serviço de interesse à frequência especificada para verificar se há novos dados. Quando novos dados estão disponíveis, uma nova instância do aplicativo lógico será executada com os dados como entrada. O gatilho pode executar tarefas adicionais, como limpeza dos dados lidos e transmitidos para o aplicativo lógico para impedir que os mesmos dados sejam consumidos várias vezes. Exemplos de tais conectores são Arquivo, SQL e Armazenamento do Azure.
2. Gatilhos push: esses gatilhos escutam dados em um ponto de extremidade ou a ocorrência de eventos e disparam uma nova instância de um aplicativo lógico. Exemplos de tais conectores são HTTP Listener e o Twitter.

###Conectores como ações###
Os conectores também podem ser usados como ações como parte do aplicativo lógico. Ações são úteis para pesquisar dados no aplicativo lógica a ser usado na execução, por exemplo, talvez seja necessário pesquisar dados de um banco de dados SQL para obter informações adicionais sobre um cliente durante o processamento de um pedido. Como alternativa, talvez seja necessário gravar, atualizar ou excluir dados em um destino, para o que você pode usar as ações fornecidas pelos conectores. Ações são mapeadas para operações em aplicativos de API (conforme definido pelos seus metadados Swagger).


<!-- Links -->

[Conector de caixa]: app-service-logic-connector-box.md
[Conector do Facebook]: app-service-logic-connector-facebook.md
[Conector do Salesforce]: app-service-logic-connector-salesforce.md
[Conector do Twitter]: app-service-logic-connector-twitter.md
[Conector do SAP]: app-service-logic-connector-sap.md
[Conector de FTP]: app-service-logic-connector-ftp.md
[Conector de HTTP]: app-service-logic-connector-http.md
[Conector do blob de armazenamento do Azure]: app-service-logic-connector-azurestorageblob.md
[Conector do Office 365]: app-service-logic-connector-office365.md
[Usando o conector do Sharepoint]: app-service-logic-connector-sharepoint.md
[Conector do SugarCRM]: app-service-logic-connector-sugarcrm.md
[Conector do QuickBooks]: app-service-logic-connector-quickbooks.md
[Conector do Yammer]: app-service-logic-connector-yammer.md
[Conector do Twilio]: app-service-logic-connector-twilio.md
[Conector de SMTP]: app-service-logic-connector-smtp.md
[Conector de SFTP]: app-service-logic-connector-sftp.md
[Conector POP3]: app-service-logic-connector-pop3.md
[Conector do DropBox]: app-service-logic-connector-dropbox.md
[Conector do Chatter]: app-service-logic-connector-chatter.md
[Conector do HDInsight]: app-service-logic-connector-hdinsight.md
[Conector de barramento de serviço do Azure]: app-service-logic-connector-azureservicebus.md
[Conector do Oracle]: app-service-logic-connector-oracle.md
[Conector do SQL]: app-service-logic-connector-sql.md
[Conector do OneDrive]: app-service-logic-connector-onedrive.md
[Conector de arquivo]: app-service-logic-connector-file.md
[Conector de margem de atraso]: app-service-logic-connector-slack.md


<!--HONumber=54-->