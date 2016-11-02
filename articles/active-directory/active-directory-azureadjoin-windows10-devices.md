<properties
    pageTitle="Usando dispositivos com Windows 10 no local de trabalho | Microsoft Azure"
    description="Fornece um instantâneo dos recursos para os usuários e a TI, realçando as diferentes maneiras como um dispositivo pode ser provisionado e usado em uma empresa com o Windows 10."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="using-windows-10-devices-in-your-workplace"></a>Usando dispositivos com Windows 10 no local de trabalho

Aplica-se a: PCs com Windows 10

O Windows 10 oferece três modelos para que as organizações possam habilitar os usuários a acessar recursos de trabalho de forma segura e conveniente.

- **Ingresso no Azure Active Directory** (Ingresso no Azure AD), para funcionários que acessam recursos como o Office 365 principalmente na nuvem. O Ingresso no Azure AD é uma experiência de provisionamento de trabalho de autoatendimento no Windows 10.
- **Ingresso no domínio**, para organizações com investimentos em aplicativos e recursos locais. O ingresso no domínio oferece uma experiência aprimorada no Windows 10 quando conectado ao Azure AD.
- **Uma nova experiência BYOD simplificada**, para os usuários que desejam adicionar uma conta corporativa ou de estudante ao Windows e acessar facilmente os recursos em dispositivos pessoais.

A tabela a seguir apresenta um instantâneo dos recursos para usuários e administradores de TI, realçando as diferentes maneiras como um dispositivo pode ser provisionado e usado em uma empresa com o Windows 10:

|                                                                                                                                                                 | Ingresso no domínio     | Ingresso no AD do Azure | Dispositivo pessoal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Entrada em dispositivo com o Windows para contas corporativas ou de estudante.                                                                                                                      | Sim             | Sim           | Não              |
| SSO (logon único) de usuário para aplicativos do Office 365 e do Azure AD. O SSO é a capacidade de entrar apenas uma vez para acessar recursos organizacionais. | Sim             | Sim           | Sim             |
| SSO do usuário para aplicativos Kerberos/NTLM.                                                                                                                                  | Sim             | Limitado       | Sim, via VPN         |
| Autorização forte e entrada conveniente para a conta corporativa ou de estudante com o Microsoft Passport e o Windows Hello.                                                                   | Sim             | Sim           | Sim             |
| Acesso à Windows Store empresarial com uma conta corporativa ou de estudante (não uma conta da Microsoft).                                                                                    | Sim             | Sim           | Sim             |
| Roaming de configurações de usuário entre dispositivos em conformidade com a empresa usando contas corporativas ou de estudante.                                                                                 | Sim             | Sim           | Sim             |
| A capacidade de restringir o acesso a aplicativos organizacionais aos dispositivos que estão em conformidade com as políticas de dispositivo organizacionais.                                                      | Sim             | Sim           | Sim             |
| Provisionamento de autoatendimento pelo usuário de dispositivos para o "trabalho em qualquer lugar".                                                                                                | Não              | Sim           | Sim             |
| Capacidade de gerenciar dispositivos.                                                                                                                                       | Sim, via GP/SCCM | Sim           | Sim             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Usar dispositivos corporativos com Ingresso no Azure AD e ingresso no domínio no Windows 10

O Windows 10 oferece duas maneiras para que os dispositivos corporativos acessem recursos de trabalho:

- Ingresso no AD do Azure
- Ingresso no domínio

 Ambos podem ser opções válidas, dependendo das necessidades e dos requisitos de uma organização. Em alguns casos, as organizações podem se beneficiar habilitando os dois métodos de implantação.

## <a name="when-to-use-azure-active-directory-join"></a>Quando usar o ingresso no Active Directory do Azure?

O ingresso no AD do Azure é uma nova experiência de provisionamento de autoatendimento no Windows 10.  Ele se destina a funcionários que acessam recursos de trabalho como o Office 365 principalmente na nuvem. É uma maneira simples de configurar computadores, tablets e telefones para a empresa. Os dispositivos são gerenciados por meio do gerenciamento de dispositivo móvel, usando controles consistentes em plataformas Windows.

**Use o ingresso no Azure AD por qualquer um destes motivos**:

- Você deseja habilitar o provisionamento de autoatendimento de dispositivos para funcionários em qualquer lugar.
- Você fornece aos usuários dispositivos móveis corporativos, como tablets e telefones.
- Você deseja gerenciar um conjunto de usuários no Azure AD em vez de no Active Directory, como funcionários temporários, prestadores de serviços ou alunos.
- Você deseja fornecer recursos de ingresso para trabalhadores em escritórios remotos com infraestrutura local limitada.
- Você não tem um Active Directory local.

Algumas organizações usam o ingresso no AD do Azure como a principal maneira de implantar dispositivos corporativos, especialmente à medida que eles estão migrando a maioria ou todos os seus recursos para a nuvem. As organizações híbridas com o Active Directory e o Azure AD também podem optar por implantar um método ou outro, dependendo do usuário ou do departamento.

