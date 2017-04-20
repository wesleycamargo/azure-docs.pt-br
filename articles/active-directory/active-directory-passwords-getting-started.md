---
title: "Guia de Introdução: Gerenciamento de Senhas do Azure AD | Microsoft Docs"
description: "Permita que os usuários redefinam suas próprias senhas, descubra os pré-requisitos para redefinição de senha e habilite o Write-back de Senha para gerenciar senhas locais no Active Directory."
services: active-directory
keywords: Gerenciamento de senha do Active Directory, gerenciamento de senhas, redefinir senha do AD do Azure
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: bde8799f-0b42-446a-ad95-7ebb374c3bec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: d1094e1b350883c206a9180ff71bd8613156360a
ms.lasthandoff: 04/13/2017


---
# <a name="getting-started-with-password-management"></a>Introdução ao Gerenciamento de Senhas
> [!IMPORTANT]
> **Você está aqui porque está tendo problemas para entrar?** Em caso afirmativo, [veja como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-my-password).
>
>

Permitir que os usuários gerenciem suas próprias senhas da nuvem do Active Directory do Azure ou do Active Directory local leva apenas algumas etapas simples. Depois de garantir que você cumpriu alguns pré-requisitos simples, você terá as opções de alteração e redefinição de senha habilitadas para toda a sua organização num piscar de olhos. Este artigo o guiará pelos seguintes conceitos:

