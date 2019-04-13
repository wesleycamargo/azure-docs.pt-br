---
title: Usar restrições de locatário para gerenciar o acesso ao SaaS na nuvem aplicativos - Azure | Microsoft Docs
description: Como usar restrições de locatário para gerenciar quais usuários podem acessar aplicativos com base em seu locatário do AD do Azure.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: celested
ms.reviewer: richagi
ms.collection: M365-identity-device-management
ms.openlocfilehash: b78897e2e03085a20f07ce8724226f0e0171861e
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545858"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Usar restrições de locatário para gerenciar o acesso a aplicativos de nuvem de SaaS

Organizações de grande porte que enfatizam a segurança desejam mudar para serviços de nuvem como o Office 365, mas precisam saber que seus usuários poderão acessar somente os recursos aprovados. Tradicionalmente, as empresas restringem endereços IP ou nomes de domínio quando desejam gerenciar o acesso. Essa abordagem falha em um mundo onde os aplicativos de software como serviço (ou SaaS) são hospedados em uma nuvem pública, em execução em nomes de domínio compartilhado como [outlook.office.com](https://outlook.office.com/) e [login.microsoftonline.com](https://login.microsoftonline.com/). Bloquear esses endereços impediria totalmente que os usuários acessassem o Outlook na web, em vez de simplesmente restringi-los a identidades e recursos aprovados.

A solução do Azure Active Directory (Azure AD) para esse desafio é um recurso chamado restrições de locatário. As restrições de locatário, as organizações podem controlar o acesso a aplicativos de nuvem de SaaS, com base no locatário do Azure AD, que os aplicativos usam para logon único. Por exemplo, você pode desejar permitir o acesso aos aplicativos do Office 365 da sua organização enquanto impede o acesso a instâncias de outras organizações desses mesmos aplicativos.  

As restrições de locatário, as organizações podem especificar a lista de locatários que seus usuários têm permissão para acessar. O Azure AD então apenas concede acesso para esses locatários permitidos.

Este artigo se concentra em restrições de locatário para o Office 365, mas o recurso deve funcionar com qualquer aplicativo de nuvem de SaaS que usa protocolos de autenticação moderna com o Azure AD para logon único. Se você usar aplicativos de SaaS com um locatário do Azure AD diferente do usado pelo Office 365, certifique-se de que todos os locatários necessários sejam permitidos. Para obter mais informações sobre os aplicativos de nuvem de SaaS, consulte o [Marketplace do Active Directory](https://azure.microsoft.com/marketplace/active-directory/).

## <a name="how-it-works"></a>Como ele funciona

A solução geral inclui os seguintes componentes:

1. **Azure AD**: Se o `Restrict-Access-To-Tenants: <permitted tenant list>` está presentes, o Azure AD somente problemas tokens de segurança para os locatários permitidos.

2. **Infraestrutura de servidor proxy local**: Essa infraestrutura é um dispositivo proxy capaz de inspeção de Secure Sockets Layer (SSL). Você deve configurar o proxy para inserir o cabeçalho que contém a lista de locatários permitidos para o tráfego destinado para o Azure AD.

3. **Software cliente**: Para dar suporte a restrições de locatário, o software cliente deve solicitar tokens diretamente do Azure AD, para que a infraestrutura do proxy pode interceptar o tráfego. Baseada em navegador e aplicativos do Office 365 atualmente oferece suporte a restrições de locatário, assim como clientes do Office que usam autenticação moderna (como OAuth 2.0).

4. **Autenticação moderna**: Serviços de nuvem devem usar a autenticação moderna para usar restrições de locatário e bloquear o acesso a todos os locatários não permitido. Você deve configurar os serviços de nuvem do Office 365 para usar protocolos de autenticação moderna por padrão. Para obter as informações mais recentes sobre o suporte do Office 365 para autenticação moderna, leia [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticação moderna do Office 365 atualizada).

O diagrama a seguir ilustra o fluxo do tráfego de alto nível. Restrições de locatário requer a inspeção de SSL apenas no tráfego do Azure AD, não para os serviços de nuvem do Office 365. Essa distinção é importante, porque o volume de tráfego para a autenticação para o Azure AD é normalmente muito menor do que o volume de tráfego para aplicativos SaaS, como o Exchange Online e SharePoint Online.

![Fluxo de tráfego de restrições de locatário – diagrama](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Configurar restrições de locatário

Há duas etapas para começar com as restrições de locatário. Primeiro, certifique-se de que seus clientes podem se conectar aos endereços à direita. Em segundo lugar, configure sua infraestrutura de proxy.

### <a name="urls-and-ip-addresses"></a>URLs e endereços IP

Para usar restrições de locatário, seus clientes devem ser capazes de se conectar às seguintes URLs do AD do Azure para autenticar: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/), e [ login.Windows.NET](https://login.windows.net/). Além disso, para acessar o Office 365, os clientes também devem ser capazes de conectar-se para os nomes de domínio totalmente qualificados (FQDNs), URLs, e os endereços IP definidos no [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Requisitos e configuração de proxy

A seguinte configuração é necessária para habilitar restrições de locatário por meio de sua infraestrutura de proxy. Essa diretriz é genérica, você deverá consultar a documentação do seu fornecedor de proxy para encontrar as etapas de implementação específicas.

#### <a name="prerequisites"></a>Pré-requisitos

- O proxy deve ser capaz de realizar a interceptação de SSL, a inserção de cabeçalho HTTP e a filtragem de destinos usando FQDNs/URLs.

- Os clientes devem confiar na cadeia de certificados apresentada pelo proxy para comunicações SSL. Por exemplo, se certificados de interno [infraestrutura de chave pública (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) são usados, o certificado de autoridade de certificado interno de emissão raiz deve ser confiável.

- Esse recurso está incluído nas assinaturas do Office 365, mas se você quiser usar restrições de locatário para controlar o acesso a outros aplicativos SaaS, em seguida, licenças do Azure AD Premium 1 são necessárias.

#### <a name="configuration"></a>Configuração

Para cada solicitação de entrada para login.microsoftonline.com, login.microsoft.com e login.windows.net, insira dois cabeçalhos HTTP: *Restrict-Access-To-Tenants* e *Restrict-Access-Context*.

Os cabeçalhos devem incluir os seguintes elementos:

- Para *Restrict-Access-To-Tenants*, use um valor de \<permitido lista locatário\>, que é uma lista separada por vírgulas de locatários para permitir que os usuários acessem. Qualquer domínio que é registrado com um locatário pode ser usado para identificar o locatário nessa lista. Por exemplo, para permitir o acesso aos locatários Contoso e Fabrikam, o par nome/valor é semelhante a: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Para *Restrict-Access-Context*, use um valor de uma única ID de diretório, declarar qual locatário está configurando o locatário as restrições. Por exemplo, para declarar Contoso como o locatário que define a política de restrições de locatário, o par nome/valor se parece com: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Você pode localizar sua ID de diretório na [portal do Azure Active Directory](https://aad.portal.azure.com/). Entre como administrador, selecione **Azure Active Directory** e selecione **Propriedades**.

Para impedir que os usuários insiram seu próprio cabeçalho HTTP com locatários não aprovados, o proxy precisará substituir os *Restrict-Access-To-Tenants* cabeçalho se já estiver presente na solicitação de entrada.

Os clientes devem ser forçados a usar o proxy para todas as solicitações para login.microsoftonline.com, login.microsoft.com e login.windows.net. Por exemplo, se os arquivos PAC forem usados para direcionar clientes para usar o proxy, os usuários finais não deve ser capazes de editar ou desabilitar os arquivos PAC.

## <a name="the-user-experience"></a>A experiência do usuário

Esta seção descreve a experiência para os usuários finais e administradores.

### <a name="end-user-experience"></a>Experiência do usuário final

Um usuário de exemplo está na rede da Contoso, mas está tentando acessar a instância da Fabrikam de um aplicativo de SaaS compartilhado como o Outlook online. Se a Fabrikam é um locatário não permitido para a instância de Contoso, o usuário vê uma mensagem de negação de acesso, que diz que você está tentando acessar um recurso que pertence a uma organização não aprovada pelo departamento de TI.

### <a name="admin-experience"></a>Experiência de admin

Embora a configuração de restrições de locatário é feita na infraestrutura do proxy corporativo, administradores podem acessar diretamente os relatórios de restrições de locatário no portal do Azure. Para exibir os relatórios:

1. Entrar para o [portal do Azure Active Directory](https://aad.portal.azure.com/). O **Centro de administração do Azure Active Directory** painel é exibido.

2. No painel esquerdo, selecione **Azure Active Directory**. A página de visão geral do Azure Active Directory é exibido.

3. No **outras funcionalidades** título, selecione **restrições de locatário**.

O administrador do locatário especificado como o locatário Restricted-Access-Context pode usar esse relatório para ver as entradas bloqueadas devido à política de restrições de locatário, incluindo a identidade usada e o diretório de destino ID. As entradas serão incluídas se o locatário que define a restrição for o locatário do usuário ou o locatário do recurso para a entrada.

Como outros relatórios no Portal do Azure, você pode usar filtros para especificar o escopo do relatório. Você pode filtrar um intervalo de tempo específico, o usuário, o aplicativo, o cliente ou o status. Se você selecionar o **colunas** botão, que você pode optar por exibir dados com qualquer combinação dos seguintes campos:

- **Usuário**
- **Aplicativo**
- **Status**
- **Data**
- **Data (UTC)** (onde UTC é o tempo Universal Coordenado)
- **Método de autenticação de MFA** (método de autenticação multifator)
- **Detalhe de autenticação de MFA** (detalhes da autenticação multifator)
- **Resultado da MFA**
- **Endereço IP**
- **Cliente**
- **Nome de Usuário**
- **Localidade**
- **ID do locatário de destino**

## <a name="office-365-support"></a>Suporte ao Office 365

Aplicativos do Office 365 devem atender a dois critérios para dar total suporte a restrições de locatário:

1. O cliente usado dá suporte à autenticação moderna.
2. A autenticação moderna está habilitada como o protocolo de autenticação padrão para o serviço de nuvem.

Consulte [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticação moderna do Office 365 atualizada) para obter as informações mais recentes sobre quais clientes do Office atualmente dão suporte à autenticação moderna. Essa página também inclui links para instruções para habilitar a autenticação moderna nos locatários do Exchange Online e Skype for Business Online. SharePoint Online já habilita a autenticação moderna por padrão.

Aplicativos baseados em navegador do Office 365 (o Portal do Office, Yammer, sites do SharePoint, Outlook na Web e muito mais) atualmente oferece suporte a restrições de locatário. Clientes espessos (Outlook, Skype para negócios, Word, Excel, PowerPoint e muito mais) podem impor restrições de locatário somente ao usar a autenticação moderna.  

Outlook e Skype para clientes de negócios que suportam autenticação moderna ainda podem usar protocolos herdados em locatários em que a autenticação moderna não está habilitada, ignorando efetivamente as restrições de locatário. Restrições de locatário podem bloquear aplicativos que usam protocolos herdados se contatarem login.microsoftonline.com, login.microsoft.com ou login.windows.net durante a autenticação.

Para o Outlook no Windows, os clientes podem optar por implementar restrições que impedem que os usuários finais adicionem contas de email não aprovadas aos seus perfis. Por exemplo, consulte a configuração da política de grupo [Prevent adding non-default Exchange accounts](https://gpsearch.azurewebsites.net/default.aspx?ref=1) (Impedir a adição de contas do Exchange não padrão).

## <a name="testing"></a>Testando

Se você quiser experimentar as restrições de locatário antes de implementá-lo para toda a organização, você tem duas opções: uma abordagem baseada em host usando uma ferramenta como o Fiddler ou uma distribuição em etapas das configurações de proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler para uma abordagem baseada em host

O Fiddler é um proxy de depuração da Web gratuito que pode ser usado para capturar e modificar o tráfego HTTP/HTTPS, incluindo a inserção de cabeçalhos HTTP. Para configurar o Fiddler para testar as restrições de locatário, execute as seguintes etapas:

1. [Baixe e instale o Fiddler](https://www.telerik.com/fiddler).

2. Configure o Fiddler para descriptografar o tráfego HTTPS, de acordo com a [documentação de ajuda do Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Configure o Fiddler para inserir os cabeçalhos *Restrict-Access-To-Tenants* e *Restrict-Access-Context* usando regras personalizadas:

   1. Na ferramenta Depurador da Web Fiddler Web, selecione o menu **Regras** e selecione **Personalizar Regras...** para abrir o arquivo CustomRules.

   2. Adicione as seguintes linhas no início do `OnBeforeRequest` função. Substitua \<domínio de locatário\> com um domínio registrado com seu locatário (por exemplo, `contoso.onmicrosoft.com`). Substitua \<directory ID\> pelo identificador de GUID do Azure AD do locatário.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Se você precisar permitir vários locatários, use uma vírgula para separar os nomes de locatário. Por exemplo: 

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Salve e feche o arquivo CustomRules.

Depois de configurar o Fiddler, você pode capturar o tráfego indo para o menu **Arquivo** e selecionando **Capturar Tráfego**.

### <a name="staged-rollout-of-proxy-settings"></a>Distribuição em etapas as configurações de proxy

Dependendo dos recursos da sua infraestrutura de proxy, você poderá liberar em etapas a distribuição de configurações para seus usuários. Aqui estão algumas opções de alto nível para consideração:

1. Use arquivos PAC para apontar os usuários de teste para uma infraestrutura de proxy de teste, enquanto os usuários normais continuam a usar a infraestrutura do proxy de produção.
2. Alguns servidores proxy podem dar suporte a configurações diferentes usando grupos.

Para obter detalhes específicos, consulte a documentação do servidor proxy.

## <a name="next-steps"></a>Próximos passos

- Leia sobre a [Updated Office 365 modern authentication](https://blogs.office.com/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Autenticação moderna do Office 365 atualizada)
- Examine as [URLs e intervalos de endereços IP do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
