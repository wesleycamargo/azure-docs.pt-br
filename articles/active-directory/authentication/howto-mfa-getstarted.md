---
title: Planejar e executar uma implantação de autenticação multifator do Azure - Active Directory do Azure
description: Planejamento de implantação de autenticação multifator do Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c2c5006eb050b70b783ab8199724e0e98766381
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59358116"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication"></a>Planejamento de uma autenticação de multifator baseado em nuvem do Azure

As pessoas estão se conectando aos recursos organizacionais em cenários cada vez mais complicados. As pessoas se conectar de dispositivos pertence à organização, particulares e públicos em e fora da rede corporativa usando Smartphones, tablets, PCs e laptops, geralmente em várias plataformas. Neste mundo sempre conectado, vários dispositivos e várias plataformas, a segurança das contas de usuário é mais importante do que nunca. As senhas, não importa sua complexidade, usado em dispositivos, redes e plataformas não são mais suficientes para garantir a segurança da conta de usuário, especialmente quando os usuários tendem a reutilização de senhas entre contas. Sofisticado de phishing e outra ataques podem resultar em nomes de usuário e senhas que está sendo lançada e vendidos em dark web a engenharia social.

[Autenticação de multifator do Azure (MFA)](concept-mfa-howitworks.md) ajuda a proteger o acesso a dados e aplicativos. Ele fornece uma camada adicional de segurança usando uma segunda forma de autenticação. As organizações podem usar [acesso condicional](../conditional-access/overview.md) tornar a solução de acordo com suas necessidades específicas.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar uma implantação de autenticação multifator do Azure, há os itens de pré-requisito que devem ser considerados.

| Cenário | Pré-requisito |
| --- | --- |
| **Somente em nuvem** ambiente de identidade com autenticação moderna | **Não há tarefas de pré-requisito adicionais** |
| **Híbrido** cenários de identidade | [O Azure AD Connect](../hybrid/whatis-hybrid-identity.md) é implantado e identidades de usuário são sincronizadas ou federadas com o local no Active Directory Domain Services com o Azure Active Directory. |
| Os aplicativos herdados são publicados para acesso à nuvem no local | Azure AD [Proxy de aplicativo](../manage-apps/application-proxy.md) é implantado. |
| Usando o Azure MFA com a autenticação RADIUS | Um [servidor de diretivas de rede (NPS)](howto-mfa-nps-extension.md) é implantado. |
| Os usuários têm o Microsoft Office 2010 ou anterior, ou o Apple Mail para iOS 11 ou anterior | Atualizar para o [Microsoft Office 2013 ou posterior](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) e o Apple mail para iOS 12 ou posterior. Não há suporte para acesso condicional por protocolos de autenticação herdados. |

## <a name="plan-user-rollout"></a>Planejar a distribuição de usuário

Seu plano de distribuição MFA deve incluir uma implantação piloto, seguida por ondas de implantação que estão dentro da sua capacidade de suporte. Iniciar a sua distribuição por meio da aplicação de políticas de acesso condicional para um pequeno grupo de usuários piloto. Depois de avaliar o efeito sobre os usuários piloto, processo usado e comportamentos de registro, você pode adicionar mais grupos à política ou adicionar mais usuários a grupos existentes.

### <a name="user-communications"></a>Comunicação do usuário

É fundamental para informar os usuários, em comunicações planejados, sobre alterações futuras, requisitos de registro de MFA do Azure e as ações de usuário necessárias. É recomendável que as comunicações são desenvolvidas em conjunto com representantes de dentro de sua organização, como os departamentos de uma comunicação, gerenciamento de alterações ou recursos humanos.

