---
title: Implantar proteção de senha do Azure AD
description: Implantar a proteção de senha do AD do Azure para proibir senhas incorretas no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54d2d600771316b0a88ea0a2486c0dedd0f84594
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286526"
---
# <a name="deploy-azure-ad-password-protection"></a>Implantar proteção de senha do Azure AD

Agora que você entende [como impor a proteção por senha do Azure AD para Windows Server Active Directory](concept-password-ban-bad-on-premises.md), a próxima etapa é planejar e executar sua implantação.

## <a name="deployment-strategy"></a>Estratégia de implantação

É recomendável que você iniciar implantações no modo de auditoria. Modo de auditoria é a configuração inicial padrão, onde as senhas podem continuar a ser definido. Senhas que seriam bloqueadas são registradas no log de eventos. Depois de implantar os servidores de proxy e agentes de controlador de domínio no modo de auditoria, você deve monitorar o impacto que a política de senha terá em usuários e o ambiente quando a política é aplicada.

Durante o estágio de auditoria, muitas organizações Saiba que:

* Eles precisam melhorar os processos operacionais existentes para usar senhas mais seguras.
* Os usuários geralmente usam as senhas não seguras.
* Eles precisam informar os usuários sobre a alteração futura na imposição de segurança, impacto possível sobre eles e como escolher senhas mais seguras.

Depois que o recurso tiver sido executado em modo de auditoria por um período razoável, você pode alternar a configuração a partir *auditar* à *impor* para exigir senhas mais seguras. O monitoramento focalizado durante esse período é uma boa ideia.

## <a name="deployment-requirements"></a>Requisitos de implantação

