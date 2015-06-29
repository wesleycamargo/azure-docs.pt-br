<properties
	pageTitle="Edições do Active Directory do Azure"
	description="Um tópico que explica as opções para as edições pagas e para a edição gratuita do Active Directory do Azure."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="TerryLan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/01/2015"
	ms.author="curtand"/>

# Edições do Active Directory do Azure

O Active Directory do Azure é um serviço que fornece recursos abrangentes de gerenciamento de acesso e identidade na nuvem. Ele combina serviços de diretório, controle de identidade avançado, gerenciamento de acesso do aplicativo e uma plataforma sofisticada para desenvolvedores, baseada em padrões. Para obter mais informações, [consulte este vídeo](http://azure.microsoft.com/documentation/videos/teched-europe-2014-cloud-identity-microsoft-azure-active-directory-explained/).

Criado sobre um conjunto grande de recursos gratuitos no Active Directory do Microsoft Azure, as edições Premium e Básica do Active Directory do Azure fornecem um conjunto de recursos mais avançados para capacitar empresas com necessidades maiores de gerenciamento de acesso e identidade. Para opções de preços para essas edições, consulte [Preços do Active Directory do Azure](http://azure.microsoft.com/pricing/details/active-directory/). Quando você se inscreve no Azure, você pode escolher as seguintes edições gratuitas e pagas do Active Directory do Azure:

- **Free** - a edição gratuita do Active Directory do Azure faz parte de todas as assinaturas do Azure. Não há nada para licenciar e nada para instalar. Com a edição gratuita, você pode gerenciar contas de usuário, sincronizar com diretórios locais, obter logon único no Azure, Office 365 e milhares de aplicativos SaaS populares como Salesforce, Workday, Concur, DocuSign, Google Apps, Box, ServiceNow, Dropbox e muito mais.
- **Basic** - a Basic Edition Active Directory do Azure fornece acesso a aplicativos e requisitos de gerenciamento de identidade por autoatendimento para workers de tarefa com necessidades priorizando nuvem. Com a Basic Edition do Active Directory do Azure, você obtém todos os recursos que o Active Directory do Azure Gratuito tem a oferecer, além do gerenciamento de acesso baseado em grupo, redefinição de senha por autoatendimento para aplicativos de nuvem, o proxy de aplicativo do Active Directory do Azure (para publicar aplicativos da Web locais usando o Active Directory do Azure), ambiente personalizável para inicializar aplicativos empresariais e de nuvem do consumidor e um SLA de nível empresarial que mantém-se 99,9% do tempo em atividade. Um administrador com a Basic Edition do Active Directory do Azure também pode ativar uma versão de avaliação do Azure Active Directory Premium.
- **Premium** - com a Premium Edition do Active Directory do Azure, você obtém todos os recursos que as edições Free e Basic do Active Directory do Azure têm a oferecer, além de capacidades adicionais de gerenciamento de identidade de nível empresarial ricas em recursos explicadas abaixo.

Para inscrever-se e começar a usar o Active Directory Premium hoje, consulte [Introdução ao Active Directory Premium do Azure](active-directory-get-started-premium.md).

> [AZURE.NOTE]O Azure Active Directory Premium e o Azure Active Directory Basic não têm suporte atualmente na China. Para obter mais informações, entre em contato conosco no [Fórum do Active Directory do Azure](http://feedback.azure.com/forums/169401-azure-active-directory).

## Recursos do Active Directory Basic do Azure

A Basic edition do Active Directory é uma oferta paga do Active Directory do Azure e inclui os seguintes recursos:

- **Identidade visual da empresa** - para tornar a experiência do usuário final ainda melhor, você pode adicionar o logotipo e esquemas de cores de sua empresa às páginas Entrada e Painel de acesso da sua organização. Depois de adicionar o logotipo, você também tem a opção de adicionar versões localizadas do logotipo para diferentes idiomas e localidades. Para mais informações, consulte [Adicionar identidade visual da empresa às páginas Entrada e do Painel de acesso](active-directory-add-company-branding.md).
- **Acesso de aplicativo com base no grupo** - use grupos para provisionar usuários e atribuir acesso de usuário em massa a milhares de aplicativos SaaS. Esses grupos podem ser criados somente na nuvem, ou então você pode aproveitar os grupos existentes que foram sincronizados a partir do seu Active Directory local. Para obter mais informações, consulte [Atribuir acesso a um aplicativo SaaS no AD do Azure para um grupo](https://msdn.microsoft.com/library/azure/dn621141.aspx).
- **Redefinição de senha por autoatendimento** -Azure sempre permitiu que os administradores do diretório redefinir senhas. Com o Active Directory Basic do Azure, você agora pode reduzir chamadas de assistência técnica quando seus usuários esquecerem a senha oferecendo a eles a possibilidade de redefinir sua senha, com a mesma experiência de logon utilizada por eles no Office 365. Para obter mais informações, consulte [Gerenciamento de senhas no AD do Azure](https://msdn.microsoft.com/library/azure/dn510386.aspx).
- **SLA empresarial de 99,9%** - garantimos pelo menos 99,9% de disponibilidade do serviço no Active Directory Basic do Azure.
- [**Proxy de aplicativo do Active Directory do Azure**](https://msdn.microsoft.com/library/azure/dn768214.aspx) - permite o acesso seguro de funcionários a aplicativos locais como o SharePoint e o Exchange/OWA da nuvem usando o Active Directory do Azure.

## Recursos do Active Directory Premium do Azure

A edição Active Directory Premium é uma oferta paga do Active Directory do Azure que inclui todos os recursos das edições Basic e Free e também os recursos a seguir:

- **Gerenciamento de grupos por autoatendimento** - o Active Directory Premium do Azure simplifica a administração cotidiana de grupos, permitindo que os usuários criem grupos, solicitem acesso a outros grupos, deleguem a propriedade de determinados grupos para que outras pessoas possam aprovar solicitações e mantenham as associações de seus grupos.

    Para obter mais informações, consulte [Gerenciamento de grupos por autoatendimento para usuários no AD do Azure](https://msdn.microsoft.com/library/azure/dn641267.aspx).

- **Relatórios de segurança e alertas avançados** – monitore e proteja o acesso aos seus aplicativos de nuvem exibindo registros detalhados que mostram anomalias mais avançadas e relatórios de padrões de acesso inconsistentes. Relatórios avançados são baseados em aprendizagem de máquina e podem ajudá-lo a formar novas estratégias para aprimorar a segurança do acesso e reagir às potenciais ameaças.

    Para obter mais informações, consulte [Exibir relatórios de acesso e uso](active-directory-view-access-usage-reports.md).

- **Multi-Factor Authentication** - A Multi-Factor Authentication (autenticação multifator) agora está incluída na Premium Edition e pode ajudá-lo a proteger o acesso a aplicativos locais (VPN, RADIUS, etc.), Azure, Microsoft Online Services como Office 365 e Dynamics CRM Online e milhares de serviços em nuvem não MS pré-integrados ao Active Directory do Azure. Simplesmente habilite a Multi-Factor Authentication para identidades do Active Directory do Azure; assim, os usuários serão solicitados a configurar uma verificação adicional na próxima vez em que entrarem.

    Para obter mais informações, consulte [Adicionando Multi-Factor Authentication ao Active Directory do Azure](https://msdn.microsoft.com/library/azure/dn249466.aspx).

- **MIM (Microsoft Identity Manager)** - a Premium Edition vem com a opção de conceder direitos de uso de um servidor MIM (e CALs) em sua rede local para dar suporte a qualquer combinação de soluções de identidade híbridas. Isso é uma ótima opção se você tiver uma variação de diretórios e bancos de dados locais que você deseja sincronizar diretamente ao Active Directory do Azure. Não há nenhum limite para o número de servidores FIM que você pode usar, no entanto, CALs MIM são concedidos com base na alocação de uma licença de usuário do Active Directory Premium do Azure.

    Para obter mais informações, consulte [Implantar MIM 2010 R2](https://www.microsoft.com/server-cloud/products/forefront-identity-manager/features.aspx).

- **SLA empresarial de 99,9%** - garantimos pelo menos 99,9% de disponibilidade do serviço no Active Directory Premium do Azure.

    Para obter mais informações, consulte [SLA do Active Directory Premium](http://azure.microsoft.com/support/legal/sla/)

- **Redefinição de senha com write-back** - o retorno da redefinição de senha por autoatendimento pode ser gravado em diretórios locais.




## Comparar edições: recursos comuns a todas as edições

- Diretório como um serviço - para a edição gratuita, há um limite de 500 mil objetos. Mas o limite de 500 mil objetos não é aplicado ao Office 365, Microsoft Intune ou qualquer outro serviço online da Microsoft que dependa do Active Directory do Azure para serviços de diretório. As edições Basic e Premium não têm nenhum limite de objetos.
- Gerenciamento de usuários e grupos usando cmdlets do Windows PowerShell ou de interface do usuário
- Registro de dispositivos
- Portal do painel de acesso para acesso de usuário de logon único (SSO) ao SaaS e aplicativos personalizados com Azure Active Directory Free e Azure Active Directory Basic, os usuários finais que receberam acesso a cada aplicativo SaaS podem ver até 10 aplicativos em seu painel de acesso e obter acesso SSO a eles (assumindo que eles tenham sido configurados primeiro com SSO pelo administrador). Os administradores podem configurar o SSO e atribuir acesso de usuário a tantos aplicativos SaaS quanto desejarem com a edição Free; no entanto, os usuários finais verão somente 10 aplicativos por vez no seu painel de acesso. O Active Directory Premium do Azure não tem nenhum limite de aplicativos.
- Provisionamento e gerenciamento de acesso a aplicativos com base no usuário
- Alteração de senhas por autoatendimento para usuários de nuvem
- Ferramenta de sincronização de diretório: para sincronização entre o Active Directory local e o Active Directory do Azure
- Relatórios de segurança padrão

## Comparar edições: recursos Premium e Basic

- Tempo de atividade de SLA de alta disponibilidade (99,9%)
- Provisionamento e gerenciamento de acesso a aplicativos com base em grupo
- Personalização do logotipo e cores da empresa para as páginas de Entrada e Painel de acesso
- Redefinição de senha por autoatendimento para usuários de nuvem
- Proxy de aplicativo: acesso remoto seguro e SSO a aplicativos Web locais

## Comparar edições: somente recursos Premium

- Gerenciamento de grupos por autoatendimento para usuários de nuvem
- Redefinição de senha por autoatendimento com write-back local
- Licenças de servidor MIM (Microsoft Identity Manager) – para sincronizar entre bancos de dados e/ou diretórios locais e o Active Directory do Azure
- Relatórios de segurança avançados sobre anomalias (baseados em aprendizagem de máquina)
- [Descoberta de aplicativos de nuvem](http://channel9.msdn.com/Series/EMS/Azure-Cloud-App-Discovery)
- Relatórios avançados sobre uso de aplicativos
- Serviço Multi-Factor Authentication para usuários de nuvem
- Servidor Multi-Factor Authentication para usuários locais

## Recursos atualmente em visualização pública

Os recursos a seguir estão atualmente em visualização pública e serão adicionados em breve:

- [Unidades administrativas](https://msdn.microsoft.com/library/azure/dn832057.aspx): um novo contêiner de recursos do Active Directory do Azure que pode ser usados para delegar permissões administrativas em subconjuntos de usuários e aplicar políticas a um subconjunto de usuários.
- [Adicione seus próprios aplicativos SaaS](https://msdn.microsoft.com/library/azure/dn893637.aspx) ao Active Directory do Azure.
- [Azure Active Directory Connect Health](https://msdn.microsoft.com/library/azure/dn906722.aspx): monitore a integridade de sua infraestrutura local do Active Directory e obtenha a análise de uso.
- Substituição de senha para LinkedIn, Facebook e Twitter. Para obter mais informações, leia [este artigo](http://blogs.technet.com/b/ad/archive/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview.aspx).
- Associação de grupo dinâmico. Para obter mais informações, consulte [este artigo](https://msdn.microsoft.com/library/azure/dn913807.aspx).
- [Acesso condicional](https://msdn.microsoft.com/library/azure/dn906877.aspx): Multi-Factor Authentication por aplicativo.
- Integração de aplicativos de RH: jornada de trabalho
- Privileged Identity Management: o gerenciamento de identidades com privilégios fornece supervisão aprimorada para ajudar a atender aos requisitos de conformidade a normas e contratos de nível de serviço.
- Solicitações de aplicativos de autoatendimento: os administradores podem fornecer uma lista de aplicativos SaaS para usuários de modo que os usuários possam escolher aqueles que desejam usar, e os aplicativos ou estarão disponíveis imediatamente ou depois da aprovação.
- API de relatório do Azure: os dados para cada relatório de segurança do Active Directory do Azure estarão disponíveis para outras ferramentas SIEM ou de monitoramento.


## O que vem a seguir

- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Adicionar identidade visual da empresa às páginas de Entrada e do Painel de acesso](active-directory-add-company-branding.md)
- [Exibir relatórios de acesso e de uso](active-directory-view-access-usage-reports.md)
 

<!---HONumber=58_postMigration-->