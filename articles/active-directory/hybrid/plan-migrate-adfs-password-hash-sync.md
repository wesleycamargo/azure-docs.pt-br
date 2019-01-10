---
title: 'Azure AD Connect: Migrar da federação para sincronização de hash de senha do Azure AD | Microsoft Docs'
description: Informações sobre como migrar seu ambiente de identidade híbrida da federação para a sincronização de hash de senha.
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
ms.openlocfilehash: a14e630c23af3e0228bf4806851f29cfab199215
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103971"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-ad"></a>Migrar da federação para a sincronização de hash de senha do Azure AD
O documento a seguir fornece orientação sobre como mudar dos Serviços de Federação do Active Directory (AD FS) para a sincronização de hash de senha.

>[!NOTE]
>Uma cópia deste documento está disponível para download [aqui](https://aka.ms/ADFSTOPHSDPDownload).


## <a name="prerequisites-for-the-migration"></a>Pré-requisitos para a migração 
Antes que você possa migrar, são necessários os pré-requisitos a seguir.
### <a name="update-azure-ad-connect"></a>Atualizar o Azure AD Connect

Para executar com êxito as etapas para migrar para a autenticação de passagem, você precisa ter pelo menos o [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0. Esta versão contém alterações significativas na maneira como a entrada de conversão é executada, além de reduzir de potencialmente horas para minutos o tempo geral para migração da federação para a autenticação de nuvem.

> [!IMPORTANT]
> Blogs, ferramentas e documentação desatualizados indicam que a conversão do usuário é uma etapa necessária ao converter domínios de federados para gerenciados. Observe que a conversão dos usuários não é mais necessária e a Microsoft está trabalhando na atualização da documentação e das ferramentas para refletir isso.

Para atualizar o Azure AD Connect para a versão mais recente, siga estas [instruções de atualização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Permissões necessárias para sincronização de hash de senha

O Azure AD Connect pode ser configurado usando configurações expressas ou a instalação personalizada. Se você tiver usado a opção de instalação personalizada, as [permissões necessárias](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) para sincronização de hash de senha poderão não estar em vigor.

A conta do serviço AD DS do Azure AD Connect precisa das permissões a seguir para ser capaz de sincronizar hashes de senha.

* Replicar alterações de diretório

* Replicar todas as alterações de diretório

Agora é um bom momento para validar que essas permissões estão em vigor para todos os domínios na floresta.

### <a name="plan-migration-method"></a>Planejar o método de migração

Há dois métodos para migrar de autenticação federada para sincronização de hash de senha e SSO contínuo. O método usado dependerá de como o AD FS foi originalmente configurado. 



- **Opção A: Usando o Azure AD Connect**. Se o AD FS foi originalmente configurado usando o Azure AD Connect, a alteração para a sincronização de hash de senha como o método de entrada do usuário deve ser executada por meio do Assistente do Azure AD Connect.   
Ao usar o Azure AD Connect, ele executa o cmdlet Set-MsolDomainAuthentication para você automaticamente quando você altera o método de entrada do usuário e, portanto, você não tem controle sobre o cancelamento da federação de todos os domínios federados verificados no locatário do Azure AD que é realizado por ele.

> [!NOTE]
> Neste momento, não é possível evitar o cancelamento da federação de todos os domínios em seu locatário quando você altera a entrada do usuário para sincronização de hash de senha quando o AAD Connect foi usado originalmente para configurar o AD FS para você.  



- **Opção B: Usando o Azure AD Connect com o PowerShell**. Esse método pode ser usado somente quando o AD FS não foi originalmente configurado com o Azure AD Connect. Você ainda precisará alterar o método de entrada do usuário por meio do Assistente do Azure AD Connect, mas a principal diferença é que ele não executa automaticamente o cmdlet Set-MsolDomainAuthentication para você, pois ele não tem reconhecimento do seu farm do AD FS e, portanto, você tem controle total sobre quais domínios são convertidos e em qual ordem.

Para entender qual método você deve usar, execute as etapas na seção a seguir.

#### <a name="verify-current-user-sign-in-settings"></a>Verificar as configurações atuais de entrada do usuário

Verifique suas configurações atuais de entrada usuário fazendo logon no portal do Azure AD [https://aad.portal.azure.com](https://aad.portal.azure.com/) com uma conta de Administrador Global.

Na seção Entrada do usuário, verifique se a federação está habilitada e se o logon único contínuo e a autenticação de passagem estão desabilitados. Também verifique o estado da sincronização de senha, que deve aparecer como desabilitado, a menos que isso tenha sido ativado anteriormente.

![Figura 5](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-azure-ad-connect-configuration"></a>Verificar a configuração do Azure AD Connect

   1. Vá para o servidor do Azure AD Connect, inicie o Azure AD Connect e selecione Configurar. 
   2. Na tela Tarefas Adicionais, selecione Exibir a Configuração Atual e, em seguida, selecione Avançar.</br>
   ![Figura 31](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)</br>
   
   3. Na tela Examinar sua Solução, observe o status da sincronização de senha.</br> 

   Se a sincronização de hash de senha está atualmente definida como desabilitada, você precisa seguir as etapas neste guia para habilitá-la. Se a sincronização de hash de senha está atualmente definida como habilitada, você pode ignorar a seção [Etapa 1 – habilitar a sincronização de hash de senha](#step-1--enable-password-hash-synchronization) neste guia sem nenhum problema.
   4. Na tela Examinar sua Solução, role para baixo até os Serviços de Federação do Active Directory (AD FS).</br>
 
   Se você vê que a configuração do AD FS está nesta seção, você pode presumir com segurança do AD FS foi originalmente configurado por meio do Azure AD Connect e, portanto, a conversão de seus domínios de federados para gerenciados pode ser orientada por meio da opção "Alterar a entrada do usuário" do Azure AD Connect. Esse processo é detalhado na seção **Opção A – mudar da federação para a sincronização de hash de senha usando o Azure AD Connect**.
   5. Se você não conseguir ver os Serviços de Federação do Active Directory (AD FS) listados nas configurações atuais, você precisará, por meio do PowerShell, converter manualmente os domínios de federados para gerenciados. Isso é detalhado na seção **Opção B – mudar da federação para a sincronização de hash de senha usando o Azure AD Connect e o PowerShell**.

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
Valide quaisquer configurações que talvez tenham sido personalizadas para a documentação de design e implantação de federação, especificamente PreferredAuthenticationProtocol, SupportsMfa e PromptLoginBehavior.

Mais informações sobre o que essas configurações fazem podem ser encontradas abaixo.

[Suporte ao parâmetro prompt=login dos Serviços de Federação do Active Directory (AD FS)](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)  
‎[Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Se o valor de SupportsMfa atualmente é definido como "True", isso significa que você está usando uma solução MFA local para injetar um desafio de segundo fator no fluxo de autenticação do usuário. Isso não funcionará mais para cenários de autenticação do Azure AD e, em vez disso, você terá que utilizar o serviço do MFA do Azure (baseado em nuvem) para executar a mesma função. Avalie cuidadosamente os requisitos de MFA antes de prosseguir e verifique se você entende como aproveitar o MFA do Azure, as implicações de licenciamento e o processo de registro do usuário final antes de converter seus domínios. Nosso guia de implantação mais detalhado para o MFA do Azure pode ser encontrado em [https://aka.ms/deploymentplans](https://aka.ms/deploymentplans).

#### <a name="backup-federation-settings"></a>Configurações de federação de backup

Embora nenhuma alteração será feita a outras terceiras partes confiáveis no seu farm do AD FS durante esse processo, é recomendável garantir que você tenha um backup válido do farm do AD FS que possa ser restaurado. Você pode fazer isso usando a [Ferramenta de Restauração Rápida do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) gratuita da Microsoft. Essa ferramenta pode ser usada para fazer backup do AD FS e restaurá-lo, para um farm existente ou um novo farm.

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
| O AD FS é altamente personalizado e dependente de configurações de personalização específicos no arquivo onload.js, que não pode ser duplicado no Azure AD (por exemplo, você alterou a experiência de entrada para que os usuários apenas insiram um formato de SamAccountName para seu nome de usuário em vez de para um UPN ou então tenham uma experiência de entrada com identidade visual altamente personalizada)| Você precisará verificar se seus requisitos de personalização atual podem ser atendidos pelo Azure AD antes de continuar. Consulte as seções de Identidade Visual do AD FS e Personalização do AD FS para obter mais informações e diretrizes.|
| Você está bloqueando os clientes de autenticação herdada por meio do AD FS.| Considere substituir os controles para bloqueio de clientes de autenticação herdada atualmente presentes no AD FS com uma combinação de [controles de acesso condicional para autenticação herdada](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) e [regras de acesso do cliente do Exchange Online](https://aka.ms/EXOCAR).|
| Você exige que os usuários executem a MFA em uma solução de servidor MFA local ao autenticar-se no AD FS.| Você não conseguirá injetar um desafio de MFA por meio da solução MFA local no fluxo de autenticação de um domínio gerenciado. No entanto, você pode usar o serviço MFA do Azure para fazê-lo, avançando uma vez que o domínio seja convertido. Se os usuários não estiverem usando o MFA do Azure hoje, isso envolverá uma etapa de registro de uso único do usuário final para a qual você precisará se preparar e que precisará comunicar aos seus usuários finais.|
| Você usa políticas de controle de acesso (regras AuthZ) atualmente no AD FS para controlar o acesso ao Office 365.| Considere substituir esses recursos com as [políticas de acesso condicional](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) e [regras de acesso do cliente do Exchange Online](https://aka.ms/EXOCAR) equivalentes do Azure AD.|

### <a name="considerations-for-common-ad-fs-customizations"></a>Considerações para personalizações comuns do AD FS

#### <a name="inside-corporate-network-claim"></a>Dentro da declaração de rede corporativa

A declaração InsideCorporateNetwork é emitida pelo AD FS se o usuário realizando a autenticação está dentro da rede corporativa. Essa declaração pode ser então passada para o Azure AD e usada para ignorar a autenticação multifator com base no local de rede dos usuários. Veja [IPs confiáveis para usuários federados](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud) para obter informações sobre como determinar se você está com esse recurso atualmente habilitado no AD FS.

A declaração InsideCorporateNetwork não estará mais disponível depois que seus domínios forem convertidos para sincronização de hash de senha. As [localizações nomeadas no Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) podem ser usadas para substituir esta funcionalidade.

Uma vez configurados os locais de chamada, todas as políticas de acesso condicional configuradas para incluir ou excluir os locais de rede "Todos os locais confiáveis" ou "IPs confiáveis de MFA" devem ser atualizados para refletir os locais nomeados recém-criados.

Para obter mais informações sobre a condição de local do acesso condicional, veja [Locais de acesso condicional do Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

#### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos ingressados no Azure AD híbrido

Ingressar em um dispositivo no Azure AD permite que você crie regras de acesso condicional que obrigam os dispositivos a atenderem aos seus padrões de acesso de segurança e conformidade e permite que os usuários se conectem a um dispositivo usando uma conta corporativa ou de estudante, em vez de uma conta pessoal. Os dispositivos ingressados no Azure AD híbrido permitem que seus dispositivos ingressados no domínio do AD sejam ingressados Azure AD. Seu ambiente federado pode ter sido configurado com esse recurso.

Para garantir que o ingresso híbrido continue a funcionar para quaisquer novos dispositivos ingressados no domínio depois que seus domínios foram convertidos para sincronização de hash de senha, o Azure AD Connect deve ser configurado para sincronizar contas de computador do Active Directory para clientes Windows 10 para o Azure AD. Para contas de computador do Windows 7 e Windows 8, o ingresso híbrido usará o SSO contínuo para registrar o computador no Azure AD e você não precisará sincronizá-los como faria para dispositivos do Windows 10. No entanto, você precisará implantar um arquivo workplacejoin.exe atualizado (por meio de um .msi) para esses clientes de nível inferior para que eles possam registrar a si mesmos usando o SSO contínuo. [Baixar o .msi](https://www.microsoft.com/download/details.aspx?id=53554). 

Para obter mais informações, consulte [Como configurar dispositivos ingressados no Azure Active Directory híbrido](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup).

#### <a name="branding"></a>Identidade visual

Sua organização pode ter [personalizado suas páginas de entrada do ADFS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) para exibir informações mais pertinentes para a organização. Nesse caso, considere a possibilidade de fazer [personalizações de página de entrada do Azure AD](https://docs.microsoft.com/azure/active-directory/customize-branding) semelhantes.

Embora as personalizações semelhantes estejam disponíveis, algumas alterações visuais devem ser esperadas. Talvez você queira incluir as alterações esperadas em suas comunicações aos usuários finais.

> [!NOTE]
> A identidade visual da empresa estará disponível somente se você comprar a licença Premium ou Básica do Azure AD ou se tiver uma licença do Office 365.

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

Após a sincronização de hash de senha e o SSO contínuo serem implantados, a experiência de entrada do usuário final mudará ao acessar o Office 365 e outros recursos associados autenticados por meio do Azure AD. Os usuários externos à rede agora verão somente a página de entrada do Azure AD, em vez de serem redirecionados à página baseada em formulários apresentada pelos servidores proxy de aplicativo Web voltados para o exterior.

Há vários elementos para planejar sua estratégia de comunicação. Estão incluídos:

* Notificar os usuários de funcionalidades futuras e já lançadas por meio de
  * Email e outros canais de comunicação interna
  * Elementos visuais, tais como cartazes
  * Comunicações ao vivo ou de outro tipo
* Determinar quem personalizará e quem enviará as comunicações e quando isso será feito.

## <a name="implementing-your-solution"></a>Implementando sua solução

Agora que você planejou sua solução, você está pronto para implementá-la. A implementação inclui os seguintes componentes:

1. Permitir a sincronização de hash de senha

2. Preparar para logon único contínuo

3. Alterar o método de entrada para sincronização de hash de senha e habilitar o SSO contínuo

### <a name="step-1--enable-password-hash-synchronization"></a>Etapa 1 – permitir a sincronização de hash de senha

A primeira etapa para implementar esta solução é permitir a sincronização de hash de senha no assistente do Azure AD Connect. A sincronização de hash de senha é um recurso opcional que pode ser habilitado em ambientes usando a federação sem nenhum impacto sobre o fluxo de autenticação. Nesse caso, o Azure AD Connect iniciará a sincronização de hashes de senha sem afetar a entrada dos usuários usando a federação.

Por esse motivo, é recomendável executar essa etapa como uma tarefa de preparação bem antes de alterar o método de entrada dos domínios. Isso lhe dará bastante tempo para validar que a sincronização de hash de senha está funcionando corretamente.

Para habilitar a sincronização de hash de senha:

   1. No servidor do Azure AD Connect, abra o assistente e selecione Configurar.
   2. Selecione Personalizar opções de sincronização e selecione Avançar.
   3. Na tela Conectar ao Azure AD, insira o nome de usuário e senha de um administrador global.
   4. Na tela Conectar seus diretórios, clique em Avançar.
   5. Na tela Filtragem de Domínio e UO, clique em Avançar.
   6. Na tela de Recursos opcionais, selecione a Sincronização de senha e selecione Avançar.
   ![Figura 21](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)</br>
   7. Selecione Avançar em todas as telas restantes e Configurar na última tela.
   8. O Azure AD Connect iniciará a sincronização de hashes de senha na próxima sincronização.

Depois que a sincronização de hash de senha tiver sido habilitada, os hashes de senha para todos os usuários no escopo de sincronização do Azure AD Connect passarão por nova operação de hash e serão gravados no Azure AD. Dependendo do número de usuários, essa operação pode levar de alguns minutos a várias horas.

Para fins de planejamento, você deve estimar que cerca de 20.000 usuários podem ser processados em 1 hora.

Para validar que a sincronização de hash de senha está funcionando corretamente, use a tarefa de solução de problemas no assistente do Azure AD Connect.

   1. Abra uma nova sessão do Windows PowerShell no servidor do Azure AD Connect com a opção Executar como administrador.
   2. Execute Set-ExecutionPolicy RemoteSigned ou Set-ExecutionPolicy Unrestricted.
   3. Iniciar o assistente do Azure AD Connect.
   4. Navegue até a página Tarefas Adicionais, selecione Solucionar problemas e clique em Avançar.
   5. Na página de solução de problemas, clique em Iniciar para iniciar o menu de solução de problemas no PowerShell.
   6. No menu principal, selecione Solucionar problemas de sincronização de hash de senha.
   7. No submenu, selecione A Sincronização de hash de senha não funciona.

Se você encontrar problemas, use as informações [neste artigo](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization) para solucionar problemas.

### <a name="step-2--prepare-for-seamless-sso"></a>Etapa 2 – preparar para o SSO contínuo

Para que todos os seus dispositivos usem o SSO contínuo, você precisa adicionar uma URL do Azure AD às configurações de zona da Intranet dos usuários usando a política de grupo no Active Directory.

Por padrão, o navegador calcula automaticamente a zona correta, Internet ou Intranet, de uma URL específica. Por exemplo, "http://contoso/" é mapeada para a Zona da Intranet, enquanto "http://intranet.contoso.com/" é mapeada para a Zona da Internet (porque a URL contém um ponto). Os navegadores não enviarão tickets Kerberos para um ponto de extremidade da nuvem, como a URL do Azure Active Directory, a menos que a URL seja explicitamente adicionada à zona da Intranet do navegador.

Siga as [etapas para distribuir](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start) as alterações necessárias para seus dispositivos.

> [!IMPORTANT]
> Fazer essa alteração não modificará a maneira em que os usuários entram no Azure AD. No entanto, é importante que essa configuração seja aplicada a todos os dispositivos antes de continuar com a Etapa 3. Observe também que os usuários entrando nos dispositivos que não receberam essa configuração simplesmente precisarão inserir o nome de usuário e senha para entrar no Azure AD.

### <a name="step-3--change-sign-in-method-to-phs-and-enable-seamless-sso"></a>Etapa 3 – alterar o método de entrada para PHS e habilitar o SSO contínuo

#### <a name="option-a---switch-from-federation-to-phs-by-using-azure-ad-connect"></a>Opção A – mudar de federação para PHS usando o Azure AD Connect

Use esse método quando o AD FS foi configurado inicialmente usando o Azure AD Connect. Você não pode usar esse método quando o AD FS não foi configurado inicialmente usando o Azure AD Connect. Primeiro **altere o método de entrada do usuário**

   1. No servidor do Azure AD Connect, abra o assistente.
   2. Selecione Alterar a Entrada do Usuário e, em seguida, selecione Avançar.
   ![Figura 27](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)</br>
   3. Na tela **Conectar ao Azure AD**, insira o nome de usuário e senha de um **administrador global**.
   4. Na tela **Entrada do Usuário**, altere o botão de opção de Federação com AD FS para Passar a Sincronização de Hash e não deixe de marcar a caixa Não converter contas de usuário, pois essa é uma etapa preterida e será removida de uma versão futura do AAD Connect. Também selecione Habilitar o logon único e selecione **Avançar**.
   ![Figura 29](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)</br>
   
   > [!NOTE]
   > Do Azure AD Connect versão 1.1.880.0 em diante, a caixa de seleção Logon único contínuo está habilitada por padrão.
   
   > [!IMPORTANT]
   > Você pode ignorar com segurança os avisos que indicam que a conversão de usuário e a sincronização de hash de senha completa são etapas necessárias para converter da federação para a autenticação de nuvem. Observe que essas etapas não são mais necessárias, pois as versões futuras do Azure AD Connect não terão uma opção para converter usuários. Se você ainda vir esses avisos, verifique que você está executando a versão mais recente do Azure AD Connect e se você está usando a versão mais recente deste guia. Para obter mais informações, veja a [seção Atualizar o Azure AD Connect](#_Update_Azure_AD).
   
   5. Na tela Habilitar Logon Único, insira as credenciais da conta do administrador de domínio e selecione Avançar.
   ![Figura 35](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)</br>
   
   > [!NOTE]
   > As credenciais de administrador de domínio são necessárias para habilitar o logon único contínuo, já que o processo executa as ações a seguir, que exigem essas permissões elevadas. As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Elas estão usadas somente para habilitar o recurso e depois são descartadas após a conclusão bem-sucedida
   >  * Uma conta de computador denominada AZUREADSSOACC (que representa o Azure AD) é criada em seu AD (Active Directory) local.
   >  * A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   >  * Além disso, os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.
   >  * As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Elas estão usadas somente para habilitar o recurso e depois são descartadas após a conclusão bem-sucedida
   
   6. Na tela Pronto para Configurar, verifique se a caixa de seleção "Iniciar Processo de Sincronização quando a configuração for concluída" está selecionada. Em seguida, selecione Configurar.
   ![Figura 36](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)</br>
   
   > [!IMPORTANT]
   > Neste momento, todos os seus domínios federados serão alterados para a autenticação gerenciada que agora aproveitará a sincronização de hash de senha como o método de autenticação.
       
   7. Abra o portal do Azure AD, selecione Azure Active Directory e, em seguida, selecione o Azure AD Connect.
   8. Verifique se a Federação está Desativada enquanto o logon único Contínuo e a Sincronização de Senha estão Habilitadas.  
  ![Figura 37](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)</br>
   9. Vá para [Testes e próximas etapas](#testing-and-next-steps).
   
   > [!IMPORTANT]
   > Ignore a seção Opção B – mudar da federação para a sincronização de hash de senha usando o Azure AD Connect e o PowerShell, pois as etapas nessa seção não se aplicam.  

#### <a name="option-b---switch-from-federation-to-phs-using-azure-ad-connect-and-powershell"></a>Opção B – mudar de federação para PHS usando o Azure AD Connect e o PowerShell

Use essa opção quando sua federação não foi configurada inicialmente por meio do Azure AD Connect.

Como parte desse processo, você habilitará o SSO contínuo e mudará seus domínios de federados para gerenciados.

   1. No servidor do Azure AD Connect, abra o assistente.
   2. Selecione Alterar a Entrada do Usuário e, em seguida, selecione Avançar. 
   3. Na tela Conectar ao Azure AD, insira o nome de usuário e senha de um administrador global.
   4. Na tela Entrada de Usuário, altere o botão de opção de Não configurar para Sincronização de Hash de Senha, selecione Habilitar logon único e, em seguida, selecione Avançar.
   
   Antes da alteração: ![Figura 20](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)</br>

   Após a alteração:  
   ![Figura 22](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)</br>
   
   > [!NOTE]
   > Do Azure AD Connect versão 1.1.880.0 em diante, a caixa de seleção Logon único contínuo está habilitada por padrão.
   
   5. Na tela Habilitar Logon Único, insira as credenciais da conta do administrador de domínio e selecione Avançar.
   
   > [!NOTE]
   > As credenciais de administrador de domínio são necessárias para habilitar o logon único contínuo, já que o processo executa as ações a seguir, que exigem essas permissões elevadas. As credenciais de administrador de domínio não são armazenadas no Azure AD Connect ou no Azure AD. Elas estão usadas somente para habilitar o recurso e depois são descartadas após a conclusão bem-sucedida.
   > * Uma conta de computador denominada AZUREADSSOACC (que representa o Azure AD) é criada em seu AD (Active Directory) local.
   > * A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
   > * Além disso, os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.
   
   6. Na tela Pronto para Configurar, verifique se a caixa de seleção "Iniciar Processo de Sincronização quando a configuração for concluída" está selecionada. Em seguida, selecione Configurar.
   ![Figura 41](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)</br>
   Quando configura a seleção, o SSO contínuo será configurado de acordo com a etapa anterior. A configuração de sincronização de hash de senha não será modificada, pois ela foi habilitada anteriormente.
   
   > [!IMPORTANT]
   > Neste momento, não será feita nenhuma alteração à forma como os usuários entram.  
   
   7. No portal do Azure AD, verifique se a Federação continua Habilitada e o logon único Contínuo agora está Habilitado.
   ![Figura 42](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Converter domínios de federado em gerenciado

Neste ponto, a federação está ainda habilitada e operacional para seus domínios. Para continuar com a implantação, cada domínio precisa ser convertido de federado para gerenciado para forçar a autenticação de usuário por meio da sincronização de hash de senha.

> [!IMPORTANT]
> Nem todos os domínios precisam ser convertidos ao mesmo tempo, você pode optar por começar com um domínio de teste em seu locatário de produção ou com o domínio com o menor número de usuários.

A conversão é executada usando o módulo do PowerShell do Azure AD.

   1. Abra o PowerShell e faça logon no Azure AD usando uma conta de administrador global.  
   2. Para converter o primeiro domínio, execute o comando a seguir:  
   
   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domainname>
   ```
   
   3. Abra o portal do Azure AD, selecione Azure Active Directory e, em seguida, selecione o Azure AD Connect.
   4. Verifique se o domínio foi convertido em gerenciado, executando o seguinte comando:
   
   ``` PowerShell
   Get-MsolDomain -DomainName <domainname>
   ```

## <a name="testing-and-next-steps"></a>Testes e próximas etapas

### <a name="test-authentication-with-phs"></a>Testar a autenticação com o PHS

Quando seu locatário estava usando a federação, os usuários foram redirecionados da página de entrada do Azure AD para seu ambiente do AD FS. Agora que o locatário está configurado para usar sincronização de hash de senha em vez de federação, os usuários não são redirecionados para o AD FS e, em vez disso, se conectarão diretamente por meio da página de entrada do Azure AD.

Abra o Internet Explorer no modo InPrivate para evitar o que o SSO contínuo conecte você automaticamente, então vá para a página de logon do Office 365 ([https://portal.office.com](https://portal.office.com/)). Digite o nome UPN do usuário e clique em Avançar. Você deve digitar o nome UPN de um usuário híbrido que foi sincronizado do Active Directory local e que costumava ser federado. O usuário verá a tela para digitar seu nome de usuário e senha.

![Figura 9](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

![Figura 12](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

Depois de digitar a senha, você deverá ser redirecionado ao portal do Office 365.

![Figura 17](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)

### <a name="test-seamless-single-sign-on"></a>Testar logon único contínuo

Conecte-se a um computador ingressado no domínio que está conectado à rede corporativa. Abra o Internet Explorer e vá para uma das seguintes URLs:  
  
[https://myapps.microsoft.com/contoso.com](https://myapps.microsoft.com/contoso.com) [https://myapps.microsoft.com/contoso.onmicrosoft.com](https://myapps.microsoft.com/contoso.onmicrosoft.com) (substitua Contoso pelo nome do seu locatário).

O usuário será redirecionado rapidamente para a página de logon do Azure AD do e verá a mensagem "Tentando entrar". Não deverá ser solicitado que ele forneça nome de usuário ou senha.

![Figura 24](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)

Em seguida, o usuário será redirecionado e conectado ao painel de acesso com êxito:

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

### <a name="troubleshooting"></a>solução de problemas

A equipe de suporte deve entender como solucionar os problemas de autenticação que ocorrem durante ou após a alteração do modelo de federação para o gerenciado. Use a seguinte documentação de solução de problemas para ajudar sua equipe de suporte a se familiarizar com as etapas comuns de solução de problemas e as ações apropriadas que podem ajudar a isolar e resolver o problema.

[Solucionar problemas de sincronização de hash de senha no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Solucionar problemas do logon único contínuo do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)  

## <a name="roll-over-the-seamless-sso-kerberos-decryption"></a>Substituir a descriptografia do Kerberos de SSO contínuo

É importante substituir frequentemente a chave de descriptografia do Kerberos da conta de computador AZUREADSSOACC (que representa o Azure AD) criada na sua floresta do AD local. É altamente recomendável que você substitua a chave de descriptografia do Kerberos pelo menos a cada 30 dias para alinhar com o modo como membros do domínio do Active Directory enviam as alterações de senha. Já que não há nenhum dispositivo associado anexado ao objeto de conta de computador AZUREADSSOACC, a substituição precisa ser executada manualmente.

Siga estas etapas no servidor do local em que você está executando o Azure AD Connect para iniciar a substituição da chave de descriptografia do Kerberos.

[Como posso substituir a chave de descriptografia de Kerberos da conta de computador AZUREADSSOACC](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq)?

## <a name="next-steps"></a>Próximas etapas

- [Conceitos de design do Azure AD Connect](plan-connect-design-concepts.md)
- [Escolha a autenticação correta](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)
- [Topologias compatíveis](plan-connect-design-concepts.md)
