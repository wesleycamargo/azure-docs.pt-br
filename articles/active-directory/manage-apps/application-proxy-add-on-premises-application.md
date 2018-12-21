---
title: Adicionar um aplicativo local – Proxy de Aplicativo no Azure Active Directory | Microsoft Docs
description: O Azure AD (Active Directory) tem um serviço de Proxy de Aplicativo que permite que os usuários acessem aplicativos locais ao entrar com sua conta do Azure AD. Este tutorial mostra como preparar seu ambiente para uso com o Proxy de Aplicativo e, em seguida, usa o portal do Azure para adicionar um aplicativo local ao seu locatário do Azure AD.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 444fb5576ed6886e5919202cf7f22ef14e1255b5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321402"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Adicionar um aplicativo local para acesso remoto por meio do Proxy de Aplicativo no Azure Active Directory

O Azure AD (Active Directory) tem um serviço de Proxy de Aplicativo que permite que os usuários acessem aplicativos locais ao entrar com sua conta do Azure AD. Esse tutorial prepara seu ambiente para uso com o Proxy de Aplicativo. Depois que seu ambiente estiver pronto, você usará o portal do Azure para adicionar um aplicativo local ao seu locatário do Azure AD.

Este tutorial:

> [!div class="checklist"]
> * Abre as portas para tráfego de saída e permite o acesso a URLs específicas
> * Instala o conector em seu servidor Windows e o registra com o Proxy de Aplicativo
> * Verifica se o conector está instalado e registrado corretamente
> * Adiciona um aplicativo local ao locatário do Azure AD
> * Verifica se um usuário de teste pode fazer logon no aplicativo usando uma conta do Azure AD.

## <a name="before-you-begin"></a>Antes de começar

Para adicionar um aplicativo ao locatário, você precisará de:

