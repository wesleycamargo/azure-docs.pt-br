---
title: Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Aborda os conceitos básicos sobre como integrar um servidor do SharePoint local com o Proxy de Aplicativo do Azure AD.
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
ms.date: 12/10/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dc80b78bbba369e0ddb5c2c1e9fd90834dc0148
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120407"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Habilitar acesso remoto ao SharePoint com o Proxy de Aplicativo do Azure AD

Este artigo descreve como integrar um servidor local do SharePoint ao proxy de aplicativo do Azure AD (Azure Active Directory).

Para habilitar o acesso remoto ao SharePoint com o Proxy de aplicativo do Azure AD, siga as seções neste artigo passo a passo.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha o SharePoint 2013 ou mais recente no seu ambiente. Além disso, considere os seguintes pré-requisitos:

* O SharePoint inclui suporte nativo a Kerberos. Portanto, os usuários que acessam sites internos remotamente por meio do Proxy de Aplicativo do Azure AD podem supor que terão uma experiência de SSO (logon único).

* Esse cenário inclui algumas mudanças na configuração do seu servidor do SharePoint. É recomendável usar um ambiente de preparo. Dessa forma, você pode fazer atualizações no servidor de preparo primeiro e depois facilitar um ciclo de testes antes de passar para o de produção.

* É necessário SSL na URL publicada. O SSL também é necessário na URL interna para fazer com que os links sejam enviados/mapeados corretamente.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Etapa 1: configurar a KCD (Delegação Restrita de Kerberos)

