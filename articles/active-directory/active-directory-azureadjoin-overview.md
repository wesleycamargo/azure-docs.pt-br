<properties 
	pageTitle="Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure| Microsoft Azure" 
	description="Um tópico que explica a Junção do Azure AD." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/30/2015" 
	ms.author="femila"/>

# Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure

## O que é a Junção do Active Directory do Azure? 
A Junção do Active Directory do Azure (Azure AD) é a funcionalidade que registra um dispositivo empresarial no Active Directory do Azure para habilitar o gerenciamento centralizado. Isso significa que agora os usuários (funcionários, alunos) podem ser conectados à nuvem da empresa através do Active Directory do Azure. Isso permite implantações simplificadas do Windows e acesso a aplicativos e recursos organizacionais por meio de dispositivos Windows, sejam eles dispositivos da empresa ou pessoais (BYOD).

A Junção do Azure AD é voltada para empresas que são principalmente ou somente a nuvem (normalmente, pequenas e médias empresas que não têm uma infraestrutura do Active Directory local). A Junção do Azure AD também pode ser usado por grandes organizações em dispositivos que são incapazes de fazer o ingresso no domínio tradicional (como dispositivos móveis, por exemplo) ou por usuários que precisam acessar principalmente o Office 365 ou outros aplicativos de SaaS do Azure AD.

Embora a junção do ingresso no domínio tradicional ainda traga a melhor experiência local em dispositivos que são capazes de realizar a junção de domínio, a junção do Azure AD é adequada para dispositivos que não são capazes de realizar o ingresso no domínio e para áreas em que você deseja gerenciar usuários na nuvem com recursos do MDM, em vez de suas experiências tradicionais de gerenciamento de domínio com a Política de Grupo e o SCCM.

