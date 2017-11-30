---
title: "O que há de novo? Notas de versão do Azure Active Directory | Microsoft Docs"
description: "Saiba o que há de novo com o Azure AD (Azure Active Directory) incluindo as mais recentes notas de versão, problemas conhecidos, correções de bug, funcionalidades preteridas e alterações futuras."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9de535b2fb70181c68fb698e847dd8361bf54385
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Novidades no Azure Active Directory




> Mantenha-se atualizado com o que há de novo no Azure Active Directory assinando este [feed](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us) em seu leitor de feed RSS favorito.



Estamos constantemente aperfeiçoando o Azure Active Directory. Para que você esteja a par das últimas novidades do desenvolvimento, este tópico fornece informações sobre:

-   As versões mais recentes 
-   Problemas conhecidos 
-   Correções de bug 
-   Funcionalidades preteridas 
-   Planos de alterações 

Revisite esta página regularmente, pois estamos realizando atualizações mensalmente.

## <a name="november-2017"></a>Novembro de 2017
 


**Tipo:** plano de alteração  
**Categoria de serviço:** ACS  
**Funcionalidade de produto:** Serviço de Controle de Acesso 

**Desativando o ACS**

O Serviço de Controle de Acesso do Microsoft Azure Active Directory (também conhecido como Serviço de Controle de Acesso ou ACS) será desativado no final de 2018.  Serão fornecidas mais informações, incluindo diretrizes sobre migração de alto nível e agenda detalhada, nas próximas semanas. Por enquanto, deixe comentários nesta página com dúvidas sobre o ACS e um membro da nossa equipe entrará em contato para ajudar a responder.

---



**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection


**Restringir o acesso do navegador ao Intune Managed Browser** 


Com esse comportamento, você poderá restringir o acesso do navegador ao Office 365 e a outros aplicativos de nuvem conectados com o Azure AD usando o Intune Managed Browser como um aplicativo aprovado. 

Essa alteração permite que você configure a seguinte condição de acesso condicional com base em aplicativos:

**Aplicativos de cliente:** Browser

**Qual é o efeito da alteração?**

Atualmente, o acesso está bloqueado ao usar essa condição. Quando a versão prévia desse comportamento estiver disponível, todo o acesso exigirá o uso do aplicativo de navegador gerenciado. 

Procure por essa funcionalidade e mais nas notas de versão e em blogs futuros. 

Para saber mais, confira [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md).

 
---


 
**Tipo:** plano de alteração  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection


**Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Azure AD**


Os aplicativos a seguir foram planejados para serem adicionados à lista de [aplicativos de cliente aprovados](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Acesso condicional baseado em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md)


---


**Tipo:** novo recurso    
**Categoria de serviço:** termos de uso  
**Funcionalidade do produto:** governança/conformidade



**Suporte dos termos de uso para diversos idiomas**


Os administradores agora podem criar novos termos de uso (TOU) que contêm vários documentos em PDF. Você pode marcar esses documentos em PDF com um idioma correspondente. Os usuários que estiverem no escopo receberão o PDF com o idioma correspondente com base em suas preferências. Se não houver nenhuma correspondência, o idioma padrão será mostrado.


---
 


**Tipo:** novo recurso  
**Categoria de serviço:** SSPR  
**Funcionalidade do produto:** autenticação de usuário


**Status de cliente de write-back de senha em tempo real**
 

Agora você pode examinar o status do seu cliente de write-back de senha local. Essa opção está disponível na seção **Integração local** da página **[Redefinição de senha](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)**. 

Se houver problemas com sua conexão com seu cliente de write-back local, você verá uma mensagem de erro exibindo:

- Informações sobre o motivo pelo qual você não pode se conectar ao seu cliente de write-back local 
- Um link para a documentação que auxilia na resolução do problema. 


