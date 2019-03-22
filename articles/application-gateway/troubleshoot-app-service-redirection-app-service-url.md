---
title: Solucionando problemas do Gateway de aplicativo do Azure com o serviço de aplicativo – redirecionamento de URL do serviço de aplicativo
description: Este artigo fornece informações sobre como solucionar o problema de redirecionamento ao Gateway de aplicativo do Azure é usado com o serviço de aplicativo do Azure
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/22/2019
ms.author: absha
ms.openlocfilehash: 359d75f10f95b0e41ccd9a869d49247355f0d5d0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58123174"
---
# <a name="troubleshoot-application-gateway-with-app-service--redirection-to-app-services-url"></a>Solucionar problemas do Gateway de Aplicativo com o Serviço de Aplicativo – redirecionamento para uma URL do Serviço de Aplicativo

 Saiba como diagnosticar e resolver problemas de redirecionamento com o Gateway de aplicativo em que a URL do serviço de aplicativo está obtendo exposta.

## <a name="overview"></a>Visão geral

Quando você configura um público voltado para o serviço de aplicativo no pool de back-end do Gateway de aplicativo e se você tiver um redirecionamento configurado no código do aplicativo, você pode ver que quando você acessar o Gateway de aplicativo, você será redirecionado pelo navegador diretamente para o aplicativo URL do serviço.

Esse problema pode ocorrer pelos seguintes motivos principais:

- Você tem um redirecionamento configurado no seu serviço de aplicativo. Redirecionamento pode ser tão simple quanto adicionar uma barra à direita para a solicitação.
- Você tem a autenticação do Azure AD que faz com que o redirecionamento.
- Você habilitou a opção "Escolher a nome de Host do endereço de back-end" nas configurações de HTTP do Gateway de aplicativo.
- Você não tem o seu domínio personalizado registrado com o serviço de aplicativo.

## <a name="sample-configuration"></a>Exemplo de configuração

- Ouvinte de HTTP: Básica ou multissite
- Pool de endereços de back-end: Serviço de Aplicativo
- Configurações de HTTP: "Escolher nome de host do endereço de back-end" habilitada
- Investigação: "Escolher nome de host nas configurações de HTTP de" habilitado

## <a name="cause"></a>Causa

Um serviço de aplicativo só pode ser acessado com os nomes de host configurado nas configurações de domínio personalizado, por padrão, é "example.azurewebsites.net" e se você deseja acessar o serviço de aplicativo usando o Gateway de aplicativo com um nome de host não registrado no serviço de aplicativo ou com Gateway de aplicativo FQDN, você precisa substituir o nome do host na solicitação original para o nome de host do serviço de aplicativo.

Para fazer isso com o Gateway de aplicativo, podemos usar a opção "Selecionar nome do host do endereço de back-end" nas configurações de HTTP e para a investigação para trabalhar, usamos "Escolher o nome do host de configurações de HTTP do back-end" na configuração da investigação.

![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Devido a isso, quando o serviço de aplicativo faz um redirecionamento, ele usa o nome do host "example.azurewebsites.net" no cabeçalho de local, em vez do nome do host original, a menos que configurado de outra forma. Você pode verificar as solicitação e resposta cabeçalhos de exemplo abaixo.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://example.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=example.azurewebsites.net

X-Powered-By: ASP.NET
```
No exemplo acima, você pode notar que o cabeçalho de resposta tem um código de status de 301 para redirecionamento e o cabeçalho de local tem o nome de host do serviço de aplicativo em vez do nome do host original "www.contoso.com".

## <a name="solution"></a>Solução

Esse problema pode ser resolvido por não ter um redirecionamento no lado do aplicativo, no entanto, se isso não for possível, que podemos deve passar o mesmo cabeçalho de host que recebe do Gateway de aplicativo para o serviço de aplicativo em vez de fazer uma substituição de host também.

Depois de fazer isso, o serviço de aplicativo fará o redirecionamento (se houver) no mesmo host cabeçalho original que aponta para o Gateway de aplicativo e não seu próprio.

Para fazer isso, você deve possuir um domínio personalizado e siga o processo mencionado a seguir.

- Registre o domínio para a lista de domínios personalizados do serviço de aplicativo. Para isso, você deve ter um CNAME no seu domínio personalizado apontando para o FQDN do serviço de aplicativo. Para obter mais informações, consulte [mapear um nome DNS personalizado existente para o serviço de aplicativo do Azure](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain).

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- Depois disso, o serviço de aplicativo está pronto para aceitar o nome do host "www.contoso.com". Agora, altere sua entrada CNAME no DNS para apontá-lo de volta para o FQDN do Gateway de aplicativo. Por exemplo, "appgw.eastus.cloudapp.azure.com".

- Certifique-se de que seu domínio "www.contoso.com" seja resolvido para o FQDN do Gateway de aplicativo quando você fizer uma consulta DNS.

- Defina sua investigação personalizada para desabilitar "Escolher o nome do host de configurações de HTTP do back-end". Isso pode ser feito no portal, desmarcando a caixa de seleção nas configurações de teste e no PowerShell usando não - PickHostNameFromBackendHttpSettings mude no comando Set-AzApplicationGatewayProbeConfig. No campo nome do host do teste, insira seu FQDN do serviço de aplicativo "example.azurewebsites.net", conforme as solicitações de investigação enviadas do Gateway de aplicativo executar isso no cabeçalho do host.

  > [!NOTE]
  > Enquanto faz a próxima etapa, verifique se que sua investigação personalizada não está associada a suas configurações de HTTP de back-end porque os ainda de configurações de HTTP tem a opção "Selecionar nome do host do endereço de back-end" habilitada neste momento.

- Defina configurações de HTTP do Gateway de aplicativo para desabilitar o "Nome do host de escolha do endereço de back-end". Isso pode ser feito no portal, desmarcando a caixa de seleção e no PowerShell por meio de não - PickHostNameFromBackendAddress mude no comando Set-AzApplicationGatewayBackendHttpSettings.

- Associe a investigação personalizada para as configurações de HTTP de back-end e verificar a integridade de back-end se ele estiver íntegro.

- Depois que isso for feito, o Gateway de aplicativo agora deve encaminhar o mesmo nome de host "www.contoso.com" para o serviço de aplicativo e o redirecionamento acontecerá mesmo nome de host. Você pode verificar as solicitação e resposta cabeçalhos de exemplo abaixo.
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [tíquete de suporte](https://azure.microsoft.com/support/options/).
