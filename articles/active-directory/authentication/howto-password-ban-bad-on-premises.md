---
title: Implantar a visualização da proteção de senha do Azure AD
description: Implantar a visualização de proteção de senha do Azure AD para banir senhas incorretas no local
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 5065399f161bcaee2f9518236a28f0f5faa0ea5b
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902020"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>Visualizar: implantar a proteção de senha do Azure AD

|     |
| --- |
| A proteção de senha do Azure AD e a lista de senha proibida personalizada são recursos de visualização pública do Azure Active Directory. Para obter mais informações sobre visualizações, consulte [Termos de Uso Suplementares para as Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Agora que temos um entendimento de [como aplicar a proteção por senha do Azure AD ao Windows Server Active Directory](concept-password-ban-bad-on-premises.md), a próxima etapa é planejar e executar a implantação.

## <a name="deployment-strategy"></a>Estratégia de implantação

A Microsoft sugere que qualquer implantação seja iniciada no modo de auditoria. O modo de auditoria é a configuração inicial padrão em que as senhas podem continuar a ser definidas e qualquer uma que seja bloqueada cria entradas no log de eventos. Depois que os servidores proxy e os agentes DC forem totalmente implantados no modo de auditoria, o monitoramento regular deverá ser feito para determinar o impacto que a imposição da política de senha teria sobre os usuários e o ambiente se a política fosse aplicada.

Durante o estágio de auditoria, muitas organizações descobrem:

* Eles precisam melhorar os processos operacionais existentes para usar senhas mais seguras.
* Os usuários estão acostumados a escolher regularmente senhas não seguras
* Eles precisam informar os usuários sobre a próxima mudança na aplicação da segurança, o impacto que isso pode causar sobre eles e ajudá-los a entender melhor como podem escolher senhas mais seguras.

Depois que o recurso estiver em execução no modo de auditoria por um tempo razoável, a configuração de imposição pode ser invertida de **Auditoria** para **Impor**, exigindo senhas mais seguras. O monitoramento focalizado durante esse período é uma boa ideia.

## <a name="known-limitation"></a>Limitações conhecidas

Há uma limitação conhecida na versão de visualização do proxy de proteção de senha do Azure AD. O uso de contas de administrador de inquilinos que exigem MFA não é suportado. Uma atualização futura do proxy de proteção de senha do Azure AD suportará o registro de proxy com contas de administrador que exigem o MFA.

## <a name="single-forest-deployment"></a>Implantação florestal única

A visualização da proteção de senha do Azure AD é implantada com o serviço de proxy em até dois servidores e o serviço do agente DC pode ser implantado de forma incremental em todos os controladores de domínio na floresta do Active Directory.

![Como os componentes de proteção de senha do Azure AD trabalham juntos](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>Baixe o software

Há dois instaladores necessários para a proteção de senha do Azure AD que podem ser baixados do [ centro de downloads da Microsoft ](https://www.microsoft.com/download/details.aspx?id=57071)

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>Instalar e configurar o serviço proxy de proteção por senha do Azure AD

1. Escolha um ou mais servidores para hospedar o serviço proxy de proteção por senha do Azure AD.
   * Cada um desses serviços pode fornecer apenas diretivas de senha para uma única floresta, e a máquina do host deve estar associada ao domínio a um domínio (raiz e filho são igualmente suportados) nessa floresta. Para que o serviço de proxy de proteção de senha do Azure AD cumpra sua missão, deve haver conectividade de rede entre pelo menos um DC em cada domínio da floresta e a máquina host do Proxy de proteção de senha do Azure AD.
   * É suportado para instalar e executar o serviço proxy de proteção por senha do Azure AD em um controlador de domínio para fins de teste, mas requer conectividade com a Internet.

   > [!NOTE]
   > A visualização pública suporta no máximo dois (2) servidores proxy por floresta.

2. Instale o software do Serviço de Proxy da Diretiva de Senha usando o pacote MSI AzureADPasswordProtectionProxy.msi.
   * A instalação do software não requer uma reinicialização. A instalação do software pode ser automatizada usando procedimentos MSI padrão, por exemplo: `msiexec.exe /i AzureADPasswordProtectionProxy.msi /quiet /qn`

3. Abra uma janela do PowerShell como administrador.
   * O software de proxy de proteção de senha do Azure AD inclui um novo módulo do PowerShell chamado AzureADPasswordProtection. As etapas a seguir se baseiam na execução de vários cmdlets desse módulo do PowerShell e presumem que você tenha aberto uma nova janela do PowerShell e importado o novo módulo da seguinte maneira:
      * `Import-Module AzureADPasswordProtection`

      > [!NOTE]
      > O software de instalação modifica a variável de ambiente PSModulePath da máquina host. Para que essa alteração tenha efeito para que o módulo do powershell do AzureADPasswordProtection possa ser importado e usado, talvez seja necessário abrir uma nova janela do console do PowerShell.

   * Verifique se o serviço está sendo executado usando o seguinte comando do PowerShell: `Get-Service AzureADPasswordProtectionProxy | fl`.
      * O resultado deve produzir um resultado com o **Status** retornando um resultado "Running".

4. Registre o proxy.
   * Depois que a etapa 3 for concluída, o serviço proxy de proteção por senha do Azure AD será executado na máquina, mas ainda não terá as credenciais necessárias para se comunicar com o Azure AD. O registro no Azure AD é necessário para habilitar essa habilidade usando o cmdlet `Register-AzureADPasswordProtectionProxy` do PowerShell. O cmdlet exige credenciais globais de administrador para o locatário do Azure, bem como privilégios de administrador de domínio do Active Directory no local no domínio raiz da floresta. Depois de ter obtido êxito para um determinado serviço de proxy, as chamadas adicionais de `Register-AzureADPasswordProtectionProxy` continuam sendo bem-sucedidas, mas são desnecessárias.
      * O cmdlet pode ser executado da seguinte maneira:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionProxy -AzureCredential $tenantAdminCreds
         ```

         O exemplo só funciona se o usuário conectado no momento também for um administrador de domínio do Active Directory para o domínio raiz. Uma alternativa é fornecer as credenciais de domínio necessárias por meio do parâmetro `-ForestCredential`.

   > [!TIP]
   > Pode haver um atraso considerável (muitos segundos) na primeira vez que esse cmdlet for executado para um determinado locatário do Azure antes que o cmdlet conclua a execução. A menos que uma falha seja relatada, esse atraso não deve ser considerado alarmante.

   > [!NOTE]
   > Espera-se que o registro do serviço proxy de proteção por senha do Azure AD seja uma etapa única durante a vida útil do serviço. O serviço de proxy executará automaticamente qualquer outra manutenção necessária a partir deste ponto. Depois de ter sido bem-sucedida para uma determinada floresta, invocações adicionais de 'Register-AzureADPasswordProtectionProxy' continuam sendo bem-sucedidas, mas são desnecessárias.

5. Registre a floresta.
   * A floresta do Active Directory local deve ser inicializada com as credenciais necessárias para se comunicar com o Azure usando o cmdlet `Register-AzureADPasswordProtectionForest` Powershell. O cmdlet exige credenciais globais de administrador para o locatário do Azure, bem como privilégios de administrador de domínio do Active Directory no local no domínio raiz da floresta. Esta etapa é executada uma vez por floresta.
      * O cmdlet pode ser executado da seguinte maneira:

         ```
         $tenantAdminCreds = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $tenantAdminCreds
         ```

         O exemplo só funciona se o usuário conectado no momento também for um administrador de domínio do Active Directory para o domínio raiz. Uma alternativa é fornecer as credenciais de domínio necessárias por meio do parâmetro -ForestCredential.

         > [!NOTE]
         > Quando vários servidores proxy estão instalados no ambiente, não importa qual servidor proxy é especificado no procedimento acima.

         > [!TIP]
         > Pode haver um atraso considerável (muitos segundos) na primeira vez que esse cmdlet for executado para um determinado locatário do Azure antes que o cmdlet conclua a execução. A menos que uma falha seja relatada, esse atraso não deve ser considerado alarmante.

   > [!NOTE]
   > O registro da floresta do Active Directory deve ser uma etapa única durante a vida útil da floresta. Os agentes do controlador de domínio em execução na floresta executarão automaticamente qualquer outra manutenção necessária a partir deste ponto. Depois de ter sido bem-sucedida para uma determinada floresta, invocações adicionais de `Register-AzureADPasswordProtectionForest` continuam sendo bem-sucedidas, mas são desnecessárias.

6. Opcional: Configure o serviço proxy de proteção por senha do Azure AD para escutar em uma porta específica.
   * O RPC sobre TCP é usado pelo software DC Agent de proteção por senha do AD do Azure nos controladores de domínio para se comunicar com o serviço proxy de proteção por senha do Azure AD. Por padrão, o serviço Proxy de diretiva de senha de proteção de senha do Azure AD escuta em qualquer ponto de extremidade RPC dinâmico disponível. Se necessário, devido a topologia de rede ou requisitos de firewall, o serviço pode ser configurado para escutar em uma porta TCP específica.
      * Para configurar o serviço para ser executado em uma porta estática, use o cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`.
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Você deve parar e reiniciar o serviço para que essas alterações entrem em vigor.

      * Para configurar o serviço para ser executado em uma porta dinâmica, use o mesmo procedimento, mas configure o StaticPort de volta para zero, da seguinte forma:
         ```
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Você deve parar e reiniciar o serviço para que essas alterações entrem em vigor.

   > [!NOTE]
   > O serviço proxy de proteção por senha do Azure AD requer uma reinicialização manual após qualquer alteração na configuração da porta. Não é necessário reiniciar o software de serviço do agente DC em execução nos controladores de domínio depois de fazer alterações de configuração dessa natureza.

   * A configuração atual do serviço pode ser consultada usando o cmdlet `Get-AzureADPasswordProtectionProxyConfiguration`, como mostra o exemplo a seguir:

      ```
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0 
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>Instalar o serviço do agente DC de proteção de senha do Azure AD

* Instale o software de serviço do agente DC de proteção de senha do Azure AD usando o pacote `AzureADPasswordProtectionDCAgent.msi` MSI:
   * A instalação do software requer uma reinicialização na instalação e desinstalação devido ao requisito do sistema operacional de que as dlls do filtro de senha sejam carregadas ou descarregadas somente após a reinicialização.
   * É suportado para instalar o serviço do agente DC em uma máquina que ainda não seja um controlador de domínio. Nesse caso, o serviço será iniciado e executado, mas ficará inativo até que a máquina seja promovida a controlador de domínio.

   A instalação do software pode ser automatizada usando procedimentos MSI padrão, por exemplo: `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > O comando msiexec de exemplo resultará em uma reinicialização imediata; isso pode ser evitado especificando o sinalizador `/norestart`.

Depois de instalado em um controlador de domínio e reinicializado, a instalação do software do agente DC de proteção de senha do Azure AD é concluída. Nenhuma outra configuração é necessária ou possível.

## <a name="multiple-forest-deployments"></a>Múltiplas implantações florestais

Não há requisitos adicionais para implantar a proteção de senha do Azure AD em várias florestas. Cada floresta é configurada independentemente conforme descrito na seção de implantação de floresta única. Cada proxy de proteção de senha do Azure AD só pode oferecer suporte a controladores de domínio da floresta à qual ingressou. O software de proteção de senha do Azure AD em uma determinada floresta não tem conhecimento do software de proteção de senha do Azure AD implantado em outra floresta, independentemente das configurações de confiança do Active Directory.

## <a name="read-only-domain-controllers"></a>Controladores de domínio somente leitura

Alterações de senha \ conjuntos nunca são processados e mantidos em controladores de domínio somente leitura (RODCs); em vez disso, eles são encaminhados para controladores de domínio graváveis. Portanto, não há necessidade de instalar o software do agente DC nos RODCs.

## <a name="high-availability"></a>Alta disponibilidade

A principal preocupação em garantir a alta disponibilidade da proteção por senha do Azure AD é a disponibilidade dos servidores proxy, quando os controladores de domínio de uma floresta estão tentando baixar novas políticas ou outros dados do Azure. A visualização pública suporta no máximo dois servidores proxy por floresta. Cada agente DC usa um algoritmo de estilo round-robin simples ao decidir qual servidor proxy chamar e ignora os servidores proxy que não estão respondendo.
Os problemas habituais associados à alta disponibilidade são mitigados pelo design do software do agente DC. O agente DC mantém um cache local da política de senha baixada mais recentemente. Mesmo se todos os servidores proxy registrados se tornarem indisponíveis por qualquer motivo, o (s) agente (s) DC continuará aplicando sua política de senha em cache. Uma frequência de atualização razoável para políticas de senha em uma implantação grande geralmente é da ordem de dias, não de horas ou menos.   Portanto, breves interrupções dos servidores proxy não causam impacto significativo na operação do recurso de proteção por senha do Azure AD ou de seus benefícios de segurança.

## <a name="next-steps"></a>Próximas etapas

Agora que você instalou os serviços necessários para a proteção de senha do Azure AD em seus servidores locais, conclua a [ configuração pós-instalação e reúna informações de relatório ](howto-password-ban-bad-on-premises-operations.md) para concluir sua implantação.

[ Visão geral conceitual da proteção por senha do Azure AD ](concept-password-ban-bad-on-premises.md)
