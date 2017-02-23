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
translationtype: Human Translation
ms.sourcegitcommit: 060fbb885f254c0de02fe422460e8e8d659ac848
ms.openlocfilehash: 503f17a629527e8c9a3bfe6cde1da31dcf1f18ef


---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Exportando uma API hospedada no Azure para o PowerApps e o Microsoft Flow

## <a name="creating-custom-apis-for-powerapps-and-microsoft-flow"></a>Criando APIs personalizadas para o PowerApps e o Microsoft Flow

A plataforma [Microsoft Business Application](https://businessplatform.microsoft.com/) inclui uma variedade de produtos que ajudam os usuários avançados a fazer mais. O [PowerApps](https://powerapps.com) é um serviço para a criação e utilização de aplicativos comerciais personalizados que se conectam aos seus dados e trabalham entre plataformas. O [Microsoft Flow](https://flow.microsoft.com) facilita a automação de fluxos de trabalho e processos de negócios entre seus aplicativos e serviços favoritos. O PowerApps e o Microsoft Flow vêm com uma variedade de conectores integrados para fontes de dados como Office 365, Dynamics 365, Salesforce e muito mais. No entanto, os usuários também precisam ser capazes de aproveitar as fontes de dados e APIs sendo criadas pela sua organização.

Da mesma forma, os desenvolvedores que desejam expor suas APIs mais amplamente dentro da organização podem querer disponibilizar suas APIs para usuários do PowerApps e do Microsoft Flow. Este tópico mostrará a você como expor uma API criada com o Serviço de Aplicativo do Azure ou com o Azure Functions para o PowerApps e o Microsoft Flow. [O Serviço de Aplicativo do Azure](https://azure.microsoft.com/services/app-service/) é uma oferta de plataforma como serviço que permite aos desenvolvedores criar com rapidez e facilidade aplicativos da Web, móveis e de API de nível empresarial. O [Azure Functions](https://azure.microsoft.com/services/functions/) é uma solução de computação sem servidor baseada em eventos que permite que você crie rapidamente códigos de autor que podem reagir a outras partes do seu sistema e escala com base na demanda.

Para saber mais sobre esses serviços, consulte:
- [Aprendizado Guiado do PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) 
- [Aprendizado Guiado do Microsoft Flow](https://flow.microsoft.com/guided-learning/learning-introducing-flow/)
- [O que é o Serviço de Aplicativo](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)
- [O que é o Azure Functions?](https://docs.microsoft.com/azure/azure-functions/functions-overview)

## <a name="sharing-an-api-definition"></a>Compartilhando uma definição de API

APIs normalmente são descritas usando um [documento API aberta](https://www.openapis.org/) (às vezes conhecido como um documento "Swagger"). Ele contém todas as informações sobre quais operações estão disponíveis e como os dados devem ser estruturados. O PowerApps e o Microsoft Flow podem criar APIs personalizadas para qualquer documento API aberta 2.0. Quando uma API personalizada é criada, ela pode ser usada exatamente da mesma forma que um dos conectores integrados e pode ser rapidamente integrada a um aplicativo.

O Serviço de Aplicativo do Azure e o Azure Functions têm [suporte integrado](https://docs.microsoft.com/azure/app-service-api/app-service-api-metadata) para criação, hospedagem e gerenciamento um documento de API aberta. Para criar um conector personalizado para um aplicativo da Web, móvel, de API ou de função, serão necessárias duas etapas:

1. [Recuperando a definição de API do Serviço de Aplicativo ou Azure Functions](#export)
2. [Importando a definição de API para o PowerApps](#import)

É possível que essas duas etapas precisem ser executadas por indivíduos diferentes dentro de uma organização, já que um determinado usuário pode não ter permissão para executar ambas as ações. Nesse caso, um desenvolvedor que tenha acesso de colaborador ao Serviço de Aplicativo ou ao aplicativo Azure Functions precisará obter a definição de API (um único arquivo JSON) ou um link para ela. Eles precisarão fornecer essa definição para um proprietário do PowerApps ou do Microsoft Flow. O proprietário pode usar os metadados para criar a API personalizada.

> [!NOTE]
> Como está sendo usada uma cópia da definição de API, o PowerApps e o Microsoft Flow não tomarão conhecimento imediatamente sobre atualizações ou alterações significativas para o aplicativo. Se uma nova versão da API for disponibilizada, essas etapas deverão ser repetidas para a nova versão. 

<a name="export"></a>
## <a name="retrieving-the-api-definition-from-app-service-or-azure-functions"></a>Recuperando a definição de API do Serviço de Aplicativo ou do Azure Functions

Nesta seção, você exportará a definição de API para sua API do serviço de aplicativo, a ser usada posteriormente no PowerApps.

1. Abra o [Portal do Azure](https://portal.azure.com) e navegue até o seu Serviço de Aplicativo ou ao aplicativo Azure Functions.

    Se usar o Serviço de Aplicativo do Azure, selecione **definição de API** da lista de configurações. 
    
    Se usar o Azure Functions, selecione **Configurações do Aplicativo de funções** e depois **Configurar metadados da API**.

2. Se uma definição de API foi fornecida, você verá um botão **Exportar para o PowerApps + Microsoft Flow**. Clique neste botão para iniciar o processo de exportação.

3. Você pode escolher **Baixar a definição de API** ou **Obter um link**. Seja qual for a opção escolhida, o resultado será fornecido ao PowerApps na próxima seção. Selecione uma dessas opções e siga as instruções.
 
4. Se a sua definição de API incluir quaisquer definições de segurança, elas serão chamadas na etapa n° 2. Durante a importação, o PowerApps e o Microsoft Flow irão detectá-las e solicitar informações de segurança. Os serviços usam isso para o registro de usuários, para que eles possam acessar a API. Se sua API requer autenticação, certifique-se de que ela seja capturada como uma _definição de segurança_ no documento API aberta.

    Reúna as credenciais relacionadas a cada definição para uso na próxima seção. Para obter uma lista de provedores de identidade que o PowerApps suporta nativamente e as credenciais que cada um requer, consulte [Registrar APIs personalizadas no PowerApps] e [Registrar APIs personalizadas no Microsoft Flow].
 
> [!NOTE]
> Se usar a autenticação do Azure Active Directory, um novo registro de aplicativo AAD será necessário, ao qual foi delegado acesso a sua API e a uma URL de resposta de _https://msmanaged-na.consent.azure-apim.net/redirect_. Consulte [este exemplo](
https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) para obter mais detalhes, substituindo sua API pelo Azure Resource Manager.
>
> Se outra pessoa importar a definição de API para o PowerApps, você fornecerá a ID do cliente e o segredo do cliente **do novo registro**, bem como a URL de recurso da sua API, além do arquivo de definição da API. Certifique-se de que esses segredos sejam gerenciados com segurança. **Não compartilhe as credenciais de segurança da API, propriamente dita.**

<a name="import"></a>
## <a name="importing-the-api-definition-into-powerapps-and-microsoft-flow"></a>Importar a definição de API para o PowerApps e o Microsoft Flow

Nesta seção, você criará uma API personalizada no PowerApps e no Microsoft Flow usando a definição de API obtida anteriormente. APIs personalizadas são compartilhadas entre os dois serviços, portanto, você só precisa importar a definição uma vez. Para mais informações sobre APIs personalizadas, consulte [Registrar APIs personalizadas no PowerApps] e [Registrar APIs personalizadas no Microsoft Flow].

**Para importar para o PowerApps:**

1. Abra o [Portal da web do powerapps](https://web.powerapps.com), conecte-se e selecione **Conexões**. Clique em **Nova conexão**.

2. Selecione **Personalizada**e, em seguida, clique em **Nova API personalizada**.

3. Forneça um nome para sua API e carregue a definição de Swagger ou cole na URL de metadados. Clique em **Próximo**.

4. Se for solicitado que você forneça detalhes de autenticação, insira os valores obtidos na seção anterior. Caso contrário, prossiga para a próxima etapa.

5. Clique em **Criar**.

**Para importar para o Microsoft Flow:**

1. Abra o [portal da web do Microsoft Flow](https://flow.microsoft.com/) e conecte-se. 

2. Clique no botão **Configurações** no canto superior direito da página (parecido com uma engrenagem) e selecione **APIs personalizadas**. Clique em **Criar API personalizada**.

3. Carregue a definição de Swagger e clique em **Continuar**.

4. Se for solicitado que você forneça detalhes de autenticação, insira os valores obtidos na seção anterior. Caso contrário, prossiga para a próxima etapa.

5. Clique na caixa de seleção na parte superior da tela.



[Registrar APIs personalizadas no PowerApps]: https://powerapps.microsoft.com/tutorials/register-custom-api/
[Registrar APIs personalizadas no Microsoft Flow]: https://flow.microsoft.com/documentation/register-custom-api/



<!--HONumber=Feb17_HO2-->


