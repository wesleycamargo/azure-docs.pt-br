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
ms.date: 06/20/2017
ms.author: mahender
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 0d166a2e5b60c3b9f911f9fc3ad49ad7f252abb4
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017


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

O Serviço de Aplicativo do Azure e o Azure Functions têm [suporte integrado](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) para criação, hospedagem e gerenciamento um documento de API aberta. Para criar um conector personalizado para um aplicativo de funções, API, móvel ou Web, o PowerApps e Flow precisam receber uma cópia da definição.

> [!NOTE]
> Como está sendo usada uma cópia da definição de API, o PowerApps e o Microsoft Flow não tomarão conhecimento imediatamente sobre atualizações ou alterações significativas para o aplicativo. Se uma nova versão da API for disponibilizada, essas etapas deverão ser repetidas para a nova versão. 

Para fornecer PowerApps e Microsoft Flow com a definição de API hospedada para seu aplicativo, siga essas etapas:

1. Abra o [Portal do Azure](https://portal.azure.com) e navegue até o seu Serviço de Aplicativo ou ao aplicativo Azure Functions.

    Se usar o Serviço de Aplicativo do Azure, selecione **definição de API** da lista de configurações. 
    
    Se utilizar Azure Function, selecione seu aplicativo de funções, escolha **Recursos de plataforma** e, em seguida, **Definição de API**. Em vez disso, também é possível optar por abrir a guia **Definição de API (visualização)**.

2. Se uma definição de API foi fornecida, você verá um botão **Exportar para o PowerApps + Microsoft Flow**. Clique neste botão para iniciar o processo de exportação.

3. Selecione o **Modo de exportação**. Isso determina as etapas que você precisa seguir para criar um conector. O Serviço de Aplicativo oferece duas opções para fornecer PowerApps e Microsoft Flow com sua definição de API:

    **Expresso** permite que você crie o conector personalizado a partir do portal do Azure. Isso requer que o usuário atual conectado possua permissão para criar conectores no ambiente de destino. Essa é a abordagem recomendada se esse requisito puder ser atendido. Se estiver utilizando esse modo, siga as instruções [Exportação de expresso](#express) abaixo.

    **Manual** permite exportar uma cópia da definição de API que pode ser importada utilizando os portais do PowerApps e Microsoft Flow. Essa é a abordagem recomendada se o usuário do Azure e o usuário com permissão para criar conectores forem pessoas diferentes ou se o conector precisar ser criado em outro locatário. Se estiver utilizando esse modo, siga as instruções [Exportação e importação manual](#manual) abaixo.

<a name="express"></a>
## <a name="express-export"></a>Exportação de expresso

Nesta seção, um novo conector personalizado será criado a partir do portal do Azure. É necessário estar conectado ao locatário para qual deseja exportar e possuir permissão para criar um conector personalizado no ambiente de destino.

1. Selecione o ambiente no qual deseja criar o conector. Em seguida, forneça um nome para o conector personalizado.

2. Se a sua definição de API incluir quaisquer definições de segurança, elas serão chamadas na etapa n° 2. Se necessário, forneça os detalhes de configuração de segurança necessários para conceder aos usuários acesso à sua API. Para obter mais informações, consulte [Autenticação](#auth) abaixo. 

3. Clique em **OK** para criar seu conector personalizado.


<a name="manual"></a>
## <a name="manual-export-and-import"></a>Exportação e importação manual

Para criar um conector personalizado para um aplicativo da Web, móvel, de API ou de função, serão necessárias duas etapas:

1. [Recuperando a definição de API do Serviço de Aplicativo ou Azure Functions](#export)
2. [Importar a definição de API para o PowerApps e o Microsoft Flow](#import)

É possível que essas duas etapas precisem ser executadas por indivíduos diferentes dentro de uma organização, já que um determinado usuário pode não ter permissão para executar ambas as ações. Nesse caso, um desenvolvedor que tenha acesso de colaborador ao Serviço de Aplicativo ou ao aplicativo Azure Functions precisará obter a definição de API (um único arquivo JSON) ou um link para ela. Eles precisarão fornecer essa definição para um proprietário do PowerApps ou do Microsoft Flow. O proprietário pode usar os metadados para criar o conector personalizado.

<a name="export"></a>
### <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Recuperando a definição de API do Serviço de Aplicativo ou do Azure Functions

Nesta seção, você exportará a definição de API para sua API de serviço de aplicativo, a ser usada posteriormente no PowerApps ou portal do Microsoft Flow.

1. Você pode escolher **Baixar a definição de API** ou **Obter um link**. Seja qual for a opção escolhida, o resultado será fornecido na próxima seção. Selecione uma dessas opções e siga as instruções.
 
2. Se a sua definição de API incluir quaisquer definições de segurança, elas serão chamadas na etapa n° 2. Durante a importação, o PowerApps e o Microsoft Flow irão detectá-las e solicitar informações de segurança. Reúna as credenciais relacionadas a cada definição para uso na próxima seção. Para obter mais informações, consulte [Autenticação](#auth) abaixo. 

<a name="import"></a>
### <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importar a definição de API para o PowerApps e o Microsoft Flow

Nesta seção, você criará um conector personalizado no PowerApps e no Microsoft Flow usando a definição de API obtida anteriormente. Os conectores personalizados são compartilhados entre os dois serviços, portanto, você só precisa importar a definição uma vez. Para obter mais informações sobre conectores personalizados, consulte [Registrar e usar conectores personalizados no PowerApps] e [Registrar e usar conectores personalizados no Microsoft Flow].

1. Abra o [Portal da web do PowerApps](https://web.powerapps.com) ou o [Portal da Web do Microsoft Flow](https://flow.microsoft.com/) e entre. 

2. Clique no botão **Configurações** (o ícone de engrenagem) no canto superior direito da página e selecione **Conectores personalizados**. 

3. Clique em **Criar conector personalizado**.

4. Na guia **Geral**, forneça um nome para a API e carregue a definição de OpenAPI ou cole a URL de metadados. Clique em **Continuar**.

4. Na guia **Segurança**, se for solicitado que você forneça detalhes de autenticação, insira os valores obtidos na seção anterior. Caso contrário, prossiga para a próxima etapa.

5. Na guia **Definições**, todas as operações definidas no arquivo OpenAPI são populadas automaticamente. Se todas as suas operações necessárias forem definidas, você poderá ir para a próxima etapa. Caso contrário, você pode adicionar e modificar as operações aqui.

6. Clique em **Criar conector**. Se você quiser testar chamadas à API, vá para a próxima etapa.

7. Na guia **Testar**, crie uma conexão, selecione uma operação para testar e insira os dados necessários para a operação.

8. Clique em **Testar operação**.


<a name="auth"></a>
## <a name="authentication"></a>Autenticação

O PowerApps e o Microsoft Flow suportam nativamente uma coleção de provedores de identidade que podem ser utilizada para fazer logon de usuários do seu conector personalizado. Se sua API exige autenticação, certifique-se de que ela seja capturada como uma _definição de segurança_ no documento OpenAPI. Durante a exportação, será necessário fornecer valores de configuração que permitem ao PowerApps um Microsoft Flow realizar ações de logon.

Esta seção aborda os tipos de autenticação que são suportados pelo fluxo expresso: chave de API, Azure Active Directory e OAuth 2.0 Genérico. Para obter uma lista completa de provedores e as credenciais necessárias, consulte [Registrar e usar conectores personalizados no PowerApps] e [Registrar e usar conectores personalizados no Microsoft Flow].

### <a name="api-key"></a>Chave de API
Quando o esquema de segurança é usado, os usuários do seu conector deverão fornecer a chave ao criar uma conexão. É possível fornecer um nome de chave de API para ajudá-lo a reconhecer qual chave é necessária. Para o Azure Functions, geralmente será uma das chaves de host que abrangem várias funções dentro do aplicativo de funções.

### <a name="azure-active-directory"></a>Azure Active Directory
Ao configurar um conector personalizado que requer o logon do Azure Active Directory, dois registros do aplicativo do Azure Active Directory são necessários: um para modelar a API de back-end e outro para modelar o conector no PowerApps e Flow.

Sua API deve ser configurada para funcionar com o primeiro registro e isso já será considerado se você utilizou o recurso de [Autenticação do Serviço de Aplicativo/Autorização](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication).

Você terá que criar manualmente o segundo registro para o conector, utilizando as etapas abordadas em [Adicionar um aplicativo do AAD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#adding-an-application). O registro precisa ter acesso delegado à sua API e um URL de resposta de`https://msmanaged-na.consent.azure-apim.net/redirect`. Consulte [este exemplo](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) para obter mais detalhes, substituindo sua API pelo Azure Resource Manager.

Os valores de configuração a seguir são necessários:
- **ID do cliente** - o ID do cliente do seu conector de registro do AAD
- **Segredo do cliente** - o segredo do cliente do seu registro do conector AAD
- **URL de Logon** - URL básica para AAD. No Azure público, isso geralmente será `https://login.windows.net`.
- **ID do locatário** - o ID do locatário a ser usado para o logon. Isso deve ser "comum" ou o ID do locatário em que o conector está sendo criado.
- **URL de Recurso** - URL de recurso do seu registro do AAD de back-end de API

> [!IMPORTANT]
> Se outro indivíduo importará a definição de API para o PowerApps e o Microsoft Flow como parte do fluxo manual, será necessário fornecer o ID do cliente e o segredo do cliente **do registro do conector**, assim como o URL de recurso da sua API. Certifique-se de que esses segredos sejam gerenciados com segurança. **Não compartilhe as credenciais de segurança da API, propriamente dita.**

### <a name="generic-oauth-20"></a>OAuth 2.0 genérico
O suporte OAuth 2.0 genérico permite que você se integre com qualquer provedor OAuth 2.0. Isso permite que você traga qualquer provedor personalizado que sem suporte nativo.

Os valores de configuração a seguir são necessários:
- **ID do Cliente** - o ID do cliente do OAuth 2.0
- **Segredo do Cliente** - o segredo do cliente do OAuth 2.0
- **URL de Autorização** - a URL de autorização do OAuth 2.0
- **URL de Token** - a URL de token do OAuth 2.0
- **URL de Atualização** - a URL de atualização do OAuth 2.0



[Registrar e usar conectores personalizados no PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Registrar e usar conectores personalizados no Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/

