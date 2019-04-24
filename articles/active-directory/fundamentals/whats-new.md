---
title: O que há de novo? Notas sobre a versão – Azure Active Directory | Microsoft Docs
description: Conheça as novidades do Azure Active Directory, como as últimas notas sobre a versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16f684674e69113a869346d5de9ef1d50734f01b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60351914"
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory

>Seja notificado sobre quando visitar novamente esta página para atualizações, copiando e colando nesta URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` em seu ![ícone de leitor de RSS feed](./media/whats-new/feed-icon-16x16.png) leitor de feed.

O Azure AD recebe melhorias de forma contínua. Para se manter atualizado com os desenvolvimentos mais recentes, este artigo fornece informações sobre:

- As versões mais recentes
- Problemas conhecidos
- Correções de bug
- Funcionalidades preteridas
- Planos de alterações

Esta página é atualizada mensalmente; portanto, visite-a regularmente. Se você estiver procurando itens que têm mais de seis meses, poderá encontrá-los nos [Arquivos de O que há de novo no Azure Active Directory](whats-new-archive.md).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Aumentar a segurança usando a política de acesso condicional baseado em proteção de aplicativo no Azure AD (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Acesso condicional com base em proteção do aplicativo agora está disponível usando o **exigir proteção de aplicativo** política. Essa nova política ajuda a aumentar a segurança de sua organização, ajudando a evitar:

- Usuários obtenham acesso a aplicativos sem uma licença do Microsoft Intune.

- Usuários não é possível obter uma política de proteção de aplicativo do Microsoft Intune.

- Usuários obtenham acesso a aplicativos sem uma política de proteção de aplicativo configurado do Microsoft Intune.

Para obter mais informações, consulte [como exigir a política de proteção do aplicativo para acesso de aplicativo de nuvem com acesso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

## <a name="march-2019"></a>Março de 2019

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Novo suporte para o Azure AD único logon e o acesso condicional no Microsoft Edge (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Nós aprimoramos o nosso suporte do Azure AD para o Microsoft Edge, inclusive fornecendo o novo suporte para acesso condicional e logon único do Azure AD. Se você tiver usado anteriormente o Microsoft Intune Managed Browser, agora você pode usar Microsoft Edge em vez disso.

Para obter mais informações sobre como configurar e gerenciar seus dispositivos e aplicativos usando o acesso condicional, consulte [exigem dispositivos para acesso de aplicativo de nuvem com acesso condicional gerenciados](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/require-managed-devices) e [exigir aplicativos de cliente para a nuvem aprovados acesso a aplicativos com acesso condicional](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/app-based-conditional-access). Para obter mais informações sobre como gerenciar o acesso usando o Microsoft Edge com políticas do Microsoft Intune, consulte [gerenciar o acesso à Internet usando um navegador protegido por política do Microsoft Intune](https://docs.microsoft.com/en-us/intune/app-configuration-managed-browser).

---

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Estrutura de experiência de identidade e a política personalizada do suporte no Azure Active Directory B2C agora está disponível (GA)

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode criar políticas personalizadas no B2C do Azure AD, incluindo as tarefas a seguir, que têm suporte em escala e, em nosso SLA do Azure:

- Criar e carregar percursos do usuário de autenticação personalizada usando políticas personalizadas.

- Descrever percursos do usuário passo a passo como trocas entre provedores de declarações.

- Definir a ramificação condicional em percursos do usuário.

- Transforme e mapear declarações para uso em decisões em tempo real e comunicações.

- Use os serviços habilitados para API REST no seu percurso do usuário de autenticação personalizada. Por exemplo, com provedores de email, os CRMs e sistemas de autorização do proprietário.

- Federar com provedores de identidade que estão em conformidade com o protocolo OpenIDConnect. Por exemplo, com vários locatários do Azure AD, provedores de conta social ou provedores de verificação de dois fatores.

Para obter mais informações sobre como criar políticas personalizadas, consulte [notas do desenvolvedor para políticas personalizadas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leia [postagem de blog de Alex Simon, incluindo estudos de caso](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Novos aplicativos federados disponíveis na Galeria de aplicativo do Azure AD - março de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros

Março de 2019, adicionamos que suportam a esses novos 14 aplicativos com federação na Galeria do aplicativo:

[Delegado de Exchange Mobile ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Sistema de auditoria com base em explicação](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [questões de desempenho do Powerschool](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [íris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizontes](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Novo Zscaler e Atlassian na Galeria do Azure AD - março de 2019 de conectores de provisionamento

**Tipo:** New recurso  
**Categoria de serviço:** Provisionamento de aplicativo  
**Funcionalidade do produto:** Integração de terceiros

Automatize a criação, atualização e exclusão de contas de usuário para os seguintes aplicativos:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](http://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](http://aka.ms/ZscalerTwoProvisioning), [Zscaler três](http://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](http://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](http://aka.ms/atlassianCloudProvisioning)

Para obter mais informações sobre como proteger melhor a sua organização por meio de provisionamento de conta de usuário automatizado, consulte [automatizar o provisionamento de usuário para aplicativos SaaS com o Azure AD](http://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar e gerenciar seus grupos do Office 365 excluídos no portal do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Agora você pode exibir e gerenciar seus grupos do Office 365 excluídos no portal do Azure AD. Essa alteração ajuda você a ver quais grupos estão disponíveis para restaurar, juntamente com permitindo que você permanentemente, exclua todos os grupos que não são necessários pela sua organização.

Para obter mais informações, consulte [restauração expirou ou grupos excluídos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>O logon único agora está disponível para aplicativos protegidos pelo Azure AD SAML no local por meio do Proxy de aplicativo (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Agora você pode fornecer uma experiência de logon único (SSO) para aplicativos SAML autenticadas, juntamente com o acesso remoto a esses aplicativos por meio do Proxy de aplicativo no local. Para obter mais informações sobre como configurar o SSO do SAML com seus aplicativos locais, consulte [SAML SSO para aplicativos locais com o Proxy de aplicativo (visualização)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Aplicativos de cliente em loops de solicitação serão interrompidos para melhorar a confiabilidade e experiência do usuário

**Tipo:** New recurso  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

Aplicativos cliente incorretamente podem emitir centenas das mesmas solicitações de logon em um curto período de tempo. Essas solicitações, se elas são bem-sucedidas ou não, contribuem para uma experiência de usuário insatisfatória e cargas de trabalho maior para o IDP, aumenta a latência para todos os usuários e reduz a disponibilidade do IDP.

Essa atualização envia um `invalid_grant` erro: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` para aplicativos cliente que emitem solicitações duplicadas várias vezes em um curto período de tempo, além do escopo da operação normal. Aplicativos cliente que encontram esse problema devem mostrar um prompt interativo, exigir que o usuário fazer logon novamente. Para obter mais informações sobre essa alteração e sobre como corrigir seu aplicativo se ele encontrar esse erro, consulte [o que há de novo para autenticação?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nova experiência do usuário Logs de auditoria agora disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Criamos um novo AD do Azure **logs de auditoria** para ajudar a melhorar a legibilidade e a como você pesquisa por suas informações. Para ver o novo **logs de auditoria** página, selecione **logs de auditoria** no **atividade** seção do AD do Azure.

![Nova página de logs de auditoria, com informações de exemplo](media/whats-new/audit-logs-page.png)

Para obter mais informações sobre os novos **logs de auditoria** página, consulte [auditoria de relatórios de atividade no portal do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Novos avisos e diretrizes para ajudar a evitar o bloqueio acidental do administrador de políticas de acesso condicional configuradas incorretamente

**Tipo:** Recurso alterado  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Para ajudar a impedir que os administradores bloqueiem a mesmos acidentalmente fora de seus próprios locatários por meio de políticas de acesso condicional configuradas incorretamente, criamos novos avisos e as diretrizes atualizadas no portal do Azure. Para obter mais informações sobre as novas diretrizes, consulte [quais são as dependências de serviço no acesso condicional do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Termos do usuário final melhorada de experiências de uso em dispositivos móveis

**Tipo:** Recurso alterado  
**Categoria de serviço:** Termos de Uso  
**Funcionalidade do produto:** Governança

Nós atualizamos nossos termos existentes das experiências de uso para ajudar a melhorar a como você examine e concorde com os termos de uso em um dispositivo móvel. Agora você pode ampliar e reduzir, voltar, baixar as informações e selecione hiperlinks. Para obter mais informações sobre os termos de uso atualizados, consulte [termos do recurso de uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Disponível de experiência de download de novos logs de atividades do Azure AD

**Tipo:** Recurso alterado  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Agora você pode baixar grandes quantidades de logs de atividades diretamente do portal do Azure. Essa atualização permite que você:

- Baixe até 250.000 linhas.

- Seja notificado após a conclusão do download.

- Personalize o nome do arquivo.

- Determine o formato de saída, JSON ou CSV.

Para obter mais detalhes sobre esse recurso, consulte [guia de início rápido: Baixar um relatório de auditoria usando o portal do Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Alterações da falha: Atualizações para a avaliação da condição pelo Exchange ActiveSync (EAS)

**Tipo:** Plano para alteração  
**Categoria de serviço:** Acesso Condicional  
**Funcionalidade do produto:** Controle de Acesso

Estamos em processo de atualização como o Exchange ActiveSync (EAS) avalia as condições a seguir:

- Local do usuário, com base no país, região ou endereço IP

- Risco de entrada

- Plataforma de dispositivos

Se você tiver usado anteriormente essas condições em suas políticas de acesso condicional, lembre-se de que o comportamento de condição pode ser alterado. Por exemplo, se você tiver usado a condição de localização do usuário em uma política, talvez a política agora está sendo ignorada com base na localização do seu usuário.

---

## <a name="february-2019"></a>fevereiro de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Criptografia do token SAML do Azure AD configurável (Versão prévia pública) 

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Agora você pode configurar qualquer aplicativo SAML com suporte para receber tokens SAML criptografados. Quando configurado e usado com um aplicativo, o AD do Azure criptografa as declarações do SAML emitidas usando uma chave pública obtida de um certificado armazenado no Azure AD.

Para obter mais informações sobre como configurar a criptografia de token SAML, consulte [criptografia do token de configurar o Azure AD SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Criar uma revisão de acesso para grupos ou aplicativos usando as Revisões de Acesso do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Revisões de acesso  
**Funcionalidade do produto:** Governança

Agora você pode incluir vários grupos ou revisão para associação de grupo ou a atribuição de aplicativo de acesso de aplicativos em um único Azure AD. Revisões de acesso com vários grupos ou aplicativos são configurados usando as mesmas configurações e todos os revisores incluídos são notificados ao mesmo tempo.

Para obter mais informações sobre como criar uma revisão de acesso usando as revisões de acesso do AD do Azure, consulte [criar uma revisão de acesso de grupos ou aplicativos em revisões de acesso do AD do Azure](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Novos Aplicativos Federados disponíveis na galeria de aplicativos do Azure AD – fevereiro de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Fevereiro de 2019, adicionamos que suportam a esses novos 27 aplicativos com federação na Galeria do aplicativo:

[Passport Euromonitor](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [acidental](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Qmlativ Skyward](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Clique em uma permissão [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [sísmicas ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Compartilhamento um sonho](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods integração de nuvem](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [conhecimento em qualquer lugar LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [Campus de UO](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dados Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Portal Netop](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud por Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp plataforma de produtividade](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Registro de MFA/SSPR combinado aprimorado

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha por autoatendimento  
**Funcionalidade do produto:** Autenticação de usuário
 
Em resposta aos comentários dos clientes, aprimoramos a experiência de visualização de registro MFA/SSPR combinada, ajudando seus usuários registrem mais rapidamente as informações de segurança para MFA e o SSPR. 

**Para ativar a experiência aprimorada para seus usuários hoje, siga estas etapas:**

1. Como um administrador global ou administrador de usuários, entre portal do Azure e acesse **Azure Active Directory > configurações do usuário > Gerenciar as configurações de recursos de visualização do painel de acesso**. 

2. No **recursos de usuários que podem usar a visualização para registrar e gerenciar informações de segurança – atualize** opção, escolha a opção Ativar os recursos para um **grupo selecionado de usuários** ou para **todos os usuários** .

Nas próximas semanas, podemos irá remover a capacidade de ativar a antiga combinado MFA/SSPR registro experiência de visualização para locatários que ainda não o tiver ativado.

**Para ver se o controle será removido do seu locatário, siga estas etapas:**

1. Como um administrador global ou administrador de usuários, entre portal do Azure e acesse **Azure Active Directory > configurações do usuário > Gerenciar as configurações de recursos de visualização do painel de acesso**.  

2.  Se o **usuários que podem usar os recursos de visualização para registrar e gerenciar informações de segurança** opção for definida como **None**, a opção será removida do seu locatário.

Independentemente se você ativou anteriormente o registro MFA/SSPR combinado antigo experiência de visualização para usuários ou não, a experiência antiga será desativada em uma data futura. Por causa disso, sugerimos que você mova para a experiência nova e aprimorada assim que possível.

Para obter mais informações sobre a experiência aprimorada de registro, consulte o [combinados de aprimoramentos avançados para o Azure AD MFA e experiência de registro de redefinição de senha](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiência de gerenciamento de política atualizada para fluxos dos usuários

**Tipo:** Recurso alterado  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Nós atualizamos o processo de criação e gerenciamento de política para fluxos de usuário (anteriormente conhecidos como internas políticas) mais fácil. Esta nova experiência agora é o padrão para todos os seus locatários do AD do Azure.

Você pode fornecer sugestões e comentários adicionais usando o Smiley ou rosto triste ícones na **enviar comentários** área na parte superior da tela do portal.

Para obter mais informações sobre a nova experiência de gerenciamento de política, consulte o [do Azure AD B2C agora tem muito mais novos recursos e personalização de JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Escolha as versões de elemento de página específicas fornecidas pelo Azure AD B2C

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode escolher uma versão específica dos elementos de página fornecida pelo Azure AD B2C. Ao selecionar uma versão específica, você pode testar suas atualizações antes que elas aparecem em uma página e você pode obter um comportamento previsível. Além disso, você pode agora optar por impor versões da página específica para permitir que as personalizações de JavaScript. Para ativar esse recurso, vá para o **propriedades** página em seus fluxos de usuário.

Para obter mais informações sobre como escolher versões específicas dos elementos da página, consulte o [do Azure AD B2C agora tem muito mais novos recursos e personalização de JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos de senha do usuário final configuráveis para B2C (GA)

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Agora você pode configurar a complexidade da senha da sua organização para seus usuários finais, em vez de precisar usar seu nativo política de senha do Azure AD. Dos **propriedades** folha de seu usuário fluxos (anteriormente conhecidos como suas políticas internas), você pode escolher uma complexidade de senha **simples** ou **forte**, ou você pode criar uma **personalizado** conjunto de requisitos.

Para obter mais informações sobre a configuração de requisito de complexidade de senha, consulte [configurar requisitos de complexidade de senhas no Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Novos modelos padrão para as experiências de autenticação de marca personalizada

**Tipo:** New recurso  
**Categoria de serviço:** B2C – Gerenciamento de Identidades do Consumidor  
**Funcionalidade do produto:** B2B/B2C

Você pode usar os novos modelos padrão, localizados na **layouts de página** folha de seus fluxos de usuário (anteriormente conhecido como as políticas internas) criar um personalizado com a marca da experiência de autenticação para seus usuários.

Para obter mais informações sobre como usar os modelos, consulte [do Azure AD B2C agora tem muito mais novos recursos e personalização de JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Janeiro de 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Colaboração do Active Directory B2B usando a autenticação de senha de uso único (Versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** B2B  
**Funcionalidade do produto:** B2B/B2C

Apresentamos a OTP (autenticação por senha de uso único) para usuários convidados de B2B que não podem ser autenticados por outros meios, tais como o Azure AD, uma MSA (conta Microsoft) ou uma federação do Google. Esse novo método de autenticação significa que os usuários convidados não precisarão criar uma nova conta Microsoft. Em vez disso, ao resgatar um convite ou acessar um recurso compartilhado, o usuário convidado poderá solicitar um código temporário que será enviado para um endereço de email. Usando esse código temporário, o usuário convidado pode continuar a conexão.

Para obter mais informações, consulte [Autenticação de senha de uso único por email (versão prévia)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e o blog [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (O Azure AD torna a colaboração e o compartilhamento perfeitos para qualquer usuário com qualquer conta).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Novas configurações de cookies do Proxy de Aplicativo do Azure AD

**Tipo:** New recurso  
**Categoria de serviço:** Proxy do aplicativo  
**Funcionalidade do produto:** Controle de Acesso

Apresentamos três novas configurações de cookie, disponíveis para seus aplicativos publicados por meio do Proxy de Aplicativo:

- **Usar Cookie Somente HTTP.** Define o sinalizador **HTTPOnly** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração fornece benefícios de segurança adicionais, como ajudar a prevenir cópia ou modificação de cookies por meio de script do lado do cliente. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar um cookie seguro.** Define o sinalizador **Secure** em seu acesso de Proxy de Aplicativo e cookies de sessão. Ativar essa configuração proporciona benefícios de segurança adicionais ao garantir que os cookies só sejam transmitidos em canais seguros TLS, como HTTPS. É recomendável ativar esse sinalizador (escolher **Sim**) para os benefícios adicionais.

- **Usar cookie persistente.** Impede que cookies de acesso expirem quando o navegador da Web é fechado. Esses cookies duram todo o tempo de vida do token de acesso. No entanto, os cookies são redefinidos se o tempo de término é atingido ou se o usuário o exclui manualmente. Recomendamos que você mantenha a configuração padrão de **Não**, apenas ativando essa configuração para aplicativos mais antigos que não compartilham cookies entre processos.

Para obter mais informações sobre os novos cookies, confira [Configurações de cookie para acessar aplicativos locais no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD – janeiro de 2019

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em janeiro de 2019, adicionamos esses 35 novos aplicativos com suporte para Federação à galeria de aplicativos:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [talento paleta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco guarda-chuva](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Administrador de acesso da Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lembrete de expiração](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR visualizador](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verbo](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital feche](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ PICCO do serviço de nuvem](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Sistema de SSO GTNexus](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [ARES para Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 para o Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas plataforma](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Novas melhorias do Azure AD Identity Protection (versão prévia pública)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Segurança de identidade e proteção

Temos o prazer de anunciar que adicionamos os seguintes aprimoramentos à oferta de versão prévia pública do Azure AD Identity Protection, incluindo:

- Uma interface do usuário mais integrada e atualizada

- APIs adicionais

- Avaliação de risco aprimorada por meio de aprendizado de máquina

- Alinhamento de todo o produto entre usuários arriscados e entradas arriscadas

Para obter mais informações sobre os aprimoramentos, confira [O que é o Azure Active Directory Identity Protection (atualizado)?](https://aka.ms/IdentityProtectionDocs) Para saber mais e compartilhar suas ideias por meio dos prompts no produto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Novo recurso de Bloqueio de Aplicativo para o aplicativo Microsoft Authenticator em dispositivos iOS e Android

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativo Microsoft Authenticator  
**Funcionalidade do produto:** Segurança de identidade e proteção

Para manter suas senhas únicas, informações do aplicativo e configurações de aplicativo mais seguras, você pode ativar o recurso de Bloqueio de Aplicativo no aplicativo Microsoft Authenticator. Ativar o Bloqueio de Aplicativo significa que você será solicitado a autenticar-se usando seu PIN ou biometria sempre que abrir o aplicativo Microsoft Authenticator.

Para obter mais informações, confira as [Perguntas frequentes do aplicativo Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Funcionalidades de exportação aprimoradas do Azure AD PIM (Privileged Identity Management)

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management

Administradores do PIM (Privileged Identity Management) agora podem exportar todas as atribuições de função ativas e qualificadas para um recurso específico, incluindo atribuições de função para todos os recursos filho. Anteriormente, era difícil para os administradores obter uma lista completa das atribuições de função para uma assinatura e eles precisavam exportar as atribuições de função para cada recurso específico.

Para obter mais informações, confira [Exibir histórico de atividade e auditoria para funções de recurso do Azure no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembro/dezembro de 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Os usuários removidos do escopo de sincronização não alternam mais para contas somente nuvem

**Tipo:** Correção  
**Categoria de serviço:** Gerenciamento de Usuários  
**Funcionalidade do produto:** Diretório

>[!Important]
>Nós ouvimos e entendemos sua frustração devido a essa correção. Portanto, revertemos essa alteração até o momento em que possamos facilitar a correção, para que você possa implementar em sua organização.

Corrigimos um bug no qual o sinalizador DirSyncEnabled de um usuário seria alternado erroneamente para **Falso** quando o objeto de AD DS (Active Directory Domain Services) fosse excluído do escopo de sincronização e, em seguida, movido para a Lixeira no Azure AD no ciclo de sincronização a seguir. Como resultado dessa correção, se o usuário for excluído do escopo de sincronização e, em seguida, restaurado da Lixeira do Azure AD, a conta do usuário permanecerá como sincronizada do AD local, conforme o esperado, e não poderá ser gerenciada na nuvem, já que a SoA (origem da autoridade) permanecerá como AD local.

Antes dessa correção, havia um problema quando o sinalizador DirSyncEnabled era alternado para Falso. Isso resultava na impressão errada de que essas contas eram convertidas em objetos somente nuvem e que as contas poderiam ser gerenciadas na nuvem. No entanto, as contas ainda retinham a SoA como propriedades locais e todas as propriedades sincronizadas (atributos de sombra) provenientes do AD local. Essa condição causou vários problemas no Azure AD e em outras cargas de trabalho de nuvem (como o Exchange Online) que esperavam tratar essas contas como sincronizadas do AD, mas agora se comportavam como contas somente nuvem.

No momento, a única maneira de converter verdadeiramente uma conta do AD de sincronizada para uma conta somente nuvem é desabilitando o DirSync no nível do locatário, que disparará uma operação de back-end para transferir a SoA. Esse tipo de alteração da SoA exige (mas não limita-se a) limpar todos os atributos relacionados locais (como LastDirSyncTime e atributos de sombra) e enviar um sinal para outras cargas de trabalho de nuvem para que o respectivo objeto também seja convertido em uma conta somente nuvem.

Essa correção, consequentemente, impede atualizações diretas no atributo ImmutableID de um usuário sincronizado do AD, que em alguns cenários no passado foram necessários. Por padrão, o ImmutableID de um objeto no Azure AD, como o nome indica, é imutável. Os novos recursos implementados no Azure AD Connect Health e no cliente de Sincronização do Azure AD Connect estão disponíveis para tratar esses cenários:

- **Atualização do ImmutableID em grande escala para muitos usuários em uma abordagem em etapas**
  
  Por exemplo, você precisa fazer uma migração longa entre florestas do AD DS. Solução: Use o Azure AD Connect para **Configurar Âncora de Origem** e, enquanto o usuário migra, copie os valores ImmutableID existentes do Azure AD para o atributo ms-DS-Consistency-Guid do usuário local do AD DS da nova floresta. Para obter mais informações, consulte [Usar ms-DS-ConsistencyGuid como sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Atualizações do ImmutableID em grande escala para muitos usuários em um único disparo**

  Por exemplo, ao implementar o Azure AD Connect, você comete um erro e agora precisa alterar o atributo SourceAnchor. Solução: Desabilite o DirSync no nível do locatário e limpe todos os valores ImmutableID inválidos. Para obter mais informações, consulte [Desativar a sincronização de diretório do Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Corresponder novamente o usuário local com um usuário existente no Azure AD** Por exemplo, um usuário que foi recriado no AD DS gera uma duplicata na conta do Azure AD, em vez de correspondê-la novamente com uma conta do Azure AD (objeto órfão) existente. Solução: Use o Azure AD Connect Health no portal do Azure para remapeamento da Âncora de Origem/ImmutableID. Para obter mais informações, consulte [Cenário de objeto órfão](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Alterações da falha: Atualizações no esquema de auditoria e de logs de entrada por meio do Azure Monitor

**Tipo:** Recurso alterado  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

No momento, estamos publicando os fluxos de log de Auditoria e Entrada por meio do Azure Monitor, para que você possa integrar os arquivos de log às suas ferramentas SIEM ou ao Log Analytics. Com base em seus comentários e em preparação para o comunicado de disponibilidade geral desse recurso, estamos fazendo as alterações a seguir em nosso esquema. Essas alterações de esquema e suas atualizações de documentação relacionadas ocorrerão até a primeira semana de janeiro.

#### <a name="new-fields-in-the-audit-schema"></a>Novos campos no esquema de Auditoria
Estamos adicionando um novo campo **Tipo de Operação**, para fornecer o tipo de operação executada no recurso. Por exemplo, **Adicionar**, **Atualizar** ou **Excluir**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campos alterados no esquema de Auditoria
Os seguintes campos estão sendo alterados no esquema de Auditoria:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|Categoria|Esse era o campo **Nome do Serviço**. Agora ele passou a ser o campo **Categorias de Auditoria**. **Nome do Serviço** foi renomeado para o campo **loggedByService**.|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|<ul><li>Gerenciamento de Usuários</li><li>Gerenciamento de grupos</li><li>Gerenciamento de aplicativos</li></ul>|
|targetResources|Inclui **TargetResourceType** no nível superior.|&nbsp;|<ul><li>Política</li><li>Aplicativo</li><li>Usuário</li><li>Agrupar</li></ul>|
|loggedByService|Fornece o nome do serviço que gerou o log de auditoria.|Nulo|<ul><li>Provisionamento de conta de usuário</li><li>Diretório principal</li><li>Redefinição de senha de autoatendimento</li></ul>|
|Result|Fornece o resultado dos logs de auditoria. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sucesso</li><li>Failure</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campos alterados no esquema de Entrada
Os seguintes campos estão sendo alterados no esquema de Entrada:

|Nome do campo|O que mudou|Valores antigos|Novos valores|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Esse era o campo **conditionalaccessPolicies**. Agora ele passou a ser o campo **appliedConditionalAccessPolicies**.|Sem alteração|Sem alteração|
|conditionalAccessStatus|Fornece o resultado do Status da Política de Acesso Condicional na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sucesso</li><li>Failure</li><li>Não aplicado</li><li>Desabilitado</li></ul>|
|appliedConditionalAccessPolicies: resultado|Fornece o resultado do Status da Política de Acesso Condicional individual na entrada. Anteriormente, isso era enumerado, mas agora mostramos o valor real.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sucesso</li><li>Failure</li><li>Não aplicado</li><li>Desabilitado</li></ul>|

Para obter mais informações sobre o esquema, confira [Interpretar o esquema de logs de auditoria do Azure AD no Azure Monitor (versão prévia)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Melhorias de Proteção de Identidade no modelo de machine learning supervisionado e no mecanismo de pontuação de risco

**Tipo:** Recurso alterado  
**Categoria de serviço:** Identity Protection  
**Funcionalidade do produto:** Pontuações de risco

As melhorias no mecanismo e avaliação de risco de entrada e de usuário relacionadas à Proteção de Identidade podem ajudar a melhorar a cobertura e a precisão de risco de usuário. Os administradores talvez observem que o nível de risco de usuário deixou de estar vinculado ao nível de risco de detecções específicas e que há um aumento no número e no nível de eventos de entrada de risco.

As detecções de risco agora são avaliadas pelo modelo de machine learning supervisionado, que calcula o risco de usuário usando recursos adicionais de entradas do usuário e um padrão de detecções. Com base nesse modelo, o administrador poderá encontrar usuários com pontuações de alto risco, mesmo se as detecções associadas a esse usuário forem de risco baixo ou médio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Os administradores podem redefinir suas próprias senhas usando o aplicativo Microsoft Authenticator (versão prévia pública)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Redefinição de senha por autoatendimento  
**Funcionalidade do produto:** Autenticação de usuário

Os administradores do Azure AD agora podem redefinir suas próprias senhas usando as notificações do aplicativo Microsoft Authenticator ou um código em qualquer aplicativo de autenticador móvel ou token de hardware. Para redefinir suas próprias senhas, os administradores agora poderão usar dois dos seguintes métodos:

- Notificação do aplicativo Microsoft Authenticator

- Outro aplicativo de autenticador móvel/código de token de hardware

- Email

- chamada telefônica

- mensagem de texto

Para obter mais informações sobre como usar o aplicativo Microsoft Authenticator para redefinir senhas, confira [Redefinição de senha de autoatendimento do Azure AD – aplicativo móvel e SSPR (versão prévia)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nova função Administrador de Dispositivo de Nuvem do Azure AD (versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** Registro e Gerenciamento de Dispositivos  
**Funcionalidade do produto:** Controle de acesso

Os administradores podem atribuir usuários à nova função Administrador de Dispositivo de Nuvem para executar tarefas de administrador de dispositivo de nuvem. Os usuários que recebem a função Administradores de Dispositivo de Nuvem podem habilitar, desabilitar e excluir dispositivos no Azure AD, além de poder ler as chaves do BitLocker do Windows 10 (se houver) no portal do Azure.

Para obter mais informações sobre funções e permissões, confira [Atribuindo funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gerenciar seus dispositivos usando o novo carimbo de data/hora de atividade no Azure AD (versão prévia pública)

**Tipo:** New recurso  
**Categoria de serviço:** Registro e Gerenciamento de Dispositivos  
**Funcionalidade do produto:** Gerenciamento do Ciclo de Vida do Dispositivo

Sabemos que, ao longo do tempo, você deve atualizar e desativar dispositivos de sua organização no Azure AD, para evitar que dispositivos obsoletos em seu ambiente. Para ajudar com esse processo, o Azure AD agora atualiza seus dispositivos com um novo carimbo de data/hora de atividade, ajudando você a gerenciar o ciclo de vida do dispositivo.

Para obter mais informações sobre como obter e usar esse carimbo de data/hora, confira [Como: Gerenciar os dispositivos obsoletos no Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Os administradores podem exigir que os usuários aceitem os Termos de uso em cada dispositivo

**Tipo:** New recurso  
**Categoria de serviço:** Termos de Uso  
**Funcionalidade do produto:** Governança
 
Os administradores agora podem ativar a opção **Exigir o consentimento dos usuários em todos os dispositivos** para exigir que os usuários aceitem os Termos de uso em todos os dispositivos que estiverem usando em seu locatário.

Para obter mais informações, confira a [seção Termos de uso por dispositivo do recurso Termos de uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Os administradores podem configurar os Termos de uso para que expirem com base em um agendamento recorrente

**Tipo:** New recurso  
**Categoria de serviço:** Termos de Uso  
**Funcionalidade do produto:** Governança
 

Os administradores agora podem ativar a opção **Expirar consentimentos** para fazer com que os Termos de uso expirem para todos os usuários com base no agendamento recorrente especificado. O agendamento pode ser anual, semestral, trimestral ou mensalmente. Depois que os termos de uso expiram, os usuários devem aceitem novamente.

Para obter mais informações, confira a [seção Adicionar Termos de uso do recurso Termos de uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Os administradores podem configurar os Termos de uso para que expirem com base no agendamento de cada usuário

**Tipo:** New recurso  
**Categoria de serviço:** Termos de Uso  
**Funcionalidade do produto:** Governança

Os administradores agora podem especificar uma duração na qual o usuário precisam aceitar novamente os Termos de uso. Por exemplo, os administradores podem especificar que os usuários precisam aceitar novamente os Termos de uso a cada 90 dias.

Para obter mais informações, confira a [seção Adicionar Termos de uso do recurso Termos de uso do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Novos emails do Azure AD PIM (Privileged Identity Management) para funções do Azure Active Directory

**Tipo:** New recurso  
**Categoria de serviço:** Privileged Identity Management  
**Funcionalidade do produto:** Privileged Identity Management
 
Os clientes que usam o Azure AD PIM (Privileged Identity Management) agora podem receber um email de resumo semanal, incluindo as seguintes informações para os últimos sete dias:

- Visão geral sobre as principais atribuições de função qualificadas e permanentes

- Número de usuários que ativaram funções

- Número de usuários atribuídos a funções no PIM

- Número de usuários atribuídos a funções fora do PIM

- Número de usuários que passaram a ser "permanentes" no PIM

Para obter mais informações sobre o PIM e as notificações por email disponíveis, confira [Notificações por email no PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>O licenciamento baseado em grupo agora está disponível

**Tipo:** Recurso alterado  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** Diretório

O licenciamento baseado em grupo está fora da versão prévia pública e agora está disponível. Como parte dessa versão geral, tornamos esse recurso mais escalonável e adicionamos a capacidade de reprocessar as atribuições de licenciamento baseado em grupo para um único usuário e a capacidade de usar o licenciamento baseado em grupo com licenças do Office 365 E3/A3.

Para obter mais informações sobre o licenciamento baseado em grupo, confira [O que é o licenciamento baseado em grupo no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Novos Aplicativos Federados disponíveis na galeria de aplicativos do Azure AD – novembro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em novembro de 2018, adicionamos estes 26 novos aplicativos com suporte de Federação à galeria de aplicativos:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps – Classic Test](https://test.plexonline.com/signon), [Plex Apps – Classic](https://www.plexonline.com/signon), [Plex Apps – UX Test](https://test.cloud.plex.com/sso), [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/) e [CRAFTS – Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Outubro de 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Os Logs do Azure AD agora funcionam com o Azure Log Analytics (visualização pública)

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

Temos o prazer de anunciar que agora você pode encaminhar seus logs do Azure AD ao Azure Log Analytics! Esse recurso mais solicitados ajuda dão acesso ainda melhor a análise para seu negócio, operações e segurança, bem como uma maneira de ajudar a monitorar sua infra-estrutura. Para obter mais informações, consulte o [logs de atividades de diretório do Azure Active Directory no Azure Log Analytics agora disponível](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Novos aplicativos federados disponíveis na galeria de aplicativos do Azure AD - outubro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em outubro de 2018, adicionamos esses 14 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Meus Pontos de Prêmio](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Teclado de discagem, [ON24 Ambiente Virtual](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Controle do Workspot](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notificações por Email de serviços de domínio do AD do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Azure AD Domain Services  
**Funcionalidade do produto:** Azure AD Domain Services

Os Serviços de Domínio do Azure AD fornecem alertas no portal do Azure sobre configurações incorretas ou problemas com o domínio gerenciado. Esses alertas incluem guias passo a passo para que você possa tentar corrigir os problemas sem precisar entrar em contato com o suporte.

A partir de outubro, você poderá personalizar as configurações de notificação do seu domínio gerenciado para que, quando novos alertas ocorrerem, um e-mail seja enviado a um grupo designado de pessoas, eliminando a necessidade de verificar constantemente o portal em busca de atualizações.

Para obter mais informações, consulte [Configurações de notificação nos Serviços de Domínio do AD do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD portal dá suporte ao uso do domínio ForceDelete API para excluir os domínios personalizados 

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de diretórios  
**Funcionalidade do produto:** Diretório

Temos o prazer de anunciar que agora você pode usar o domínio ForceDelete API para excluir nomes de domínios personalizados renomeando referências, como usuários, grupos e aplicativos de seu nome de domínio personalizado (contoso.com) volta para o (de nome de domínio padrão inicial de forma assíncrona Contoso.onmicrosoft.com).

Essa alteração ajuda você a excluir mais rapidamente seus nomes de domínio personalizados se sua organização não usar mais o nome ou se você precisar usar o nome de domínio com outro AD do Azure.

Para obter mais informações, consulte [Excluir um nome de domínio personalizado](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>Setembro de 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Permissões de função de administrador atualizadas para grupos dinâmicos

**Tipo:** Correção  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Corrigimos um problema para que funções de administrador específicas agora possam criar e atualizar regras de associação dinâmica, sem a necessidade de ser o proprietário do grupo.

As funções são:

- Administrador global

- Administrador do Intune

- Administrador de usuários

Para obter mais informações, consulte [Criar um grupo dinâmico e verificar o status](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Definições de configuração simplificada de SSO (logon único) para alguns aplicativos de terceiros

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Sabemos que configurar SSO (logon único) para aplicativos SaaS (software como serviço) pode ser um desafio devido à natureza específica de cada configuração de aplicativo. Criamos uma experiência de configuração simplificada para preencher automaticamente as definições de configuração de SSO para os seguintes aplicativos SaaS de terceiros:

- Zendesk

- ArcGIS Online

- Jamf Pro

Para começar a usar essa experiência de um único clique, acesse a página do **portal do Azure** > **configuração de SSO** do aplicativo. Para obter mais informações, consulte [integração de aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Página: Azure Active Directory – Onde seus dados estão localizados?

**Tipo:** New recurso  
**Categoria de serviço:** Outros  
**Funcionalidade do produto:** GoLocal

Selecione a região da sua empresa na página **Azure Active Directory – Onde seus dados estão localizados** para exibir qual datacenter do Azure armazena os dados em repouso do Azure AD de todos os seus serviços do Azure AD. Você pode filtrar as informações por serviços específicos do Azure AD da região da sua empresa.

Para acessar esse recurso e obter mais informações, consulte [Azure Active Directory – Onde seus dados estão localizados](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Novo plano de implantação disponível para o painel de acesso Meus Aplicativos

**Tipo:** New recurso  
**Categoria de serviço:** Meus Aplicativos  
**Funcionalidade do produto:** SSO

Confira o novo plano de implantação que está disponível para o painel de acesso Meus Aplicativos (https://aka.ms/deploymentplans).
O painel de acesso Meus Aplicativos fornece aos usuários um único lugar para localizar e acessar os aplicativos. Esse portal também fornece aos usuários oportunidades de autoatendimento, como solicitar acesso a aplicativos e grupos ou gerenciamento de acesso a esses recursos em nome de outros.

Para obter mais informações, consulte [O que é o portal Meus Aplicativos?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nova guia de Solução de problemas e Suporte na página de entradas de Logs do portal do Azure

**Tipo:** New recurso  
**Categoria de serviço:** Relatórios  
**Funcionalidade do produto:** Monitoramento e relatório

A nova guia **Solução de problemas e Suporte** na página **Entradas** do portal do Azure serve para ajudar administradores e engenheiros de suporte a solucionar problemas relacionados a entradas do Azure AD. Esta nova guia oferece o código de erro, a mensagem de erro e as recomendações de correção (caso haja alguma) para ajudar a resolver o problema. Se você não conseguir resolver o problema, também oferecemos uma nova maneira de criar um tíquete de suporte usando a experiência de **Copiar para a área de transferência**, que preenche os campos **ID da Solicitação** e **Data (UTC)** do arquivo de log em seu tíquete de suporte.  

![Logs de entrada que mostra a nova guia](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Suporte aprimorado para propriedades de extensão personalizadas usadas para criar regras de associação dinâmica

**Tipo:** Recurso alterado  
**Categoria de serviço:** Gerenciamento de grupos  
**Funcionalidade do produto:** Colaboração

Com essa atualização, agora você pode clicar no link **Obter propriedades de extensão personalizadas** no construtor de regra dinâmico de grupo dos usuários, inserir sua ID do aplicativo exclusiva e receber a lista completa das propriedades de extensão personalizadas para usar ao criar uma regra de associação dinâmica para usuários. Essa lista também pode ser atualizada para obter as novas propriedades de extensão personalizadas do aplicativo.

Para obter mais informações sobre como usar propriedades de extensão personalizadas para regras de associação dinâmica, consulte [Propriedades de extensão e propriedades de extensão personalizadas](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Microsoft Azure Active Directory

**Tipo:** Plano para alteração  
**Categoria de serviço:** Acesso condicional  
**Funcionalidade do produto:** Segurança de identidade e proteção

Os seguintes aplicativos estão na lista de [aplicativos do cliente aprovados](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Para obter mais informações, consulte:

- [Acesso condicional com base no aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Novo suporte para redefinição de senha de autoatendimento da tela de bloqueio do Windows 7/8/8.1

**Tipo:** New recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** Autenticação de usuário

Depois de configurar esse novo recurso, os usuários verão um link para redefinir a senha da tela de **bloqueio** de um dispositivo que executa o Windows 7, Windows 8 ou Windows 8.1. Ao clicar nesse link, o usuário é guiado pelo mesmo fluxo de redefinição de senha que no navegador da Web.

Para obter mais informações, consulte [Como habilitar a redefinição de senha do Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Aviso de alteração: Os códigos de autorização não ficarão mais disponíveis para reutilização 

**Tipo:** Plano para alteração  
**Categoria de serviço:** Autenticações (Logons)  
**Funcionalidade do produto:** Autenticação de usuário

A partir de 15 de novembro de 2018, o Azure AD deixará de aceitar códigos de autenticação usados anteriormente para aplicativos. Essa alteração de segurança ajuda a deixar o Azure AD em conformidade com a especificação do OAuth e será aplicada aos pontos de extremidade v1 e v2.

Se seu aplicativo reutiliza códigos de autorização para obter tokens para vários recursos, recomendamos que você use o código para obter um token de atualização e, em seguida, utilize esse token de atualização para adquirir tokens adicionais para outros recursos. Os códigos de autorização só podem ser usados uma vez; porém, os códigos de atualização podem ser usados várias vezes em diferentes recursos. Um aplicativo que tente reutilizar um código de autenticação durante o fluxo de código OAuth receberá um erro invalid_grant.

Para isso e outras alterações relacionadas a protocolos, consulte [a lista completa das novidades em autenticação](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Novos Aplicativos Federados disponíveis na galeria de Aplicativos do Azure AD – setembro de 2018

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** Integração de terceiros
 
Em setembro de 2018, adicionamos esses 16 novos aplicativos com suporte de Federação à galeria de aplicativos:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Software de Recrutamento Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Para obter mais informações sobre os aplicativos, consulte [integração de aplicativos SaaS com o Active Directory do Azure](https://aka.ms/appstutorial). Para obter mais informações sobre como listar seu aplicativo na galeria de aplicativos do Azure AD, consulte [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Suporte para métodos de transformações de declarações adicionais

**Tipo:** New recurso  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Apresentamos os novos métodos de transformação de declaração, ToLower () e ToUpper (), que podem ser aplicados aos tokens SAML por meio da página baseada em SAML **Configuração de logon único**.

Para obter mais informações, consulte [Como personalizar declarações emitidas no token SAML para aplicativos empresariais no Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interface do usuário atualizada da configuração de aplicativo baseado em SAML (versão prévia)

**Tipo:** Recurso alterado  
**Categoria de serviço:** Aplicativos empresariais  
**Funcionalidade do produto:** SSO

Como parte da nossa interface do usuário atualizada de configuração de aplicativo baseado em SAML, você obterá:

- Uma experiência atualizada de explicação passo a passo para configurar seus aplicativos baseados em SAML.

- Maior visibilidade sobre o que está ausente ou incorreto em sua configuração.

- A capacidade de adicionar vários endereços de email para notificação de expiração de certificado.

- Novos métodos de transformação de declaração, ToLower () e ToUpper () e muito mais.

- Uma forma de carregar seu próprio token de assinatura de certificado para aplicativos empresariais.

- Uma maneira de definir o formato NameID para aplicativos SAML e uma maneira de definir o valor NameID como Extensões de diretório.

Para ativar este modo de exibição atualizado, clique no link **Experimentar nossa nova experiência** na parte superior da página **Logon único**. Para obter mais informações, confira [Tutorial: Configurar o logon único baseado em SAML para um aplicativo com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).