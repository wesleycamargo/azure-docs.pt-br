<properties 
   pageTitle="Usando conectores" 
   description="Usando conectores" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="prkumar" 
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


#Usando conectores#

Os conectores são aplicativos de API que permitem se conectar aos serviços em execução na nuvem ou localmente para obter, colocar dados ou executar ações.  Eles podem ser usados nos Serviços de Aplicativos do Azure, aplicativos Web, móveis e lógicos.

O item Serviços de Aplicativos do Azure fornece vários conectores prontos para uso para ajudá-lo a se conectar aos serviços de seu interesse, concentrando seus esforços na lógica de negócios real do seu aplicativo.  Além disso, a plataforma é facilmente extensível escrevendo seus próprios aplicativos de API para conexão aos seus aplicativos herdados.

Estes são alguns dos principais recursos dos conectores do BizTalk:

1. Lidar com segurança OAuth para se conectar a serviços populares de SaaS (Facebook, Twitter, Office 365, SalesForce)
2. Conectividade aos ativos locais por meio de uma Retransmissão do Barramento de Serviço.
3. Conectividade para protocolos comuns receber e enviar dados.

##Aplicativos lógicos e conectores##
Aplicativos lógicos são compostos por gatilhos e ações.  Alguns conectores podem ser usados como gatilhos para identificar se os novos dados estão disponíveis e o acionamento de um aplicativo lógico com esses dados como entrada.  Conectores também podem ser usados como uma ação no meio de um aplicativo lógico para pesquisar dados, gravar dados ou executar outras ações que tenham suporte no conector.

###Conectores como gatilhos###
Vários conectores fornecem gatilhos para aplicativos lógicos.  Os gatilhos são de dois tipos:

1. Gatilhos de pesquisa:  Esses gatilhos pesquisarão seu serviço de interesse à frequência especificada para verificar se há novos dados.  Quando novos dados estão disponíveis, uma nova instância do aplicativo lógico será executada com os dados como entrada.  O gatilho pode executar tarefas adicionais, como limpeza dos dados lidos e transmitidos para o aplicativo lógico para impedir que os mesmos dados sejam consumidos várias vezes.  Exemplos de tais conectores são Arquivo, SQL e Armazenamento do Azure.
2. Gatilhos por push:  Esses gatilhos escutam dados em um ponto de extremidade ou a ocorrência de eventos e disparam uma nova instância de um aplicativo lógico.  Exemplos de tais conectores são HTTP Listener e o Twitter.

###Conectores como ações###
Os conectores também podem ser usados como ações como parte do aplicativo lógico.  Isso pode ser útil para buscar dados no aplicativo lógico a ser usado na execução, por exemplo, talvez seja necessário consultar um banco de dados SQL para obter informações adicionais sobre um cliente ao processar um pedido do cliente em um aplicativo lógico.  Como alternativa, talvez seja necessário gravar, atualizar ou excluir dados em um destino, para o que você pode usar as ações fornecidas pelos conectores.

##Como usar conectores##
Os artigos a seguir fornecem exemplos de como alguns dos conectores prontos para uso podem ser usados.

* [Usando o conector HTTP]
* [Usando o conector FTP]
* [Usando o conector Box]
* [Usando o conector de Blob de armazenamento do Azure]
* [Usando o conector do Facebook]
* [Usando o conector do Twitter]
* [Usando o conector do Salesforce]
* [Usando o conector do Office 365]
* [Usando o conector do Sharepoint]
* [Usando o conector do SAP]

Para obter mais detalhes, consulte a Referência de API do Conector [http://aka.ms/appservicesconnectorreference](http://aka.ms/appservicesconnectorreference).


<!-- Links -->

[Usando o conector Box]: app-service-logic-connector-box.md
[Usando o conector do Facebook]: app-service-logic-connector-facebook.md
[Usando o conector do Salesforce]: app-service-logic-connector-salesforce.md
[Usando o conector do Twitter]: app-service-logic-connector-twitter.md
[Usando o conector do SAP]: app-service-logic-connector-sap.md
[Usando o conector FTP]: app-service-logic-connector-ftp.md
[Usando o conector HTTP]: app-service-logic-connector-http.md
[Usando o conector de Blob de armazenamento do Azure]: app-service-logic-connector-azurestorageblob.md
[Usando o conector do Office 365]: app-service-logic-connector-office365.md
[Usando o conector do Sharepoint]: app-service-logic-connector-sharepoint.md


<!--HONumber=49-->