* Uma [assinatura premium ou básica do Microsoft Azure AD](https://azure.microsoft.com/pricing/details/active-directory). 
* Uma conta de administrador de aplicativo.

### <a name="windows-server"></a>Windows Server
Como o aplicativo sendo adicionado é local, você precisa de um servidor Windows executando o Windows Server 2012 R2 ou posterior no qual possa instalar o conector do Proxy de Aplicativo. Esse servidor do conector precisa se conectar aos serviços do Proxy de Aplicativo no Azure e aos aplicativos locais que planeja publicar.

Para alta disponibilidade no seu ambiente de produção, é recomendável ter mais de um servidor Windows.  Para este tutorial, um servidor Windows é suficiente.

**Recomendações para o servidor do conector**

1. Localize o servidor do conector fisicamente próximo aos servidores de aplicativos para otimizar o desempenho entre o conector e o aplicativo. Para obter mais informações, confira [Considerações sobre a topologia de rede](application-proxy-network-topology.md).

2. O servidor do conector e os servidores de aplicativos Web devem pertencer ao mesmo domínio do Active Directory. Ter os servidores no mesmo domínio é um requisito para usar o SSO (logon único) com IWA (Autenticação Integrada do Windows) e KCD (delegação restrita de Kerberos). Se o servidor do conector e os servidores de aplicativos Web estiverem em domínios do Active Directory diferentes, você precisará usar a delegação baseada em recursos para o logon único. Para obter mais informações, consulte [KCD para logon único com Proxy de aplicativo](application-proxy-configure-single-sign-on-with-kcd.md).

**Requisitos de software**

O servidor do conector Windows precisa ter o TLS 1.2 habilitado antes de instalar o conector do Proxy de Aplicativo. Os conectores existentes com versões abaixo de 1.5.612.0 continuarão funcionando nas versões anteriores do TLS até novo aviso. 

Para habilitar o TLS 1.2:

1. Defina as seguintes chaves do registro:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Reinicie o servidor

  
## <a name="prepare-your-on-premises-environment"></a>Preparar o ambiente local
Para preparar o ambiente para o proxy de aplicativo do Azure AD, primeiro você precisa habilitar a comunicação com data centers do Azure. Se houver um firewall no caminho, verifique se ele está aberto para que o conector possa fazer solicitações HTTPS (TCP) para o Proxy de Aplicativo.

### <a name="open-ports"></a>Abrir portas

Abra as seguintes portas para o tráfego de **saída**. 

   | Número da porta | Como ele é usado |
   | --- | --- |
   | 80 | Baixando as CRLs (listas de certificados revogados) ao validar o certificado SSL |
   | 443 | Toda a comunicação de saída com o serviço do proxy de aplicativo |

Se o firewall impor o tráfego de acordo com os usuários originadores, abra também as portas 80 e 443 para o tráfego proveniente dos serviços do Windows que são executados como um serviço de rede.

Se você já estiver usando o Proxy de Aplicativo, poderá ter uma versão mais antiga do conector instalado.  Siga este tutorial para instalar a versão mais recente do conector. As versões anteriores à 1.5.132.0 também exigem que as seguintes portas estejam abertas: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Permitir acesso às URLs

Permita o acesso às seguintes URLs:

| URL | Como ele é usado |
| --- | --- |
| \*.msappproxy.net<br>servicebus.windows.net | Comunicação entre o conector e o serviço de nuvem do Proxy de Aplicativo |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | O Azure usa essas URLs para verificar os certificados |
| login.windows.net<br>login.microsoftonline.com | O conector usa essas URLs durante o processo de registro. |

Se seu firewall ou proxy permitir lista de permissões de DNS, você poderá adicionar as conexões a msappproxy.net e servicebus.windows.net à lista de permissões. Caso contrário, precisa permitir o acesso aos [Intervalos de IP do DataCenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653), que são atualizados a cada semana.

## <a name="install-and-register-a-connector"></a>Instalar e registrar um conector
Para usar o Proxy de Aplicativo, você precisará instalar um conector em cada servidor Windows que você optar por usar com o serviço de Proxy de Aplicativo. O conector é um agente que gerencia a conexão de saída de servidores de aplicativos locais para o Proxy de Aplicativo no Azure AD. Você pode instalar um conector em servidores que também tenham outros agentes de autenticação instalados, como o Azure AD Connect.

Para instalar o conector:

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de aplicativos do diretório que usa o Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deverá ser admin@contoso.com ou qualquer outro alias de administrador nesse domínio.
2. O diretório atual é exibido sob seu nome de usuário no canto superior direito. Verifique se você está conectado ao diretório que usa o Proxy de Aplicativo. Se você precisar alterar os diretórios, selecione esse ícone.
3. Na folha da esquerda, clique em **Azure Active Directory** e em **Proxy de Aplicativo**.
4. Clique em **Baixar serviço do conector**.
5. Leia os Termos de serviço.  Quando estiver pronto, clique em **Aceitar termos e Baixar**.
6. Na parte inferior da janela, você verá um prompt para baixar o **AADApplicationProxyConnectorInstaller.exe**. Clique em **Executar** para instalar o conector. Um assistente de instalação é aberto. 
7. Siga as instruções no Assistente para instalar. Quando for solicitado que você registre o conector com o Proxy de Aplicativo para seu locatário do Azure AD, forneça suas credenciais de administrador do aplicativo.
    - Para IE (Internet Explorer), se a **Configuração de Segurança Aprimorada do IE** estiver **Ativada**, você poderá não ver a tela de registro. Siga as instruções na mensagem de erro para obter o acesso. Certifique-se de que a Segurança Aprimorada do Internet Explorer está **Desativado**.

### <a name="general-remarks"></a>Comentários gerais

Caso já tenha instalado um conector, reinstale-o para obter a versão mais recente.

Se você optar por ter mais de um servidor Windows para seus aplicativos locais, precisará instalar e registrar o conector em cada servidor. Você pode organizar os conectores em grupos de conectores. Para obter mais informações, confira [Grupos de conectores](application-proxy-connector-groups.md). 

Se sua organização usa servidores proxy para se conectar à internet, você precisará configurá-los para o Proxy de aplicativo.  Para obter mais informações, consulte [trabalhar com existentes locais servidores proxy](application-proxy-configure-connectors-with-proxy-servers.md). 

Para saber mais sobre conectores, planejamento de capacidade e como eles permanecem atualizados, confira [Noções básicas sobre conectores de Proxy de Aplicativo do Azure AD](application-proxy-connectors.md). 

Se o aplicativo usar WebSockets para se conectar, verifique se você instalou o conector mais recente.  Os WebSockets são compatíveis com versões do conector 1.5.612.0 ou posteriores.


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Verificar se o conector está instalado e registrado corretamente

Você pode usar o portal do Azure ou seu servidor Windows para confirmar que um novo conector foi instalado corretamente.

### <a name="verify---azure-portal"></a>Verificar – portal do Azure
Para confirmar se o conector foi instalado e registrado corretamente:

1. Entre no seu diretório de locatário no [portal do Azure](https://portal.azure.com).
2. Clique em **Azure Active Directory** e em **Proxy de Aplicativo**. Todos os seus conectores e grupos de conector são exibidos nesta página. 
3. Selecione um conector para verificar seus detalhes. Um rótulo verde ativo indica que seu conector pode se conectar ao serviço. No entanto, mesmo que o rótulo esteja verde, um problema de rede ainda poderá bloquear o recebimento de mensagens pelo conector. 

    ![Conectores do Proxy de Aplicativo Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Para obter mais ajuda com a instalação de um conector, confira [Problemas ao instalar o conector de Application Proxy](application-proxy-connector-installation-problem.md).

### <a name="verify---windows-server"></a>Verificar – servidor do Windows
Para confirmar se o conector foi instalado e registrado corretamente:

1. Abra o Gerenciador de Serviços do Windows clicando na tecla **Windows** e inserindo *services.msc*.
2. Verifique se o status dos dois serviços a seguir é **Em Execução**.
   - **Conector de Proxy de Aplicativo do Microsoft AAD** habilita a conectividade
   - O **Atualizador de conector do Proxy de Aplicativo do Microsoft AAD** é um serviço de atualização automática. O atualizador verifica novas versões do conector e o atualiza conforme necessário.

    ![Serviços do Conector de Proxy de Aplicativo - captura de tela](./media/application-proxy-enable/app_proxy_services.png)

3. Se o status dos serviços não for **em execução**, clique com o botão direito do mouse em cada serviço e escolha **iniciar**. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Adicionar um aplicativo local ao Azure AD

Agora que você preparou seu ambiente e instalou um conector, está pronto para adicionar aplicativos de locais ao Azure AD.  

1. Entre como administrador no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Novo aplicativo**.

    ![Adicionar um aplicativo empresarial](./media/application-proxy-publish-azure-portal/add-app.png)

3. Selecione **Todos** e, em seguida, selecione **Aplicativo local**.  

    ![Adicionar seu próprio aplicativo](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Forneça as seguintes informações sobre o aplicativo:

    ![Configurar seu aplicativo](./media/application-proxy-publish-azure-portal/configure-app.png)

    | Campo | DESCRIÇÃO |
    | :---- | :---------- |
    | **Nome** | O nome do aplicativo que será exibido no painel de acesso e no portal do Azure. |
    | **URL Interna** | A URL para acessar o aplicativo de dentro de sua rede privada. Você pode fornecer um caminho específico no servidor back-end para publicar, enquanto o restante do servidor é não publicado. Assim, você pode publicar sites diferentes no mesmo servidor como diferentes aplicativos, e dar a cada um deles seu próprio nome e suas regras de acesso.<br><br>Se você publicar um caminho, verifique se ele inclui todas as imagens, scripts e folhas de estilo necessários para seu aplicativo. Por exemplo, se o aplicativo estiver em https://yourapp/app e utilizar imagens localizadas em https://yourapp/media, você deverá publicar https://yourapp/ como o caminho. Essa URL interna não precisa ser a página de aterrissagem que os usuários veem. Para obter mais informações, consulte [Definir uma página inicial personalizada para aplicativos publicados](application-proxy-configure-custom-home-page.md). |
    | **URL Externa** | O endereço para os usuários acessarem o aplicativo de fora da sua rede. Se você não quiser usar o domínio padrão de Proxy de Aplicativo, leia sobre [domínios personalizados no Proxy de Aplicativo do Azure AD](application-proxy-configure-custom-domain.md).|
    | **Pré-autenticação** | Como o Proxy de Aplicativo verifica os usuários antes de conceder a eles o acesso ao aplicativo.<br><br>**Azure Active Directory** – o Proxy de Aplicativo redireciona os usuários para entrar com o Azure AD, que autentica as permissões para o diretório e o aplicativo. É recomendável manter essa opção como padrão, para que você possa tirar proveito dos recursos de segurança do Azure AD como acesso condicional e Autenticação Multifator.<br><br>**Passagem** – os usuários não precisam ser autenticados no Azure Active Directory para acessar o aplicativo. Você ainda pode configurar os requisitos de autenticação no back-end. |
    | **Grupo de Conectores** | Conectores processam o acesso remoto ao seu aplicativo e o ajudam a organizar conectores e aplicativos por região, rede ou finalidade. Se você ainda não tiver grupos de conectores criados, seu aplicativo é atribuído a **Padrão**.<br><br>Se o aplicativo usa WebSockets para se conectar, todos os conectores do grupo devem ser da versão 1.5.612.0 ou posterior.|

5. Se necessário, defina configurações adicionais. Para a maioria dos aplicativos, você deve manter essas configurações em seus estados padrão. 

    ![Configurar seu aplicativo](./media/application-proxy-publish-azure-portal/additional-settings.png)

    | Campo | DESCRIÇÃO |
    | :---- | :---------- |
    | **Tempo Limite do Aplicativo Back-end** | Defina esse valor como **Longo** somente se o aplicativo estiver lento para se autenticar e se conectar. |
    | **Usar Cookie Somente HTTP** | Defina esse valor como **Sim** para que os cookies do Proxy de Aplicativo incluam o sinalizador HTTPOnly no cabeçalho da resposta HTTP. Se estiver usando os Serviços de Área de Trabalho Remota, defina isso como **Não**.|
    | **Usar um Cookie Seguro**| Defina esse valor como **Sim** para garantir que os cookies sejam transmitidos apenas por um canal seguro, como uma solicitação HTTPS criptografada.
    | **Converter URLs nos Cabeçalhos** | Mantenha esse valor como **Sim** a menos que seu aplicativo exija o cabeçalho de host original na solicitação de autenticação. |
    | **Converter URLs no Corpo do Aplicativo** | Mantenha esse valor como **Não** a menos que você tenha inserido no código HTML links para outros aplicativos locais e não use domínios personalizados. Para saber mais, consulte [Conversão de link com o Proxy de Aplicativo](application-proxy-configure-hard-coded-link-translation.md). |
   


6. Selecione **Adicionar**.

## <a name="test-the-application"></a>Testar o aplicativo

Para testar se o seu aplicativo foi adicionado corretamente, você adicionará uma conta de usuário ao aplicativo e tentará entrar. 

### <a name="add-a-user-for-testing"></a>Adicionar um usuário para teste
Antes de adicionar um usuário ao aplicativo, verifique se que a conta de usuário já tem permissões para acessar o aplicativo de dentro da rede corporativa.

Para adicionar um usuário de teste:

1. De volta à folha **Início Rápido**, selecione **Atribuir um usuário para teste**.

    ![Atribuir um usuário para teste](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Na folha **Usuários e grupos**, selecione **Adicionar usuário**.

    ![Adicionar um usuário ou grupo](./media/application-proxy-publish-azure-portal/add-user.png)

3. Na folha **Adicionar atribuição**, selecione **Usuários e grupos** e escolha a conta que você deseja adicionar. 
4. Selecione **Atribuir**.

### <a name="test-the-sign-on"></a>Testar o logon

Para testar o logon no aplicativo:

1. No seu navegador, navegue até a URL externa configurada durante a etapa de publicação. 
2. Você deve ver a tela inicial e ser capaz de entrar com a conta de teste que configurou.

    ![Testar seu aplicativo publicado](./media/application-proxy-publish-azure-portal/test-app.png)

Para solucionar problemas, confira [Solucionar problemas e mensagens de erro do Proxy do Aplicativo](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você preparou seu ambiente local para trabalhar com o Proxy de Aplicativo e, em seguida, instalou e registrou o conector de Proxy de Aplicativo. Depois, você adicionou um aplicativo ao seu locatário do Azure AD e verificou que funcionou entrando no aplicativo com uma conta do Azure AD.

Você fez essas coisas:
> [!div class="checklist"]
> * Abriu as portas para tráfego de saída e permitiu o acesso a URLs específicas
> * Instalou o conector em seu servidor Windows e o registrou com o Proxy de Aplicativo
> * Verificou se o conector estava instalado e registrado corretamente
> * Adicionou um aplicativo local ao locatário do Azure AD
> * Verificou se um usuário de teste podia fazer logon no aplicativo usando uma conta do Azure AD.

Agora, você está pronto para configurar o aplicativo para logon único. Há vários métodos de logon único e escolher o melhor método depende da forma como seu aplicativo faz a autenticação. O link a seguir ajuda a localizar o tutorial de logon único adequado para seu aplicativo.

> [!div class="nextstepaction"]
>[Configurar Logon Único](what-is-single-sign-on.md#choosing-a-single-sign-on-method)





