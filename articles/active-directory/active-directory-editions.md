<properties
	pageTitle="Edições do Active Directory do Azure | Microsoft Azure"
	description="Um tópico que explica as opções para as edições pagas e para a edição gratuita do Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="MarkusVi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/04/2015"
	ms.author="markvi"/>

# Edições do Active Directory do Azure

Todos os serviços comerciais do Microsoft Online dependem do Active Directory do Azure para logon e outras necessidades de identidade. Se você assinar qualquer um dos serviços comerciais do Microsoft Online (por exemplo, Office 365, Microsoft Azure, etc.), você obterá o Active Directory do Azure (AD do Azure) com acesso a todos os recursos Gratuitos, descritos abaixo.

O Active Directory do Azure é um serviço que fornece recursos abrangentes de gerenciamento de acesso e de identidade na nuvem para seus funcionários, parceiros e clientes. Ele combina serviços de diretório, governança avançada de identidade, uma plataforma sofisticada baseada em padrões para desenvolvedores e gerenciamento de acesso de aplicativo para seu próprio aplicativo ou qualquer um dos milhares de aplicativos pré-integrados. Com a edição Gratuita do Active Directory do Azure, você pode gerenciar usuários e grupos, sincronizar com diretórios locais, obter logon único no Azure, Office 365 e em milhares de aplicativos SaaS populares como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e muito mais. Para saber mais sobre o Active Directory do Azure, leia [O que é o AD do Azure?](active-directory-whatis.md)


Para aprimorar seu Active Directory do Azure, é possível adicionar recursos pagos usando as Active Directory Basic e Premium editions do Azure. As edições pagas do Active Directory do Azure são criadas em seu diretório gratuito existente, fornecendo recursos corporativos que abrangem autoatendimento, monitoramento avançado, relatórios de segurança, MFA (Multi-Factor Authentication) e acesso seguro para a sua força de trabalho móvel.

As assinaturas do Office 365 incluem recursos adicionais do Active Directory do Azure descritos na tabela de comparação abaixo.


