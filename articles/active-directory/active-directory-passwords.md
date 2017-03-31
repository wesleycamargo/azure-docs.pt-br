---
title: "Azure Active Directory de redefinição de senha | Microsoft Docs"
description: "Descrição dos recursos de gerenciamento de senhas no AD do Azure, incluindo redefinição de senha, alteração, relatórios de gerenciamento de senhas e write-back para seu Active Directory local."
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: be6164fc-bae1-49df-af76-761329ba70a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 4904a3e21a54a2c60ee8b35a46370a9759a029f3
ms.lasthandoff: 03/28/2017


---
# <a name="azure-active-directory-password-reset-for-it-administrators"></a>Redefinição de senhas do Azure Active Directory para administradores de TI
> [!IMPORTANT]
> **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-your-password).
>
>

O autoatendimento tem sido das principais metas para os departamentos de TI em todo o mundo, como uma medida de redução de custos e economia de trabalho.  De fato, o mercado é inundado com produtos que permitem que você gerencie seus grupos locais, senhas ou perfis de usuário na nuvem ou no local. O Azure Active Directory (Azure AD) diferencia-se de outras ofertas fornecendo alguns dos recursos de autoatendimento mais fáceis de usar e mais poderosos disponíveis atualmente.

**Gerenciamento de Senhas do AD do Azure** é um conjunto de recursos que permitem que os usuários gerenciem qualquer senha de qualquer dispositivo, a qualquer momento, de qualquer local, enquanto permanecem em conformidade com as políticas de segurança que você define.

## <a name="admins-learn-about-how-to-get-started-with-azure-ad-password-reset"></a>ADMINISTRADORES: saiba mais sobre como começar com a redefinição de senha do Azure AD
Se você for um administrador que deseja habilitar a redefinição de senha do Azure AD ou simplesmente saber mais sobre ela, comece pelos links abaixo para chegar aonde você quer.