![](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## Por que as empresas devem adotar a Junção do Azure AD 

 * **Se sua empresa estiver principalmente na nuvem**: se você adotar ou estiver se mudando para um modelo no qual está reduzindo o volume local e deseja operar mais na nuvem, a junção do Azure AD pode beneficiá-lo. Você pode ter criado contas do Azure AD manualmente ou sincronizando seu AD local. De qualquer forma, você tem uma conta no Azure AD e pode usá-la para entrar no Windows 10. Os usuários podem juntar seus computadores ao Azure AD por meio do processo do OOBE ou através das Configurações. Agora, seus usuários têm acesso de SSO a seus recursos de nuvem, como o Office 365, no navegador ou em aplicativos do Office. 
* **Instituições educacionais**: um dos grandes cenários sobre o qual ouvimos é como instituições de ensino têm dois tipos de usuários: professores e alunos. Os membros do corpo docente são considerados integrantes de longo prazo da organização e é desejável criar contas locais para eles. Mas os alunos são membros de prazo mais curto da organização e, portanto, podem ser gerenciados no Azure AD, de modo que a escala do diretório pode ser enviada para a nuvem em vez do local. Agora, esses alunos podem entrar no Windows com sua conta do Azure AD e obter acesso aos recursos do Office 365 em aplicativos do Office. 
* **Empresas de varejo**: outro aspeto de que os clientes nos falam é seu desejo de ter um gerenciamento mais fácil dos trabalhadores sazonais. Novamente, funcionários de longo prazo, em período integral, têm contas locais e normalmente usariam computadores dentro do domínio. Mas trabalhadores sazonais são membros de prazo mais curto da organização e, portanto, é desejável gerenciá-los onde seja possível mover as licenças de usuário mais facilmente. A criação desses usuários na nuvem com licenças do Office 365 permite que os usuários tenham os benefícios de entrar em aplicativos do Windows e do Office com uma conta do Azure AD, mantendo uma maior mobilidade de suas licenças quando eles saem. 
* **Outras empresas**: e, para além desses cenários específicos, você pode notar que, mesmo que mantenha os usuários em seu diretório do AD local, você ainda se beneficiaria de ter usuários com a junção do Azure AD devido à experiência de junção simplificada, ao gerenciamento de dispositivos no Azure AD, o registro automático do MDM e o logon único no Azure AD e em recursos locais.  

## Recursos da Junção do Azure AD
Com a Junção do Azure AD, você obtém o seguinte:

* **Autoprovisionamento de dispositivos corporativos**: com o Windows 10, os usuários finais podem configurar um dispositivo totalmente novo na experiência de configuração inicial pelo usuário, sem o envolvimento de TI.


* **Suporte para formatos modernos**: a Junção do Azure AD funciona em dispositivos que não têm recursos tradicionais de ingresso no domínio.


* **Usa contas organizacionais existentes**: os usuários finais não precisam mais criar e manter uma conta pessoal da Microsoft para obter a melhor experiência em dispositivos emitidos da empresa, como no Windows 8. Em vez disso, eles podem usar sua conta de trabalho existente no Azure AD. Para muitas organizações, isso significa basicamente configurar e fazer logon no Windows com as mesmas credenciais que são usadas para acessar o Office 365.


* **Registro automático de MDM**: os dispositivos podem ser registrados automaticamente no gerenciamento quando conectados ao Azure AD. Isso funciona com o Microsoft Intune e MDMs de terceiros. Quando gerenciado com o Intune, a TI será ser capaz de monitorar/gerenciar dispositivos com a junção do Azure AD junto com dispositivos com junção de domínio no console de gerenciamento do SCCM.


* **Logon único para recursos da empresa**: os usuários aproveitem o logon único da área de trabalho do Windows para aplicativos e recursos na nuvem, como o Office 365 e milhares de aplicativos de negócios que dependem do Azure AD para autenticação através do [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Dispositivos da empresa unidos ao Azure AD também aproveitarão o SSO em recursos locais quando o dispositivo estiver na rede corporativa, e de qualquer lugar quando os recursos estiverem expostos por meio do [Proxy de aplicativo do Azure AD](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Roaming de estado do sistema operacional**: coisas como as configurações de acessibilidade, senhas de Wi-Fi e sites serão sincronizadas entre dispositivos corporativos sem a necessidade de uma conta pessoal da Microsoft.


* **Windows Store pronta para a empresa**: a Windows Store dará suporte à aquisição e ao licenciamento de aplicativos com contas do Azure AD. As organizações serão capaz de licenciar aplicativos por volume e torná-los disponíveis para os usuários em sua organização.

## Como diferentes dispositivos funcionam com a Junção do Azure AD

| Dispositivo corporativo (unido a um domínio local) | Dispositivo corporativo (unido à nuvem) | Dispositivo pessoal |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Os usuários entram no Windows com as credenciais de trabalho (como fazem hoje) | Os usuários podem entrar no Windows com as credenciais de trabalho que são gerenciadas no Azure AD. Isso é relevante para dispositivos corporativos em três casos: a organização não tem o AD local (por exemplo, pequenas empresas), a organização não cria todas as contas de usuário no AD (por exemplo, alunos, consultores, trabalhadores sazonais), dispositivos corporativos que não podem ser associados a um domínio (local), como telefones ou tablets que executam um SKU móvel. Por exemplo, um dispositivo secundário levado ao chão de fábrica/varejo, funciona para organizações gerenciadas e federadas. | Os usuários entram no Windows com suas credenciais pessoais do MSA (sem alteração) |
| Os usuários têm acesso ao roaming de configurações e à Windows Store – esses serviços funcionam com a conta de trabalho (sem necessidade de uma MSA pessoal). Exige que as empresas conectem seu AD local ao Azure AD | Configuração de autoatendimento – os usuários podem ir para a FRX (tela de apresentação) por meio de suas contas de trabalho (como alternativa ao provisionamento dos dispositivos pela TI – os dois métodos têm suporte) | É muito fácil adicionar uma conta de trabalho gerenciada no AD ou Azure AD |
| SSO da área de trabalho para sites/aplicativos/recursos de trabalho, localmente ou nos aplicativos em nuvem que usam o Azure AD para autenticação | Registro automático no diretório corporativo (Azure AD) e autorregistro no MDM. (recurso do Azure AD Premium) | Fornece o SSO entre aplicativos e sites/recursos com essa conta de trabalho |
| Os usuários podem adicionar sua MSA pessoal para acessar suas imagens/arquivos pessoais sem afetar os dados da empresa (as configurações de roaming continuam funcionando com a conta de trabalho). A conta de MSA habilita o SSO e não exige mais o roaming das configurações | SSPR (redefinição de senha de autoatendimento) no winlogon (capacidade de redefinir senha esquecida) (é necessário o AzureAD Premium para isso) | Fornece acesso à seção/frente da Store para que os usuários possam obter e usar aplicativos de LoB em seus dispositivos pessoais | |

## Próximas etapas
* [Saiba mais sobre os cenários de uso e considerações de implantação para a Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=August15_HO6-->