Para obter mais informações, consulte [Integração local](active-directory-passwords-how-it-works.md#on-premises-integration).

 
---
 
**Tipo:** novo recurso  
**Categoria de serviço:** Azure AD  
**Funcionalidade do produto:** Identity Security & Protection



**Acesso condicional com base no aplicativo Azure AD** 


Agora você pode restringir o acesso ao Office 365 e a outros aplicativos de nuvem conectados do Azure AD para [aplicativos cliente aprovados](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement) que dão suporte a políticas da Proteção de Aplicativo do Intune usando o [acesso condicional com base no aplicativo Azure AD](active-directory-conditional-access-mam.md). Políticas da Proteção de Aplicativo do Intune são usadas para configurar e proteger os dados da empresa nesses aplicativos cliente.

Ao combinar as políticas de acesso condicional [baseado em aplicativo](active-directory-conditional-access-mam.md) com [baseado em dispositivo](active-directory-conditional-access-policy-connected-applications.md), você tem a flexibilidade para proteger dados pessoais e dispositivos da empresa.

As condições e os controles a seguir agora estão disponíveis para uso com acesso condicional com base no aplicativo:

**Condição de plataforma com suporte**

- iOS
- Android

**Condição de aplicativos cliente**

- Aplicativos móveis e clientes de desktop

**Controle de acesso**

- Exigir um aplicativo cliente aprovado


Para saber mais, confira [Acesso condicional baseado em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md).

 
---



**Tipo:** novo recurso  
**Categoria de serviço:** registro e gerenciamento de dispositivos  
**Funcionalidade do produto:** Identity Security & Protection

 

**Gerenciamento de dispositivos do Azure AD no portal do Azure**


Agora você pode encontrar todos os dispositivos conectados ao Azure AD e as atividades relacionadas ao dispositivo em um único local. Há uma nova experiência de administração para gerenciar todas as suas identidades e configurações de dispositivos no portal do Azure. Nesta versão, você pode:

- Exibir todos os dispositivos que estão disponíveis para acesso condicional no Azure AD

- Exibir propriedades, incluindo dispositivos vinculados do Azure AD híbrido

- Localize chaves do BitLocker para os dispositivos vinculados do Azure AD, gerencie seu dispositivo com o Intune e muito mais.

- Gerenciar configurações relacionadas ao dispositivo do Azure AD


Para obter mais informações, consulte [Gerenciar dispositivos usando o portal do Azure](device-management-azure-portal.md).



 
---


**Tipo:** novo recurso    
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection 



**Suporte para macOS como plataforma de dispositivo para acesso condicional do Azure AD** 
 

Agora você pode incluir (ou excluir) macOS como condição de plataforma de dispositivo em sua política de acesso condicional do Azure AD. Com a adição do macOS às plataformas de dispositivos com suporte, você pode:

- **Registrar e gerenciar dispositivos macOS usando o Intune** -semelhante a outras plataformas como iOS e Android, um aplicativo de portal da empresa está disponível para macOS para realizar registros unificados. O novo aplicativo de portal da empresa para macOS permite que você registre um dispositivo com o Intune e registre-o com o Azure AD.
 
- **Certifique-se de que dispositivos macOS sigam as políticas de conformidade da sua organização definidas no Intune** -no Intune no portal do Azure, agora você pode definir políticas de conformidade para dispositivos macOS. 
  
- **Restringir o acesso a aplicativos no Azure AD apenas a dispositivos compatíveis macOS** -a criação de política de acesso condicional tem macOS como uma opção de plataforma de dispositivo separada. Essa opção permite criar políticas de acesso condicional específicas do macOS para o conjunto de aplicativos destinados no Azure.

Para obter mais informações, consulte:

- [Criar uma política de conformidade do dispositivo para dispositivos macOS com o Intune](https://aka.ms/macoscompliancepolicy)
- [Acesso condicional no Azure Active Directory](active-directory-conditional-access-azure-portal.md)


 
---


**Tipo:** novo recurso    
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** autenticação de usuário


**Extensão NPS para o MFA do Azure** 


A extensão do Servidor de Políticas de Rede (NPS) para o Azure MFA adiciona recursos MFA baseados em nuvem à sua infraestrutura de autenticação usando os seus servidores existentes. Com a extensão do NPS, você pode adicionar verificação por chamada telefônica, mensagem de texto ou aplicativo ao fluxo de autenticação existente sem a necessidade de instalar, configurar e manter novos servidores. 

Esta extensão foi criada para organizações que desejam proteger conexões VPN sem implantar o Servidor do Azure MFA. A extensão NPS atua como um adaptador entre RADIUS e Azure MFA baseado em nuvem para fornecer um segundo fator de autenticação para usuários federados ou sincronizados.


Para obter mais informações, consulte [Integrar sua infraestrutura existente do NPS à Autenticação Multifator do Azure](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)

 
---


**Tipo:** novo recurso    
**Categoria de serviço:** gerenciamento de usuário  
**Funcionalidade do produto:** diretório 


**Restaurar ou remover permanentemente os usuários excluídos**


No centro de administração do Azure AD, agora você pode:

- Restaurar um usuário excluído 
- Excluir permanentemente um usuário 


**Para experimentar:**

1. No centro de administração do Azure AD, selecione [**Todos os usuários**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) na seção **Gerenciar**. 

2. Na lista **Mostrar**, selecione **Usuários excluídos recentemente**. 

4. Selecione um ou mais usuários excluídos recentemente e, em seguida, restaure-os ou exclua-os permanentemente.

 
---



 
**Tipo:** recurso alterado  
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection

**Novos aplicativos de cliente aprovados para acesso condicional com base em aplicativos do Azure AD**


Os aplicativos a seguir foram adicionados à lista de [aplicativos cliente aprovados](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement):

- Microsoft Planner

- Proteção de Informações do Microsoft Azure 


Para obter mais informações, consulte:

- [Requisito de aplicativo cliente aprovado](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Acesso condicional baseado em aplicativo do Azure Active Directory](active-directory-conditional-access-mam.md)


---



**Tipo:** recurso alterado    
**Categoria de serviço:** acesso condicional  
**Funcionalidade do produto:** Identity Security & Protection


**Capacidade de 'OR' entre os controles em uma política de acesso condicional** 
 
A capacidade de controles de acesso condicional 'OR' (Exigir um dos controles selecionados) foi lançada. Esse recurso permite que você crie políticas com um **OR** entre os controles de acesso. Por exemplo, você pode usar esse recurso para criar uma política que exige que um usuário entre usando a autenticação multifator **OR** (ou) esteja em um dispositivo compatível.

Para saber mais, confira [Controles de acesso condicional no Azure Active Directory](active-directory-conditional-access-controls.md).

 
---



**Tipo:** recurso alterado    
**Categoria de serviço:** proteção de identidade  
**Funcionalidade do produto:** Identity Security & Protection

**Agregação de eventos de risco em tempo real**

Para melhorar sua experiência de administração, no Azure AD Identity Protection, todos os eventos de risco em tempo real que foram originados no mesmo endereço IP em um determinado dia agora são agregados por cada tipo de evento de risco. Essa alteração limita o volume de eventos de risco mostrados sem qualquer alteração na segurança de usuário.

A detecção em tempo real subjacente funciona sempre que o usuário fizer logon. Se você tiver uma configuração de política de segurança de risco de conexão para MFA ou para bloquear acesso, ela ainda será disparada durante cada conexão arriscada.

 
---
 




## <a name="october-2017"></a>Outubro de 2017

**Tipo:** plano de alteração  
**Categoria de serviço:** relatórios  
**Funcionalidade do produto**: gerenciamento de ciclo de vida de identidade  


**Reprovar relatórios do Azure AD**

O Portal do Azure oferece:

- Um novo console de administração do Azure Active Directory 
- Novas APIs para gerar relatórios de atividades e de segurança
 
Devido a essas novas funcionalidades, as APIs de relatórios sob o ponto de extremidade **/reports** serão desativadas em 10 de dezembro de 2017. 

---

**Tipo:** corrigido   
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** SSO  


**Detecção automática de campo de conexão**


O Azure Active Directory dá suporte à detecção automática campo de entrada para os aplicativos que renderizam um campo de nome de usuário e senha HTML.  Essas etapas são documentadas em [Como capturar automaticamente os campos de entrada para um aplicativo](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application). Você pode encontrar essa funcionalidade adicionando um aplicativo *Inexistente na galeria* na página **Aplicativos empresariais** do [Portal do Azure](http://aad.portal.azure.com). Além disso, você pode configurar o modo de **Logon único** neste novo aplicativo para **Logon único baseado em senha** digitando uma URL da Web e salvando a página.
 
Devido a um problema de serviço, essa funcionalidade ficou temporariamente desabilitada por um período. O problema foi resolvido e a detecção automática do campo de entrada está disponível novamente.

---

**Tipo:** novo recurso  
**Categoria de serviço:** MFA  
**Funcionalidade do produto:** Identity Security & Protection  


**Novos recursos de MFA**

A MFA (Autenticação Multifator) é uma parte essencial da proteção da sua organização. Para tornar as credenciais mais adaptável e a experiência perfeita, os seguintes recursos foram adicionados: 

- A integração do desafio multifator resulta diretamente no relatório de logon único do Azure AD, incluindo o acesso programático aos resultados da MFA

- Integração mais profunda da configuração de MFA com a experiência de configuração do Azure AD no portal do Azure

Com essa versão prévia pública, o gerenciamento e os relatórios de MFA são parte integrante da experiência de configuração central do Azure AD. Agregar os dois recursos permite que você gerencie a funcionalidade de portal de gerenciamento de MFA dentro da experiência do Azure AD.

Para saber mais, veja [Referência para relatórios de autenticação multifator no portal do Azure](active-directory-reporting-activity-sign-ins-mfa.md) 


---
**Tipo:** novo recurso  
**Categoria de serviço:** termos de uso  
**Funcionalidade do produto:** governança  


**Apresentar os termos de uso**

Os termos de uso do Azure AD fornecem um método simples para apresentar as informações aos usuários finais. Isso garante que os usuários vejam os avisos de isenção de responsabilidade relevantes para os requisitos legais ou de conformidade.

Use os termos de uso do Azure AD nos seguintes cenários:

- Termos gerais de uso para todos os usuários em sua organização. 

- Termos específicos de uso baseados nos atributos de um usuário (por exemplo, médicos vs enfermeiras ou funcionários locais vs internacionais, realizado por grupos dinâmicos). 

- Termos específicos de uso para acessar aplicativos de alto impacto nos negócios, como o Salesforce.

Para obter mais informações, consulte [Termos de Uso do Azure Active Directory](active-directory-tou.md).


---
**Tipo:** novo recurso  
**Categoria de serviço:** PIM  
**Funcionalidade do produto:** Privileged Identity Management  

**Aprimoramentos no Privileged Identity Management**

Com o Azure Active Directory Privileged Identity Management (PIM), você pode gerenciar, controlar e monitorar o acesso aos Recursos do Azure (Versão prévia) em sua organização para:

- Assinaturas
- Grupos de recursos
- Máquinas virtuais. 

Todos os recursos do Portal do Azure que aproveitam a funcionalidade de RBAC (Controle de Acesso Baseado em Volume) do Azure podem tirar proveito de todas as capacidades de segurança e gerenciamento de ciclo de vida que o Azure AD PIM tem a oferecer.

Para saber mais, consulte [PIM para recursos do Azure](privileged-identity-management/azure-pim-resource-rbac.md).


---
**Tipo:** novo recurso  
**Categoria de serviço:** revisões de acesso  
**Funcionalidade do produto:** governança  

**Apresentar revisões de acesso**


As revisões de acesso (versão prévia) permitem que as organizações gerenciem com eficiência as associações de grupos e o acesso a aplicativos corporativos: 

- Você pode renovar o acesso do usuário convidado usando as revisões de acesso do acesso deles a aplicativos e a associações de grupos. As informações fornecidas pelas revisões de acesso permitem aos revisores decidir com eficiência se o acesso dos convidados deve continuar.

- Você pode renovar o certificado de acesso de funcionários a aplicativos e associações de grupos com as revisões de acesso.

Você pode coletar os controles de revisão de acesso em programas relevantes para que sua organização rastreie as análises de conformidade ou aplicativos suscetíveis a riscos.

Para obter mais informações, consulte [Revisões de acesso do Azure AD](active-directory-azure-ad-controls-access-reviews-overview.md).


---
**Tipo:** novo recurso  
**Categoria de serviço:** Meus aplicativos  
**Funcionalidade do produto:** SSO  


**Ocultar aplicativos de terceiros dos Meus aplicativos e do inicializador do Office 365**

Agora é possível gerenciar melhor os aplicativos que aparecem em seus portais de usuário por meio de uma nova propriedade **ocultar aplicativo**. Ocultar aplicativos ajuda nos casos em que os blocos de aplicativos estão aparecendo para serviços de back-end ou há blocos duplicados que acabam por encher os inicializadores de aplicativos do usuário. O botão de alternância encontra-se na seção de propriedades do aplicativo de terceiros e é chamado **Visível para usuário?** Também é possível ocultar um aplicativo programaticamente por meio do PowerShell. 

Para obter mais informações, consulte [Ocultar um aplicativo de terceiros da experiência do usuário no Azure Active Directory](active-directory-coreapps-hide-third-party-app.md). 


**O que está disponível?**

 Como parte da transição para o novo console de administrador, 2 novas APIs para recuperar os Logs de atividades do Azure AD estão disponíveis. O novo conjunto de APIs fornece funcionalidades de filtragem e classificação mais sofisticadas, bem como atividades mais avançadas de auditoria e de conexão. Os dados disponíveis anteriormente por meio de relatórios de segurança agora podem ser acessados por meio da API de eventos de risco do Identity Protection do Microsoft Graph.


## <a name="september-2017"></a>Setembro de 2017

**Tipo:** recurso alterado  
**Categoria de serviço:** Microsoft Identity Manager  
**Funcionalidade do produto**: gerenciamento de ciclo de vida de identidade  


**Hotfix para Microsoft Identity Manager**

Um pacote cumulativo de atualizações de hotfix (build 4.4.1642.0) está disponível desde 25 de setembro de 2017 para o MIM (Microsoft Identity Manager) 2016 2016 Service Pack 1 (SP1). Esse pacote cumulativo:

- Resolve problemas e adiciona melhorias
- Ele é uma atualização cumulativa que substitui todas as atualizações do MIM 2016 SP1 até o build 4.4.1459.0 para o Microsoft Identity Manager 2016. 
- Você precisa ter o **Microsoft Identity Manager 2016 build 4.4.1302.0.** 

Para obter mais informações, consulte [Pacote cumulativo de atualizações de hotfix (build 4.4.1642.0) disponível para o Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562). 

---
