---
title: "Proteger o acesso aos Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Adicione segurança para proteger o acesso a gatilhos, entradas e saídas, parâmetros de ação e serviços usados com fluxos de trabalho em Aplicativos Lógicos do Azure."
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/22/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: d090ce5a912a2079d2e47d13caf60ca701f0e548
ms.openlocfilehash: 2cc83c6f10272139f148b450e3c1c8cc91fd68f9


---

# <a name="securing-a-logic-app"></a>Proteger um aplicativo lógico

Há muitas ferramentas disponíveis para ajudar a proteger seu aplicativo lógico.

* Proteger o acesso para acionar um aplicativo lógico (gatilho de solicitação de HTTP)
* Proteger o acesso para gerenciar, editar ou ler um aplicativo lógico
* Proteger o acesso ao conteúdo de entradas e saídas para uma execução
* Proteger parâmetros ou entradas em ações em um fluxo de trabalho
* Proteger o acesso aos serviços que recebem solicitações de um fluxo de trabalho

## <a name="secure-access-to-trigger"></a>Proteger o acesso a gatilhos

Ao trabalhar com um aplicativo lógico que é acionado em uma solicitação HTTP ([Solicitação](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md)), você pode restringir o acesso para que somente clientes autorizados possam acionar o aplicativo lógico.  Todas as solicitações para um aplicativo lógico são criptografadas e protegidas por SSL.

### <a name="shared-access-signature"></a>Assinatura de acesso compartilhado

Cada ponto de extremidade de solicitação para um aplicativo lógico inclui uma [Assinatura de Acesso Compartilhado](../storage/storage-dotnet-shared-access-signature-part-1.md) (SAS) como parte da URL.  Cada URL contém um parâmetro de consulta `sp`, `sv`, e `sig`.  As permissões são especificadas por `sp`e correspondem aos métodos HTTP permitidos, `sv` é a versão usada para gerar e `sig` é usado para autenticar o acesso para disparar.  É gerado usando o algoritmo SHA256 com uma chave secreta em todos os caminhos de URL e propriedades.  A chave secreta nunca é exposta e publicada e é mantida criptografada e armazenada como parte do aplicativo lógico.  Seu aplicativo lógico somente autorizará gatilhos que contêm uma assinatura válida criada com a chave secreta.

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Você pode regenerar uma nova chave segura a qualquer momento por meio da API REST ou do portal do Azure.  Todas as URLs atuais que foram geradas anteriormente usando a chave antiga serão invalidadas e não estarão mais autorizadas a acionar o aplicativo lógico.

1. No portal do Azure, abra o aplicativo lógico que você cuja chave você deseja regenerar
1. Clique no item de menu **Chaves de Acesso** em **Configurações**
1. Escolha a chave a regenerar e conclua o processo

As URLs obtidas após a regeneração são assinadas com a nova chave de acesso.

#### <a name="creating-callback-urls-with-an-expiration-date"></a>Criar URLs de retorno de chamada com uma data de expiração

Se você estiver compartilhando a URL com terceiros, você pode gerar URLs com chaves específicas e datas de vencimento, conforme necessário.  Isso permite que você reverta perfeitamente ou garanta acesso para acionar um aplicativo restringindo a um determinado período de tempo.  Você pode especificar uma data de expiração para uma URL por meio da [API REST de aplicativos lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers) da seguinte maneira:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `NotAfter` como uma cadeia de caracteres de data JSON, que retorna uma URL de retorno de chamada que só será válida até que a data e hora `NotAfter`.

#### <a name="creating-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Quando você gera ou lista URLs de retorno de chamada para gatilhos com base em solicitações, você também pode especificar qual chave usar para acessar a URL.  Você pode gerar uma URL assinada por uma chave específica por meio da [API REST de aplicativos lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers) da seguinte maneira:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `KeyType` como `Primary` ou `Secondary`.  Isso retorna uma URL assinada pela chave segura especificada.

### <a name="restrict-incoming-ip-addresses"></a>Restringir endereços IP de entrada

