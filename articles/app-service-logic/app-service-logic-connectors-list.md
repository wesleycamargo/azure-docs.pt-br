---
title: "Lista de Aplicativos de API e de Conectores no Serviço de Aplicativo do Microsoft Docs"
description: "Leia sobre os Conectores e os Aplicativos de API no Serviço de Aplicativo do Azure"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: cgronlun
ms.assetid: 984a425d-ba64-48cc-90dc-bb624411e0f0
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6028fc53661978f63bbac89be6214ffebdc22405


---
# <a name="list-of-connectors-and-api-apps-to-use-in-your-logic-apps"></a>Lista de Conectores e de Aplicativos de API a serem usados em seus Aplicativos Lógicos
> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2014-12-01-preview de aplicativos lógicos. Para a versão de GA (Disponibilidade Geral) de aplicativos lógicos, confira [Lista de novos conectores](../connectors/apis-list.md).
> 
> 

Saiba mais sobre todos os conectores e Aplicativos de API disponibilizados pela Microsoft a serem usados em seus Aplicativos Lógicos 

Para obter informações de preço e uma lista do que é incluído com cada camada de serviço, consulte [Preços do Serviço de Aplicativo do Azure](https://azure.microsoft.com/pricing/details/app-service/).

> [!NOTE]
> Para começar com Aplicativos Lógicos antes de se inscrever para uma conta do Azure, vá para [Experimentar Aplicativo Lógico](https://tryappservice.azure.com/?appservice=logic). Você pode criar instantaneamente um aplicativo lógico de início e de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a name="core-connectors"></a>Conectores principais
A tabela a seguir lista todos os conectores e aplicativos de API criados pela Microsoft, que estão disponíveis como Conectores principais:

| Nome | Descrição |
| --- | --- |
| [Bing Translator](https://azure.microsoft.com/marketplace/partners/bing/microsofttranslator/) |Use o Bing para traduzir textos para outro idioma. |
| [HTTP](app-service-logic-connector-http.md) |O ouvinte HTTP abre um ponto de extremidade que atua como um servidor HTTP e escuta solicitações HTTP ou HTTPS. A ação de HTTP não exige um aplicativo de API e há suporte nativo para esse tipo de ação nos Aplicativos Lógicos. |
| [Margem de atraso](app-service-logic-connector-slack.md) |Conecte-se à margem de atraso e poste mensagens em canais de margem de atraso. |

## <a name="enterprise-integration-connectors"></a>Conectores de integração Enterprise
A tabela a seguir lista todos os conectores e Aplicativos de API criados pela Microsoft que estão disponíveis como Conectores de integração Enterprise:

| Nome | Descrição |
| --- | --- |
| [Regras do BizTalk](app-service-logic-use-biztalk-rules.md) |Usar as regras do BizTalk para definir e controlar a lógica de negócios em uma organização. Políticas de negócios podem ser atualizadas sem recompilar ou sem reimplantar os aplicativos associados. |
| [Extrator de XPath do BizTalk](app-service-logic-xpath-extract.md) |Procura e extrai dados de conteúdo XML com base em um XPath que você escolher. |
| [Conector do DB2](app-service-logic-connector-db2.md) |Conecta-se a um banco de dados IBM DB2, local e em uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix. <br/><br/>Sem gatilhos. As ações incluem as instruções de tabela selecionar, inserir, atualizar, excluir e personalizar<br/><br/>Esse conector também inclui o Microsoft Client para DRDA para conectar a um servidor Informix em uma rede TCP/IP. |
| [Arquivo](app-service-logic-connector-file.md) |Usando esse conector, você pode se conectar ao sistema de arquivos local ou em rede e concluir diferentes tarefas de arquivo, incluindo carregar, excluir, listar arquivos e muito mais. |
| [Informix](app-service-logic-connector-informix.md) |Conecta-se a um banco de dados IBM Informix, local e em uma máquina virtual do Azure executando um sistema operacional Windows. Ele pode mapear operações de API Web e OData para comandos de Linguagem de Consulta Estruturada Informix.<br/><br/>Sem gatilhos. As ações incluem as instruções de tabela selecionar, inserir, atualizar, excluir e personalizar.<br/><br/>Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. Esse conector também inclui um Microsoft Client para DRDAA para conectar a um servidor do Informix em uma rede TCP/IP. |
| [Microsoft SQL Server](app-service-logic-connector-sql.md) |Conecta-se ao SQL Server local ou a um Banco de Dados SQL do Azure. Você pode criar, atualizar, obter e excluir entradas em uma tabela de banco de dados SQL. |
| MQ |Conecta-se ao IBM WebSphere MQ Server versão 8, localmente e em uma máquina virtual do Azure executando um sistema operacional Windows. Ao usar localmente, a Rota Expressa do Azure ou a VPN podem ser usadas. O conector também inclui o Microsoft Client para MQ.<br/><br/>Sem gatilhos. Sem ações.<br/><br/>**Observação** Atualmente não pode ser usado com Aplicativos Lógicos. |

## <a name="connectors-as-triggers"></a>Conectores como gatilhos
Vários conectores fornecem gatilhos para aplicativos lógicos. Os gatilhos são de dois tipos:

1. Gatilhos de Pesquisa: esses gatilhos pesquisam seu serviço de interesse a uma frequência especificada para verificar se há novos dados. Quando novos dados estão disponíveis, uma nova instância do Aplicativo Lógico é executada com os dados como entrada. O gatilho pode executar tarefas adicionais, como limpeza dos dados lidos e transmitidos para o Aplicativo Lógico para impedir que os mesmos dados sejam consumidos várias vezes. Exemplos de tais conectores são Arquivo, SQL e Armazenamento do Azure.
2. Gatilhos Push: esses gatilhos escutam em busca de dados em um ponto de extremidade ou da ocorrência de eventos. Em seguida, disparam uma nova instância de um Aplicativo Lógico. Exemplos de tais conectores são HTTP Listener e o Twitter.

## <a name="connectors-as-actions"></a>Conectores como ações
Os conectores também podem ser usados como ações dentro do Aplicativo Lógico. Ações são úteis para pesquisar dados no Aplicativo Lógico que podem, em seguida, ser usado na execução. Por exemplo, talvez seja necessário pesquisar dados de um banco de dados SQL para obter informações adicionais sobre um cliente durante o processamento de um pedido. Ou então, talvez seja necessário escrever, atualizar ou excluir dados em um destino. Você pode fazer isso usando as ações fornecidas pelos conectores. As ações são mapeadas para operações em aplicativos de API (conforme definido pelos seus metadados Swagger).

## <a name="create-your-own-connectors-and-api-apps"></a>Criar seus próprios conectores e aplicativos de AP
[Referência a aplicativos de API e conectores](http://aka.ms/appservicesconnectorreference)  
[Gatilhos de aplicativo de API do Serviço de Aplicativo do Azure](../app-service-api/app-service-api-dotnet-triggers.md)  
[Logic App Reference (Referência de aplicativo lógico)](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## <a name="more-on-connectors-and-api-apps"></a>Mais informações sobre conectores e aplicativos de API
[O que são conectores e aplicativos de API do BizTalk](app-service-logic-what-are-biztalk-api-apps.md)  
[Usando o Gerenciador de Conexão Híbrida no Serviço de Aplicativo do Azure](app-service-logic-hybrid-connection-manager.md)  
[Gerenciar e monitorar aplicativos de API e conectores internos](app-service-logic-monitor-your-connectors.md)




<!--HONumber=Nov16_HO2-->


