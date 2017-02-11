---
title: "Definir configurações de aplicativos do Azure Functions | Microsoft Docs"
description: "Saiba como definir configurações de aplicativos do Azure Functions."
services: 
documentationcenter: .net
author: rachelappel
manager: erikre
editor: 
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: dotnet
ms.devlang: na
ms.topic: article
ms.date: 10/28/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 836c5b48c6b9a635d4255e3954b7b9c0a2bae482


---
# <a name="how-to-configure-azure-function-app-settings"></a>Como definir configurações de aplicativos do Azure Functions
## <a name="settings-overview"></a>Visão geral das configurações
Você pode gerenciar as configurações de aplicativos do Azure Functions clicando no link **Configurações do aplicativo de funções** no canto inferior esquerdo do portal. As configurações do aplicativo de funções do Azure se aplicam a todas as funções no aplicativo.

1. Vá para o [portal do Azure](http://portal.azure.com) e entre com sua conta do Azure.
2. Clique em **Configurações do aplicativo de funções** no canto inferior esquerdo do portal. Essa ação revela diversas opções de configurações que podem ser escolhidas. 

![Configurações do aplicativo de funções do Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="memory-size"></a>Tamanho da memória
Você pode configurar a quantidade de memória para alocar para suas funções no aplicativo de funções atual. 

Para configurar a memória, mova o controle deslizante para a quantidade de memória desejada. O máximo é 128 MB.

![Configurar o tamanho da memória do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-memory-size.png)

## <a name="continuous-integration"></a>Integração contínua
Você pode integrar o seu aplicativo de funções com GitHub, Visual Studio Team Services e muito mais.

1. Clique no link **Configurar integração contínua**. Isso abre um painel **Implantações** com opções.
2. Clique em **Configurar** no painel **Implantações** para revelar um painel de **Fonte da implantação** com uma opção: clique **Escolher fonte** para mostrar fontes disponíveis. 
3. Clique em qualquer uma das fontes de implantação disponíveis para escolhê-la: Visual Studio Team Services, OneDrive, Local Git Repository, GitHub, Bitbucket, DropBox ou repositório externo. 
   
    ![Configurar CI da função do aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-ci.png)
4. Insira suas credenciais e informações conforme solicitado pelas várias fontes de implantação. As credenciais e as informações solicitadas podem ser ligeiramente diferentes dependendo de qual fonte que você escolheu. 

Uma instalado o CI, o código conectado enviado para a fonte configurada é automaticamente implantado para esse aplicativo de funções.

## <a name="authenticationauthorization"></a>Autenticação/autorização
Para as funções que utilizem um acionador HTTP, você pode requerer a autenticação das chamadas.

1. Para configurar a autenticação, clique no link **Configurar autenticação**.
2. Ative o botão de **Autenticação do serviço de aplicativo** na posição **On**.

![Configurar CI da função do aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

A maioria dos provedores de autenticação pede uma ID de cliente/chave de API e um segredo; no entanto, as opções de Conta da Microsoft e Facebook também permitem que você defina escopos (credenciais de autorização específicas). O Active Directory tem várias definições de configuração expressa ou avançada que podem ser definidas.

Para obter detalhes sobre como configurar provedores de autenticação específicos, consulte [Visão geral de autenticação do serviço de aplicativo do Azure](../app-service/app-service-authentication-overview.md).

## <a name="cors"></a>CORS
Normalmente, por motivos de segurança, chamadas para os hosts (domínios) de fontes externas, como chamadas Ajax de um navegador, não são permitidas. Caso contrário, um código mal-intencionado poderia sejam enviado para e executado no back-end. A rota mais segura é, portanto, colocar na lista negra todas as fontes de código, exceto algumas em que você confia. Você pode configurar de quais fontes podem ser aceitas chamadas de funções do Azure configurando o compartilhamento de recursos entre origens (CORS). O CORS permite que você liste os domínios que são a fonte de JavaScript que pode chamar funções em seu aplicativo de funções do Azure. 

1. Para configurar o CORS, clique no link **Configurar CORS**. 
2. Insira os domínios que você deseja a permitir.

![Configurar CORS da função do aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

## <a name="api-definition"></a>Definição da API
Permita que os clientes consumam mais facilmente as suas funções acionadas por HTTP.

1. Para configurar uma API, clique em **Configurar metadados de API**. 
2. Insira a URL que aponta para um arquivo json de Swagger.

![Configurar API da função do aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)

Para obter mais informações sobre como criar definições de API com Swagger, visite [Introdução aos aplicativos de API, ASP.NET e Swagger no Azure](../app-service-api/app-service-api-dotnet-get-started.md).

## <a name="application-settings"></a>Configurações do aplicativo
Gerencie variáveis de ambiente, versões do Framework, depuração remota, configurações do aplicativo, cadeias de conexão, documentos padrão, etc. Essas configurações são específicas de seu aplicativo de funções. 

Para definir as configurações do aplicativo, clique no link **Definir configurações de aplicativo**. 

![Definir configurações de aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

## <a name="in-portal-console"></a>Console no portal
Você pode executar comandos tipo DOS com o console de funções do Azure no portal. Os comandos comuns incluem criação e navegação de diretório e arquivo, bem como execução scripts e arquivos em lote. 

> [!NOTE]
> Você pode carregar scripts, mas primeiro você deve configurar um cliente de FTP em **Configurações avançadas** da função do Azure.
> 
> 

Para abrir o console no portal, clique em **Abrir console de desenvolvimento**.

![Configurar o tamanho da memória do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

> [!NOTE]
> Trabalhar em um console com arte ASCII assim faz com que você pareça legal.
> 
> 

## <a name="kudu"></a>Kudu
O Kudu permite que você acesse recursos administrativos avançados de um aplicativo de funções.

Para abrir o Kudu, clique em **Acessar Kudu**. Essa ação abre uma janela do navegador totalmente nova com o administrador da web do Kudu.

> [!NOTE]
> Como alternativa, você pode iniciar o **Kudu** inserindo "scm" na URL da sua função, como mostrado aqui: ```https://<YourFunctionName>.scm.azurewebsites.net/```
> 
> 

Na página da web do Kudu, você pode exibir e gerenciar informações do sistema, configurações do aplicativo, variáveis de ambiente, cabeçalhos HTTP, variáveis de servidor e muito mais.

![Configurar Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

## <a name="advanced-settings"></a>Configurações avançadas
Faça a gestão da sua aplicação de funções como qualquer outra instância do Serviço de Aplicações. Essa opção fornece acesso a todas as configurações discutidas anteriormente, além de várias outras.  

Para abrir as configurações avançadas, clique no link **Configurações avançadas**. 

![Definir configurações do Serviço de Aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-settings.png)

Para obter detalhes sobre como configurar cada serviço de aplicativo, consulte [Definir configurações de serviço de aplicativo do Azure](../app-service-web/web-sites-configure.md).

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO3-->