Além da Assinatura de Acesso Compartilhado, talvez você queira restringir chamando um aplicativo lógico somente de clientes específicos.  Por exemplo, se você gerenciar seu ponto de extremidade por meio do Gerenciamento de API do Azure, você pode restringir o aplicativo lógico para só aceitar a solicitação quando a solicitação é proveniente do endereço IP de instância de Gerenciamento de API.

Essa configuração pode ser definida nas configurações de aplicativo lógico:

1. No portal do Azure, abra o aplicativo lógico cuja chave você deseja regenerar
1. Clique no item de menu **Configuração de Controle de Acesso** em **Configurações**
1. Especifique a lista de intervalos de endereços IP a serem aceitos pelo gatilho

Um intervalo IP válido assume o formato `192.168.1.1/255`.  Se quiser que o aplicativo lógico seja acionado apenas como um aplicativo lógico aninhado, selecione a opção **Somente outros aplicativos lógicos**.  Isso irá gravar uma matriz vazia para o recurso, o que significa que somente chamadas do serviço em si (aplicativos lógicos pai) acionarão com êxito.

> [!NOTE]
> Um aplicativo lógico com um gatilho de solicitação ainda pode ser executado por meio da API REST/Gerenciamento `/triggers/{triggerName}/run`, independentemente do IP.  Isso requer autenticação em relação a API REST do Azure e todos os eventos apareceriam no Log de Auditoria do Azure.  Defina políticas de controle de acesso de apropriadamente.

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Definir intervalos de IP na definição de recurso

Se você estiver usando um [modelo de implantação](logic-apps-create-deploy-template.md) para automatizar suas implantações, as configurações de intervalo IP podem ser configuradas no modelo de recurso.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "triggers": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}

