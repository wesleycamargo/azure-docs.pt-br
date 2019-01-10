---
title: 'Azure AD Connect: Migrar da federação para a autenticação de passagem do Azure AD | Microsoft Docs'
description: Informações sobre como migrar seu ambiente de identidade híbrida da federação para a autenticação de passagem.
services: active-directory
author: billmath
manager: mtillman
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/13/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: c7d236769d5e9adca0402affc2d0eccdf78a6837
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107745"
---
# <a name="migrate-from-federation-to-pass-through-authentication-for-azure-ad"></a>Migrar da federação para a autenticação de passagem do Azure AD
O documento a seguir fornece orientação sobre como mudar dos Serviços de Federação do Active Directory (AD FS) para a autenticação de passagem.

>[!NOTE]
>Uma cópia deste documento está disponível para download [aqui](https://aka.ms/ADFSTOPTADPDownload).

## <a name="prerequisites-for-pass-through-authentication"></a>Verificando os pré-requisitos da autenticação de passagem
Antes que você possa migrar, são necessários os pré-requisitos a seguir.
### <a name="update-azure-ad-connect"></a>Atualizar o Azure AD Connect

Para executar com êxito as etapas para migrar para a autenticação de passagem, você precisa ter pelo menos o [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Esta versão contém alterações significativas na maneira como a entrada de conversão é executada, além de reduzir de potencialmente horas para minutos o tempo geral para migração da federação para a autenticação de nuvem.

> [!IMPORTANT]
> Blogs, ferramentas e documentação desatualizados indicam que a conversão do usuário é uma etapa necessária ao converter domínios de federados para gerenciados. A **conversão de usuários** não é mais necessária e a Microsoft está trabalhando na atualização da documentação e das ferramentas para refletir isso.

Para atualizar o Azure AD Connect para a versão mais recente, siga estas [instruções de atualização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="plan-authentication-agent-number-and-placement"></a>Planejar o posicionamento e o número de agentes de autenticação

A autenticação de passagem é feita pela implantação de agentes leves no servidor do Azure AD Connect e nos Windows Servers locais. Instale os agentes tão próximos dos seus controladores de domínio do Active Directory quanto for possível, a fim de reduzir a latência.

Para autenticação da maioria dos clientes, dois ou três agentes são suficientes para alta disponibilidade e capacidade, sendo que um locatário não pode ter mais de 12 agentes registrados. O primeiro agente é sempre instalado no servidor do AAD Connect propriamente dito. Consulte as [informações sobre estimativas de tráfego de rede e diretrizes de desempenho](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-current-limitations) para entender as limitações de agente e as opções de implantação do agente.

### <a name="plan-migration-method"></a>Planejar o método de migração

Há dois métodos para migrar de autenticação federada para PTA e SSO contínuo. O método usado dependerá de como o AD FS foi originalmente configurado.

- **Usando o Azure AD Connect**. Se o AD FS foi originalmente configurado usando o Azure AD Connect, a alteração para a autenticação de passagem *precisa* executada por meio do assistente do Azure AD Connect.

Ao usar o Azure AD Connect, ele executa o cmdlet Set-MsolDomainAuthentication para você automaticamente quando você altera o método de entrada do usuário e, portanto, você não tem controle sobre o cancelamento da federação de todos os domínios federados verificados no locatário do Azure AD que é realizado por ele.  
‎  
> [!NOTE]
> Neste momento, não é possível evitar o cancelamento da federação de todos os domínios em seu locatário quando você altera a entrada do usuário para autenticação de passagem quando o AAD Connect foi usado originalmente para configurar o AD FS para você.  
‎
- **Usando o Azure AD Connect com o PowerShell**. Esse método pode ser usado somente quando o AD FS não foi originalmente configurado com o Azure AD Connect. Você ainda precisará alterar o método de entrada do usuário por meio do Assistente do Azure AD Connect, mas a principal diferença é que ele não executa automaticamente o cmdlet Set-MsolDomainAuthentication para você, pois ele não tem reconhecimento do seu farm do AD FS e, portanto, você tem controle total sobre quais domínios são convertidos e em qual ordem.

Para entender qual método você deve usar, execute as etapas na seção a seguir.

#### <a name="verify-current-user-sign-in-settings"></a>Verificar as configurações atuais de entrada do usuário

Verifique suas configurações atuais de entrada usuário fazendo logon no portal do Azure AD [https://aad.portal.azure.com](https://aad.portal.azure.com/) com uma conta de Administrador Global. 

Na seção **Entrada do Usuário**, verifique se a **federação** está **habilitada** e se o **logon único contínuo** e a **autenticação de passagem** estão **desabilitados**. 

![Figura 5](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image1.png)

#### <a name="verify-how-federation-was-configured"></a>Verificar como a federação foi configurada

   1. Vá para o servidor do Azure AD Connect, inicie o Azure AD Connect e selecione **Configurar**.
   2. Na tela **Tarefas Adicionais**, selecione **Exibir a Configuração Atual** e, em seguida, selecione **Avançar**.</br>
   ![Figura 31](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image2.png)</br>
   3. Na tela **Examinar sua Solução**, role para baixo até os **Serviços de Federação do Active Directory (AD FS)**.</br>
   Se você vê que a configuração do AD FS está nesta seção, você pode presumir com segurança do AD FS foi originalmente configurado por meio do Azure AD Connect e, portanto, a conversão de seus domínios de federados para gerenciados pode ser orientada por meio da opção **Alterar a entrada do usuário** do Azure AD Connect. Esse processo é detalhado na seção **Opção 1: configurar a autenticação de passagem usando o Azure AD Connect**.  
‎
   4. Se você não conseguir ver os Serviços de Federação do Active Directory (AD FS) listados nas configurações atuais, você precisará, por meio do PowerShell, converter manualmente os domínios de federados para gerenciados. Isso é detalhado na seção **Opção 2 – mudar de federação para PTA usando o Azure AD Connect e o PowerShell**.

### <a name="document-current-federation-settings"></a>Configurações de federação atuais do documento

Você pode encontrar a configuração atual de federação usando o cmdlet Get-MsolDomainFederationSettings.

O comando é:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Por exemplo: 

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```


Valide quaisquer configurações que talvez tenham sido personalizadas para a documentação de design e implantação de federação, especificamente **PreferredAuthenticationProtocol**, **SupportsMfa** e **PromptLoginBehavior**.

Mais informações sobre o que essas configurações fazem podem ser encontradas abaixo.

[Suporte ao parâmetro prompt=login dos Serviços de Federação do Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Se o valor de SupportsMfa atualmente é definido como "True", isso significa que você está usando uma solução MFA local para injetar um desafio de segundo fator no fluxo de autenticação do usuário. Isso não funcionará mais para cenários de autenticação do Azure AD e, em vez disso, você terá que utilizar o serviço do MFA do Azure (baseado em nuvem) para executar a mesma função. Avalie cuidadosamente os requisitos de MFA antes de prosseguir e verifique se você entende como aproveitar o MFA do Azure, as implicações de licenciamento e o processo de registro do usuário final antes de converter seus domínios.

#### <a name="backup-federation-settings"></a>Configurações de federação de backup

Embora nenhuma alteração será feita a outras terceiras partes confiáveis no seu farm do AD FS durante esse processo, é recomendável garantir que você tenha um backup válido do farm do AD FS que possa ser restaurado. Você pode fazer isso usando a [Ferramenta de Restauração Rápida do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) gratuita da Microsoft. Essa ferramenta pode ser usada para fazer backup do AD FS e restaurá-lo, seja para um farm existente ou para um novo farm.

Se você optar por não usar a Ferramenta de Restauração Rápida do AD FS, deverá no mínimo exportar o objeto de confiança de terceira parte confiável da "Plataforma de Identidade do Microsoft Office 365" e quaisquer regras de declaração personalizadas associadas que você possa ter adicionado. Você pode fazer isso por meio do exemplo do PowerShell a seguir

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-ad-fs-usage"></a>Considerações de implantação e uso do AD FS

### <a name="validate-your-current-ad-fs-usage"></a>Validar seu uso atual do AD FS

Antes de converter de federado para gerenciado, você deve examinar detalhadamente o modo como está usando o AD FS hoje para o Azure AD/Office 365 e outros aplicativos (objetos de confiança de terceira parte confiável). Você também deve considerar a seguinte tabela:

| Se| Então |
|-|-|
| Você manterá o AD FS para esses outros aplicativos.| Você usará o AD FS e o Azure AD e precisará considerar a experiência do usuário final como um resultado. Os usuários podem precisar se autenticar duas vezes em alguns cenários, uma vez para o Azure AD (em que obterão o SSO daí em diante para outros aplicativos como o Office 365) e novamente para quaisquer aplicativos ainda associados ao AD FS como um objeto de confiança de terceira parte confiável. |
| O AD FS é altamente personalizado e dependente de configurações de personalização específicos no arquivo onload.js, que não pode ser duplicado no Azure AD (por exemplo, você alterou a experiência de entrada para que os usuários apenas insiram um formato de SamAccountName para seu nome de usuário em vez de para um UPN ou então tenham uma experiência de logon com identidade visual altamente personalizada)| Você precisará verificar se seus requisitos de personalização atual podem ser atendidos pelo Azure AD antes de continuar. Consulte as seções de Identidade Visual do AD FS e Personalização do AD FS para obter mais informações e diretrizes.|
| Você está bloqueando os clientes de autenticação herdada por meio do AD FS.| Considere substituir os controles para bloqueio de clientes de autenticação herdada atualmente presentes no AD FS com uma combinação de [controles de acesso condicional para autenticação herdada](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) e [regras de acesso do cliente do Exchange Online](https://aka.ms/EXOCAR). |
| Você exige que os usuários executem a MFA em uma solução de servidor MFA local ao autenticar-se no AD FS.| Você não conseguirá injetar um desafio de MFA por meio da solução MFA local no fluxo de autenticação de um domínio gerenciado. No entanto, você pode usar o serviço MFA do Azure para fazê-lo, avançando uma vez que o domínio seja convertido. Se os usuários não estiverem usando o MFA do Azure hoje, isso envolverá uma etapa de registro de uso único do usuário final para a qual você precisará se preparar e que precisará comunicar aos seus usuários finais. |
| Você usa políticas de controle de acesso (regras AuthZ) atualmente no AD FS para controlar o acesso ao Office 365.| Considere substituir esses recursos com as [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) e [regras de acesso do cliente do Exchange Online](https://aka.ms/EXOCAR) equivalentes do Azure AD.|

### <a name="considerations-for-common-ad-fs-customizations"></a>Considerações para personalizações comuns do AD FS

#### <a name="inside-corporate-network-claim"></a>Dentro da declaração de rede corporativa

A declaração InsideCorporateNetwork é emitida pelo AD FS se o usuário realizando a autenticação está dentro da rede corporativa. Essa declaração pode ser então passada para o Azure AD e usada para ignorar a autenticação multifator com base no local de rede dos usuários. Veja [IPs confiáveis para usuários federados](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) para obter informações sobre como determinar se você está com esse recurso atualmente habilitado no AD FS.

A declaração InsideCorporateNetwork não estará mais disponível depois que seus domínios forem convertidos para autenticação de passagem. As [localizações nomeadas no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) podem ser usadas para substituir esta funcionalidade.

Uma vez configurados os locais de chamada, todas as políticas de acesso condicional configuradas para incluir ou excluir os locais de rede "Todos os locais confiáveis" ou "IPs confiáveis de MFA" devem ser atualizados para refletir os locais nomeados recém-criados.

Para obter mais informações sobre a condição de local do acesso condicional, veja [Locais de acesso condicional do Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos ingressados no Azure AD híbrido

Ingressar em um dispositivo no Azure AD permite que você crie regras de acesso condicional que obrigam os dispositivos a atenderem aos seus padrões de acesso de segurança e conformidade e permite que os usuários se conectem a um dispositivo usando uma conta corporativa ou de estudante, em vez de uma conta pessoal. Os dispositivos ingressados no Azure AD híbrido permitem que seus dispositivos ingressados no domínio do AD sejam ingressados Azure AD. Seu ambiente federado pode ter sido configurado com esse recurso.

Para garantir que o ingresso híbrido continue a funcionar para quaisquer novos dispositivos ingressados no domínio depois que seus domínios foram convertidos para autenticação de passagem, o Azure AD Connect deve ser configurado para sincronizar contas de computador do Active Directory para clientes Windows 10 para o Azure AD. Para contas de computador do Windows 7 e Windows 8, o ingresso híbrido usará o SSO contínuo para registrar o computador no Azure AD e você não precisará sincronizá-los como faria para dispositivos do Windows 10. No entanto, você precisará implantar um arquivo workplacejoin.exe atualizado (por meio de um .msi) para esses clientes de nível inferior para que eles possam registrar a si mesmos usando o SSO contínuo. [Baixar o .msi](https://www.microsoft.com/download/details.aspx?id=53554).

Para obter mais informações sobre essa solicitação, consulte [Como configurar dispositivos híbridos unidos do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)

#### <a name="branding"></a>Identidade visual

Sua organização pode ter [personalizado suas páginas de entrada do ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes para a organização. Nesse caso, considere a possibilidade de fazer [personalizações de página de entrada do Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding) semelhantes.

Embora as personalizações semelhantes estejam disponíveis, algumas alterações visuais devem ser esperadas. Talvez você queira incluir as alterações esperadas em suas comunicações aos usuários finais.

> [!NOTE]
> A identidade visual da empresa estará disponível somente se você comprar a licença Premium ou Básica do Azure AD ou se tiver uma licença do Office 365.

## <a name="planning-for-smart-lockout"></a>Planejamento de bloqueio inteligente

O bloqueio inteligente do Azure AD protege contra ataques de senha de força bruta e impede que a conta do Active Directory local seja bloqueada quando a autenticação de passagem está sendo usada e uma política de grupo de bloqueio de conta é definida no Active Directory. 

Para obter mais informações, veja [Recurso de bloqueio inteligente e como editar sua configuração](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-smart-lockout).

## <a name="planning-deployment-and-support"></a>Planejando a implantação e o suporte

### <a name="plan-the-maintenance-window"></a>Planejar a janela de manutenção

Enquanto o processo de conversão de domínio em si é relativamente rápido, o Azure AD pode ainda enviar algumas solicitações de autenticação para servidores do AD FS por um período de até quatro horas após a conclusão da conversão de domínio. Durante essa janela de quatro horas e dependendo de vários caches do lado do serviço, essas autenticações podem não ser aceitas pelo Azure AD e os usuários receberão um erro, pois eles ainda serão capazes de autenticar com êxito no AD FS, mas o Azure AD deixará de aceitar um token emitido pelo usuário, já que esse objeto de confiança de federação foi removida.

> [!NOTE]
> Isso só afetará usuários que acessarem os serviços por meio de um navegador durante essa janela de conversão de post até o cache do lado do serviço ser limpo. Os clientes herdados (Exchange ActiveSync, Outlook 2010/2013) não devem ser afetados, pois o Exchange Online mantém um cache de suas credenciais por um período de tempo que é usado para autenticar o usuário novamente de modo silencioso sem a necessidade de voltar para o AD FS. Credenciais armazenadas no dispositivo para esses clientes são usadas para que eles se reautentiquem silenciosamente após esse cache ser limpo e, portanto, os usuários não devem receber nenhum prompt de senha como resultado do processo de conversão de domínio. Por outro lado, para clientes de autenticação moderna (Office 2013/2016, aplicativos IOS e Android), eles usam um token de atualização válido para obter novos tokens de acesso para acesso contínuo aos recursos em vez de ir para o AD FS e, portanto, são imunes a quaisquer prompts de senha como um resultado do processo de conversão de domínio e continuarão a funcionar sem nenhuma configuração adicional necessária.
> [!IMPORTANT]
> Não desligue seu ambiente do AD FS nem remova o objeto de confiança de terceira parte confiável do Office 365 até que você tenha verificado que todos os usuários estão autenticando com êxito usando a autenticação de nuvem.

### <a name="plan-for-rollback"></a>Planejar a reversão

Se um grande problema é encontrado e não pode ser resolvido rapidamente, você pode optar por reverter novamente a solução para federação. É importante planejar o que fazer se a implantação não funcionar conforme o planejado. Se a conversão do domínio ou dos usuários falhar durante a implantação ou se for preciso reverter a federação, você deverá compreender como atenuar qualquer eventual interrupção e reduzir o impacto para os usuários.

#### <a name="rolling-back"></a>Reverter

Consulte a documentação de design e implantação de federação para detalhes da sua implantação específica. O processo deve envolver:

* Converter domínios gerenciados em federados usando Convert-MSOLDomainToFederated 

* Se necessário, configurar regras de declarações adicionais.

### <a name="plan-change-communications"></a>Planejar comunicações de alteração

Uma parte importante do planejamento de implantação e suporte é garantir que os usuários finais sejam proativamente informados sobre as alterações e o que eles podem enfrentar ou devem fazer. 

Após a autenticação de passagem e o SSO contínuo serem implantados, a experiência de entrada do usuário final mudará ao acessar o Office 365 e outros recursos associados autenticados por meio do Azure AD. Os usuários externos à rede agora verão somente a página de entrada do Azure AD, em vez de serem redirecionados à página baseada em formulários apresentada pelos servidores proxy de aplicativo Web voltados para o exterior.

Há vários elementos para planejar sua estratégia de comunicação. Estão incluídos:

* Notificar os usuários de funcionalidades futuras e já lançadas por meio de
  * Email e outros canais de comunicação interna
  * Elementos visuais, tais como cartazes
  * Comunicações ao vivo ou de outro tipo
* Determinar quem personalizará e quem enviará as comunicações e quando isso será feito.

## <a name="implementing-your-solution"></a>Implementando sua solução

Agora que você planejou sua solução, você está pronto para implementá-la. A implementação inclui os seguintes componentes:

   1. Preparar para logon único contínuo
   2. Alterando o método de entrada para autenticação de passagem e habilitando o SSO contínuo

## <a name="step-1--prepare-for-seamless-sso"></a>Etapa 1 – preparar para o SSO contínuo

Para que todos os seus dispositivos usem o SSO contínuo, você precisa adicionar uma URL do Azure AD às configurações de zona da Intranet dos usuários usando a política de grupo no Active Directory.

Por padrão, o navegador calcula automaticamente a zona correta, Internet ou Intranet, de uma URL específica. Por exemplo, "http://contoso/" é mapeada para a Zona da Intranet, enquanto "http://intranet.contoso.com/" é mapeada para a Zona da Internet (porque a URL contém um ponto). Os navegadores não enviarão tickets Kerberos para um ponto de extremidade da nuvem, como a URL do Azure Active Directory, a menos que a URL seja explicitamente adicionada à zona da Intranet do navegador.

Siga as [etapas para distribuir](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) as alterações necessárias para seus dispositivos.

> [!IMPORTANT]
> Fazer essa alteração não modificará a maneira em que os usuários entram no Azure AD. No entanto, é importante que essa configuração seja aplicada a todos os seus dispositivos antes de continuar com a Etapa 3. Observe também que os usuários entrando nos dispositivos que não receberam essa configuração precisarão simplesmente inserir o nome de usuário e senha para entrar no Azure AD.

## <a name="step-2--change-sign-in-method-to-pta-and-enable-seamless-sso"></a>Etapa 2 – alterar o método de entrada para PTA e habilitar o SSO contínuo

### <a name="option-a-configuring-pta-by-using-azure-ad-connect"></a>Opção A: Configurar o PTA usando o Azure AD Connect

Use esse método quando o AD FS foi configurado inicialmente usando o Azure AD Connect. Você não pode usar esse método quando o AD FS não foi configurado inicialmente usando o Azure AD Connect.

> [!IMPORTANT]
> Lembre-se de que seguindo as etapas abaixo, todos os seus domínios serão convertidos de federados para gerenciados. Leia a seção Planejar o método de migração para obter mais informações.[](#_Plan_Migration_Method)

Primeiro, você precisa alterar o método de logon:

   1. No servidor do Azure AD Connect, abra o assistente.
   2. Selecione **Alterar a Entrada do Usuário** e, em seguida, selecione **Avançar**. 
   3. Na tela **Conectar ao Azure AD**, insira o nome de usuário e senha de um administrador global.
   4. Na tela **Entrada do Usuário**, altere o botão de opção de **Federação com o AD FS** para **Autenticação de Passagem**, selecione **Habilitar logon único** e, em seguida, selecione **Avançar**.
   5. Na tela Habilitar Logon Único, insira as credenciais da conta do administrador de domínio e selecione Avançar.  

   > [!NOTE]
   > As credenciais de administrador de domínio são necessárias para habilitar o logon único contínuo, já que o processo executa as ações a seguir, que exigem essas permissões elevadas. As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Elas estão usadas somente para habilitar o recurso e depois são descartadas após a conclusão bem-sucedida
   >
   > * Uma conta de computador denominada AZUREADSSOACC (que representa o Azure AD) é criada em seu AD (Active Directory) local.
   > * A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > * Além disso, os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.
   > * As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Elas estão usadas somente para habilitar o recurso e depois são descartadas após a conclusão bem-sucedida

   6. Na tela **Pronto para Configurar**, verifique se a caixa de seleção **Iniciar Processo de Sincronização quando a configuração for concluída** está selecionada. Em seguida, selecione **Configurar**.</br>
   ![figura](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image8.png)</br>
   7. Abra o **portal do Azure AD**, selecione **Azure Active Directory** e, em seguida, selecione o **Azure AD Connect**.
   8. Verifique se a **federação está desabilitada** e se o **logon único contínuo** e a **sincronização de senha** estão **habilitados**.</br>
   ![figura](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image9.png)</br>

Em seguida, você precisa implantar métodos de autenticação adicionais. Abra o **portal do Azure**, navegue até **Azure Active Directory, Azure AD Connect** e clique em **Autenticação de Passagem**.

Na página **Autenticação de passagem**, clique no botão de Download. Na tela **Baixar Agente**, clique em **Aceitar termos e baixar**.

O download dos agentes de autenticação adicionais será iniciado. Instale o agente de autenticação secundário em um servidor ingressado no domínio. 

> [!NOTE]
> O primeiro agente sempre é instalado no servidor do Azure AD Connect propriamente dito, como parte das alterações de configuração feitas na seção Entrada do Usuário da ferramenta do Azure AD Connect. Eventuais agentes de autenticação adicionais devem ser instalados em um servidor separado. É recomendável ter entre 2 e 3 agentes de autenticação adicionais disponíveis. 

Execute a instalação do agente de autenticação. Durante a instalação, você precisará fornecer as credenciais de uma conta de **administrador global**.

![Figura 1243067202](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image11.png)

![Figura 1243067210](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image12.png)

Depois de instalar o agente de autenticação, você pode voltar para a página Integridade do agente de autenticação de passagem para verificar o status dos agentes adicionais.


Vá para Testes e próximas etapas.

> [!IMPORTANT]
> Ignore a seção Opção B: mudar de federação para PTA usando o Azure AD Connect e o PowerShell, pois as etapas nessa seção não se aplicam.  

### <a name="option-b---switch-from-federation-to-pta-using-azure-ad-connect-and-powershell"></a>Opção B – mudar de federação para PTA usando o Azure AD Connect e o PowerShell

Use essa opção quando sua federação não foi configurada inicialmente por meio do Azure AD Connect. Primeiro você precisa habilitar a autenticação de passagem:

   1. No servidor do Azure AD Connect, abra o assistente.
   2. Selecione **Alterar a Entrada do Usuário** e, em seguida, selecione **Avançar**.
   3. Na tela **Conectar ao Azure AD**, insira o nome de usuário e senha de um administrador global.
   4. Na tela **Entrada do Usuário**, altere o botão de opção de **Não configurar** para **Autenticação de Passagem**, selecione **Habilitar logon único** e, em seguida, selecione **Avançar**.
   5. Na tela **Habilitar Logon Único**, insira as credenciais da conta do administrador de domínio e selecione **Avançar**.

   > [!NOTE]
   > As credenciais de administrador de domínio são necessárias para habilitar o logon único contínuo, já que o processo executa as ações a seguir, que exigem essas permissões elevadas. As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Elas estão usadas somente para habilitar o recurso e depois são descartadas após a conclusão bem-sucedida.
   >
   > * Uma conta de computador denominada AZUREADSSOACC (que representa o Azure AD) é criada em seu AD (Active Directory) local.
   > * A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > * Além disso, os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.

   6. Na tela **Pronto para Configurar**, verifique se a caixa de seleção **Iniciar Processo de Sincronização quando a configuração for concluída** está selecionada. Em seguida, selecione **Configurar**.</br>
   ‎![figura](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image18.png)</br>
   Ao selecionar Configurar, ocorrem as seguintes etapas:
   * O primeiro agente de autenticação de passagem é instalado
   * O recurso de passagem é habilitado
   * O logon único contínuo é habilitado.  
   7. Verifique se a **federação** ainda está **habilitada** e se o **logon único contínuo** e a **autenticação de passagem** agora estão habilitados.
   ![Figura 2](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image19.png)
   8. Selecione **Autenticação de Passagem** e verifique se o status é **Ativa**.</br>
   
   Se o agente de autenticação não estiver ativo, siga [estas etapas de solução de problemas](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication) antes de prosseguir com o processo de conversão de domínio na próxima etapa. Se converter seus domínios antes da validação de que seus agentes de PTA foram instalados com êxito e de que o status deles aparece como "Ativo" no portal do Azure, você correrá o risco de causar uma interrupção de autenticação.  
   9. Em seguida, implante agentes de autenticação adicionais. Abra o **portal do Azure**, navegue até **Azure Active Directory**, **Azure AD Connect** e clique em **Autenticação de Passagem**.
   10. Na página **Autenticação de passagem**, clique no botão de **Download**. Na tela **Baixar Agente**, clique em **Aceitar termos e baixar**.
   
   O download dos agentes de autenticação adicionais será iniciado. Instale o agente de autenticação secundário em um servidor ingressado no domínio.

  > [!NOTE]
  > O primeiro agente sempre é instalado no servidor do Azure AD Connect propriamente dito, como parte das alterações de configuração feitas na seção Entrada do Usuário da ferramenta do Azure AD Connect. Eventuais agentes de autenticação adicionais devem ser instalados em um servidor separado. É recomendável ter entre 2 e 3 agentes de autenticação adicionais disponíveis.
  
   11. Execute a instalação do agente de autenticação. Durante a instalação, você precisará fornecer as credenciais de uma conta de **administrador global**.</br>
   ![Figura 1243067215](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image23.png)</br>
   ![Figura 1243067216](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image24.png)</br>
   12. Depois de instalar o agente de autenticação, você pode voltar para a página Integridade do agente de autenticação de passagem para verificar o status dos agentes adicionais.

Neste ponto, a federação está ainda habilitada e operacional para seus domínios. Para continuar com a implantação, cada domínio precisa ser convertido de federado em gerenciado para que a autenticação de passagem comece a atender a solicitações de autenticação para o domínio.

Nem todos os domínios precisam ser convertidos ao mesmo tempo, você pode optar por começar com um domínio de teste em seu locatário de produção ou com o domínio com o menor número de usuários.

A conversão é executada usando o módulo do PowerShell do Azure AD.

   1. Abra o **PowerShell** e faça logon no Azure AD usando uma conta de **administrador global**.
   2. Para converter o primeiro domínio, execute o comando a seguir:
 
 ``` PowerShell
 Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
 ```
 
   3. Abra o **portal do Azure AD**, selecione **Azure Active Directory** e, em seguida, selecione o **Azure AD Connect**.  
   4. Depois de converter todos os seus domínios federados, verifique se a **federação está desabilitada** e se o **logon único contínuo** e a **sincronização de senha** estão **habilitados**.</br>
   ![figura](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image26.png)</br>

## <a name="testing-and-next-steps"></a>Testes e próximas etapas

### <a name="test-pass-through-authentication"></a>Testar a autenticação de passagem 

Quando seu locatário estava usando a federação, os usuários foram redirecionados da página de entrada do Azure AD para seu ambiente do AD FS. Agora que o locatário está configurado para usar autenticação de passagem em vez de federação, os usuários não são redirecionados para o AD FS e, em vez disso, se conectarão diretamente por meio da página de entrada do Azure AD.

Abra o Internet Explorer no modo InPrivate para evitar o que o SSO contínuo conecte você automaticamente, então vá para a página de logon do Office 365 ([https://portal.office.com](https://portal.office.com/)). Digite o **nome UPN** do usuário e clique em **Avançar**. Você deve digitar o nome UPN de um usuário híbrido que foi sincronizado do Active Directory local e que costumava ser federado. O usuário verá a tela para digitar seu nome de usuário e senha.

![Figura 18](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image27.png)

![Figura 19](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image28.png)

Depois de digitar a senha, você deverá ser redirecionado ao portal do Office 365.

![Figura 23](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image29.png)

### <a name="test-seamless-single-sign-on"></a>Testar logon único contínuo

   1. Faça logon em um computador ingressado no domínio que está conectado à rede corporativa. 
   2. Abra o **Internet Explorer** ou o **Chrome** e vá para uma das seguintes URLs:   
      ‎  
      [https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (substitua Contoso pelo seu domínio).

      O usuário será redirecionado rapidamente para a página de entrada do Azure AD e verá a mensagem "Tentando entrar". Não deverá ser solicitado que ele forneça nome de usuário ou senha.</br>
   ![Figura 24](media/plan-migrate-adfs-pass-through-authentication/migrating-adfs-to-pta_image30.png)</br>
   3. Em seguida, o usuário será redirecionado e conectado ao painel de acesso com êxito:

> [!NOTE]
> O logon único contínuo funciona em serviços do Office 365 que são compatíveis com a dica de domínio (por exemplo, myapps.microsoft.com/contoso.com). O portal do Office 365 (portal.office.com) atualmente não é compatível com dica de domínio e, portanto, espera-se que os usuários precisem digitar seus nomes UPN. Depois que um nome UPN for inserido, o logon único contínuo pode recuperar o tíquete do Kerberos em nome do usuário e conectar esse usuário sem digitar uma senha.
> [!TIP]
> Considere a possibilidade de implantar o [Ingresso híbrido no Azure AD no Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) para uma experiência de logon único aprimorada.

### <a name="removal-of-the-relying-party-trust"></a>Remoção do objeto de confiança de terceira parte confiável

Depois de ter validado que todos os usuários e clientes estão autenticando com êxito por meio do Azure AD, pode ser considerado seguro remover o objeto de confiança de terceira parte confiável do Office 365.

Se o AD FS não está sendo usado para outras finalidades (outros objetos de confiança de terceiras partes confiáveis foram configurados), é seguro encerrar o AD FS agora.

### <a name="rollback"></a>Reversão

Se um grande problema é encontrado e não pode ser resolvido rapidamente, você pode optar por reverter novamente a solução para federação.

Consulte a documentação de design e implantação de federação para detalhes da sua implantação específica. O processo deve envolver:

* Converter domínios gerenciados em federados usando Convert-MSOLDomainToFederated
* Se necessário, configurar regras de declarações adicionais.

### <a name="enable-synchronization-of-userprincipalname-updates"></a>Habilitar a sincronização de atualizações de userPrincipalName

Historicamente, atualizações do atributo UserPrincipalName usando o serviço de sincronização local são bloqueadas, a menos que estas duas condições sejam verdadeiras:

* O usuário é gerenciado (não federado).
* Não foi atribuída uma licença ao usuário.

Para obter instruções sobre como verificar ou habilitar esse recurso, consulte o artigo a seguir:

[Sincronizar atualizações de userPrincipalName](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features).

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Substituir a chave de descriptografia do Kerberos de SSO contínuo

É importante substituir frequentemente a chave de descriptografia do Kerberos da conta de computador AZUREADSSOACC (que representa o Azure AD) criada na sua floresta do AD local. É altamente recomendável que você substitua a chave de descriptografia do Kerberos pelo menos a cada 30 dias para alinhar com o modo como membros do domínio do Active Directory enviam as alterações de senha. Já que não há nenhum dispositivo associado anexado ao objeto de conta de computador AZUREADSSOACC, a substituição precisa ser executada manualmente.

Siga estas etapas no servidor do local em que você está executando o Azure AD Connect para iniciar a substituição da chave de descriptografia do Kerberos.

[Como posso substituir a chave de descriptografia de Kerberos da conta de computador AZUREADSSOACC](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="monitoring-and-logging"></a>Monitoramento e registro em log

Os servidores que executam os agentes de autenticação devem ser monitorados para manter a disponibilidade da solução. Além dos contadores de desempenho geral do servidor, os agentes de autenticação expõem objetos de desempenho que podem ser usados para entender os erros e estatísticas de autenticação.

Agentes de autenticação de operações de log para logs de eventos do Windows em "Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin".

Os logs de solução de problemas podem ser habilitados, se necessário.

Veja mais informações sobre [monitoramento e registro em log](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-Pass-through-authentication).

## <a name="next-steps"></a>Próximas etapas

- [Conceitos de design do Azure AD Connect](plan-connect-design-concepts.md)
- [Escolha a autenticação correta](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Topologias compatíveis](plan-connect-design-concepts.md)