> [AZURE.NOTE]Para obter as opções de preço dessas edições, confira [Preço do Active Directory do Azure](https://azure.microsoft.com/pricing/details/active-directory/). <br>Atualmente, não há suporte para o Azure Active Directory Premium e o Azure Active Directory Basic na China. Entre em contato conosco no Fórum do Active Directory do Azure para obter mais informações


- **Azure Active Directory Basic** - Projetada para profissionais de tarefas com necessidades que priorizam a nuvem, esta edição fornece soluções de gerenciamento de identidades por autoatendimento e de acesso a aplicativos centrados na nuvem. Com a Basic edition do Active Directory do Azure, você obtém recursos que aumentam sua produtividade e reduzem os custos, como o gerenciamento de acesso baseado em grupo, redefinição de senha por autoatendimento para aplicativos em nuvem e o Proxy de Aplicativo do Active Directory do Azure (para publicar aplicativos web locais usando o Active Directory do Azure), tudo com o apoio de um SLA de nível empresarial de 99,9% de tempo de atividade.
 
- **Azure Active Directory Premium** - Projetada para capacitar as organizações com necessidades mais exigentes de gerenciamento de identidades e acesso, a Active Directory Premium edition do Azure adiciona funcionalidades de gerenciamento de identidades de nível empresarial rica em recursos e permite que os usuários híbridos acessem diretamente as funcionalidades locais e na nuvem. Esta edição inclui tudo o que necessário para o profissional de informações e os administradores de identidades em ambientes híbridos no acesso a aplicativos, IAM (gerenciamento de identidades e acesso) por autoatendimento, proteção de identidade e segurança na nuvem. Ela dá suporte a recursos avançados de administração e delegação, como grupos dinâmicos e gerenciamento de grupos por autoatendimento. Inclui o Microsoft Identity Manager (um pacote de gerenciamento de identidades e acesso local) e fornece recursos de write-back na nuvem, possibilitando soluções como a redefinição de senha por autoatendimento para seus usuários locais.

Para inscrever-se e começar a usar o Active Directory Premium hoje, consulte [Introdução ao Active Directory Premium do Azure](active-directory-get-started-premium.md).


> [AZURE.NOTE]Uma série de recursos do Active Directory do Azure está disponível por meio de edições “pré-pagas”:
>
>- O Active Directory B2C é a solução de gerenciamento de identidades e acesso para seus aplicativos voltados para o consumidor. Para obter mais detalhes, veja [Active Directory B2C do Azure](https://azure.microsoft.com/documentation/services/active-directory-b2c/)
 
>-	O Azure Multi-Factor Authentication pode ser usado por usuário ou por provedores de autenticação. Para obter mais detalhes, veja [O que é o Azure Multi-Factor Authentication?](multi-factor-authentication.md)


<br>

| Tipo de recurso| Recursos| Edição Gratuita| Edição Básica| Premium Edition| Somente aplicativos do Office 365 |
| --- | --- | --- | --- | --- | --- |
| **Recursos comuns**| Objetos do diretório [1]| Até 500 mil objetos| Não há limite de objeto| Não há limite de objeto| Nenhum limite de objeto para contas de usuário do Office 365|
| | [Gerenciamento de usuários e grupos (adicionar/atualizar/excluir), provisionamento baseado em usuário](active-directory-administer.md), [registro de dispositivos](active-directory-conditional-access-device-registration-overview.md)| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|
| | [SSO para aplicativos SAAS/Aplicativos personalizados/aplicativos de Proxy de Aplicativo](active-directory-enable-sso-scenario.md)| 10 aplicativos por usuário [2]| 10 aplicativos por usuário [2]| sem limite| 10 aplicativos por usuário [2]|
| | [Alteração de senhas por autoatendimento para usuários de nuvem](active-directory-passwords-update-your-own-password.md)| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|
| | [Connect – para sincronizar entre diretórios locais e o Active Directory do Azure](active-directory-aadconnect.md)| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|
| | **Visualização**:[ colaboração B2B](active-directory-b2b-collaboration-overview.md)| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|
| | [Segurança/relatórios de uso](active-directory-view-access-usage-reports.md)| Relatórios básicos| Relatórios básicos| Relatórios avançados| Relatórios básicos|
| **Recursos básicos e Premium**| [Provisionamento e gerenciamento de acesso a aplicativos com base em grupo](active-directory-accessmanagement-group-saasapps.md)| | ![Verificação][12]| ![Verificação][12]| |
| | [Redefinição de senha por autoatendimento para usuários de nuvem](active-directory-passwords.md)| | ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|
| | [Identidade visual da empresa (Páginas de logon e personalização do Painel de Acesso)](active-directory-add-company-branding.md)| | ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|
| | [Proxy de Aplicativo](active-directory-application-proxy-get-started.md)| | ![Verificação][12]| ![Verificação][12]| |
| | [Tempo de atividade de SLA de alta disponibilidade (99,9%)](https://azure.microsoft.com/support/legal/sla/)| | ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|
| **Recursos somente Premium**| Gerenciamento de grupo de autoatendimento/adições de aplicativo de autoatendimento/associação a grupos dinâmicos| | | ![Verificação][12]| |
| | [Redefinição/alteração/desbloqueio de senha de autoatendimento com write-back local](active-directory-passwords-getting-started.md/#enable-users-to-reset-or-change-their-ad-passwords)| | | ![Verificação][12]| |
| | [Multi-Factor Authentication (nuvem e local)](multi-factor-authentication.md)| | | ![Verificação][12]| Limitado à nuvem somente para aplicativos do Office 365|
| | [Licenças de usuário do MIM (Microsoft Identity Manager) e servidor MIM [3]](http://www.microsoft.com/server-cloud/products/microsoft-identity-manager/default.aspx)| | | ![Verificação][12]| |
| | [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)| | | ![Verificação][12]| |
| | [Azure Active Directory Connect Health](active-directory-aadconnect-health.md)| | | ![Verificação][12]| |
| | Substituição automática de senha para contas de grupo| | | ![Verificação][12]| |
| | **Visualização**: acesso condicional| | | ![Verificação][12]| |
| | **Visualização**: Privileged Identity Management| | | ![Verificação][12]| |
| **Recursos relacionados ao Windows 10 e ao Ingresso no AD do Azure**| Adicione um dispositivo com Windows 10 à AD do Azure, ao Desktop SSO, ao Microsoft Passport para AD do Azure e à recuperação do Administrator Bitlocker| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]| ![Verificação][12]|
| | Registro automático do MDM, recuperação de autoatendimento do Bitlocker, administração local extra para dispositivos do Windows 10 via Ingresso no AD do Azure| | | ![Verificação][12]| |





[1] A cota de uso padrão é de 150 mil objetos. Um objeto é uma entrada no serviço do diretório, representado por seu nome diferenciado exclusivo. Um exemplo de objeto é uma entrada de usuário para propósitos de autenticação. Se você precisa ultrapassar essa cota padrão, entre em contato com o suporte. O limite de 500 mil objetos não se aplica ao Office 365, ao Microsoft Intune ou a qualquer outro serviço online da Microsoft pago que dependa do Active Directory do Azure para ter serviços de diretório.

[2] Com o AD do Azure Gratuito e o AD do Azure Básico, os usuários finais que receberam acesso a aplicativos SaaS podem ver até dez aplicativos em seu painel de acesso e ter acesso com SSO a eles. Os administradores podem configurar SSO e atribuir acesso do usuário a tantos aplicativos SaaS quantos quiserem com Gratuito e Básico; no entanto, os usuários finais só verão 10 aplicativos em seu Painel de Acesso de cada vez.

[3] Direitos de software de servidor Microsoft Identity Manager são concedidos com licenças do Windows Server (qualquer edição). Como o Microsoft Identity Manager é executado no Sistema Operacional do Windows Server, desde que o servidor esteja executando uma cópia válida e licenciada do Windows Server, o Microsoft Identity Manager pode ser instalado e utilizado nesse servidor. Nenhuma outra licença separada é necessária para o servidor do Microsoft Identity Manager.



## O que vem a seguir

- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
- [Exibir relatórios de acesso e de uso](active-directory-view-access-usage-reports.md)


<!--Image references-->
[12]: ./media/active-directory-editions/ic195031.png

<!---HONumber=AcomDC_1210_2015-->