```

### <a name="adding-azure-active-directory-oauth-or-other-security"></a>Adicionar Azure Active Directory, OAuth, ou outra proteção

Se você quiser adicionar qualquer protocolo de autorização adicional além de um aplicativo lógico, use o [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/).  Isso fornece monitoramento avançado, segurança, política e documentação para qualquer ponto de extremidade, permitindo que um aplicativo lógico seja exposto como uma API.  O Gerenciamento de API do Azure pode expor um ponto de extremidade público ou privado para o aplicativo lógico, que pode aproveitar o Azure Active Directory, certificado, OAuth ou outros padrões de segurança.  Quando uma solicitação é recebida, o gerenciamento de API do Azure encaminhará a solicitação para o aplicativo lógico (executando quaisquer transformações necessárias ou restrições em andamento).  Você pode usar as configurações de intervalo IP de entrada no aplicativo lógico para permitir que somente o aplicativo lógico seja disparado do Gerenciamento de API.

## <a name="secure-access-to-manage-or-edit-a-logic-app"></a>Proteger o acesso para gerenciar ou editar um aplicativo lógico

Você pode restringir o acesso a operações de gerenciamento em um aplicativo lógico para que somente usuários ou grupos específicos sejam capazes de realizar operações no recurso.  Aplicativos lógicos usam o recurso de [Controle de Acesso Baseado em Função (RBAC)](../active-directory/role-based-access-control-configure.md) do Azure e podem ser personalizados com as mesmas ferramentas.  Há algumas funções internas que você pode aos atribuir membros da sua assinatura:

* **Colaborador do Aplicativo Lógico** - Fornece acesso para exibir, editar e atualizar um aplicativo lógico.  Não pode remover o recurso ou executar operações de administração.
* **Operador de Aplicativo Lógico** - Pode exibir o aplicativo lógico e histórico de execução e habilitar/desabilitar.  Não é pode editar ou atualizar a definição.

Você também pode aproveitar o [Bloqueio de Recurso do Azure](../azure-resource-manager/resource-group-lock-resources.md) para evitar a modificação ou exclusão de um aplicativo lógico.  Isso é importante para evitar que os recursos de produção sejam modificados ou excluídos.

## <a name="secure-access-to-contents-of-the-run-history"></a>Proteger o acesso ao conteúdo do histórico de execução

Você pode restringir o acesso ao conteúdo de entradas ou saídas de execuções anteriores para intervalos de endereços IP específicos.  

Todos os dados dentro de uma execução de fluxo de trabalho são criptografados em trânsito e em repouso.  Quando uma chamada para o histórico de execução é feita, o serviço autentica a solicitação e fornece links para a solicitação e resposta de entradas e saídas.  Esse link pode ser protegido para que somente solicitações para exibir o conteúdo de um intervalo de endereços IP designado retornem o conteúdo.  Isso pode ser usado para controle adicional de acesso.  Você pode até mesmo especificar um endereço IP como `0.0.0.0` para que ninguém possa acessar entradas/saídas.  Somente alguém com permissões de administrador poderá remover essa restrição, fornecendo a possibilidade para acesso 'just-in-time' ao conteúdo de fluxo de trabalho.

Essa configuração pode ser definida nas configurações de recurso do portal do Azure:

1. No portal do Azure, abra o aplicativo lógico cuja chave você deseja regenerar
1. Clique no item de menu **Configuração de Controle de Acesso** em **Configurações**
1. Especificar a lista de intervalos de endereços IP para acesso ao conteúdo

#### <a name="setting-ip-ranges-on-the-resource-definition"></a>Definir intervalos de IP na definição de recurso

Se você estiver usando um [modelo de implantação](logic-apps-create-deploy-template.md) para automatizar suas implantações, as configurações de intervalo IP podem ser configuradas no modelo de recurso.  

``` json
{
    "properties": {
        "definition": {
        },
        "parameters": {},
        "accessControl": {
            "contents": {
                "allowedCallerIpAddresses": [
                    {
                        "addressRange": "192.168.12.0/23"
                    },
                    {
                        "addressRange": "2001:0db8::/64"
                    }
                ]
            }
        }
    },
    "type": "Microsoft.Logic/workflows"
}
```

## <a name="secure-parameters-and-inputs-within-a-workflow"></a>Proteger parâmetros e entradas em um fluxo de trabalho

Há alguns aspectos de uma definição de fluxo de trabalho para os quais talvez você queira definir parâmetros para implantação em mais de um ambiente.  Além disso, alguns desses parâmetros podem ser parâmetros de proteção que você deseja que não apareçam ao editar um fluxo de trabalho - como uma ID de cliente e o segredo do cliente para [Autenticação do Azure Active Directory](../connectors/connectors-native-http.md#authentication) de uma ação de HTTP.

### <a name="using-parameters-and-secure-parameters"></a>Usar parâmetros e proteger parâmetros

A [linguagem de definição de fluxo de trabalho](http://aka.ms/logicappsdocs) fornece uma operação `@parameters()` para acessar o valor de um parâmetro de recurso no tempo de execução.  Além disso, você pode [especificar parâmetros no modelo de implantação do recurso](../azure-resource-manager/resource-group-authoring-templates.md#parameters).  Se você especificar o tipo de parâmetro para ser `securestring`, ele não será retornado com o restante da definição de recurso - o que significa que ele não poderá ser acessado ao exibir o recurso após a implantação.

> [!NOTE]
> Se o parâmetro é usado nos cabeçalhos ou no corpo de uma solicitação, ele pode ser visível ao acessar o histórico de execução e a solicitação de HTTP de saída.  Certifique-se de definir as políticas de acesso ao conteúdo apropriadamente.
> Cabeçalhos de autorização nunca são visíveis por meio de entradas ou saídas - assim, se o segredo está sendo usado, não será recuperável.

#### <a name="resource-deployment-template-with-secrets"></a>Modelo de implantação de recursos com segredos

A seguir está um exemplo de uma implantação que faz referência a um parâmetro de proteção de `secret` no tempo de execução.  Em um arquivo de parâmetros separado, você poderia especificar o valor de ambiente para o `secret`, ou utilizar o [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md) para recuperar os segredos no momento da implantação.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "secretDeploymentParam": {
      "type": "securestring"
    }
  },
  "variables": {},
  "resources": [
    {
      "name": "secret-deploy",
      "type": "Microsoft.Logic/workflows",
      "location": "westus",
      "tags": {
        "displayName": "LogicApp"
      },
      "apiVersion": "2016-06-01",
      "properties": {
        "definition": {
          "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "actions": {
            "Call_External_API": {
              "type": "http",
              "inputs": {
                "headers": {
                  "Authorization": "@parameters('secret')"
                },
                "body": "This is the request"
              },
              "runAfter": {}
            }
          },
          "parameters": {
            "secret": {
              "type": "SecureString"
            }
          },
          "triggers": {
            "manual": {
              "type": "Request",
              "kind": "Http",
              "inputs": {
                "schema": {}
              }
            }
          },
          "contentVersion": "1.0.0.0",
          "outputs": {}
        },
        "parameters": {
          "secret": {
            "value": "[parameters('secretDeploymentParam')]"
          }
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="secure-access-to-services-receiving-requests-from-a-workflow"></a>Proteger o acesso aos serviços que recebem solicitações de um fluxo de trabalho

Há várias maneiras para ajudar a proteger qualquer ponto de extremidade que o aplicativo lógico precisa acessar.

### <a name="using-authentication-on-outbound-requests"></a>Usar a autenticação em solicitações de saída

Ao trabalhar com uma ação HTTP, HTTP + Swagger (API Open) ou Webhook, você pode adicionar autenticação para a solicitação que está sendo enviada.  Isso pode incluir a autenticação básica, autenticação de certificado ou autenticação do Azure Active Directory.  Detalhes sobre como configurar essa autenticação podem ser encontrados [neste artigo](../connectors/connectors-native-http.md#authentication).

### <a name="restricting-access-to-logic-app-ip-addresses"></a>Restringir o acesso a endereços IP de aplicativo lógico

Todas as chamadas de aplicativos lógicos vêm de um conjunto específico de endereços IP por região.  Você pode adicionar filtragem adicional para aceitar somente solicitações desses endereços IP designados.  Uma lista de endereços IP pode ser encontrada [neste artigo](logic-apps-limits-and-config.md#configuration).

### <a name="on-premises-connectivity"></a>Conectividade local

Aplicativos lógicos fornecem integração com alguns serviços que propiciam comunicação no local segura e confiável.

#### <a name="on-premises-data-gateway"></a>Gateway de dados local

Muitos dos conectores de aplicativos lógicos gerenciados fornecem conectividade segura para sistemas locais, incluindo o File System, SQL, SharePoint, DB2 e muito mais.  O gateway aproveita canais criptografados por meio do Barramento de Serviço do Azure para retransmissão de dados no local, e todo o tráfego origina-se de tráfego de saída seguro do agente de gateway.  Para obter mais detalhes sobre como o gateway funciona, consulte [este artigo](logic-apps-gateway-install.md#how-the-gateway-works).

#### <a name="azure-api-management"></a>Gerenciamento de API do Azure

O [Gerenciamento de API do Azure](https://azure.microsoft.com/services/api-management/) tem uma série de opções de conectividade local incluindo integração VPN e ExpressRoute site a site para proxy seguro e a comunicação com sistemas locais.  No designer do aplicativo lógico, você pode selecionar rapidamente uma API exposta do Gerenciamento de API do Azure em um fluxo de trabalho, fornecendo acesso rápido a sistemas locais.

#### <a name="hybrid-connections-from-azure-app-services"></a>Conexões híbridas dos Serviços de Aplicativo do Azure

Você pode usar o recurso de conexão híbrida local para a API do Azure e aplicativos da Web para se comunicar no local.  Detalhes sobre conexões híbridas e como configurá-las podem ser encontrados [neste artigo](../app-service-web/web-sites-hybrid-connection-get-started.md).

## <a name="next-steps"></a>Próximas etapas
[Criar um modelo de implantação](logic-apps-create-deploy-template.md)  
[Manipulação de exceção](logic-apps-exception-handling.md)  
[Monitorar seus aplicativos lógicos](logic-apps-monitor-your-logic-apps.md)  
[Diagnosticando falhas e problemas nos aplicativos lógicos](logic-apps-diagnosing-failures.md)  



<!--HONumber=Jan17_HO4-->