* Todos os controladores de domínio que obtém o agente de controlador de domínio de serviço para a proteção de senha do AD do Azure instalada deve executar o Windows Server 2012 ou posterior.
* Todas as máquinas que obtêm o proxy do serviço de proteção por senha do Azure AD instalada deve executar o Windows Server 2012 R2 ou posterior.
* Todos os computadores em que o serviço de Proxy de proteção de senha do AD do Azure será instalado devem ter o .NET 4.7 instalado.
  .NET 4.7 já deve estar instalado em um Windows Server totalmente atualizado. Se isso não for o caso, baixe e execute o instalador encontrado em [o .NET Framework 4.7 o instalador offline para o Windows](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Todos os computadores, incluindo controladores de domínio, que obtém os componentes de proteção de senha do Azure AD instalados deve ter o tempo de execução C Universal instalado. Você pode obter o tempo de execução, tornando-se de que ter todas as atualizações do Windows Update. Ou você pode obtê-lo em um pacote de atualização específicas do sistema operacional. Para obter mais informações, consulte [atualização para o tempo de execução C Universal no Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Conectividade de rede deve existir entre pelo menos um controlador de domínio em cada domínio e pelo menos um servidor que hospeda o serviço de proxy para a proteção por senha. Essa conectividade deve permitir que o controlador de domínio acessar a porta de mapeador de ponto de extremidade RPC 135 e a porta do servidor RPC no serviço de proxy. Por padrão, a porta do servidor RPC é uma porta dinâmica da RPC, mas ele pode ser configurado para [usar uma porta estática](#static).
* Todos os computadores que hospedam o serviço de proxy devem ter acesso à rede para os pontos de extremidade a seguir:

    |**Ponto de extremidade**|**Finalidade**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Solicitações de autenticação|
    |`https://enterpriseregistration.windows.net`|Funcionalidade de proteção de senha do Microsoft Azure Active Directory|

* Todos os computadores que hospedam o serviço de proxy para a proteção por senha deve ser configurada para permitir o tráfego de saída HTTP do TLS 1.2.
* Uma conta de Administrador Global para registrar o serviço de proxy para a proteção por senha e de floresta com o Azure AD.
* Uma conta que tenha privilégios de administrador de domínio do Active Directory no domínio de raiz da floresta para registrar a floresta do Active Directory do Windows Server com o Azure AD.
* Qualquer domínio do Active Directory que executa o software do serviço de agente de controlador de domínio deve usar o Distributed arquivo sistema DFSR (replicação) para a replicação do sysvol.
* O serviço de distribuição de chaves deve ser habilitado em todos os controladores de domínio no domínio que executam o Windows Server 2012. Por padrão, esse serviço é habilitado por meio do início do gatilho manual.

## <a name="single-forest-deployment"></a>Implantação de floresta única

O diagrama a seguir mostra como os componentes básicos de proteção de senha do AD do Azure funcionam juntos em um ambiente do Active Directory local.

![Como os componentes de proteção por senha do Azure AD trabalham em conjunto](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

É uma boa ideia examinar como o software funciona antes de implantá-lo. Ver [visão geral conceitual de proteção por senha do Azure AD](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>Baixe o software

Há dois instaladores necessários para a proteção de senha do AD do Azure. Elas estão disponíveis pela [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Instalar e configurar o serviço de proxy para a proteção por senha

1. Escolha um ou mais servidores para hospedar o serviço de proxy para a proteção por senha.
   * Cada serviço desse tipo só pode fornecer políticas de senha para uma única floresta. O computador host deve ser associado a um domínio na floresta. Domínios raiz e filho têm suporte. Você precisa de conectividade de rede entre pelo menos um controlador de domínio em cada domínio da floresta e a máquina de proteção de senha.
   * Você pode executar o serviço de proxy em um controlador de domínio para teste. Mas esse controlador de domínio, em seguida, exige conectividade com a internet, que pode ser uma preocupação de segurança. Recomendamos essa configuração somente para teste.
   * É recomendável que pelo menos dois servidores proxy para redundância. Ver [alta disponibilidade](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Serviço de Proxy de proteção de senha de instalar o AD do Azure usando o `AzureADPasswordProtectionProxySetup.exe` instalador de software.
   * A instalação do software não requer uma reinicialização. A instalação do software pode ser automatizada usando procedimentos MSI padrão, por exemplo:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > O serviço de Firewall do Windows deve ser executado antes de instalar o pacote AzureADPasswordProtectionProxySetup.msi para evitar um erro de instalação. Se o Firewall do Windows está configurado para não executar, a solução alternativa é habilitar temporariamente e executar o serviço de Firewall durante a instalação. O software de proxy não tem nenhuma dependência específica no Firewall do Windows após a instalação. Se você estiver usando um firewall de terceiros, ele ainda deve ser configurado para satisfazer os requisitos de implantação. Entre elas estão permitindo o acesso de entrada para a porta 135 e o proxy de porta do servidor RPC. Ver [requisitos de implantação](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Abra uma janela do PowerShell como administrador.
   * O software de proxy de proteção de senha inclui um novo módulo do PowerShell, *AzureADPasswordProtection*. As etapas a seguir executar diversos cmdlets desse módulo do PowerShell. Importe o novo módulo da seguinte maneira:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Para verificar se o serviço está em execução, use o seguinte comando do PowerShell:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     O resultado deve mostrar uma **Status** "Running".

1. Registre o proxy.
   * Após a conclusão da etapa 3, o serviço de proxy está em execução no computador. Mas o serviço ainda não tem as credenciais necessárias para se comunicar com o Azure AD. O registro com o Azure AD, é necessário:

     `Register-AzureADPasswordProtectionProxy`

     Esse cmdlet requer credenciais de administrador global para seu locatário do Azure. Você também precisa de privilégios de administrador de domínio local do Active Directory no domínio raiz da floresta. Depois que esse comando for bem-sucedido de uma vez para um serviço de proxy, as invocações adicionais dele terá êxito, mas são desnecessárias.

      O `Register-AzureADPasswordProtectionProxy` cmdlet oferece suporte aos seguintes modos de autenticação de três.

     * Modo de autenticação interativo:

        ```PowerShell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Esse modo não funciona em sistemas operacionais de Server Core. Em vez disso, use um dos seguintes modos de autenticação. Além disso, esse modo pode falhar se a configuração de segurança reforçada do Internet Explorer estiver habilitada. A solução alternativa é desabilitar essa configuração, registre-se o proxy e, em seguida, habilitá-la novamente.

     * Modo de autenticação de código de dispositivo:

        ```PowerShell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Você deve concluir a autenticação, seguindo as instruções exibidas em um dispositivo diferente.

     * Modo de autenticação silenciosa (baseada em senha):

        ```PowerShell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Esse modo falhará se a autenticação multifator do Azure é necessária. Nesse caso, use um dos modos de autenticação de dois anterior.

       Atualmente, não precisa especificar o *- ForestCredential* parâmetro, que é reservado para a funcionalidade futura.

   
   Registro do serviço de proxy para a proteção de senha é necessário apenas uma vez no tempo de vida do serviço. Depois disso, o serviço de proxy executará automaticamente qualquer outra manutenção necessária.

   > [!TIP]
   > Pode haver um atraso considerável antes da conclusão na primeira vez que esse cmdlet é executado para um locatário do Azure específico. A menos que uma falha será relatada, não se preocupe com esse atraso.

1. Registre a floresta.
   * Você deve inicializar a floresta do Active Directory local com as credenciais necessárias para se comunicar com o Azure usando o `Register-AzureADPasswordProtectionForest` cmdlet do PowerShell. O cmdlet requer credenciais de administrador global para seu locatário do Azure. Ele também requer privilégios de administrador de domínio local do Active Directory no domínio raiz da floresta. Esta etapa é executada uma vez por floresta.

      O `Register-AzureADPasswordProtectionForest` cmdlet oferece suporte aos seguintes modos de autenticação de três.

     * Modo de autenticação interativo:

        ```PowerShell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Esse modo não funcionará em sistemas operacionais de Server Core. Em vez disso, use um dos seguintes modos de autenticação de dois. Além disso, esse modo pode falhar se a configuração de segurança reforçada do Internet Explorer estiver habilitada. A solução alternativa é desabilitar essa configuração, registre-se o proxy e, em seguida, habilitá-la novamente.  

     * Modo de autenticação de código de dispositivo:

        ```PowerShell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Você deve concluir a autenticação, seguindo as instruções exibidas em um dispositivo diferente.

     * Modo de autenticação silenciosa (baseada em senha):
        ```PowerShell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Esse modo falhará se a autenticação multifator do Azure é necessária. Nesse caso, use um dos modos de autenticação de dois anterior.

       Esses exemplos êxito apenas se o usuário conectado no momento também seja um administrador de domínio do Active Directory para o domínio raiz. Se isso não for o caso, você pode fornecer as credenciais de domínio alternativo por meio de *- ForestCredential* parâmetro.

   > [!NOTE]
   > Se vários servidores proxy estiverem instalados em seu ambiente, não importa qual servidor de proxy que você usa para registrar a floresta.

   > [!TIP]
   > Pode haver um atraso considerável antes da conclusão na primeira vez que esse cmdlet é executado para um locatário do Azure específico. A menos que uma falha será relatada, não se preocupe com esse atraso.

   Registro da floresta do Active Directory é necessário apenas uma vez no tempo de vida da floresta. Depois disso, os agentes do controlador de domínio na floresta executará automaticamente qualquer outra manutenção necessária. Depois de `Register-AzureADPasswordProtectionForest` é executado com êxito para uma floresta, as invocações adicionais do cmdlet bem-sucedida, mas são desnecessárias.

   Para `Register-AzureADPasswordProtectionForest` para ter êxito, pelo menos um controlador de domínio executando o Windows Server 2012 ou posterior deve estar disponível no domínio do servidor proxy. Mas o software do agente de controlador de domínio não precisa ser instalado em controladores de domínio antes desta etapa.

1. Configure o serviço de proxy para a proteção de senha para se comunicar por meio de um proxy HTTP.

   Se seu ambiente exigir o uso de um proxy específico de HTTP para se comunicar com o Azure, use este método: Criar uma *AzureADPasswordProtectionProxy.exe.config* arquivo na pasta %ProgramFiles%\Azure AD Proxy\Service de proteção de senha. Incluem o seguinte conteúdo:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Se seu proxy HTTP requer autenticação, adicione a *useDefaultCredentials* marca:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Em ambos os casos, substitua `http://yourhttpproxy.com:8080` com o endereço e porta do servidor proxy HTTP específico.

   Se seu proxy HTTP está configurado para nós uma política de autorização, você deve conceder acesso à conta de computador do Active Directory do computador que hospeda o serviço de proxy para a proteção por senha.

   É recomendável que você para e reiniciar o serviço de proxy depois de criar ou atualizar o *AzureADPasswordProtectionProxy.exe.config* arquivo.

   O serviço de proxy não oferece suporte para o uso de credenciais específicas para se conectar a um proxy HTTP.

1. Opcional: Configure o serviço de proxy para a proteção de senha escutar em uma porta específica.
   * O software do agente de controlador de domínio para a proteção de senha nos controladores de domínio usa o RPC sobre TCP para se comunicar com o serviço de proxy. Por padrão, o serviço de proxy escuta em qualquer ponto de extremidade dinâmico de RPC disponível. Mas você pode configurar o serviço para escutar em uma porta TCP específica, se isso for necessário devido a requisitos de firewall em seu ambiente ou a topologia de rede.
      * <a id="static" /></a>Para configurar o serviço para ser executado em uma porta estática, use o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Você deve parar e reiniciar o serviço para que essas alterações entrem em vigor.

      * Para configurar o serviço para ser executado em uma porta dinâmica, use o mesmo procedimento, mas defina *StaticPort* como zero novamente:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Você deve parar e reiniciar o serviço para que essas alterações entrem em vigor.

   > [!NOTE]
   > O serviço de proxy para a proteção de senha requer uma reinicialização manual após qualquer alteração na configuração de porta. Mas você não precisa reiniciar o software do serviço de agente de controlador de domínio em controladores de domínio depois de fazer essas alterações de configuração.

   * Para consultar a configuração atual do serviço, use o `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>Instalar o serviço de agente de controlador de domínio

   Instalar o serviço de agente de controlador de domínio para a proteção por senha usando o `AzureADPasswordProtectionDCAgentSetup.msi` pacote.

   A instalação de software ou a desinstalação, requer uma reinicialização. Isso ocorre porque o filtro de senha DLLs só são carregadas ou descarregadas por uma reinicialização.

   Você pode instalar o serviço do agente de controlador de domínio em um computador que ainda não é um controlador de domínio. Nesse caso, o serviço iniciar e executar mas permanecem inativo até que a máquina será promovida para um controlador de domínio.

   Você pode automatizar a instalação do software usando os procedimentos padrão do MSI. Por exemplo: 

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > O comando de exemplo msiexec aqui faz com que uma reinicialização imediata. Para evitar isso, use o `/norestart` sinalizador.

A instalação é concluída depois que o software do agente de controlador de domínio é instalado em um controlador de domínio, e esse computador seja reinicializado. Nenhuma outra configuração é necessária ou possível.

## <a name="multiple-forest-deployments"></a>Múltiplas implantações florestais

Não há requisitos adicionais para implantar a proteção de senha do Azure AD em várias florestas. Cada floresta independentemente está configurada conforme descrito na seção "implantação de floresta única". Cada proxy de proteção de senha só pode oferecer suporte a controladores de domínio da floresta de que ele está associado a. O software de proteção de senha em qualquer floresta fica ciente dos software de proteção de senha que é implantado em outras florestas, independentemente das configurações de confiança do Active Directory.

## <a name="read-only-domain-controllers"></a>Controladores de domínio somente leitura

Senha/conjuntos de alterações não são processados e persistentes em controladores de domínio somente leitura (RODCs). Eles são encaminhados para controladores de domínio graváveis. Portanto, você não precisa instalar o software do agente de controlador de domínio em RODCs.

## <a name="high-availability"></a>Alta disponibilidade

A preocupação principal de disponibilidade para proteção por senha é a disponibilidade de servidores proxy quando os controladores de domínio em uma floresta tentam baixar novas políticas ou outros dados do Azure. Cada agente de controlador de domínio usa um algoritmo de round-robin-style simples ao decidir qual servidor proxy para chamar. O agente ignora os servidores de proxy que não estão respondendo. Para implantações do Active Directory mais totalmente conectadas com replicação íntegra do estado da pasta de diretório e o sysvol, dois servidores de proxy é o suficiente para garantir a disponibilidade. Isso resulta em download em tempo hábil de novas políticas e outros dados. Mas você pode implantar servidores proxy adicionais.

O design do software agente DC minimiza os problemas comuns que estão associados com alta disponibilidade. O agente do DC mantém um cache local da política de senha baixado mais recentemente. Mesmo se todos os registrados servidores proxy se tornar indisponíveis, os agentes de controlador de domínio continuam a impor sua política de senha armazenada em cache. Uma frequência de atualização razoável para políticas de senha em uma grande implantação costuma *dias*, não em horas ou menos. Assim, interrupções breves dos servidores proxy não afetam significativamente a proteção por senha do Azure AD.

## <a name="next-steps"></a>Próximas etapas

Agora que você instalou os serviços que você precisa para a proteção de senha do AD do Azure em seus servidores locais, [executar configuração pós-instalação e reunir informações de relatório](howto-password-ban-bad-on-premises-operations.md) para concluir a implantação.

[ Visão geral conceitual da proteção por senha do Azure AD ](concept-password-ban-bad-on-premises.md)