Para aplicativos locais que usam a autenticação do Windows, você pode obter SSO (logon único) usando o protocolo de autenticação Kerberos e um recurso chamado KCD (delegação restrita de Kerberos). A KCD, quando configurada, permite que o conector do Proxy de Aplicativo obtenha um token do Windows para um usuário, mesmo que ele não tenha entrado no Windows diretamente. Para saber mais sobre a KCD, confira [Visão geral da delegação restrita de Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Para configurar o KCD para um servidor do SharePoint, use os procedimentos nas seções sequenciais a seguir:

### <a name="ensure-that-sharepoint-web-application-is-running-under-a-domain-account"></a>Verifique se o aplicativo Web do SharePoint está em execução em uma conta de domínio

Primeiro, verifique se o aplicativo Web do SharePoint está em execução em uma conta de domínio, não no sistema local, no serviço local ou no serviço de rede. Faça isso para que você possa anexar SPNs (nomes da entidade de serviço) a essa conta. Os SPNs são a maneira como o protocolo Kerberos identifica serviços diferentes. E você precisará da conta mais tarde para configurar a KCD.

> [!NOTE]
> Você precisará ter uma conta do Azure AD criada anteriormente para o serviço. Sugerimos que você permita uma alteração automática de senha. Para obter mais informações sobre o conjunto completo de etapas e a solução de problemas, confira [Configurar a alteração automática da senha no SharePoint](https://technet.microsoft.com/library/ff724280.aspx).

Para garantir que seus sites estejam em execução em uma conta de serviço definida, realize as seguintes etapas:

1. Abra o site da **Administração Central do SharePoint**.
2. Vá para **Segurança** e selecione **Configurar contas de serviço**.
3. Selecione **Pool de Aplicativos Web – SharePoint – 80**. As opções podem ser ligeiramente diferentes com base no nome do seu pool Web ou caso o pool Web use o SSL por padrão.

   ![Opções para configurar uma conta de serviço](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Se o campo **Selecionar uma conta para este componente** estiver definido como **Serviço Local** ou **Serviço de Rede**, você precisará criar uma conta. Caso contrário, você já poderá passar para a próxima etapa.
5. Escolha **Registrar uma nova conta gerenciada**. Depois que a conta é criada, é preciso definir o **Pool de Aplicativos Web** antes de poder usá-la.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Definir um nome da entidade de serviço para a conta de serviço do SharePoint

Antes de configurar a KCD, você precisa:

* Identificar a conta de domínio executando o aplicativo Web do SharePoint que o Proxy do Azure AD vai expor.
* Escolher uma URL interna que será configurada no Proxy do Azure AD e no SharePoint. Essa URL interna não pode já ter sido usada no aplicativo Web e nunca será exibida no navegador da Web.

Supondo que a URL interna escolhida seja <https://sharepoint>, o SPN será:

```
HTTP/SharePoint
```

> [!NOTE]
> Respeite as seguintes recomendações para a URL interna:
> * Usar HTTPS
> * Não usar portas personalizadas
> * No DNS, criar um Host (A) para apontar para o SharePoint WFE (ou balanceador de carga) e não para um Alias (CName)

Para registrar esse SPN, execute o comando abaixo no prompt de comando como um administrador do domínio:

```
setspn -S HTTP/SharePoint demo\spAppPoolAccount
```

Este comando define o SPN _HTTP/SharePoint_ para a conta de pool de aplicativos do SharePoint _demo\spAppPoolAccount_.

Substitua _HTTP/SharePoint_ pelo SPN para a URL interna e _demo\spAppPoolAccount_ pela conta do pool de aplicativos em seu ambiente. O comando Setspn pesquisa pelo SPN antes de adicioná-lo. Se já existir, você verá um erro de **Valor de SPN Duplicado**. Nesse caso, considere remover o SPN existente se ele não estiver definido na conta de pool de aplicativos correta.

Você pode verificar se o SPN foi adicionado executando o comando Setspn com a opção -L. Para saber mais sobre esse comando, confira [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-sharepoint-application-pool-account"></a>Verifique se o conector é confiável para delegação para o SPN adicionado à conta de pool de aplicativos do SharePoint

Configure a KCD para que o serviço Proxy de Aplicativo do Azure AD seja capaz de delegar identidades do usuário à conta do pool de aplicativos do SharePoint. Configure a KCD habilitando o conector Proxy de Aplicativo para recuperar tíquetes Kerberos para seus usuários que foram autenticados no Azure AD. Em seguida, esse servidor passa o contexto ao aplicativo de destino, ou o SharePoint, nesse caso.

Para configurar a KCD, repita as etapas a seguir para cada computador conector:

1. Faça logon como um administrador de domínio em um DC e abra **Usuários e Computadores do Active Directory**.
2. Encontre o computador em que o conector está em sendo executado. Neste exemplo, ele é o mesmo servidor do SharePoint.
3. Clique duas vezes no computador e então clique na guia **Delegação**.
4. Verifique se as configurações de delegações estão definidas como **Confiar neste computador para delegação somente para os serviços especificados**. Então, selecione **Usar qualquer protocolo de autenticação**.
5. Clique no botão **Adicionar**, clique em **Usuários ou Computadores** e localize a conta de pool de aplicativos do SharePoint, por exemplo, _demo\spAppPoolAccount_.
6. Na lista de SPNs, selecione aquele que você criou anteriormente para a conta de serviço.
7. Clique em **OK**. Clique em **OK** novamente para salvar as alterações.
  
   ![Configurações de delegação](./media/application-proxy-integrate-with-sharepoint-server/delegation-box2.png)

## <a name="step-2-configure-azure-ad-proxy"></a>Etapa 2: configurar o Proxy do Azure AD

Agora que você configurou a KCD, está pronto para configurar o Proxy de Aplicativo do Azure AD.

1. Publique seu site do SharePoint com as seguintes configurações. Para obter instruções passo a passo, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-publish-azure-portal.md).
   * **URL Interna**: a URL interna do SharePoint que foi escolhida anteriormente, por exemplo, **<https://SharePoint/>**.
   * **Método de pré-autenticação**: Azure Active Directory
   * **Traduzir URL nos cabeçalhos**: NÃO

   >[!TIP]
   >O SharePoint usa o valor _Cabeçalho de Host_ para pesquisar o site. Ele também gera links com base nesse valor. O resultado final é que qualquer link que o SharePoint gere será uma URL publicada corretamente definida para usar a URL externa. Configurar o valor para **SIM** também permite que o conector encaminhe a solicitação ao aplicativo de back-end. No entanto, configurar o valor como **NÃO** significa que o conector não enviará o nome de host interno. Em vez disso, o conector envia o cabeçalho do host como a URL publicada para o aplicativo de back-end.

   ![Publicar SharePoint como aplicativo](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Depois que seu aplicativo for publicado, defina as configurações de logon único com as seguintes etapas:

   1. Na página de aplicativo no portal, selecione **Logon único**.
   2. Para o modo de logon único, selecione **Autenticação Integrada do Windows**.
   3. Defina o SPN do Aplicativo Interno para o valor que você definiu anteriormente. Neste exemplo, isso seria **HTTP/SharePoint**.
   4. Em "Identidade de Logon Delegada", selecione **Nome da conta SAM local**.

   ![Configurar a Autenticação Integrada do Windows para SSO](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Para concluir a configuração de seu aplicativo, vá para a seção **Usuários e grupos** e atribua usuários ao acessar este aplicativo. 

## <a name="step-3-configure-sharepoint-to-use-kerberos-and-azure-ad-proxy-urls"></a>Etapa 3: configurar o SharePoint para usar o Kerberos e URLs do Proxy do Azure AD

A próxima etapa é estender o aplicativo Web do SharePoint para uma nova zona, configurado com o Kerberos e o mapeamento de acesso alternativo apropriado para permitir que o SharePoint lide com solicitações de entrada enviadas para a URL interna e responda com links criados para a URL externa.

1. Inicie o **Shell de Gerenciamento do SharePoint**.
2. Execute o seguinte script para estender o aplicativo Web para a zona Extranet e habilitar a autenticação Kerberos:

   ```powershell
   # Replace "http://spsites/" with the URL of your web application
   # Replace "https://sharepoint-f128.msappproxy.net/" with the External URL in your Azure AD proxy application
   $winAp = New-SPAuthenticationProvider -UseWindowsIntegratedAuthentication -DisableKerberos:$false
   Get-SPWebApplication "http://spsites/" | New-SPWebApplicationExtension -Name "SharePoint - AAD Proxy" -SecureSocketsLayer -Zone "Extranet" -Url "https://sharepoint-f128.msappproxy.net/" -AuthenticationProvider $winAp
   ```

3. Abra o site da **Administração Central do SharePoint**.
4. Em **Configurações do Sistema**, selecione **Configurar Mapeamentos Alternativos de Acesso**. A caixa Mapeamentos Alternativos de Acesso é aberta.
5. Selecione o seu site, por exemplo, **SharePoint - 80**. Por enquanto, a zona Extranet ainda não tem a URL interna definida corretamente:

   ![Caixa Mapeamentos Alternativos de Acesso](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

6. Clique em **Adicionar URLs Internas**.
7. Na caixa de texto **Protocolo, host e porta da URL**, digite a **URL interna** configurada no proxy do Azure AD, por exemplo, <https://SharePoint/>.
8. Selecione Zona **Extranet** na lista suspensa.
9. Clique em **Salvar**.
10. Agora, os Mapeamentos Alternativos de Acesso deve ficar assim:

    ![Corrigir Mapeamentos Alternativos de Acesso](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

## <a name="step-4-ensure-that-an-https-certificate-is-configured-for-the-iis-site-of-the-extranet-zone"></a>Etapa 4: verificar se um certificado HTTPS está configurado para o site do IIS da zona Extranet

A configuração do SharePoint foi concluída, mas como a URL interna da zona Extranet é <https://SharePoint/>, um certificado deve ser definido para o site.

1. Abra o console do Windows PowerShell.
2. Execute o seguinte script para gerar um certificado autoassinado e adicioná-lo ao computador MEU armazenamento:

   ```powershell
   # Replace "SharePoint" with the actual hostname of the Internal URL of your Azure AD proxy application
   New-SelfSignedCertificate -DnsName "SharePoint" -CertStoreLocation "cert:\LocalMachine\My"
   ```

   > [!NOTE]
   > Os certificados autoassinados são adequados somente para fins de teste. Em ambientes de produção, é altamente recomendável usar certificados emitidos por uma autoridade de certificação.

3. Abra o console "Gerenciador dos Serviços de Informações da Internet".
4. Expanda o servidor no modo de exibição de árvore, expanda "Sites", selecione o site “SharePoint – Proxy do AAD” e clique em **Associações**.
5. Selecione a associação HTTPS e clique em **Editar...** .
6. No campo do certificado SSL, escolha o certificado **SharePoint** e clique em OK.

Agora você pode acessar o site do SharePoint externamente por meio do Proxy de Aplicativo do Azure AD.

## <a name="next-steps"></a>Próximas etapas

* [Trabalhando com domínios personalizados no Proxy de Aplicativo do Azure AD](application-proxy-configure-custom-domain.md)
* [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md)
