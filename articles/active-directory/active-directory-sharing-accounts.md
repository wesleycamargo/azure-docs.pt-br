<properties
	pageTitle="Compartilhar contas usando o AD do Azure | Microsoft Azure"
	description="Descreve como o Active Directory do Azure permite que as organizações compartilhem com segurança contas para aplicativos locais e serviços de nuvem do consumidor."
	services="active-directory"
	documentationCenter=""
	authors="msStevenPo"
 	manager="stevenpo"
	editor=""/>

 <tags
	ms.service="active-directory"
 	ms.workload="identity"
 	ms.tgt_pltfrm="na"
 	ms.devlang="na"
 	ms.topic="article"
 	ms.date="10/13/2015"  
 	ms.author="stevenpo"/>

# Compartilhar contas com o AD do Azure

Frequentemente em organizações, nos vemos em situações em que um único nome de usuário e senha precisam ser usados por várias pessoas. Isso geralmente ocorre em dois casos:

- Ao acessar aplicativos que exigem um logon exclusivo e uma senha para cada usuário, seja aplicativos locais ou serviços em nuvem do consumidor (por exemplo, contas de mídia social corporativa).
- Durante a criação de ambientes de vários usuários. Você pode ter uma conta local única com privilégios elevados e que pode ser usada para as principais atividades de instalação, administração e recuperação (por exemplo, a conta "administrador global" local para o Office 365 ou a conta raiz da Salesforce).

Tradicionalmente, essas contas devem ser compartilhadas distribuindo as credenciais (nome de usuário/senha) para os indivíduos certos ou armazená-las em um local compartilhado onde vários agentes confiáveis podem acessá-los.

## O que há de errado com o modelo tradicional de compartilhamento?

O modelo tradicional de compartilhamento tem várias desvantagens:

- Habilitar o acesso a novos aplicativos exige que você distribua credenciais para qualquer pessoa que precise de acesso.
- Cada aplicativo compartilhado pode exigir seu próprio conjunto exclusivo de credenciais compartilhadas, exigindo que os usuários se lembrem de vários conjuntos de credenciais. Quando os usuários precisam lembrar de várias credenciais, o risco de eles recorrerem a práticas arriscadas aumenta (por exemplo, escrever senhas).
- Você não pode determinar quem tem acesso a um aplicativo.
- Você não pode determinar quem acessou um aplicativo.
- Quando você precisar remover o acesso a um aplicativo, é necessário atualizar as credenciais e redistribuí-las para todos que precisam acessar o aplicativo.

## Como o Active Directory do Azure ajuda?

O AD do Azure fornece uma nova abordagem para usar contas compartilhadas que elimina essas desvantagens. O administrador do AD do Azure configura quais aplicativos um usuário pode acessar usando o Painel de Acesso. Para cada aplicativo, o administrador escolhe o tipo de logon único mais adequado para o aplicativo. Um desses tipos, logon único baseado em senha, permite ao AD do Azure agir como um tipo de agente durante o processo de logon no aplicativo. O administrador pode facilmente conceder ou revogar acesso a um aplicativo e também saber a qualquer momento quem tem acesso à conta e quem acessou no passado.

Os usuários fazem logon uma vez usando suas contas institucionais, a mesma conta que eles usam regularmente para acessar a área de trabalho ou email. Eles podem descobrir e acessar apenas os aplicativos aos quais eles estão atribuídos. Com contas compartilhadas, essa lista de aplicativos pode incluir qualquer número de credenciais compartilhadas. O usuário final não precisa se lembrar ou escrever as diversas contas que podem estar utilizando.

Contas compartilhadas não apenas aumentam a supervisão e melhoram a usabilidade, elas também aumentam a segurança. Os usuários com permissões para usar as credenciais não vejam a senha compartilhada, mas em vez disso, obtém as permissões para usar a senha como parte de um fluxo de autenticação orquestrado. Além disso, com alguns aplicativos de SSO de senha, você tem a opção de o AD do Azure periodicamente substituir (atualização) a senha usando senhas grandes e complexas, aumentando a segurança da conta.

O AD do Azure oferece suporte a contas compartilhadas para qualquer usuário licenciado do Enterprise Mobility Suite (EMS), Premium ou Basic, em todos os tipos de aplicativos de logon único com senha. Você pode compartilhar contas para qualquer um dos milhares de aplicativos previamente integrados na galeria de aplicativos e pode adicionar seu próprio aplicativo de autenticação de senha com [aplicativos personalizados de SSO](active-directory-single-sign-on-newly-acquired-saas-apps.md).

Para usar o AD do Azure para compartilhar uma conta, você precisará:

- Adicionar uma [galeria de aplicativos](https://azure.microsoft.com/pt-BR/marketplace/active-directory/) do aplicativo ou um [aplicativo personalizado](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)
- Configurar o aplicativo para logon único (SSO) com senha
- Use [atribuição baseada em grupo](active-directory-accessmanagement-group-saasapps.md) e selecione a opção de inserir uma credencial compartilhada
- Opcional: em alguns aplicativos, como o Facebook, Twitter ou LinkedIn, você pode habilitar a opção de [substituição de senha automatizada do AD do Azure](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx).

Usando o AD do Azure você pode tornar sua conta compartilhada mais segura com a autenticação multifator (MFA) (saiba mais sobre [como proteger aplicativos com o AD do Azure](multi-factor-authentication-get-started.md)) e você pode delegar a capacidade de gerenciar quem tem acesso ao aplicativo usando o Gerenciamento de Grupo [Autoatendimento do AD do Azure](active-directory-accessmanagement-self-service-group-management.md).

## Como posso começar?

Primeiro, se você ainda não estiver usando o AD do Azure e for um administrador de TI:

- [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory/) - você pode se inscrever em uma avaliação gratuita de 30 dias hoje mesmo e implantar sua primeira solução de nuvem em menos de 5 minutos usando este link

Recursos do AD do Azure que permitem o compartilhamento de contas incluem:

- [Logon único com senha](active-directory-passwords-getting-started.md)
- Agente de logon único com senha
- [Atribuição de grupo](active-directory-accessmanagement-self-service-group-management.md)
- Aplicativos com senha personalizada
- [Painel/relatórios de uso de aplicativo](active-directory-passwords-get-insights.md)
- Portais de acesso do usuário final
- [Proxy do aplicativo](active-directory-application-proxy-get-started.md)
- [Active Directory Marketplace](http://azure.microsoft.com/marketplace/active-directory/all/)

Conteúdo relacionado:

- [Proteger aplicativos com acesso condicional](active-directory-conditional-access.md)
- [Gerenciamento de grupo de autoatendimento/SSAA](active-directory-accessmanagement-self-service-group-management.md)

<!---HONumber=Oct15_HO3-->