* [**Principais dicas de nossos clientes para ler antes de começar**](#top-tips-from-our-customers-to-read-before-you-begin)
 * [**DICA IMPORTANTE: NAVEGAÇÃO DE DOCUMENTAÇÃO** - Use o sumário e o recurso de pesquisa do seu navegador para encontrar respostas](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
 * [**Dica 1: LICENCIAMENTO** - Verifique se entendeu corretamente os requisitos de licenciamento](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
 * [**Dica 2: TESTE** - Teste com um usuário final, não com um administrador e faça o piloto com um pequeno grupo de usuários](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
 * [**Dica 3: IMPLANTAÇÃO** -preencha os dados de seus usuários previamente para que eles não precisem se registrar](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
 * [**Dica 4: IMPLANTAÇÃO** -Redefina as senhas para eliminar a necessidade de se informar senhas temporárias](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
 * [**Dica 5: WRITE-BACK** - Examine o log de eventos do aplicativo em seu computador AAD Connect para solucionar problemas de write-back de senha](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
 * [**Dica 6: WRITE-BACK** - Não deixe de habilitar as permissões corretas, as regras de firewall e as configurações de conexão para write-back de senha](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
 * [**Dica 7: RELATÓRIOS** - Veja quem está registrando ou redefinindo senhas com os Logs de Auditoria do Azure AD SSPR](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
 * [**Dica 8: SOLUÇÃO DE PROBLEMAS** - Leia nosso guia de solução de problemas e perguntas frequentes para resolver vários deles](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
 * [**Tip 9: SOLUÇÃO DE PROBLEMAS** - Se ainda precisar de ajuda, inclua informações suficientes para podermos ajudá-lo](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)
* [**Como habilitar os usuários para redefinir suas senhas do Azure Active Directory**](#enable-users-to-reset-their-azure-ad-passwords)
 * [Pré-requisitos de redefinição de senha por autoatendimento](#prerequisites)
 * [Etapa 1: configurar a política de redefinição de senha](#step-1-configure-password-reset-policy)
 * [Etapa 2: adicionar dados de contato para o usuário de teste](#step-2-add-contact-data-for-your-test-user)
 * [Etapa 3: redefinir sua senha como um usuário](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Como habilitar usuários para redefinir ou alterar suas senhas do Active Directory local**](#enable-users-to-reset-or-change-their-ad-passwords)
 * [Pré-requisitos de Write-back de Senha](#writeback-prerequisites)
 * [Etapa 1: baixar a versão mais recente do Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 * [Etapa 2: habilitar Write-back de Senha no Azure AD Connect através da interface de usuário ou do PowerShell e verificar](#step-2-enable-password-writeback-in-azure-ad-connect)
 * [Etapa 3: configurar o seu firewall](#step-3-configure-your-firewall)
 * [Etapa 4: configurar as permissões apropriadas](#step-4-set-up-the-appropriate-active-directory-permissions)
 * [Etapa 5: redefinir a sua senha do AD como um usuário e verificar](#step-5-reset-your-ad-password-as-a-user)

## <a name="top-tips-from-our-customers-to-read-before-you-begin"></a>Principais dicas de nossos clientes para ler antes de começar
Abaixo estão algumas das principais dicas úteis para os clientes que implantam o gerenciamento de senhas em sua organização.

* [**DICA IMPORTANTE: NAVEGAÇÃO DE DOCUMENTAÇÃO** - Use o sumário e o recurso de pesquisa do seu navegador para encontrar respostas](#top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers)
* [**Dica 1: LICENCIAMENTO** - Verifique se entendeu corretamente os requisitos de licenciamento](#tip-1-licensing---make-sure-you-understand-the-licensing-requirements)
* [**Dica 2: TESTE** - Teste com um usuário final, não com um administrador e faça o piloto com um pequeno grupo de usuários](#tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users)
* [**Dica 3: IMPLANTAÇÃO** -preencha os dados de seus usuários previamente para que eles não precisem se registrar](#tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register)
* [**Dica 4: IMPLANTAÇÃO** -Redefina as senhas para eliminar a necessidade de se informar senhas temporárias](#tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords)
* [**Dica 5: WRITE-BACK** - Examine o log de eventos do aplicativo em seu computador AAD Connect para solucionar problemas de write-back de senha](#tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback)
* [**Dica 6: WRITE-BACK** - Não deixe de habilitar as permissões corretas, as regras de firewall e as configurações de conexão para write-back de senha](#tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback)
* [**Dica 7: RELATÓRIOS** - Veja quem está registrando ou redefinindo senhas com os Logs de Auditoria do Azure AD SSPR](#tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs)
* [**Dica 8: SOLUÇÃO DE PROBLEMAS** - Leia nosso guia de solução de problemas e perguntas frequentes para resolver vários deles](#tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues)
* [**Tip 9: SOLUÇÃO DE PROBLEMAS** - Se ainda precisar de ajuda, inclua informações suficientes para podermos ajudá-lo](#tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you)

### <a name="top-tip-documentation-navigation---use-our-table-of-contents-and-your-browsers-find-feature-to-find-answers"></a>DICA IMPORTANTE: NAVEGAÇÃO DE DOCUMENTAÇÃO - Use o sumário e o recurso de pesquisa do seu navegador para encontrar respostas
Se você estiver usando uma de nossas documentações, tentamos fornecer links rápidos para todos os pontos de interesse dos administradores em nosso sumário.

Confira o sumário abaixo:
* [Redefinição de senha do Azure AD: sumário da documentação](https://docs.microsoft.com/azure/active-directory/active-directory-passwords)

### <a name="tip-1-licensing---make-sure-you-understand-the-licensing-requirements"></a>Dica 1: LICENCIAMENTO - Verifique se entendeu corretamente os requisitos de licenciamento
Para a redefinição de senhas do AD do Azure funcionar, você deverá ter pelo menos uma vez licença atribuída na sua organização. Não impomos licença por usuário na experiência de redefinição de senha em si. No entanto, se você usar o recurso sem uma licença atribuída a um usuário, será considerado como uma violação ao contrato de licenciamento da Microsoft e as licenças terão de ser atribuídas a esses usuários.

Aqui estão alguns documentos que podem ajudar você a compreender quais licenças são necessárias para a redefinição de senha.
* [Informações gerais de licenciamento para redefinição de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-customize#what-customization-options-are-available)
* [Informações de licenciamento para redefinição de senha por recurso](https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability)
* [Cenários com suporte para write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#scenarios-supported-for-password-writeback)

### <a name="tip-2-testing---test-with-an-end-user-not-an-administrator-and-pilot-with-a-small-set-of-users"></a>Dica 2: TESTE - Teste com um usuário final, não com um administrador e faça o piloto com um pequeno grupo de usuários
Quando você testa um administrador, impomos a política de redefinição de senha do administrador, que está definida abaixo.  Isso significa que você NÃO verá os resultados esperados da política configurada para seus usuários finais.

As políticas configuradas na experiência do usuário administrativa SÓ se aplicam aos usuários finais e não aos administradores. A Microsoft impõe políticas de redefinição de senha de alta segurança para os administradores por padrão, que podem ser diferentes das políticas definidas para os usuários finais, a fim de garantir que sua organização permaneça segura.

#### <a name="administrator-password-reset-policy"></a>Política de redefinição de senha de administrador
* **Aplica-se a** - qualquer função de administrador (Administrador global, administrador de assistência técnica, administrador de senha, etc.)
* **A política de um portão se aplica...**
 * ...aos primeiros 30 dias depois de uma avaliação ser iniciada **OU**
 * ... quando um domínio personalizado não está presente **E** o Azure AD Connect não está sincronizando identidades
 * **_Requer_**: **um** email de autenticação, email alternativo, telefone de autenticação, telefone celular ou telefone comercial para ter um valor presente
* **A política de dois portões se aplica...**
 * ...após os 30 primeiros dias de uma versão de avaliação **OU**
 * ...quando um domínio personalizado está presente **OU**
 * ... quando você habilitou o Azure AD Connect para sincronizar as identidades do seu ambiente local
 * _**Requer**_: **duas** opções de email de autenticação, email alternativo, telefone de autenticação, telefone celular ou telefone comercial para ter um valor presente

### <a name="tip-3-deployment---pre-populate-data-for-your-users-so-they-dont-have-to-register"></a>Dica 3: IMPLANTAÇÃO -preencha os dados de seus usuários previamente para que eles não precisem se registrar
Muitos não percebem que você não precisa fazer com que seus usuários se registrem para usar o recurso de redefinição de senha.  Definindo propriedades de telefone ou email para os usuários com antecedência, você pode estender a redefinição de senha para toda a sua organização imediatamente, **sem a necessidade de ações por parte dos usuários!**

Para saber como fazer isso usando uma API, o PowerShell ou o Azure AD Connect, leia a documentação abaixo:
* [Implantando redefinição de senha sem exigir que os usuários se registrem](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [Quais dados são usados na redefinição de senha?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-4-deployment---use-password-reset-to-obviate-the-need-to-communicate-temporary-passwords"></a>Dica 4: IMPLANTAÇÃO -Redefina as senhas para eliminar a necessidade de se informar senhas temporárias
Isso é um marca para a dica 3. Depois de configurar os usuários para redefinição de senha previamente, imagine um cenário em que um funcionário se associa a sua empresa pela primeira vez. Em vez de comunicar a senha temporária para ele, você pode apenas fazer com que ele navegue até o [Portal de redefinição de senha do Azure AD](https://passwordreset.microsoftonline.com) para redefini-las.

Se o usuário estiver usando um [dispositivo Windows 10 ingressado no domínio do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-azureadjoin-devices-group-policy), ele poderá até mesmo fazer isso direto na tela Out of Box do Windows 10, obtendo acesso a um novo PC sem precisar levantar um dedo :).

Para saber como fazer isso usando uma API, o PowerShell ou o Azure AD Connect, leia a documentação abaixo. Depois de preencher previamente esses dados, basta enviar os usuários para redefinir suas senhas e eles poderão entrar em suas contas imediatamente:
* [Implantando redefinição de senha sem exigir que os usuários se registrem](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#deploying-password-reset-without-requiring-end-user-registration)
* [Quais dados são usados na redefinição de senha?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-data-is-used-by-password-reset)

### <a name="tip-5-writeback---look-at-the-application-event-log-on-your-aad-connect-machine-to-troubleshoot-password-writeback"></a>Dica 5: WRITE-BACK - Examine o log de eventos do aplicativo em seu computador AAD Connect para solucionar problemas de write-back de senha
O log Eventos de Aplicativo do Azure AD Connect contém um conjunto sofisticado de informações de log que descreve muito do que está acontecendo com o serviço de write-back de senha em tempo real. Para obter acesso a este log, siga as etapas abaixo:

1. Entrar no seu computador **Azure Connect AD**
2. Abrir o **Visualizador de Eventos do Windows** pressionando **Iniciar** e digitando **"Visualizador de Eventos"**
3. Abrir o log de eventos **Aplicativo**
4. Procurar eventos das seguintes fontes: **PasswordResetService** ou **ADSync** para saber mais sobre o possível problema

Para obter uma lista completa de eventos que podem aparecer nesse log, bem como outras orientações mais detalhadas para solução de problemas de write-back de senha, confira:
* [Solução de problemas: write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [Códigos de erro do log de eventos de write-back](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [Solução de problemas: conectividade de write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [Implantação de write-back - Etapa 3: Configurar seu firewall](#step-3-configure-your-firewall)
* [Implantação de write-back - Etapa 4: configurar as permissões apropriadas](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-6-writeback---ensure-you-enable-the-correct-permissions-firewall-rules-and-connection-settings-for-password-writeback"></a>Dica 6: WRITE-BACK - Não deixe de habilitar as permissões corretas, as regras de firewall e as configurações de conexão para write-back de senha
Para write-back funcionar corretamente, você deve fazer com que:

1. As devidas **permissões do Active Directory** sejam definidas para os usuários usando o recurso de write-back de senha, de modo que eles tenham direitos para modificar suas próprias senhas e sinalizadores de desbloqueio de conta no AD
2. As devidas **portas de firewall** tenham sido abertas para permitir que o serviço de write-back de senha se comunique com o mundo externo em segurança usando uma conexão de saída
3. As devidas **exceções de firewall** sejam feitas para as principais URLs de serviço de redefinição de senha, como o Barramento de Serviço
4. Seu **proxy e firewall não estejam encerrando conexões de saída ociosas**. Recomendamos 10 minutos ou mais

Para obter uma lista completa de orientações de solução de problemas e diretrizes específicas para a configuração de permissões e regras de firewall para write-back de senha, confira:
* [Solução de problemas: write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)
* [Códigos de erro do log de eventos de write-back](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
* [Solução de problemas: conectividade de write-back de senha](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)
* [Implantação de write-back - Etapa 3: Configurar seu firewall](#step-3-configure-your-firewall)
* [Implantação de write-back - Etapa 4: configurar as permissões apropriadas](#step-4-set-up-the-appropriate-active-directory-permissions)

### <a name="tip-7-reporting---see-who-is-registering-or-resetting-passwords-with-the-azure-ad-sspr-audit-logs"></a>Dica 7: RELATÓRIOS - Veja quem está registrando ou redefinindo senhas com os Logs de Auditoria do Azure AD SSPR
Depois que a redefinição de senha é implantada e está funcionando, a próxima etapa lógica é ver o funcionamento e analisar quem ainda precisa ser registrado, os problemas comuns que os usuários enfrentam na redefinição e o retorno sobre o investimento do recurso.

Usando os Logs de Auditoria de Redefinição de Senha do Azure AD, você pode fazer isso e muito mais no Portal do Azure, no PowerBI, na API de Relatório de Eventos do Azure AD ou no PowerShell.  Para saber mais sobre como usar esses recursos de relatório, confira:
* [Visão geral de relatórios de gerenciamento de senhas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#overview-of-password-management-reports)
* [Como exibir relatórios de gerenciamento de senhas no portal do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-view-password-management-reports)
* [Tipos de atividades de Gerenciamento de senhas de autoatendimento no Portal do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#self-service-password-management-activity-types-in-the-new-azure-portal)
* [Como recuperar eventos de gerenciamento de senhas da API de Relatórios e Eventos do Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api)
* [Como baixar eventos de registro de redefinição de senha rapidamente com o PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-get-insights#how-to-download-password-reset-registration-events-quickly-with-powershell)

### <a name="tip-8-troubleshoot---read-our-troubleshooting-guide-and-faq-to-solve-many-issues"></a>Dica 8: SOLUÇÃO DE PROBLEMAS - Leia nosso guia de solução de problemas e perguntas frequentes para resolver vários deles
Você sabia que a redefinição de senha tem um conjunto avançado de orientações sobre solução de problemas e uma seção de perguntas frequentes? É bem possível que você consiga encontrar uma resposta às suas dúvidas nos links abaixo.

Além disso, você também pode usar a folha **"Suporte e solução de problemas"** no [Portal do Azure](https://portal.azure.com) para acessar um conjunto avançado de conteúdo de solução de problemas, diretamente da experiência de usuário administrativo de gerenciamento de senha encontrada em **Azure Active Directory** -> **Usuários e Grupos** -> **Redefinição de Senha** -> **Suporte e Solução de problemas** no painel de navegação à esquerda.

Links para orientações de solução de problemas de redefinição de senha e perguntas frequentes:
* [Solucionar problemas do Gerenciamento de Senhas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot)
* [Perguntas frequentes sobre Gerenciamento de Senhas](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-faq)

### <a name="tip-9-troubleshoot---if-you-still-need-help-include-enough-information-for-us-to-assist-you"></a>Tip 9: SOLUÇÃO DE PROBLEMAS - Se ainda precisar de ajuda, inclua informações suficientes para podermos ajudá-lo
Se você ainda precisar de ajuda para solucionar problemas, estaremos aqui. Você pode abrir um caso de suporte ou entrar em contato com a equipe de gerenciamento de contas para falar conosco diretamente. Adoraríamos ouvir o que você tem para nos dizer!

Mas, antes de nos procurar, **verifique se você reuniu todas as informações solicitadas abaixo** para que possamos ajudá-lo rapidamente!
* [Informações a serem incluídas quando precisar de ajuda](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#information-to-include-when-you-need-help)

#### <a name="ways-to-provide-password-reset-feedback"></a>Maneiras de fornecer comentários sobre redefinição de senha
* [Solicitações de recurso ou solução de problemas - postagem nos fóruns do MSDN Azure AD](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)
* [Solicitações de recurso ou solução de problemas - postagem no StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
* [Solicitações de recurso ou solução de problemas - Tweet @azuread!](https://twitter.com/azuread)
* [Somente solicitações de recursos - deixe uma nota no UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory)

## <a name="enable-users-to-reset-their-azure-ad-passwords"></a>Permitir que os usuários redefinam suas senhas Azure AD
Esta seção o orienta pelos processos de habilitação da redefinição de senha por autoatendimento para o seu diretório em nuvem do AAD, registro de usuários para a redefinição de senha por autoatendimento e, por fim, de execução de um teste de redefinição de senha por autoatendimento como um usuário.

* [Pré-requisitos de redefinição de senha por autoatendimento](#prerequisites)
* [Etapa 1: configurar a política de redefinição de senha](#step-1-configure-password-reset-policy)
* [Etapa 2: adicionar dados de contato para o usuário de teste](#step-2-add-contact-data-for-your-test-user)
* [Etapa 3: redefinir sua senha como um usuário](#step-3-reset-your-azure-ad-password-as-a-user)

### <a name="prerequisites"></a>Pré-requisitos
Antes de habilitar e usar a redefinição de senha por autoatendimento, você deve concluir os seguintes pré-requisitos:

* Criar um locatário do AAD. Para saber mais, consulte o [Introdução ao Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/)
* Obtenha uma assinatura do Azure. Para saber mais, consulte [O que é um locatário do Azure AD?](active-directory-administer.md#what-is-an-azure-ad-tenant).
* Associe o seu locatário do AAD com a sua assinatura do Azure. Para saber mais sobre as assinaturas do Azure, consulte [Como as assinaturas do Azure são associadas ao Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
* Atualize para o AD do Azure Premium, Básico ou use uma licença paga do O365. Para saber mais, consulte [Edições do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/).

  > [!NOTE]
  > Para habilitar a redefinição de senha de autoatendimento, atualize para o AD do Azure Premium, para o AD do Azure Básico ou para uma licença paga do O365.  Para habilitar a redefinição de senha de autoatendimento para seus usuários locais, atualize para o AD do Azure Premium. Para saber mais, consulte [Edições do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/). Estas informações incluem instruções detalhadas sobre como se inscrever para o Azure AD Premium ou Basic, como ativar o seu plano de licença e ativar o acesso ao Azure AD e como atribuir acesso a contas de administrador e usuário.
  >
  >
* Crie pelo menos uma conta de administrador e uma conta de usuário no diretório do AAD.
* Atribua uma licença do AAD Premium ou Básico ou uma licença paga do O365 às contas de administrador e de usuário que você criou.

### <a name="step-1-configure-password-reset-policy"></a>Etapa 1: configurar a política de redefinição de senha
Para configurar a política de redefinição de senha de usuário, conclua as seguintes etapas:

1. Abra um navegador de sua escolha e acesse o [Portal Clássico do Azure](https://manage.windowsazure.com).
2. No [Portal Clássico do Azure](https://manage.windowsazure.com), localize a **extensão do Active Directory** na barra de navegação à esquerda.

   ![Gerenciamento de Senhas no AD do Azure][001]
3. Na guia **Diretório** , clique no diretório no qual você deseja configurar a política de redefinição de senha de usuário, por exemplo, Wingtip Toys.

    ![][002]
4. Clique na guia **Configurar** .

   ![][003]

5. Na guia **Configurar**, role para baixo até a seção **política de redefinição de senha de usuário**.  Aqui é onde você configura todos os aspectos da política de redefinição de senha de usuário para um determinado diretório. *Se você não vir a guia de Configuração, verifique se você se inscreveu no Azure Active Directory Premium ou Basic e __atribuiu uma licença__ à conta de administrador que está configurando este recurso.*  

   > [!NOTE]
   > **A política que você define aplica-se somente a usuários finais na sua organização, e não aos administradores**. Por motivos de segurança, a Microsoft controla a política de redefinição de senha para administradores. A política atual para administradores exige dois desafios – Telefone Celular e Endereço de Email.

   ![][004]
6. Para configurar a política de redefinição de senha do usuário, deslize o botão de alternância **usuários habilitados para redefinição de senha** para a configuração **sim**.  Isso revela vários outros controles que permitem que você configure o modo como esse recurso funciona no seu diretório.  Fique à vontade para personalizar como achar melhor a redefinição de senha.  Se desejar saber mais sobre o que cada controle de política de redefinição de senha faz, consulte [Personalizar: Gerenciamento de Senhas do Azure AD](active-directory-passwords-customize.md).

   ![][005]
7. Depois de configurar a sua política de redefinição de senha de usuário conforme desejado para o seu locatário, clique no botão **Salvar** na parte inferior da tela.

   > [!NOTE]
   > É recomendada uma política de redefinição de senha de usuário de dois desafios, para que você possa ver como a funcionalidade opera no caso mais complexo.
   >
   >

   ![][006]

   > [!NOTE]
   > **A política que você define aplica-se somente a usuários finais na sua organização, e não aos administradores**. Por motivos de segurança, a Microsoft controla a política de redefinição de senha para administradores. A política atual para administradores exige dois desafios – Telefone Celular e Endereço de Email.
   >
   >

### <a name="step-2-add-contact-data-for-your-test-user"></a>Etapa 2: adicionar dados de contato para o usuário de teste
Você tem várias opções para especificar o modo como dados de usuários na sua organização são usados para redefinição de senha.

* Editar usuários no [Portal Clássico do Azure](https://manage.windowsazure.com) ou no [Portal de Administração do Office 365](https://portal.microsoftonline.com)
* Usar o AAD Connect para sincronizar propriedades do usuário no Azure AD a partir de um domínio local do Active Directory
* Usar o Windows PowerShell para editar propriedades de usuário
* Permitir que os usuários registrem seus próprios dados guiando-os para o portal de registro em [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* Exigir que os usuários se registrem para redefinição de senha ao entrarem no Painel de Acesso em [http://myapps.microsoft.com](http://myapps.microsoft.com) definindo a opção de configuração de SSPR **Exigir que os usuários se registrem ao entrarem no painel de acesso** como **Sim**.

Se desejar saber mais sobre que dados são usados pela redefinição de senha, bem como quaisquer requisitos de formatação para esses dados, consulte [Quais dados são usados pela redefinição de senha?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### <a name="to-add-user-contact-data-via-the-user-registration-portal"></a>Para adicionar dados de contato de usuário por meio do Portal de Registro de Usuário
1. Para usar o portal de registro de redefinição de senha, você deve fornecer aos usuários da sua organização um link para essa página ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) ou habilitar a opção para exigir que os usuários se registrem automaticamente.  Depois que clicarem nesse link, eles são solicitados a entrar com uma conta organizacional.  Depois de fazer isso, eles veem a seguinte página:

   ![][007]
2. Aqui, os usuários podem fornecer e verificar seus telefones celulares, endereços de email alternativos ou perguntas de segurança.  Este é um exemplo de verificação de telefone celular.

   ![][008]
3. Depois que um usuário especificar essas informações, a página será atualizada para indicar que as informações são válidas (ofuscadas abaixo).  Clicando nos botões **concluir** ou **cancelar**, o usuário será levado para o Painel de Acesso.

   ![][009]
4. Quando um usuário confirmar essas duas informações, seu perfil será atualizado com os dados fornecidos.  Neste exemplo, o número do **Telefone Comercial** foi especificado manualmente, portanto o usuário também pode usá-lo como um método de contato para redefinir sua senha.

   ![][010]

### <a name="step-3-reset-your-azure-ad-password-as-a-user"></a>Etapa 3: redefinir sua senha do Azure AD como um usuário
Agora que você configurou uma política de redefinição de usuário e especificou detalhes de contato para o usuário, esse usuário pode executar uma redefinição de senha por autoatendimento.

#### <a name="to-perform-a-self-service-password-reset"></a>Para executar uma redefinição de senha por autoatendimento
1. Se você visitar um site como [**portal.microsoftonline.com**](http://portal.microsoftonline.com), verá uma tela de logon como a abaixo.  Clique no link **Não consegue acessar sua conta?** para testar a interface de usuário da redefinição de senha.

   ![][011]
2. Depois de clicar em **Não consegue acessar sua conta?**, você será direcionado a uma nova página que solicitará a **ID de usuário** para a qual você deseja redefinir uma senha.  Insira a sua **ID de usuário** de teste aqui, passe o captcha e clique em **avançar**.

   ![][012]
3. Como o usuário especificou um **telefone comercial**, um **telefone celular** e um **email alternativo** neste caso, você verá que ele recebe todas essas opções para passar o primeiro desafio.

   ![][013]
4. Nesse caso, opte por **ligar** para o **telefone comercial** primeiro.  Observe que ao selecionar um método baseado em telefone, os usuários serão solicitados a **verificar seus números de telefone** antes de poderem redefinir suas senhas.  Isso serve para impedir que pessoas mal-intencionadas enviem spam para números de telefone dos usuários da sua organização.

   ![][014]
5. Assim que o usuário confirmar o número de telefone, clicar em ligar fará com que um controle giratório seja exibido e que o telefone toque.  Uma mensagem será reproduzida após ele atender o telefone, indicando que o **usuário deverá pressionar "#"** para verificar sua conta.  Pressionar esta tecla verificará automaticamente se o usuário passou no primeiro desafio e avançará a interface de usuário para a segunda etapa de verificação.

   ![][015]
6. Após passar no primeiro desafio, a interface de usuário é atualizada automaticamente para removê-lo da lista de escolhas do usuário.  Neste caso, como você usou primeiro o seu **Telefone Comercial**, somente o **Telefone Celular** e o **Email Alternativo** permanecem como opções válidas para serem usadas como o desafio da segunda etapa de verificação.  Clique na opção **Enviar email para o meu email alternativo** .  Depois que tiver feito isso, pressionar Email enviará um email para o email alternativo registrado.

   ![][016]
7. Aqui está um exemplo de um email que os usuários verão. Observe a marca do locatário:

   ![][017]
8. Após o email chegar, a página será atualizada e você poderá inserir a verificação encontrada no email na caixa de entrada mostrada abaixo.  Após inserir um código correto, o botão Avançar acende e você consegue passar na segunda etapa de verificação.

   ![][018]
9. Após atender os requisitos da política organizacional, você pode escolher uma nova senha.  A senha é validada se atender aos requisitos de senha "forte" do AAD (consulte [Política de senha no Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)), e um validador de força é exibido para indicar ao usuário se a senha digitada atende a essa política.

   ![][019]
10. Após fornecer senhas correspondentes que atendem à política organizacional, sua senha é redefinida e você pode entrar com a sua nova senha imediatamente.

    ![][020]

## <a name="enable-users-to-reset-or-change-their-ad-passwords"></a>Permitir que os usuários redefinam ou alterem suas senhas do AD
Esta seção o orienta pela configuração da redefinição de senha para gravar senhas novamente em Active Directory local.

* [Pré-requisitos de Write-back de Senha](#writeback-prerequisites)
* [Etapa 1: baixar a versão mais recente do Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
* [Etapa 2: habilitar Write-back de Senha no Azure AD Connect através da interface de usuário ou do PowerShell e verificar](#step-2-enable-password-writeback-in-azure-ad-connect)
* [Etapa 3: configurar o seu firewall](#step-3-configure-your-firewall)
* [Etapa 4: configurar as permissões apropriadas](#step-4-set-up-the-appropriate-active-directory-permissions)
* [Etapa 5: redefinir a sua senha do AD como um usuário e verificar](#step-5-reset-your-ad-password-as-a-user)

### <a name="writeback-prerequisites"></a>Pré-requisitos de write-back
Antes de poder habilitar e usar o Write-back de Senha, você deve concluir os seguintes pré-requisitos:

* Ter um locatário do Azure AD com o Azure AD Premium ativado.  Para saber mais, confira [Edições do Active Directory do Azure](active-directory-editions.md).
* Você deve conectar o Azure AD Connect ao Emulador de Controlador de Domínio Primário para que o write-back de senha funcione.  Se preciso, você pode configurar o Azure AD Connect para usar um Controlador de Domínio Primário, clicando com o botão direito nas **propriedades** do conector de sincronização do Active Directory e selecionando **configurar partições de diretório**. Em seguida, procure a seção **configurações de conexão do controlador de domínio** e marque a caixa **usam somente controladores de domínio preferenciais**.  Observação: se o controlador de DC Preferencial não for um emulador de PDC, o Azure AD Connect tentará ainda alcançar o PDC para executar o write-back de senha.
* Configurar e habilitar a redefinição de senha no seu locatário.  Para saber mais, consulte [Permitir que os usuários redefinam suas senhas do Azure AD](#enable-users-to-reset-their-azure-ad-passwords)
* Ter pelo menos uma conta de administrador e uma conta de usuário de teste com uma licença do Azure AD Premium que você pode usar para testar esse recurso.  Para saber mais, consulte [Edições do Active Directory do Azure](active-directory-editions.md).

  > [!NOTE]
  > Certifique-se de que a conta de administrador usada para habilitar o Write-back de Senha é uma conta de administrador de nuvem (criado no Azure AD), e não uma conta federada (criado no AD local e sincronizada com o Azure AD).
  >
  >
* Ter uma implantação local única ou de várias florestas do AD executando o Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 ou Windows Server 2012 R2 com service packs mais recentes instalados.

  > [!NOTE]
  > Se você estiver executando uma versão anterior do Windows Server 2008 ou 2008 R2, você pode usar este recurso, mas será necessário [Baixar e instalar a atualização KB 2386717](https://support.microsoft.com/kb/2386717) antes de poder aplicar a política de senha local do AD na nuvem.
  >
  >
* Ter a ferramenta Azure AD Connect instalada e preparar o ambiente do AD para sincronização para a nuvem.  Para saber mais, consulte [Usar a sua infraestrutura de identidade local na nuvem](connect/active-directory-aadconnect.md).

  > [!NOTE]
  > Antes de testar o write-back de senha, certifique-se de primeiro concluir uma importação e sincronização completas no AD e no AD do Azure no AD do Azure
  >
  >
* Se estiver usando o Azure AD Sync ou o Azure AD Connect, a **TCP 443** de saída (e, em alguns casos, as **TCP 9350 a 9354**) precisam estar abertas.  Veja [Etapa 3: configurar seu firewall](#step-3-configure-your-firewall) para obter mais informações. Não há mais suporte para o uso do DirSync para esse cenário.  Se ainda estiver usando o DirSync, atualize para a versão mais recente do Azure Connect AD antes de implantar o write-back de senha.

  > [!NOTE]
  > É altamente recomendável que qualquer pessoa que use as ferramentas Azure AD Sync ou DirSync atualizem para a versão mais recente do Azure AD Connect para garantir a melhor experiência possível e obter novos recursos conforme são lançados.
  >
  >

### <a name="step-1-download-the-latest-version-of-azure-ad-connect"></a>Etapa 1: baixar a versão mais recente do Azure AD Connect
O Write-back de Senha está disponível nas versões do Azure AD Connect ou na ferramenta Azure AD Sync com o número de versão **1.0.0419.0911** ou superior.  O Write-back de Senha com o desbloqueio de conta automático está disponível nas versões do Azure AD Connect ou na ferramenta Azure AD Sync com o número de versão **1.0.0485.0222** ou superior. Se você estiver executando uma versão mais antiga, atualize para pelo menos essa versão antes de continuar. [Clique aqui para baixar a versão mais recente do Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect).

#### <a name="to-check-the-version-of-azure-ad-sync"></a>Para verificar a versão do Azure AD Sync
1. Navegue até **%ProgramFiles%\Azure Active Directory Sync\**.
2. Encontre o executável **ConfigWizard.exe** .
3. Clique com o botão direito no executável e selecione a opção **Propriedades** no menu de contexto.
4. Clique na guia **Detalhes** .
5. Encontre o campo **Versão do arquivo** .

   ![][021]

Se este número de versão for maior que ou igual a **1.0.0419.0911** ou se você estiver instalando o Azure AD Connect, poderá passar para a [Etapa 2: habilitar o Write-back de Senha no Azure AD Connect através da interface de usuário ou do PowerShell e verificar](#step-2-enable-password-writeback-in-azure-ad-connect).

> [!NOTE]
> Se esta for a primeira vez que você instala a ferramenta Azure AD Connect, é recomendável que você siga algumas práticas úteis para preparar o ambiente para a sincronização de diretórios.  Antes de instalar a ferramenta de Azure AD Connect, você deve ativar a sincronização de diretórios no [Portal de Administração do Office 365](https://portal.microsoftonline.com) ou no [Portal Clássico do Azure](https://manage.windowsazure.com).  Para saber mais, consulte [Gerenciar o Azure AD Connect](active-directory-aadconnect-whats-next.md).
>
>

### <a name="step-2-enable-password-writeback-in-azure-ad-connect"></a>Etapa 2: habilitar o Write-back de Senha no Azure AD Connect
Agora que você já baixou a ferramenta Azure AD Connect, você está pronto para habilitar o Write-back de Senha.  Você pode fazer isso de duas maneiras.  Você pode habilitar o Write-back de Senha na tela de recursos opcionais do assistente de configuração do Azure Connect AD ou habilitá-lo por meio do Windows PowerShell.

#### <a name="to-enable-password-writeback-in-the-configuration-wizard"></a>Para habilitar o Write-back de Senha no assistente de configuração
1. No seu **Computador do Directory Sync**, abra o assistente de configuração do **Azure AD Connect**.
2. Avance pelas etapas até chegar à tela de configuração **recursos opcionais** .
3. Marque a opção **Write-back de Senha** .

   ![][022]
4. Conclua o assistente, a página final resumirá as alterações e incluirá a alteração de configuração de Write-back de Senha.

> [!NOTE]
> Você pode desabilitar o Write-back de Senha a qualquer momento reexecutando o assistente e desmarcando o recurso, ou definindo a configuração **Gravar senhas novamente no Diretório Local** definida como **Não** na seção **Política de Redefinição de Senha de Usuário** da guia **Configurar** no [Portal Clássico do Azure](https://manage.windowsazure.com).  Para saber mais sobre como personalizar a sua experiência de redefinição de senha, confira [Personalizar: Gerenciamento de Senhas do Azure AD](active-directory-passwords-customize.md)
>
>

#### <a name="to-enable-password-writeback-using-windows-powershell"></a>Para habilitar o Write-back de Senha usando o Windows PowerShell
1. No seu **Computador do Directory Sync**, abra uma nova **janela do Windows PowerShell com privilégios elevados**.
2. Se o módulo ainda não tiver carregado, digite o comando `import-module ADSync` para carregar os cmdlets do Azure AD Connect na sua sessão atual.
3. Obter a lista de Conectores AAD no seu sistema executando o cmdlet `Get-ADSyncConnector` e armazenando os resultados no `$aadConnectorName``$aadConnectorName = Get-ADSyncConnector|where-object {$_.name -like "*AAD"}`
4. Para obter o status atual do write-back do conector atual, execute o seguinte cmdlet: `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name`
5. Habilite o Write-back de Senha executando o cmdlet: `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName.name –Enable $true`

> [!NOTE]
> Se for solicitada uma credencial, certifique-se de que a conta de administrador que você especificar como AzureADCredential é uma **conta de administrador na nuvem (criada no Azure AD)**, e não uma conta federada (criado no AD local e sincronizada com o Azure AD.
>
> [!NOTE]
> Você pode desabilitar o Write-back de Senha por meio do PowerShell repetindo as mesmas instruções acima, mas passando `$false` ou definindo a configuração **Gravar senhas novamente no Diretório Local** como **Não** na seção **Política de Redefinição de Senha do Usuário** da guia **Configurar** no [Portal Clássico do Azure](https://manage.windowsazure.com).
>
>

#### <a name="verify-that-the-configuration-was-successful"></a>Verificar se a configuração foi bem-sucedida
Se a configuração for bem-sucedida, você verá a mensagem “Write-back de redefinição de senha habilitado” na janela do Windows PowerShell, ou uma mensagem de êxito na interface de usuário de configuração.

Você também pode verificar o serviço foi instalado corretamente abrindo o Visualizador de eventos, navegando até o log de eventos do aplicativo e procurando o evento **31005 - OnboardingEventSuccess** da origem **PasswordResetService**.

  ![][023]

### <a name="step-3-configure-your-firewall"></a>Etapa 3: configurar o seu firewall
Depois de você ter habilitado o Write-back de Senha, precisará verificar se o computador que executa o Azure AD Connect pode acessar os serviços de nuvem da Microsoft para receber as solicitações de write-back de senha. Esta etapa envolve atualizar as regras de conexão em seus dispositivos de rede (servidores proxy, firewalls etc.) para permitir conexões de saída para certas [URLs de propriedade da Microsoft e endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US) nas portas de rede específicas. Essas alterações podem variar dependendo da versão da ferramenta Azure AD Connect. Para obter mais contexto, você pode ler mais sobre [como funciona o write-back de senha](active-directory-passwords-learn-more.md#how-password-writeback-works) e [ modelo de segurança de write-back de senha](active-directory-passwords-learn-more.md#password-writeback-security-model).

#### <a name="why-do-i-need-to-do-this"></a>Por que preciso fazer isto?

Para fazer o Write-back de Senha funcionar corretamente, o computador que executa o Azure AD Connect precisa ser capaz de se comunicar com o Serviço de Redefinição de Senha, bem como o Barramento de Serviço do Azure.

Para a ferramenta Azure AD Connect **1.1.443.0** e superior:

- A versão mais recente da ferramenta Azure AD Connect precisará de acesso **HTTPS de saída** a:
    - *passwordreset.microsoftonline.com*
    - *.servicebus.windows.net*

Para as versões da ferramenta Azure AD Connect **1.0.8667.0** a **1.1.380.0**:

- **Opção 1:** permitir todas as conexões HTTPS de saída na porta 443 (usando a URL ou o Endereço IP).
    - Quando usar isto:
        - Use esta opção se quiser a configuração mais simples que não precisa ser atualizada quando os intervalos de IP do Datacenter do Azure mudam com o tempo.
    - Etapas necessárias:
        - permitir todas as conexões HTTPS de saída na porta 443 usando a URL ou o endereço IP.
<br><br>
- **Opção 2:** permitir as conexões HTTPS de saída em intervalos de IP e URLs específicos
    - Quando usar isto:
        - Use esta opção se você estiver em um ambiente de rede restrito ou não se sente confortável em permitir conexões de saída.
        - Nessa configuração, para o write-back de senha continuar a funcionar, você precisará garantir que seus dispositivos de rede sejam atualizados semanalmente com os IPs mais recentes na lista de Intervalos de IP do Datacenter do Microsoft Azure. Esses intervalos de IP estão disponíveis como um arquivo XML que é atualizado toda quarta-feira (Hora do Pacífico) e entram em vigor na segunda-feira seguinte (Hora do Pacífico).
    - Etapas necessárias:
        - Permitir todas as conexões HTTPS de saída para *.servicebus.windows.net
        - Permitir todas as conexões HTTPS de saída para todos os IPs na lista de Intervalos de IP do Datacenter do Microsoft Azure e manter essa configuração atualizada semanalmente. A lista está disponível para download [aqui](https://www.microsoft.com/download/details.aspx?id=41653).

> [!NOTE]
> Se você configurou o Write-back de Senha seguindo as instruções acima e não vê nenhum erro no log de eventos do Azure AD Connect, mas está recebendo erros de conectividade durante o teste, pode ser o caso de um dispositivo de rede em seu ambiente estar bloqueando as conexões HTTPS para os endereços IP. Por exemplo, embora uma conexão com *https://*.servicebus.windows.net* seja permitida, uma conexão com um endereço IP específico dentro desse intervalo pode estar bloqueada. Para resolver esse problema, você precisará configurar seu ambiente de rede para permitir todas as conexões HTTPS de saída na porta 443 para qualquer endereço IP ou URL (Opção 1 acima) ou trabalhará com sua equipe de rede para permitir explicitamente as conexões HTTPS com endereços IP específicos (Opção 2 acima).

**Para as versões anteriores:**

- Permitir conexões TCP de saída na porta 443, 9350-9354 e porta 5671
- Permitir conexões de saída para *https://ssprsbprodncu-sb.accesscontrol.windows.net/*

> [!NOTE]
> Se você estiver em uma versão do Azure AD Connect anterior a 1.0.8667.0, a Microsoft recomenda atualizar para a [versão mais recente do Azure Connect AD](https://www.microsoft.com/download/details.aspx?id=47594), que inclui vários aprimoramentos da rede de write-back para facilitar a configuração.

Após configurar os dispositivos de rede, reinicialize o computador executando a ferramenta Azure AD Connect.

#### <a name="idle-connections-on-azure-ad-connect-114430-and-up"></a>Conexões ociosas no Azure AD Connect (1.1.443.0 e superior)
A ferramenta Azure AD Connect enviará pings/keepalives periódicos para pontos de extremidade do Barramento de Serviço a fim de garantir que as conexões ficarão ativas. Se a ferramenta detectar que muitas conexões estão sendo encerradas, ele aumentará a frequência de pings automaticamente para o ponto de extremidade. O menor 'intervalo ping' será de 1 ping a cada 60 segundos. No entanto, **recomendamos que os proxies/firewalls permitam a persistência das conexões ociosas por pelo menos 2 a 3 minutos.** \*Para versões mais antigas, sugerimos quatro minutos ou mais.

### <a name="step-4-set-up-the-appropriate-active-directory-permissions"></a>Etapa 4: configurar as permissões apropriadas do Active Directory
Para cada floresta que contenha os usuários cujas senhas serão redefinidas, se X for a conta que foi especificada para essa floresta no assistente de configuração (durante a configuração inicial), X deverá receber os direitos estendidos **Redefinir Senha**, **Alterar Senha**, **Permissões de Gravação** no `lockoutTime`, e **Permissões de Gravação** na `pwdLastSet` no objeto raiz de cada domínio da floresta OU nas OUs de usuário que devam estar no escopo do SSPR.  Você pode usar a última opção se deseja definir o escopo de suas permissões de redefinição para apenas um conjunto específico de objetos de usuário, caso isso não seja possível em relação à raiz do domínio. A direita deve ser marcada como herdada por todos os objetos de usuário.  

Se não tiver certeza sobre qual conta a descrição acima se refere, abra a interface do usuário da configuração do Azure Active Directory Connect e clique na opção **Examinar sua Solução** .  A conta a qual você precisa adicionar a permissão está sublinhada em vermelho na captura de tela abaixo.

**<font color="red">Certifique-se de definir essa permissão para cada domínio em cada floresta de seu sistema, caso contrário o write-back de senha não funcionará corretamente.</font>**

  ![][032]

  Definir essas permissões permite que a conta de serviço MA de cada floresta gerencie senhas em nome de contas de usuário na floresta. Se você não atribuir essas permissões, em seguida, mesmo que o write-back pareça estar configurado corretamente, os usuários encontrarão erros ao tentar gerenciar suas senhas locais na nuvem. Aqui estão as etapas detalhadas sobre como você pode fazer isso usando o snap-in de gerenciamento **Usuários e Computadores do Active Directory** :

> [!NOTE]
> Pode levar até uma hora para que essas permissões sejam replicadas em todos os objetos no diretório.
>
>

#### <a name="to-set-up-the-right-permissions-for-writeback-to-occur"></a>Para configurar as permissões corretas para que o write-back ocorra
1. Abra o snap-in **Usuários e Computadores do Active Directory** com uma conta que tenha as permissões de administração de domínio apropriadas.
2. Na opção **Exibir Menu**, verifique se **Recursos Avançados** está habilitado.
3. No painel esquerdo, clique com o botão direito no objeto que representa a raiz do domínio.
4. Clique na guia **Segurança** .
5. Em seguida, clique em **Avançado**.

   ![][024]
6. Na guia **Permissões**, clique em **Adicionar**.

   ![][025]
7. Selecione a conta para a qual você deseja conceder permissões (é a mesma conta que foi especificada ao configurar a sincronização para essa floresta).
8. No menu suspenso na parte superior, selecione **Objetos descendentes de Usuário**.
9. Na caixa de diálogo **Entrada de Permissão** exibida, marque as caixas **Redefinir Senha**, **Alterar Senha**, **Permissões de Gravação** no `lockoutTime`, e **Permissões de Gravação** na `pwdLastSet`.

   ![][026]
   ![][027]
   ![][028]
10. Em seguida, clique em **Aplicar/Ok** por meio de todas as caixas de diálogo abertas.

### <a name="step-5-reset-your-ad-password-as-a-user"></a>Etapa 5: redefinir sua senha do AD como um usuário
Agora que o Write-back de senha foi habilitado, você pode testar se ele funciona redefinindo a senha de um usuário cuja conta foi sincronizada para o seu locatário de nuvem.

#### <a name="to-verify-password-writeback-is-working-properly"></a>Para verificar se o Write-back de Senha está funcionando corretamente
1. Navegue até [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) ou vá para qualquer tela de logon de ID organizacional e clique no link **Não consegue acessar sua conta?** .

   ![][029]
2. Agora você deve ver uma nova página que solicita a ID de usuário para a qual você deseja redefinir uma senha. Insira a sua ID de usuário de teste e prossiga com o fluxo de redefinição de senha.
3. Depois de redefinir a sua senha, você verá uma tela semelhante a esta. Isso significa que você redefiniu a sua senha com êxito em seus diretórios locais e/ou na nuvem.

   ![][030]
4. Para verificar se a operação foi bem-sucedida ou diagnosticar erros, vá para o **computador do Directory Sync**, abra **Visualizador de Eventos**, navegue até o **log de eventos do aplicativo** e procure pelo evento **31002 - PasswordResetSuccess** da fonte **PasswordResetService** do seu usuário de teste.

   ![][031]



## <a name="next-steps"></a>Próximas etapas
Veja abaixo links para todas as páginas de documentação sobre Redefinição de Senha do AD do Azure:

* **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md#reset-my-password).
* [**Como funciona**](active-directory-passwords-how-it-works.md)- saiba mais sobre os seis diferentes componentes do serviço e o que cada um deles faz
* [**Personalizar**](active-directory-passwords-customize.md)- aprenda a personalizar a aparência e o comportamento do serviço de acordo com as necessidades de sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - aprenda a implantar rapidamente e gerenciar com eficiência as senhas em sua organização
* [**Obter percepções**](active-directory-passwords-get-insights.md) – saiba mais sobre nossos recursos integrados de relatórios
* [**Perguntas frequentes**](active-directory-passwords-faq.md) - obtenha respostas para perguntas frequentes
* [**Solução de problemas**](active-directory-passwords-troubleshoot.md) – aprenda a solucionar rapidamente os problemas com o serviço
* [**Saiba mais**](active-directory-passwords-learn-more.md) – aprofunde-se nos detalhes técnicos do funcionamento do serviço

[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"
[032]: ./media/active-directory-passwords-getting-started/032.jpg "Image_032.jpg"