Escolas e universidades, por exemplo, podem gerenciar funcionários no Active Directory e alunos no Azure AD. Algumas empresas talvez queiram gerenciar filiais ou departamentos de vendas remotos no Azure AD. O Ingresso no Azure AD e o ingresso no domínio são métodos que podem ser utilizados em uma organização híbrida. O ingresso no Azure AD pode ser um ótimo complemento ao ingresso no domínio para a implantação de dispositivos em um ambiente de trabalho.

**Se o acesso mais comum aos recursos da empresa for por meio da nuvem, sua organização poderá ter benefícios adicionais se**:

- Você puder remover dependências na infraestrutura de identidade local.
- Você puder simplificar o modelo de implantação de dispositivos afastando-se das soluções de geração de imagens, permitindo a configuração de autoatendimento.
- Você puder usar o gerenciamento de dispositivos móveis para gerenciar todos os dispositivos em diferentes plataformas.

Para saber mais sobre o Ingresso no Azure AD, confira [Estendendo recursos de nuvem para dispositivos com Windows 10 usando o Ingresso no Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-(or-keep-using-it)"></a>Quando usar o ingresso no domínio (ou continuar a usá-lo)

Nos últimos 15 anos, muitas organizações têm usado o ingresso no domínio para conectar dispositivos de trabalho. Ele habilita os usuários a entrar em seus dispositivos com suas contas corporativas ou de estudante do Active Directory. O ingresso no domínio também permite que o departamento de TI gerencie esses dispositivos de forma centralizada e completa. As organizações geralmente dependem de métodos de geração de imagens para provisionar dispositivos e, muitas vezes, usam o SCCM (System Center Configuration Manager) ou a GP (Política de Grupo) para gerenciá-los.

**Sua empresa deve usar o ingresso no domínio (ou continuar a usá-o) por qualquer um dos seguintes motivos**:

- Você tem aplicativos Win32 implantados nesses dispositivos que usam o Kerberos/NTLM.
- Você precisa de GP ou SCCM/DCM para gerenciar dispositivos.
- Você deseja continuar a usar soluções de geração de imagens para configurar dispositivos de seus funcionários.

**O ingresso no domínio do Windows 10 também lhe oferece os seguintes benefícios quando conectado ao Azure AD**:

- Autenticação forte associada ao dispositivo e entrada conveniente para contas corporativas ou de estudante com o Microsoft Passport e o Windows Hello.
- Acesso à Windows Store corporativa para dispositivos que usam contas corporativas ou de estudante (não é necessária uma conta da Microsoft).
- Roaming compatível com a empresa de configurações de usuário entre dispositivos que usam contas corporativas ou de estudante (não é necessária uma conta da Microsoft).
- A capacidade de restringir o acesso a aplicativos organizacionais aos dispositivos que estão em conformidade com as políticas de dispositivo organizacionais.

Para saber mais sobre o Ingresso no Azure AD, confira [Estendendo recursos de nuvem para dispositivos com Windows 10 usando o Ingresso no Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Habilitar o ingresso de dispositivos pessoais para o trabalho ou a escola

Para dar suporte a BYOD na empresa, o Windows 10 fornece ao usuário a capacidade de "adicionar uma conta corporativa ou de estudante" a seu computador, tablet ou telefone. Depois que o usuário adiciona uma conta corporativa ou de estudante, o dispositivo é registrado no Azure AD e, opcionalmente, é registrado no sistema de gerenciamento de dispositivos móveis que a organização configurou. O diretório mostrará esses dispositivos com o status 'Registrado' vs. 'Ingressado no AD do Azure'. Os administradores de TI podem aplicar políticas diferentes com base nessas informações, com mais informalidade em dispositivos pessoais do que em dispositivos corporativos, se desejarem.

Os usuários podem adicionar uma conta corporativa ou de estudante para seus dispositivos pessoais de forma muito conveniente. Eles podem fazer isso ao acessar um aplicativo de trabalho pela primeira vez ou podem fazê-lo manualmente, por meio do menu Configurações. Essa conta fornecerá SSO para os recursos organizacionais.

Para saber mais sobre o Ingresso no AD do Azure, confira [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Habilitar o ingresso no domínio ou o Ingresso no Azure AD

Todos os métodos descritos anteriormente (ingresso no domínio, Ingresso no Azure AD e Adicionar conta corporativa ou de estudante) têm pontos de entrada na experiência do usuário do Windows 10. No entanto, todos eles exigem um administrador de TI para habilitar a funcionalidade na infraestrutura para que a experiência funcione.

## <a name="requirements-for-deploying-azure-ad-join"></a>Requisitos para a implantação do Ingresso no Azure AD

Para implantar o ingresso no AD do Azure para um conjunto de usuários, você precisa do seguinte:

- Uma assinatura do Azure AD.
- Uma assinatura do Azure AD Premium, como registro automático de gerenciamento de dispositivos móveis, se você precisar de mais recursos.
- Gerenciamento de dispositivos móveis – por exemplo, uma assinatura do Microsoft Intune, gerenciamento de dispositivos móveis para o Office 365 ou qualquer um dos fornecedores parceiros de gerenciamento de dispositivos móveis que se integram ao Azure AD. Confira a [seção de perguntas frequentes](#frequently-asked-questions) no fim deste artigo para obter mais informações.

Se suas instalações são híbridas, é altamente recomendável que você implante o Azure AD Connect para estender o diretório local ao Azure AD.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Requisitos para usar o ingresso no domínio com o Azure AD

O ingresso no domínio continuará a funcionar como sempre. No entanto, para obter os benefícios do Azure AD, você precisará do seguinte:

- Uma assinatura do Azure AD.
- Uma implantação do Azure AD Connect para estender o diretório local ao Azure AD.
- Uma política que permita que dispositivos que ingressaram no domínio tenham acesso condicional ao Azure AD.
- Uma política que permita o acesso a dispositivos "que ingressaram no domínio" se você desejar restringir o acesso para alguns dispositivos.
- System Center Configuration Manager versão 1509 para Visualização Técnica, para habilitar regras para exigir dispositivos compatíveis. (Confira a documentação do TechNet e a postagem de blog.)

Para saber mais sobre ingresso no domínio no Windows 10, confira <link-to-DJ-in-Win10-deployment-guide>.

## <a name="requirements-for-using-byod-and-"add-a-work-or-school-account""></a>Requisitos para usar BYOD e "Adicionar uma conta corporativa ou de estudante"

Para habilitar BYOD ("traga seu próprio dispositivo") com contas corporativas ou de estudante, você precisa do seguinte:

- Uma assinatura do Azure AD.
- Uma assinatura do Azure AD Premium, como registro automático de gerenciamento de dispositivos móveis, se você precisar de mais recursos.

## <a name="requirements-for-using-microsoft-passport"></a>Requisitos para usar o Microsoft Passport

Para habilitar o Microsoft Passport, você precisará do seguinte:

- Uma PKI (infraestrutura de chave pública) para suporte a autenticação baseada em certificado que usa o Microsoft Passport.
- Uma assinatura do Intune para suporte a autenticação baseada em certificado que usa o Microsoft Passport para ingresso para o Ingresso no Azure AD e contas corporativas ou de estudante.
- Versão 1509 do System Center Configuration Manager para visualização técnica (confira a documentação do TechNet e a postagem de blog) para suporte de autenticação baseada em certificado que usa o Microsoft Passport para ingresso no domínio.
- Uma política para habilitar o Microsoft Passport na organização.

Como alternativa ao uso de uma PKI, você pode habilitar o Microsoft Passport baseado em chave usando o seguinte procedimento:

- Implante controladores de domínio do Windows Server 2016 "Visualização de Produção 1" (sem a necessidade de níveis funcionais de domínio ou floresta; vários controladores de domínio para redundância servindo a cada site do Active Directory serão suficientes).
- Definir a política para habilitar o Microsoft Passport na organização.

Para saber mais sobre o Microsoft Passport e o Windows Hello no Windows 10, confira <link-to-MS-Passport-and-Windows-Hello-document>.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad?"></a>Quais produtos de gerenciamento de dispositivos móveis de parceiro são integrados ao Azure AD?

Os seguintes produtos de fornecedores se integram com o AD do Azure para o registro unificado e o acesso condicionado no Windows 10:

- AirWatch da VMware
- Citrix Xenmobile
- Lightspeed Mobile Manager
- Gerenciamento de dispositivos móveis locais SOTI

### <a name="what-about-workplace-join-in-windows-10?"></a>E quanto a Ingressar no local de trabalho no Windows 10?
O ingresso no local de trabalho era usado no Windows 8.1 para habilitar o BYOD. No Windows 10, o BYOD é habilitado usando "Adicionar conta corporativa ou de estudante", conforme explicado anteriormente neste documento. Para organizações que não integram o gerenciamento de dispositivos móveis ao Azure AD, os usuários podem registrar o dispositivo no gerenciamento manualmente em **Configurações** > **Contas** > **Acesso corporativo**.


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10?"></a>Os usuários podem conectar sua conta da Microsoft à conta de domínio no Windows 10?
Não no Windows 10. No Windows 8.1, os usuários de dispositivos que ingressaram no domínio podiam "conectar" sua conta da Microsoft (por exemplo, Hotmail, Live, Outlook, Xbox etc.) à conta de domínio para habilitar determinadas experiências, como o SSO para serviços Live, o uso da Windows Store e o roaming de configurações de usuário entre vários dispositivos. No Windows 10, a funcionalidade de "conexão" da conta da Microsoft foi desativada. O usuário pode adicionar uma ou mais contas da Microsoft como contas adicionais para habilitar o SSO para serviços de consumidor, como a Windows Store. Isso é feito em **Configurações** > **Contas** > **Sua conta**.

Os usuários que estão atualizando de dispositivos que ingressaram no domínio do Windows 8.1 e que tiveram sua conta da Microsoft conectada terão essa conta adicionada automaticamente à lista de contas adicionais que eles usam.


## <a name="additional-information"></a>Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)



<!--HONumber=Oct16_HO2-->