A Microsoft fornece [modelos de comunicação](https://aka.ms/mfatemplates) e [documentação do usuário final](../user-help/security-info-setup-signin.md) para ajudar suas comunicações de rascunho. Você pode enviar os usuários [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com) para registrar diretamente, selecionando o **informações de segurança** links nessa página.

## <a name="deployment-considerations"></a>Considerações de implantação

Autenticação multifator do Azure é implantada pela imposição de políticas de acesso condicional. Um [política de acesso condicional](../conditional-access/overview.md) pode exigir que os usuários realizem a autenticação multifator quando determinados critérios forem atendidos, como:

* Todos os usuários, um usuário específico, um membro de uma função de grupo ou atribuída
* Aplicativo de nuvem específica que está sendo acessado
* Plataforma de dispositivos
* Estado do dispositivo
* Local de rede ou o endereço IP localizados geograficamente
* Aplicativos cliente
* Risco de entrada (requer o Identity Protection)
* Dispositivo em conformidade
* Dispositivo adicionado ao Azure AD híbrido
* Aplicativo cliente aprovado
 

Use os modelos de email e cartazes personalizáveis [materiais de distribuição de autenticação multifator] para implementar a autenticação multifator para sua organização. (https://www.microsoft.com/en-us/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all)

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Habilitar Autenticação Multifator com acesso condicional

Políticas de acesso condicional impõem o registro, exigindo que os usuários não registrados concluir o registro na primeira entrada, uma consideração de segurança importante.


[O Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) contribui para uma política de registro e as políticas de detecção e correção de risco automatizada na história da autenticação multifator do Azure. As políticas podem ser criadas para forçar alterações de senha quando há uma ameaça de identidade comprometida ou exigir MFA quando uma entrada é considerado arriscado pelos seguintes [eventos](../reports-monitoring/concept-risk-events.md):

* Credenciais vazadas
* Entradas de endereços IP anônimos
* Viagem impossível a locais atípicos
* Entradas de locais desconhecidos
* Entradas de dispositivos infectados
* Entradas de endereços IP com atividades suspeitas

Alguns dos eventos de risco detectados pelo Azure Active Directory Identity Protection ocorrem em tempo real e alguns exigem processamento offline. Os administradores podem optar por bloquear os usuários que exibem comportamentos arriscados e corrigir manualmente, exigem uma alteração de senha ou exigem a autenticação multifator como parte de suas políticas de acesso condicional.

## <a name="define-network-locations"></a>Definir locais de rede

É recomendável que as organizações usar acesso condicional para definir sua rede usando [localizações nomeadas](../conditional-access/location-condition.md#named-locations). Se sua organização estiver usando a proteção de identidade, considere o uso de políticas com base em risco, em vez de localizações nomeadas.

### <a name="configuring-a-named-location"></a>Configurando um local nomeado

1. Abra **Azure Active Directory** no portal do Azure
2. Clique em **acesso condicional**
3. Clique em **localizações nomeadas**
4. Clique em **novo local**
5. No **nome** campo, forneça um nome significativo
6. Selecione se você está definindo o local usando intervalos de IP ou países/regiões
   1. Se usando intervalos de IP
      1. Decida se deseja marcar o local como confiável. Conectar de um local confiável diminui o risco de entrada do usuário. Só marca esse local como confiável se você souber que os intervalos IP inseridos estão estabelecidos e são credíveis na sua organização.
      2. Especifique os intervalos de IP
   2. Se o uso de países/regiões
      1. Expanda o menu suspenso e selecione os países ou regiões que você deseja definir para essa localização nomeada.
      2. Decida se deseja incluir áreas desconhecidas. As áreas desconhecidas são endereços IP que não podem ser mapeados para um país ou uma região.
7. Clique em **Criar**

## <a name="plan-authentication-methods"></a>Métodos de autenticação de plano

Os administradores podem escolher o [métodos de autenticação](../authentication/concept-authentication-methods.md) que deseja disponibilizar para os usuários. É importante permitir que mais de um único método de autenticação para que os usuários têm um método de backup disponível no caso de seu método primário não está disponível. Os métodos a seguir estão disponíveis para os administradores habilitem:

### <a name="notification-through-mobile-app"></a>Notificação pelo aplicativo móvel

Uma notificação por push é enviada para o aplicativo Microsoft Authenticator em seu dispositivo móvel. O usuário vê a notificação e seleciona **aprovar** para concluir a verificação. Notificações por push por meio de um aplicativo móvel oferecem a opção menos intrusiva para os usuários. Eles também são a opção mais confiável e segura, porque eles usam uma conexão de dados em vez de telefonia.

> [!NOTE]
> Se sua organização tiver funcionários trabalhando em ou em trânsito para a China, os **notificação pelo aplicativo móvel** método **dispositivos Android** não funciona nesse país. Métodos alternativos devem ser disponibilizados para os usuários.

### <a name="verification-code-from-mobile-app"></a>Código de verificação de aplicativo móvel

Um aplicativo móvel como o aplicativo Microsoft Authenticator gera um novo código de verificação OATH a cada 30 segundos. O usuário digita o código de verificação na interface de entrada. A opção de aplicativo móvel pode ser usada se o telefone tem uma data ou sinal de celular ou não.

### <a name="call-to-phone"></a>Ligue para o telefone

Uma chamada de voz automatizada é feita para o usuário. O usuário responde a chamada e pressiona **#** no teclado do telefone para aprovar sua autenticação. Chamada para telefone é um método excelente de backup para o código de notificação ou verificação de um aplicativo móvel.

### <a name="text-message-to-phone"></a>Mensagem de texto para telefone

Uma mensagem de texto que contém um código de verificação é enviada para o usuário, o usuário é solicitado a inserir o código de verificação na interface de entrada.

### <a name="choose-verification-options"></a>Escolher opções de verificação

1. Navegue até **do Azure Active Directory**, **Usuários**, **Autenticação Multifator**.

   ![Acessando o portal de Autenticação Multifator na folha de usuários do Azure Active Directory no portal do Azure](media/howto-mfa-getstarted/users-mfa.png)

1. Na nova guia que é aberta, navegue até **configurações do serviço**.
1. Em **opções de verificação**, verifique todas as caixas para os métodos disponíveis aos usuários.

   ![Configurar métodos de verificação na guia configurações do serviço de Autenticação Multifator](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Clique em **Save**.
1. Feche a guia **configurações de serviço**.

## <a name="plan-registration-policy"></a>Planejar a política de registro

Os administradores devem determinar como os usuários serão registrar seus métodos. As organizações devem [habilitar a nova experiência de registro combinado](howto-registration-mfa-sspr-combined.md) redefinição de MFA do Azure e uma senha de autoatendimento (SSPR). SSPR permite aos usuários redefinir a senha de uma maneira segura usando os mesmos métodos que eles usam a autenticação multifator. Recomendamos que isso combinado registro atualmente em visualização pública, porque ele é uma ótima experiência para usuários, com a capacidade de registrar uma vez para ambos os serviços. Habilitando os mesmos métodos para SSPR e o Azure MFA permitirá que os usuários a ser registrada para usar os dois recursos.

### <a name="registration-with-identity-protection"></a>Registro com o Identity Protection

Se sua organização estiver usando o Azure Active Directory Identity Protection [configurar a política de registro MFA](../identity-protection/howto-mfa-policy.md) para solicitar que os usuários se registrem na próxima vez que entrar no modo interativo.

### <a name="registration-without-identity-protection"></a>Registro sem proteção de identidade

Se sua organização não tiver licenças que habilitam a proteção de identidade, os usuários são solicitados a registrar na próxima vez que a MFA é necessária ao entrar. Os usuários não podem ser registrados para MFA, se eles não usarem aplicativos protegidos com MFA. É importante obter todos os usuários registrados para que os atores ruins não é possível adivinhar a senha de um usuário e se registrar para MFA em seu nome, efetivamente assumindo o controle da conta.

#### <a name="enforcing-registration"></a>Aplicar o registro

Usando as etapas a seguir uma política de acesso condicional pode forçar os usuários se registrem para a autenticação multifator

1. Crie um grupo, adicionar todos os usuários que não estão registrados.
2. Usando o acesso condicional, impor a autenticação multifator para este grupo para acesso a todos os recursos.
3. Periodicamente, reavalie a associação de grupo e remover os usuários que registraram do grupo.

Você pode identificar os usuários de MFA do Azure registrados e não registrados com comandos do PowerShell que dependem de [módulo MSOnline PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0).

#### <a name="identify-registered-users"></a>Identificar usuários registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identificar os usuários não registrados

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

## <a name="plan-conditional-access-policies"></a>Planejar políticas de acesso condicional

Para planejar sua estratégia de política de acesso condicional, que determinará quando a MFA e outros controles são necessárias, consulte [o que é acesso condicional no Azure Active Directory?](../conditional-access/overview.md).

É importante que você impeça que está sendo bloqueado inadvertidamente seu locatário do AD do Azure. Você pode reduzir o impacto dessa falta acidental de acesso administrativo por [criando duas ou mais contas de acesso de emergência em seu locatário](../users-groups-roles/directory-emergency-access.md) e excluindo-os da sua política de acesso condicional.

### <a name="create-conditional-access-policy"></a>Criar política de acesso condicional

1. Entre no [portal do Azure](https://portal.azure.com) usando uma conta de administrador global.
1. Navegue até **do Azure Active Directory**, **acesso condicional**.
1. Selecione **Nova política**.
1. .Forneça um nome significativo para a política.
1. Em **usuários e grupos**:
   * Na guia **Incluir**, selecione o botão de opção **Todos os usuários**
   * Sobre o **excluir** guia, marque a caixa **usuários e grupos** e escolha suas contas de acesso de emergência.
   * Clique em **Concluído**.
1. Em **Aplicativos na nuvem**, selecione o botão de opção **Todos os aplicativos na nuvem**.
   * OPCIONALMENTE: Na guia **Excluir**, escolha os aplicativos na nuvem para os quais sua organização não exige o MFA.
   * Clique em **Concluído**.
1. Na seção **Condições**:
   * OPCIONALMENTE: Se você tiver habilitado o Azure Identity Protection, poderá optar por avaliar o risco de conexão como parte da política.
   * OPCIONALMENTE: Se você tiver configurado localizações confiáveis ou nomeadas, poderá especificar para incluir ou excluir essas localizações da política.
1. Em **Concessão**, verifique se o botão de opção **Conceder acesso** está selecionado.
    * Marque a caixa para **Exigir autenticação multifator**.
    * Clique em **Selecionar**.
1. Ignore a seção **Sessão**.
1. Defina a alternância **Habilitar política** para **Ativado**.
1. Clique em **Criar**.

![Criar uma política de acesso condicional para habilitar a MFA para usuários do portal do Azure no grupo piloto](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)

## <a name="plan-integration-with-on-premises-systems"></a>Planejar a integração com sistemas locais

Alguns aplicativos herdados e local que autentica diretamente no AD do Azure exigem etapas adicionais para usar MFA incluindo:

* Herdado aplicativos locais, que será necessário usar o proxy de aplicativo.
* Aplicativos de RADIUS no local, que serão necessário usar o adaptador do MFA com o servidor NPS.
* Aplicativos no local do AD FS, que será necessário usar o adaptador do MFA com AD FS 2016.

Aplicativos que autenticar diretamente com o Azure AD e têm autenticação moderna (WS-Fed, SAML, OAuth, OpenID Connect) podem fazer uso de políticas de acesso condicional diretamente.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Usar a MFA do Azure com o Proxy de aplicativo do Azure AD

Aplicativos que residem localmente pode ser publicados para o Azure AD de locatário por meio [Proxy de aplicativo do Azure AD](../manage-apps/application-proxy.md) e podem tirar proveito da autenticação multifator do Azure se eles estiverem configurados para usar o Azure AD pré-autenticação.

Esses aplicativos estão sujeitos às políticas de acesso condicional que impõem a autenticação multifator, assim como qualquer outro aplicativo do Azure AD integrado.

Da mesma forma, se a autenticação multifator do Azure será aplicada a todas as entradas do usuário, local serão protegidos para aplicativos publicados com o Proxy de aplicativo do Azure AD.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>A integração de autenticação multifator do Azure com o servidor de políticas de rede

A extensão do Servidor de Políticas de Rede (NPS) para o Azure MFA adiciona recursos MFA baseados em nuvem à sua infraestrutura de autenticação usando os seus servidores existentes. Com a extensão do NPS, você pode adicionar a chamada telefônica, mensagem de texto ou verificação de aplicativo de telefone para o fluxo de autenticação existente. Essa integração tem as seguintes limitações:

* Com o protocolo CHAPv2, há suporte apenas a notificações de push de aplicativo do autenticador e a chamada de voz.
* Políticas de acesso condicional não podem ser aplicadas.

A extensão NPS atua como um adaptador entre RADIUS e o Azure MFA baseado em nuvem para fornecer um segundo fator de autenticação para proteger [VPN](howto-mfa-nps-extension-vpn.md), [conexões de Gateway de área de trabalho remota](howto-mfa-nps-extension-rdg.md), ou outro capaz de RADIUS aplicativos. Os usuários se registrar para MFA do Azure nesse ambiente é desafiado a todas as tentativas de autenticação, a falta de média de políticas de acesso condicional MFA sempre é necessária.

#### <a name="implementing-your-nps-server"></a>Implementando o servidor NPS

Se você tiver uma instância NPS implantada e em uso já, referenciar [integrar a autenticação multifator do Azure de sua infraestrutura existente do NPS](howto-mfa-nps-extension.md). Se você estiver configurando o NPS pela primeira vez, consulte [servidor de diretivas de rede (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para obter instruções. Diretrizes de solução de problemas pode ser encontrada no artigo [resolver mensagens de erro da extensão NPS para autenticação multifator do Azure](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Preparar o NPS para usuários que não são registrados na MFA

Escolha o que acontece quando os usuários que não são registrados com o MFA tentam autenticar. Use a configuração do registro `REQUIRE_USER_MATCH` no caminho do registro `HKLM\Software\Microsoft\AzureMFA` para controlar o comportamento do recurso. Essa configuração tem uma opção de configuração única.

| Chave | Valor | Padrão |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | TRUE / FALSE | Não definido (equivalente a TRUE) |

A finalidade dessa configuração é determinar o que fazer quando um usuário não estiver inscrito na MFA. Os efeitos de alterar essa configuração são listados na tabela a seguir.

| Configurações | Status MFA do usuário | Efeitos |
| --- | --- | --- |
| A chave não existe | Não registrado | Desafio de MFA for bem-sucedido |
| Valor definido como True / não definida | Não registrado | Desafio de MFA for bem-sucedido |
| Chave definida como False | Não registrado | Autenticação sem MFA |
| Chave definida como False ou True | Registrados | Deve ser autenticado com MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integrar com serviços de Federação do Active Directory

Se sua organização for federada com o Azure AD, você pode usar [autenticação de multifator do Azure para proteger os recursos do AD FS](multi-factor-authentication-get-started-adfs.md), tanto localmente quanto na nuvem. O MFA do Azure permite que você reduza as senhas e fornecem uma maneira mais segura de autenticar. Começando com o Windows Server 2016, agora você pode configurar Azure MFA para autenticação primária.

Ao contrário de com o AD FS no Windows Server 2012 R2, o adaptador do AD FS 2016 MFA do Azure integra-se diretamente com o Azure AD e não requer um servidor de MFA do Azure no local. O adaptador do MFA do Azure é incorporado ao Windows Server 2016, e não é necessário para uma instalação adicional.

Ao usar o Azure MFA com AD FS 2016 e o aplicativo de destino está sujeito à política de acesso condicional, há considerações adicionais:

* Acesso condicional está disponível quando o aplicativo é uma parte confiável para o Azure AD, federada com o AD FS 2016.
* Acesso condicional não está disponível quando o aplicativo é uma parte confiável para o AD FS 2016 e é gerenciado ou federado com o AD FS 2016.
* Acesso condicional também não está disponível quando o AD FS 2016 é configurado para usar o Azure MFA como método de autenticação primária.

#### <a name="ad-fs-logging"></a>Registro em log do AD FS

Padrão do AD FS 2016 registro em log no Log de segurança do Windows e o log do administrador do AD FS, contém informações sobre as solicitações de autenticação e seu êxito ou falha. Dados de log de eventos dentro desses eventos indica se a MFA do Azure foi usado. Por exemplo, pode conter uma ID de evento de auditoria FS AD 1200:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Renovar e gerenciar certificados

Em cada servidor do AD FS, no computador local meu Store, haverá um Azure MFA autoassinado certificado intitulado UO = Microsoft AD FS do Azure MFA, que contém a data de validade do certificado. Verifique se o período de validade deste certificado em cada servidor do AD FS para determinar a data de validade.

Se o período de validade de seus certificados está prestes a expirar, [gerar e verificar um novo certificado MFA em cada servidor do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

As diretrizes a seguir fornece detalhes sobre como gerenciar os certificados Azure MFA em seus servidores do AD FS. Quando você configura o AD FS com o Azure MFA, os certificados gerados por meio de `New-AdfsAzureMfaTenantCertificate` cmdlet do PowerShell são válidos por 2 anos. Renovar e instale os certificados renovados antes da expiração ovoid interrupções no serviço MFA.

## <a name="implement-your-plan"></a>Implementar seu plano

Agora que você planejou sua solução, você pode implementar seguindo as etapas abaixo:

1. Atender os pré-requisitos necessários
   1. Implante [do Azure AD Connect](../hybrid/whatis-hybrid-identity.md) para todos os cenários híbridos
   1. Implante [Proxy de aplicativo do Azure AD](../manage-apps/application-proxy.md) para em todos os aplicativos locais publicados para acesso à nuvem
   1. Implante [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) para qualquer autenticação RADIUS
   1. Certifique-se de que os usuários atualizaram para versões com suporte do Microsoft Office com autenticação moderna habilitada
1. Configurar escolhido [métodos de autenticação](#choose-verification-options)
1. Definir seu [chamado locais de rede](../conditional-access/location-condition.md#named-locations)
1. Selecionar grupos para começar a distribuir a MFA.
1. Configurar seu [políticas de acesso condicional](#create-conditional-access-policy)
1. Configurar a política de registro MFA
   1. [MFA combinado e SSPR](howto-registration-mfa-sspr-combined.md)
   1. Com [Identity Protection](../identity-protection/howto-mfa-policy.md)
1. Enviar comunicações com o usuário e obter os usuários registrem-se em [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Manter o controle de quem está registrado](#identify-non-registered-users)

## <a name="manage-your-solution"></a>Gerenciar sua solução

Relatórios para o Azure MFA

Autenticação multifator do Azure fornece relatórios por meio do portal do Azure:

| Relatório | Local padrão | DESCRIÇÃO |
| --- | --- | --- |
| Alertas de fraudes e uso | Microsoft Azure AD > Entradas | Fornece informações sobre o uso geral, resumo do usuário e detalhes do usuário, assim como um histórico de alertas de fraude enviados durante o intervalo de datas especificado. |

## <a name="troubleshoot-mfa-issues"></a>Solucionar problemas MFA

Encontre soluções para problemas comuns com o Azure MFA na [artigo de solução de problemas do Azure a autenticação multifator](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) sobre o Microsoft Support Center.

## <a name="next-steps"></a>Próximos passos

* [Quais são os métodos de autenticação?](concept-authentication-methods.md)
* [Habilitar registro convergido para a Autenticação Multifator do Microsoft Azure Active Directory e a redefinição de senha por autoatendimento do Microsoft Azure AD](concept-registration-mfa-sspr-converged.md)
* Por que foi um usuário solicitado ou não é solicitado a executar a MFA? Consulte a seção [relatório de entradas do Azure AD nos relatórios do documento de autenticação multifator do Azure](howto-mfa-reporting.md#azure-ad-sign-ins-report).
