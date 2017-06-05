---
title: Exportando uma API hospedada no Azure para o PowerApps e o Microsoft Flow | Microsoft Docs
description: "Visão geral de como expor uma API hospedada no serviço de aplicativo ao PowerApps e ao Microsoft Flow"
services: app-service
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 6a6043de57eb211c93c510cf2a139141c3950662
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportando uma API hospedada no Azure para o PowerApps e o Microsoft Flow

## <a name="creating-custom-connectors-for-powerapps-and-microsoft-flow"></a>Criando conectores personalizados para o PowerApps e o Microsoft Flow

O [PowerApps](https://powerapps.com) é um serviço para a criação e utilização de aplicativos comerciais personalizados que se conectam aos seus dados e trabalham entre plataformas. O [Microsoft Flow](https://flow.microsoft.com) facilita a automação de fluxos de trabalho e processos de negócios entre seus aplicativos e serviços favoritos. O PowerApps e o Microsoft Flow vêm com uma variedade de conectores integrados para fontes de dados como Office 365, Dynamics 365, Salesforce e muito mais. No entanto, os usuários também precisam ser capazes de aproveitar as fontes de dados e APIs sendo criadas pela sua organização.

Da mesma forma, os desenvolvedores que desejam expor suas APIs mais amplamente dentro da organização podem querer disponibilizar suas APIs para usuários do PowerApps e do Microsoft Flow. Este tópico mostrará a você como expor uma API criada com o Serviço de Aplicativo do Azure ou com o Azure Functions para o PowerApps e o Microsoft Flow. [O Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) é uma oferta de plataforma como serviço que permite aos desenvolvedores criar com rapidez e facilidade aplicativos da Web, móveis e de API de nível empresarial. O [Azure Functions](https://azure.microsoft.com/services/functions/) é uma solução de computação sem servidor baseada em eventos que permite que você crie rapidamente códigos de autor que podem reagir a outras partes do seu sistema e escala com base na demanda.

Para saber mais sobre esses serviços, consulte:
- [Aprendizado Guiado do PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Aprendizado Guiado do Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [O que é o Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [O que é o Azure Functions?](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>Compartilhando uma definição de API

APIs normalmente são descritas usando um [documento API aberta](https://www.openapis.org/) (às vezes conhecido como um documento "Swagger"). Ele contém todas as informações sobre quais operações estão disponíveis e como os dados devem ser estruturados. O PowerApps e o Microsoft Flow podem criar conectores personalizados para qualquer documento API aberta 2.0. Quando um conector personalizado é criado, ele pode ser usado exatamente da mesma forma que um dos conectores integrados e pode ser rapidamente integrado a um aplicativo.

O Serviço de Aplicativo do Azure e o Azure Functions têm [suporte integrado](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) para criação, hospedagem e gerenciamento um documento de API aberta. Para criar um conector personalizado para um aplicativo da Web, móvel, de API ou de função, serão necessárias duas etapas:

1. [Recuperando a definição de API do Serviço de Aplicativo ou Azure Functions](#export)
2. [Importando a definição de API para o PowerApps](#import)

É possível que essas duas etapas precisem ser executadas por indivíduos diferentes dentro de uma organização, já que um determinado usuário pode não ter permissão para executar ambas as ações. Nesse caso, um desenvolvedor que tenha acesso de colaborador ao Serviço de Aplicativo ou ao aplicativo Azure Functions precisará obter a definição de API (um único arquivo JSON) ou um link para ela. Eles precisarão fornecer essa definição para um proprietário do PowerApps ou do Microsoft Flow. O proprietário pode usar os metadados para criar o conector personalizado.

> [!NOTE]
> Como está sendo usada uma cópia da definição de API, o PowerApps e o Microsoft Flow não tomarão conhecimento imediatamente sobre atualizações ou alterações significativas para o aplicativo. Se uma nova versão da API for disponibilizada, essas etapas deverão ser repetidas para a nova versão. 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Recuperando a definição de API do Serviço de Aplicativo ou do Azure Functions

Nesta seção, você exportará a definição de API para sua API do serviço de aplicativo, a ser usada posteriormente no PowerApps.

1. Abra o [Portal do Azure](https://portal.azure.com) e navegue até o seu Serviço de Aplicativo ou ao aplicativo Azure Functions.

    Se usar o Serviço de Aplicativo do Azure, selecione **definição de API** da lista de configurações. 
    
    Se usar o Azure Functions, selecione **Configurações do Aplicativo de funções** e depois **Configurar metadados da API**.

2. Se uma definição de API foi fornecida, você verá um botão **Exportar para o PowerApps + Microsoft Flow**. Clique neste botão para iniciar o processo de exportação.

3. Você pode escolher **Baixar a definição de API** ou **Obter um link**. Seja qual for a opção escolhida, o resultado será fornecido na próxima seção. Selecione uma dessas opções e siga as instruções.
 
4. Se a sua definição de API incluir quaisquer definições de segurança, elas serão chamadas na etapa n° 2. Durante a importação, o PowerApps e o Microsoft Flow irão detectá-las e solicitar informações de segurança. Os serviços usam isso para o registro de usuários, para que eles possam acessar a API. Se sua API requer autenticação, certifique-se de que ela seja capturada como uma _definição de segurança_ no documento API aberta.

    Reúna as credenciais relacionadas a cada definição para uso na próxima seção.
 
> [!NOTE]
> Se usar a autenticação do Azure Active Directory, um novo registro de aplicativo AAD será necessário, ao qual foi delegado acesso a sua API e a uma URL de resposta de _https://msmanaged-na.consent.azure-apim.net/redirect_. Consulte [este exemplo](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) para obter mais detalhes, substituindo sua API pelo Azure Resource Manager.
>
> Se outra pessoa importar a definição de API para o PowerApps, você fornecerá a ID do cliente e o segredo do cliente **do novo registro**, bem como a URL de recurso da sua API, além do arquivo de definição da API. Certifique-se de que esses segredos sejam gerenciados com segurança. **Não compartilhe as credenciais de segurança da API, propriamente dita.**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importar a definição de API para o PowerApps e o Microsoft Flow

Nesta seção, você criará um conector personalizado no PowerApps e no Microsoft Flow usando a definição de API obtida anteriormente. Os conectores personalizados são compartilhados entre os dois serviços, portanto, você só precisa importar a definição uma vez. Para obter mais informações sobre conectores personalizados, consulte [Registrar e usar conectores personalizados no PowerApps] e [Registrar e usar conectores personalizados no Microsoft Flow].

**Para importar para o Microsoft Flow ou PowerApps:**

1. Abra o [Portal da web do PowerApps](https://web.powerapps.com) ou o [Portal da Web do Microsoft Flow](https://flow.microsoft.com/) e entre. 

2. Clique no botão **Configurações** (o ícone de engrenagem) no canto superior direito da página e selecione **Conectores personalizados**. 

3. Clique em **Criar conector personalizado**.

4. Na guia **Geral**, forneça um nome para a API e carregue a definição de OpenAPI ou cole a URL de metadados. Clique em **Continuar**.

4. Na guia **Segurança**, se for solicitado que você forneça detalhes de autenticação, insira os valores obtidos na seção anterior. Caso contrário, prossiga para a próxima etapa.

5. Na guia **Definições**, todas as operações definidas no arquivo OpenAPI são populadas automaticamente. Se todas as suas operações necessárias forem definidas, você poderá ir para a próxima etapa. Caso contrário, você pode adicionar e modificar as operações aqui.

6. Clique em **Criar conector**. Se você quiser testar chamadas à API, vá para a próxima etapa.

7. Na guia **Testar**, crie uma conexão, selecione uma operação para testar e insira os dados necessários para a operação.

8. Clique em **Testar operação**.



[Registrar e usar conectores personalizados no PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Registrar e usar conectores personalizados no Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/