| Tópico |  |
| --- | --- |
| Cenários com suporte |[O que é possível com a redefinição de senha do Azure AD?](#what-is-possible-with-azure-ad-password-reset) |
| Por que usá-la? |[Por que usar a redefinição de senha do Azure AD?](#why-use-azure-ad-password-reset) |
| Preços e disponibilidade |[Preços e disponibilidade](#pricing-and-availability) |
| Habilitar a redefinição de senha |[Habilitar a redefinição de senha para os usuários](#enable-password-reset-for-your-users) |
| Personalizar seu funcionamento |[Personalizar o comportamento de redefinição de senha](#customize-password-reset-behavior) |
| Distribuí-la para meus usuários |[Configurar usuários para usar a redefinição de senha](#configure-your-users-to-use-password-reset) |
| Exibir relatórios |[Exibir atividade de redefinição de senha com relatórios integrados](#view-password-reset-activity-with-integrated-reports) |
| Redefinir a senha de um usuário |[Gerenciar as senhas dos usuários](#manage-your-users-passwords) |
| Definir políticas de senha da minha organização |[Definir políticas de senha](#set-password-policies) |
| Solucionar um problema |[Solucionar um problema](#troubleshoot-a-problem) |
| Perguntas frequentes |[Leia as perguntas frequentes](#read-a-faq) |
| Detalhes técnicos |[Entender os detalhes técnicos](#understand-the-technical-details) |
| Recursos recém-lançados |[Atualizações de serviço recentes](#recent-service-updates) |
| Links para documentação adicional |[Links para documentação de redefinição de senha](#links-to-password-reset-documentation) |

### <a name="what-is-possible-with-azure-ad-password-reset"></a>O que é possível com a redefinição de senha do Azure AD?
Aqui estão algumas das coisas que você pode fazer com os recursos de gerenciamento de senhas do AD do Azure.

* **senha por autoatendimento** permite que os usuários finais ou os administradores alterem suas senhas expiradas ou não expiradas sem chamar um administrador ou o suporte técnico em busca de suporte.
* **senha por autoatendimento** permite que os usuários finais ou os administradores redefinam suas senhas automaticamente, sem chamar um administrador ou o suporte técnico em busca de suporte. A redefinição de senha por autoatendimento exige o AD Premium ou Basic do Azure. Para obter mais informações, consulte Edições do Active Directory do Azure.
* **Redefinição de senha iniciada pelo administrador** permite que um administrador redefina a senha de um usuário final ou de outro administrador de dentro do [Portal de Gerenciamento do Azure](https://manage.windowsazure.com).
* **Relatórios de atividade de gerenciamento de senha** fornecem aos administradores percepções sobre as atividades de registro e de redefinição de senha que ocorrem em suas organizações.
* **Write-back de senha** permite o gerenciamento de senhas locais na nuvem, de modo que todos os cenários acima podem ser executados por, ou em nome de, usuários federados ou sincronizados por senha. O Write-back de senha requer o AD Premium do Azure. Para saber mais, consulte Introdução ao AD Premium do Azure.

### <a name="why-use-azure-ad-password-reset"></a>Por que usar a redefinição de senha do Azure AD?
Aqui estão alguns dos motivos pelos quais você deve usar os recursos de gerenciamento de senhas do AD do Azure

* **Reduzir os custos** - a redefinição do suporte assistido por senha normalmente é de 20% nos gastos de TI da organização
* **Aprimorar as experiências do usuário** - os usuários não querem chamar a assistência técnica e passar uma hora no telefone sempre que esquecem suas senhas
* **Reduzir os volumes de assistência técnica** - o gerenciamento de senhas é gerador de maior volume de assistência técnica para a maioria das organizações
* **Habilitar a mobilidade** - os usuários podem redefinir suas senhas onde quer que estejam

### <a name="pricing-and-availability"></a>Preços e disponibilidade
A redefinição de senha do Azure AD está disponível em três camadas, dependendo da assinatura que você tem:

* **Azure AD Gratuito** : os administradores somente nuvem podem redefinir suas próprias senhas
* **Azure AD Basic ou qualquer assinatura do O365 paga** : usuários somente nuvem e administradores somente nuvem podem redefinir suas próprias senhas
* **Azure AD Premium** : qualquer usuário ou administrador, incluindo somente nuvem, federados ou usuários de sincronização de senha, pode redefinir sua própria senha (requer [a habilitação do write-back de senha](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords))

Para saber mais sobre preços do Azure AD Basic ou Premium, visite a página [Detalhes de preços do Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="enable-password-reset-for-your-users"></a>Habilitar a redefinição de senha para os usuários
| Tópico |  |
| --- | --- |
| Como habilitar a redefinição de senha para usuários de nuvem? |[Permitir que os usuários redefinam suas senhas da nuvem do Active Directory do Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| Como posso habilitar a redefinição de senha e alterá-la para usuários locais? |[Habilitar usuários para redefinir ou alterar suas senhas do Active Directory local](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| Como faço o escopo da redefinição de senha para um conjunto específico de usuários? |[Restringir a redefinição de senha a usuários específicos](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| Como testar a redefinição de senha de nuvem? |[Redefinir sua senha do Azure AD como um usuário](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| Como testar a redefinição de senha no local? |[Redefinir sua senha do AD local como um usuário](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| Como desabilitar a redefinição de senha em um momento posterior? |[Configuração: usuários habilitados para a redefinição de senha](active-directory-passwords-customize.md#users-enabled-for-password-reset) |

## <a name="customize-password-reset-behavior"></a>Personalizar o comportamento de redefinição de senha
| Tópico |  |
| --- | --- |
| Como posso alterar os métodos de autenticação com suporte? |[Configuração: métodos de autenticação disponíveis para os usuários](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| Como posso alterar a quantidade necessária de métodos de autenticação? |[Configuração: quantidade necessária de métodos de autenticação](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| Como faço para definir as perguntas de segurança personalizadas? |[Configuração: perguntas de segurança personalizadas](active-directory-passwords-customize.md#custom-security-questions) |
| Como posso definir as perguntas de segurança localizadas pré-configuradas? |[Configuração: perguntas de segurança baseadas em conhecimento](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| Como posso alterar quantas perguntas de segurança são necessárias? |[Configuração: número de perguntas de segurança para registro ou redefinição](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| Como posso personalizar como um usuário entra em contato com um administrador? |[Configuração: personalizar o link “entre em contato com o seu administrador”](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| Como posso permitir aos usuários desbloquear contas do AD sem redefinir a senha? |[Configuração: permitir que os usuários desbloqueiem suas contas do AD sem redefinir a senha](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| Como habilitar notificações de redefinição de senha para os usuários? |[Configuração: notificar os usuários quando suas senhas forem redefinidas](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| Como habilitar notificações de redefinição de senha para administradores? |[Configuração: notificar outros administradores quando um administrador redefinir suas senhas](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| Como posso personalizar a aparência da redefinição de senha? |[Configuração: nome, identidade visual e logotipo da empresa ](active-directory-passwords-customize.md#password-management-look-and-feel) |

## <a name="configure-your-users-to-use-password-reset"></a>Configurar usuários para usar a redefinição de senha
| Tópico |  |
| --- | --- |
| Como saber se uma conta está configurada com redefinição de senha? |[O que faz uma conta ser configurada para redefinição de senha?](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| Como configurar meus usuários para redefinição de senha? |[Maneiras de popular os dados de autenticação de redefinição de senha dos usuários](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| Como faço para carregar dados dos meus usuários manualmente? |[Carregando dados de redefinição de senha por conta própria](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| Como posso usar o PowerShell para ler ou definir dados dos meus usuários? |[Como acessar os dados de redefinição de senha de seus usuários](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| Como posso sincronizar dados de redefinição de senha no local? |[Quais dados são usados na redefinição de senha?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| Como posso usar um email de campanha para que meus usuários se registrem e usem a redefinição de senha? |[Distribuição baseada em email de redefinição de senha](active-directory-passwords-best-practices.md#email-based-rollout) |
| Como posso forçar meus usuários a fazer o registro durante a conexão? |[Imposição de distribuição baseada em registro de redefinição de senha](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| Como posso forçar meus usuários a reconfirmar seus registros periodicamente? |[Configuração: número de dias antes que os usuários tenham que reconfirmar seus dados de autenticação](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| Quais são as práticas recomendadas sobre a comunicação de redefinição de senha para usuários finais? |[Criando seu próprio portal de senha para uso pelos usuários](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |

## <a name="view-password-reset-activity-with-integrated-reports"></a>Exibir atividade de redefinição de senha com relatórios integrados
| Tópico |  |
| --- | --- |
| Onde posso ver relatórios de redefinição de senha? |[Visão geral dos relatórios de gerenciamento de senhas](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| Onde posso ver como os usuários estão usando a redefinição de senha em minha organização? |[Exibir atividade de redefinição de senha](active-directory-passwords-get-insights.md#how-to-view-password-management-reports) |
| Onde posso ver quantos usuários estão se registrando e em que estão se registrando? |[Exibir atividade de registro de redefinição de senha](active-directory-passwords-get-insights.md#how-to-view-password-management-reports) |
| Como obter relatórios de redefinição de senha de uma API? |[Criando um aplicativo do Azure AD para acessar a API de relatório](active-directory-reporting-api-getting-started.md) |
| Que tipo de informação de relatórios de redefinição de senha está disponível em uma API? |[Eventos de registro e de redefinição de senha disponíveis na API de geração de relatórios](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |

## <a name="manage-your-users-passwords"></a>Gerenciar as senhas dos usuários
| Tópico |  |
| --- | --- |
| Como posso redefinir uma senha de usuário no portal de gerenciamento do O365? |[Redefinir a senha do usuário no Office 365](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| Como posso redefinir uma senha de usuário usando o PowerShell? |[Redefinir uma senha de usuário com Set-MsolUserPassword](https://msdn.microsoft.com/library/azure/dn194140.aspx) |

## <a name="set-password-policies"></a>Definir políticas de senha
| Tópico |  |
| --- | --- |
| Como posso definir a política de expiração de senha da organização no Office 365? |[Definir política de expiração de senha](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| Como posso usar o PowerShell para definir que as senhas de um usuário específico nunca expirem? |[Usar o PowerShell para definir que a senha de usuário individual nunca expire](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| Como posso usar o PowerShell para descobrir se uma senha de usuário está definida para nunca expirar |[Verificar o status de expiração de senha do usuário usando o PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |

## <a name="troubleshoot-a-problem"></a>Solucionar um problema
| Tópico |  |
| --- | --- |
| Quais informações devo fornecer ao suporte se precisar de ajuda? |[Informações a serem incluídas quando precisar de ajuda](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| Como posso corrigir um problema com a redefinição de senha |[Solucionar problemas do portal de redefinição de senha](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| Como posso corrigir um problema com write-back de senha? |[Solucionar problemas de write-back de senha](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Como posso corrigir um problema de conectividade de write-back de senha |[Solucionar problemas de conectividade de write-back de senha](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| Como corrigir um problema de configuração de redefinição de senha |[Solucionar problemas de configuração de redefinição de senha no portal de gerenciamento do Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| Como posso corrigir um problema com relatórios de redefinição de senha |[Solucionar problemas de relatórios de gerenciamento de senha no portal de gerenciamento do Azure](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| Como corrigir um problema de registro de redefinição de senha |[Solucionar problemas do portal de registro de redefinição de senha](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| Códigos de erro do log de eventos de write-back de senha |[Códigos de erro do log de eventos de write-back de senha](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |

## <a name="read-a-faq"></a>Leia as perguntas frequentes
| Tópico |  |
| --- | --- |
| Quero ler as perguntas frequentes sobre registro para redefinição de senha |[Perguntas frequentes sobre registro de redefinição de senha](active-directory-passwords-faq.md#password-reset-registration) |
| Quero ler as perguntas frequentes sobre redefinição de senha |[Perguntas frequentes sobre redefinição de senha](active-directory-passwords-faq.md#password-reset) |
| Quero ler as perguntas frequentes sobre relatórios de redefinição de senha |[Perguntas frequentes sobre relatórios de gerenciamento de senha](active-directory-passwords-faq.md#password-management-reports) |
| Quero ler as perguntas frequentes sobre write-back de senha |[Perguntas frequentes sobre write-back de senha](active-directory-passwords-faq.md#password-writeback) |

## <a name="understand-the-technical-details"></a>Entender os detalhes técnicos
| Tópico |  |
| --- | --- |
| Quero saber o que é o write-back de senha |[Visão geral de write-back de senha](active-directory-passwords-learn-more.md#password-writeback-overview) |
| Quero saber como funciona o write-back de senha |[Como funciona o write-back de senha?](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| Quero saber quais são os cenários com suporte a write-back de senha |[Cenários com suporte para write-back de senha](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| Quero saber como o write-back de senha é protegido |[Modelo de segurança de write-back de senha](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| Quero saber como funciona o portal de redefinição de senha |[Como funciona o portal de redefinição de senha?](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| Quero saber quais dados são usados na redefinição de senha |[Quais dados são usados na redefinição de senha?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## <a name="recent-service-updates"></a>Atualizações de serviço recentes
#### <a name="enforce-password-reset-registration-at-sign-in-to-office-365-apps---november-2015"></a>Impor o registro de redefinição de senha ao entrar nos aplicativos do Office 365: novembro de 2015
* Depois de habilitar o recurso [registro imposto](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) , os usuários precisarão se registrar em qualquer lugar em que se conectarem usando uma conta de trabalho ou escolar.  Isso aumenta significativamente o ritmo de integração das organizações à redefinição de senha.  Com esse novo recurso, vimos a integração de empresas de grande porte em até duas semanas!

#### <a name="support-for-unlocking-on-premises-ad-accounts-without-resetting-a-password---november-2015"></a>Suporte para desbloquear contas do AD local sem a redefinição de senha - novembro de 2015
* O recurso Somente desbloquear (sem redefinir) é uma grande ajuda na assistência técnica hoje em dia.  Na verdade, muitas organizações gastam 70% de seu orçamento de redefinição de senha desbloqueando contas!  Para atender a essa demanda, com a redefinição de senha do Azure AD, você pode habilitar um recurso para permitir que os usuários desbloqueiem contas do AD local separadamente da redefinição de senha.  Confira como ativá-lo aqui: [Configuração: permitir que os usuários desbloqueiem suas contas do AD local sem redefinir a senha](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password).

#### <a name="usability-updates-to-registration-page---october-2015"></a>Atualizações de usabilidade na página de registro: outubro de 2015
* Agora, quando um usuário tiver dados já registrados, ele poderá simplesmente clicar em "boa aparência" para atualizar os dados sem a necessidade de enviar novamente o email ou fazer uma chamada telefônica.

#### <a name="improved-reliability-of-password-writeback---september-2015"></a>Confiabilidade melhorada de write-back de senha: setembro de 2015
* A partir da versão de setembro do Azure AD Connect, agora o agente de write-back de senha repetirá com mais agressividade as conexões e os recursos de failover adicionais mais robustos.

#### <a name="api-for-retrieving-password-reset-reporting-data---august-2015"></a>API para recuperar os dados de relatório da redefinição de senha: agosto de 2015
* Agora, os dados dos relatórios da redefinição de senha podem ser recuperados diretamente com o uso dos [Relatórios do AD do Azure e da API de Eventos](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).

#### <a name="support-for-azure-ad-password-reset-during-cloud-domain-join---august-2015"></a>Suporte para a redefinição de senha do Azure AD durante o ingresso no domínio na nuvem: agosto de 2015
* Agora, qualquer usuário de nuvem pode redefinir sua senha diretamente na tela de conexão do Windows 10 durante a experiência de integração de associação do domínio em nuvem.  Observe que isso ainda não é exibido na tela de conexão do Windows 10.

#### <a name="enforce-password-reset-registration-at-sign-in-to-azure-and-federated-apps---july-2015"></a>Impor o registro de redefinição de senha ao entrar no Azure e nos aplicativos federados: julho de 2015
* Além de aplicar o registro ao entrar em myapps.microsoft.com, agora temos suporte para impor o registro durante as conexões no Portal de Gerenciamento do Azure e em quaisquer aplicativos de logon único federados

#### <a name="security-question-localization-support---may-2015"></a>Suporte à localização da pergunta de segurança: maio de 2015
* Agora, você tem a opção de selecionar perguntas de segurança predefinidas que estão localizadas na linguagem O365 completa definida ao configurar as Perguntas de Segurança para a redefinição de senha.

#### <a name="account-unlock-support-during-password-reset---june-2015"></a>Suporte para o desbloqueio de conta durante a redefinição de senha: junho de 2015
* Se você estiver usando o write-back de senha e redefinir sua senha quando a conta for bloqueada, poderemos desbloquear automaticamente sua conta do Active Directory!

#### <a name="branded-self-service-password-reset-sspr-registration---april-2015"></a>Registro de redefinição de senha por autoatendimento (SSPR) com marca - abril de 2015
* A página de registro da redefinição de senha agora contém a marca com o logotipo da empresa!

#### <a name="security-questions---march-2015"></a>Perguntas de segurança: março de 2015
* Lançamos perguntas de segurança para GA!

#### <a name="account-unlock---march-2015"></a>Desbloqueio de conta: março de 2015
* Agora os usuários podem desbloquear suas contas quando ocorre uma redefinição de senha

## <a name="coming-soon"></a>Em breve
A seguir estão alguns dos recursos interessantes nos quais estamos trabalhando agora!

**Suporte para lembrar os usuários de atualizar seu dados registrados durante a conexão** : trabalho em andamento

* Atualmente, oferecemos suporte para lembrar os usuários para atualizarem seus dados registrados ao acessarem myapps.microsoft.com, mas estamos trabalhando na capacidade de fazer isso para todas as conexões.

## <a name="next-steps"></a>Próximas etapas
Veja abaixo links para todas as páginas de documentação sobre redefinição de senha do Azure AD:

* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-your-password).
* [**Como funciona**](active-directory-passwords-how-it-works.md) – saiba mais sobre os seis diferentes componentes do serviço e o que cada um deles faz
* [**Introdução**](active-directory-passwords-getting-started.md) – saiba como permitir que os usuários redefinam e alterem suas senhas na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md)- aprenda a personalizar a aparência e o comportamento do serviço de acordo com as necessidades de sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização
* [**Obter percepções**](active-directory-passwords-get-insights.md) – saiba mais sobre nossos recursos integrados de relatórios
* [**Perguntas frequentes**](active-directory-passwords-faq.md) - obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) – aprenda a solucionar rapidamente os problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) – aprofunde-se nos detalhes técnicos do funcionamento do serviço

