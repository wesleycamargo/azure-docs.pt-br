---
title: "Aplicativos de API do Serviço de Aplicativo: o que mudou | Microsoft Docs"
description: "Saiba quais são as novidades dos Aplicativos de API no Serviço de Aplicativo do Azure."
services: app-service\api
documentationcenter: .net
author: mohitsriv
manager: wpickett
editor: tdykstra
ms.assetid: a9b58066-e8fd-48b8-a651-4613b1736433
ms.service: app-service-api
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: d60ca38364d7ea7991af18e39407f42a8c9abb84


---
# <a name="app-service-api-apps---whats-changed"></a>Aplicativos de API do Serviço de Aplicativo: o que mudou
No evento Connect(), em novembro de 2015, foram [anunciados](https://azure.microsoft.com/blog/azure-app-service-updates-november-2015/)vários aprimoramentos para o Serviço de Aplicativo do Azure. Esses aprimoramentos incluem alterações subjacentes aos Aplicativos de API para alinhar da melhor forma possível com Aplicativos Web e Móveis, reduzir a contagem de conceitos e melhorar o desempenho da implantação e do tempo de execução. A partir de 30 de novembro de 2015, os novos aplicativos de API criados usando o Portal de Gerenciamento do Azure ou as ferramentas mais recentes refletirão essas alterações. Este artigo descreve essas alterações e como reimplantar os aplicativos existentes para tirar proveito dos recursos.

## <a name="feature-changes"></a>Alterações de recurso
Os principais recursos dos Aplicativos de API: autenticação, CORS e metadados de API, foram movidos diretamente para o Serviço de Aplicativo. Com essa alteração, os recursos ficam disponíveis em Aplicativos de API, Web e Móveis. Na verdade, todos os três compartilham o mesmo tipo de recurso **Microsoft.Web/sites** no Gerenciador de Recursos. O gateway de Aplicativos de API não é mais necessário ou oferecidos em Aplicativos de API. Isso também facilita o uso da API de Gerenciamento do Azure, pois haverá apenas um gateway de Gerenciamento de API.

![Visão geral de Aplicativos de API](./media/app-service-api-whats-changed/api-apps-overview.png)

Um princípio de design fundamental da atualização dos Aplicativos de API é a possibilidade de usar sua API como ela está e na linguagem de sua preferência.  Se sua API já estiver implantada como um Aplicativo Web ou Aplicativo Móvel, não será necessário reimplantar o aplicativo para tirar proveito dos novos recursos. Se você estiver atualmente na visualização dos Aplicativos de API, confira a orientação de migração detalhada abaixo.

### <a name="authentication"></a>Autenticação
Os recursos existentes de autenticação de Aplicativos de API, Aplicativos/Serviços Móveis e Aplicativos Web para uso imediato foram unificados e estão disponíveis em uma única folha de autenticação do Serviço de Aplicativo do Azure no Portal de Gerenciamento. Para obter uma introdução sobre os serviços de autenticação no Serviço de Aplicativo, confira [Expandindo a autenticação/autorização do Serviço de Aplicativo](https://azure.microsoft.com/blog/announcing-app-service-authentication-authorization/).

Para cenários de API, há diversos recursos novos relevantes:

* **Suporte ao uso direto do Active Directory do Azure**, sem precisar trocar o token AAD no código do cliente por um token de sessão: seu cliente pode incluir apenas os tokens AAD no cabeçalho de Autorização, de acordo com a especificação de token de portador. Isso também significa que um SDK específico ao Serviço de Aplicativo é exigido no lado do cliente ou do servidor. 
* **Acesso entre serviços ou "Interno"**: se você tiver um processo de daemon ou algum outro cliente que precise de acesso às APIs sem uma interface, você poderá solicitar um token usando uma entidade de serviço do AAD e passá-lo ao Serviço de Aplicativo para autenticação com seu aplicativo.
* **Autorização adiada**: muitos aplicativos têm restrições de acesso diferentes para partes diferentes do aplicativo. Talvez você queira que algumas APIs estejam disponíveis publicamente, enquanto outras exijam o logon. O recurso de Autenticação/Autorização original era tudo ou nada, e todo o site exigia o logon. Essa opção ainda existe, mas você também pode permitir, como alternativa, que o código do aplicativo processe as decisões de acesso depois que o Serviço de Aplicativo tiver autenticado o usuário.

Para saber mais sobre os novos recursos de autenticação, veja [Autenticação e autorização para Aplicativos de API no Serviço de Aplicativo do Azure](app-service-api-authentication.md). Para saber mais sobre como migrar aplicativos de API existentes do modelo de aplicativos de API anterior para o novo, confira [Migração de aplicativos de API existentes](#migrating-existing-api-apps) posteriormente neste artigo.

### <a name="cors"></a>CORS
Em vez de uma configuração de aplicativo **MS_CrossDomainOrigins** delimitada por vírgula, agora há uma folha no Portal de Gerenciamento do Azure para configuração de CORS. Como alternativa, é possível configurá-lo usando as ferramentas do Gerenciador de Recursos, como o Azure PowerShell, a CLI ou o [Gerenciador de Recursos](https://resources.azure.com/). Defina a propriedade **cors** no tipo de recurso **Microsoft.Web/sites/config** para o recurso **&lt;nome do site&gt;/web**. Por exemplo:

    {
        "cors": {
            "allowedOrigins": [
                "https://localhost:44300"
            ]
        }
    } 

### <a name="api-metadata"></a>Metadados da API
Agora, a folha de definição da API está disponível em Aplicativos Web, Móveis e de API. No Portal de Gerenciamento, você pode especificar uma URL relativa ou uma URL absoluta apontando para um ponto de extremidade que hospeda a representação Swagger 2.0 de sua API. Como alternativa, ela pode ser configurada usando as ferramentas do Gerenciador de Recursos. Defina a propriedade **apiDefinition** no tipo de recurso **Microsoft.Web/sites/config** para o recurso **&lt;nome do site&gt;/web**. Por exemplo:

    {
        "apiDefinition":
        {
            "url": "https://myStorageAccount.blob.core.windows.net/swagger/apiDefinition.json"
        }
    }

Neste momento, o ponto de extremidade de metadados deve estar publicamente acessível sem autenticação para que vários clientes downstream (por exemplo, a geração de cliente de API REST do Visual Studio e o fluxo de "Adicionar API" do PowerApps) o consumam. Isso significa que se você estiver usando a autenticação do Serviço de Aplicativo e quiser expor a definição da API de dentro do próprio aplicativo, será necessário usar a opção Autenticação Adiada descrita anteriormente para que a rota até os metadados do Swagger seja pública.

## <a name="management-portal"></a>Portal de Gerenciamento
A seleção de **Novo > Web + Móvel > Aplicativo de API** no portal criará aplicativos de API que refletem os novos recursos descritos neste artigo. **Procurar > Aplicativos de API** mostrará apenas esses novos aplicativos de API. Quando você navega em um aplicativo de API, a folha compartilha o mesmo layout e recursos dos Aplicativos Web e Móveis. As únicas diferenças são o conteúdo de início rápido e a organização das configurações.

Os Aplicativos de API existentes (ou aplicativos de API do Marketplace, criados a partir de Aplicativos Lógicos) com os recursos da Visualização anterior ainda estarão visíveis no designer dos Aplicativos Lógicos e ao navegar por todos os recursos em um grupo de recursos.

## <a name="visual-studio"></a>Visual Studio
A maioria das ferramentas de Aplicativos Web funcionará com novos aplicativos de API, pois eles compartilham o mesmo tipo de recurso base **Microsoft.Web/sites** . As ferramentas do Visual Studio do Azure, no entanto, devem ser atualizadas para a versão 2.8.1 ou posterior, pois expõem diversos recursos específicos às APIs. Baixe o SDK da [página de downloads do Azure](https://azure.microsoft.com/downloads/).

Com a racionalização dos tipos de Serviço de Aplicativo, a publicação também é unificada em **Publicar > Serviço de Aplicativo do Microsoft Azure**:

![Publicação de Aplicativos de API](./media/app-service-api-whats-changed/api-apps-publish.png)

Para saber mais sobre o SDK 2.8.1, leia a [postagem de blog](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-1-for-net/)com o anúncio.

Como alternativa, você pode importar manualmente o perfil de publicação no portal de gerenciamento a fim de habilitar a publicação. No entanto, o Gerenciador de Nuvem, a geração de código e aseleção/criação de aplicativos de API exigirão o SDK 2.8.1 ou superior.

## <a name="migrating-existing-api-apps"></a>Migração de aplicativos de API existentes
Se a sua API personalizada for implantada para a versão de visualização anterior dos Aplicativos de API, solicitamos que você migre para o novo modelo de Aplicativos de API até 31 de dezembro de 2015. Como o modelo antigo e o novo têm base nas APIs Web hospedadas no Serviço de Aplicativo, grande parte do código existente pode ser reutilizada.

### <a name="hosting-and-redeployment"></a>Hospedagem e reimplantação
As etapas para reimplantação são iguais à implantação de qualquer API Web existente no Serviço de Aplicativo. Etapas:

1. Criar um aplicativo de API vazio. Isso pode ser feito no portal, em Novo > Aplicativo de API, no Visual Studio desde a publicação, ou nas ferramentas do Gerenciador de Recursos. Ao usar as ferramentas ou modelos do Gerenciador de Recursos, defina o valor de **tipo** como **api** no tipo de recurso **Microsoft.Web/sites** para que os guias de início rápido e as configurações no Portal de Gerenciamento sejam orientados para cenários de API.
2. Conecte e implante seu projeto no aplicativo de API vazio usando qualquer um dos mecanismos de implantação com suporte do Serviço de Aplicativo. Leia a [documentação de implantação do Serviço de Aplicativo do Azure](../app-service-web/web-sites-deploy.md) para saber mais. 

### <a name="authentication"></a>Autenticação
Os serviços de autenticação do Serviço de Aplicativo oferecem suportam aos mesmos recursos disponíveis no modelo anterior dos Aplicativos de API. Se você estiver usando tokens de sessão e precisar de SDKs, use os seguintes SDKs de cliente e servidor:

* Cliente: [SDK de cliente móvel do Azure](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
* Servidor: [Extensão de autenticação .NET de aplicativo móvel do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/) 

Se em vez disso você estiver usando os SDKs alfa do Serviço de Aplicativo, eles já estarão obsoletos:

* Cliente: [SDK do Serviço de Aplicativo do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.AppService)
* Servidor: [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service)

No entanto, especificamente com o Active Directory do Azure, nada específico ao Serviço de Aplicativo será necessário se você estiver usando o token AAD diretamente.

### <a name="internal-access"></a>Acesso interno
O modelo de Aplicativos de API anterior incluía um nível de acesso interno integrado. Isso exigia a utilização do SDK para assinar solicitações. Conforme descrito anteriormente, com o novo modelo de Aplicativos de API, as entidades de serviço do AAD podem ser usadas como uma alternativa para a autenticação entre serviços sem exigir um SDK específico ao Serviço de Aplicativo. Saiba mais em [Autenticação de entidade de serviço para Aplicativos de API no Serviço de Aplicativo do Azure](app-service-api-dotnet-service-principal-auth.md).

### <a name="discovery"></a>Descoberta
O modelo anterior de Aplicativos de API apresentava APIs para a descoberta de outros aplicativos de API em tempo de execução, no mesmo grupo de recursos por trás do mesmo gateway. Isso é especialmente útil em arquiteturas que implementam padrões de microsserviço. Embora isso não tenha suporte direto, há várias opções disponíveis:

1. Use a API do Azure Resource Manager para descoberta.
2. Coloque o Gerenciamento de API do Azure a frente de suas APIs hospedadas no Serviço de Aplicativo. O Gerenciamento de API do Azure serve como uma fachada e pode fornecer uma URL externa estável, mesmo se a topologia interna mudar.
3. Crie seu próprio aplicativo de API de descoberta e faça com que outros aplicativos de API registrem-se com o aplicativo de descoberta na inicialização.
4. No momento da implantação, preencha as configurações do aplicativo de todos os aplicativos de API (e clientes) com os pontos de extremidade dos outros aplicativos de API. Isso é viável em implantações de modelo e nos Aplicativos de API, já que agora oferecem controle da URL.

## <a name="using-api-apps-with-logic-apps"></a>Usando aplicativos de API com aplicativos lógicos
O novo modelo de aplicativos de API funciona bem com [a versão de esquema 2015-08-01 dos Aplicativos Lógicos](../app-service-logic/app-service-logic-schema-2015-08-01.md).

## <a name="next-steps"></a>Próximas etapas
Para saber mais, leia os artigos na seção [Documentação de Aplicativos de API](https://azure.microsoft.com/documentation/services/app-service/api/). Eles foram atualizados para refletir o novo modelo para Aplicativos de API. Além disso, acesse os fóruns para obter detalhes adicionais ou orientação sobre migração:

* [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureAPIApps)
* [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-api-apps)




<!--HONumber=Nov16_HO3-->


