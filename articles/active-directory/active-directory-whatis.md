<properties
	pageTitle="O que é o Active Directory do Azure?"
	description="Use o Active Directory do Azure para estender suas identidades locais existentes para a nuvem ou desenvolva aplicativos integrados do AD do Azure."
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="terrylan"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/11/2015"
	ms.author="curtand"/>


# O que é o Active Directory do Azure?


[Como ele funciona?](active-directory-works.md)<br> [Introdução](active-directory-get-started.md)<br> [Próximas etapas](active-directory-next-steps.md)<br> [Saiba mais](active-directory-learn-map.md)

O Active Directory do Azure (AD do Azure) oferece uma maneira fácil para que sua empresa estenda seus investimentos locais para tirar proveito dos serviços de nuvem, com a flexibilidade e a segurança que o Active Directory sempre forneceu. Ele é uma plataforma de nuvem que soluciona problemas da era de nuvem, estendendo a infraestrutura de identidades que você já tem sem a necessidade de mover nada. Você pode continuar a se beneficiar dos seus investimentos existentes e recursos locais, enquanto aproveita o AD do Azure para obter o gerenciamento de identidade e acesso na nuvem.

Com o AD do Azure, você pode controlar centralmente o acesso a aplicativos e recursos, adicionar recursos existentes (serviços de nuvem ou aplicativos locais) com facilidade e integrar aplicativos que você está desenvolvendo. - Os usuários podem obter recursos por autoatendimento e acesso de logon único para todos os seus aplicativos para que eles não precisem saber onde localizar cada aplicativo, nem precisem lembrar de uma senha separada. - Sua empresa pode colaborar na nuvem com parceiros comerciais, fornecedores e clientes comerciais que você pode convidar para sites do SharePoint (ou participar dos sites do SharePoint deles).

Com o AD do Azure, você pode gerenciar tudo em um único local e ter esse controle refletido tanto na nuvem quanto localmente. Não há duplicação de administração; o AD do Azure se conecta diretamente ao que você já tem. [Saiba mais](active-directory-aadconnect.md).




> [AZURE.NOTE]Para usar o Active Directory do Azure, você precisa de uma conta do Azure. Se você ainda não tem uma conta, você pode [inscrever-se para uma conta gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/).


## O que o Active Directory do Azure pode fazer para mim?

Como é um serviço abrangente, o AD do Azure oferece benefícios diferentes para pessoas realizando diferentes funções em uma organização.

- Se você é um tomador de decisões comerciais, use o AD do Azure para alcançar a promessa de aplicativos de nuvem e uma força de trabalho móvel com a certeza de seus requisitos de governança serem atendidos.
- Para provedores de serviço, o AD do Azure permite que você lide facilmente com todas as suas necessidades de identidade e acesso, conectando os seus serviços às soluções de identidade existentes dos clientes, enquanto lhe fornece alcance aos clientes do Microsoft Azure e do Office 365. O AD do Azure também pode lidar com todas as suas necessidades de acesso de back-office; portanto, seja ele interno ou externo, você pode ter certeza de que as pessoas certas terão o acesso correto.
- Se você é um profissional de TI, use o AD do Azure para aumentar o controle e visibilidade das operações na “velocidade da nuvem”. Com o AD do Azure, você saberá o que as pessoas estão usando e as capacitará por meio de ofertas por autoatendimento. <br> <br>

![][1]

##As nove coisas mais importantes que você deve saber sobre o AD do Azure

### Gerenciar senhas do Active Directory de qualquer lugar

Use o Active Directory do Azure para capacitar os usuários a gerenciarem suas senhas do Active Directory ou do Active Directory do Azure em qualquer dispositivo, em qualquer local. Um administrador pode gerenciar políticas de senha e notificação, e pode ver a atividade de auditoria detalhada dessas operações de redefinição de senha no momento em que elas ocorrem. [Saiba mais sobre gerenciamento de senhas do AD do Azure](http://aka.ms/ssproverview)

### Definir regras para o acesso aos recursos de nuvem

Além disso, você pode definir as regras e políticas que controlam quem tem acesso a recursos e aplicativos de nuvem, e sob quais condições. Por exemplo, você pode exigir uma autenticação multifator (MFA) e gerenciar o acesso com base no dispositivo ou local. [Saiba mais sobre a MFA do Azure](../multi-factor-authentication.md).

### Logon único em qualquer aplicativo

O Active Directory do Azure fornece logon único seguro para aplicativos de nuvem e locais, incluindo o Microsoft Office 365 e milhares de aplicativos SaaS, como Salesforce, Workday, DocuSign, ServiceNow e Box. [Saiba mais sobre os Aplicativos SaaS](http://azure.microsoft.com/marketplace/active-directory/).

### Funciona com qualquer dispositivo

Os usuários podem iniciar aplicativos de um painel de acesso personalizado baseado na Web, aplicativos móveis, Office 365 ou portais da empresa personalizados utilizando suas credenciais de trabalho existentes — e ter a mesma experiência independentemente de estarem funcionando em dispositivos Windows, Mac OS X, Android ou iOS.

### Definir regras para acesso externo

Os usuários externos podem entrar na rede local através do firewall com segurança, usando o proxy de aplicativo interno. Todas as regras e políticas, incluindo a Multi-Factor Authentication, podem ser aplicadas para o acesso a aplicativos na nuvem ou aplicativos herdados locais usando o Proxy de Aplicativo - sem a necessidade de reescrevê-los ou expô-los diretamente na Internet. [Saiba mais sobre o Proxy de Aplicativo do AD do Azure](https://msdn.microsoft.com/library/azure/dn768219.aspx).

### Monitorar o acesso dos usuários

Por fim, o AD do Azure fornece informações sobre o que está acontecendo em sua organização ao seu alcance. Com relatórios avançados e análises, você obtém informações exclusivas sobre o acesso dos usuários. Por exemplo, usando a detecção de aplicativos, você pode descobrir quais aplicativos são usados ativamente em sua organização. [Saiba mais sobre a descoberta de aplicativos de nuvem do AD do Azure](https://appdiscovery.azure.com/).

### Análise, auditoria e relatórios avançados
O AD do Azure fornece vários relatórios diários de acesso e uso para administradores ([Relatórios do AD do Azure](active-directory-view-access-usage-reports.md)), e relatórios personalizados e técnicas avançadas para dados estão disponíveis usando a API de geração de relatórios ([API de geração de relatórios do AD do Azure](active-directory-reporting-api-getting-started.md)). Ainda mais relatórios estão disponíveis nas edições pagas do AD do Azure, incluindo a auditoria de ações privilegiadas ([Auditoria do AD do Azure](active-directory-view-access-usage-reports.md)).

### Uma experiência simples e segura para todos

Exemplos: - os usuários finais obtêm uma experiência simples, colocando seus perfis, aplicativos, além da capacidade de gerenciar o acesso a recursos em um único lugar, sem a necessidade de treinamento especializado. Autenticação multifator, aplicativos SaaS, ferramentas híbridas e recursos de autoatendimento estão todos prontos.

- Os administradores têm acesso ao portal de gerenciamento do AD do Azure e ao Windows PowerShell para um gerenciamento abrangente.

- Os desenvolvedores têm um conjunto consistente de APIs RESTful e acesso fácil à publicação e consumo das interfaces de aplicativo.

### Selecione sua preferência

O AD do Azure tem três tipos:

- A edição Gratuito, que é apenas um serviço de diretório de nuvem.
- A edição Basic, que é um serviço de diretório de nuvem que também fornece acesso a aplicativos SaaS.
- A Premium Edition, que é uma solução de serviço de diretório gerenciado abrangente, voltado para regras e por autoatendimento.

Para verificar os recursos descritos aqui, ative a [Avaliação gratuita do Azure](http://azure.microsoft.com/trial/get-started-active-directory/).

[Saiba mais sobre as edições do AD do Azure](active-directory-editions.md)


## Recursos adicionais

* [Inscrever-se no Azure como uma organização](sign-up-organization.md)
* [Identidade do Azure](fundamentals-identity.md)

<!--Image references-->
[1]: ./media/active-directory-whatis/Azure_Active_Directory.png

<!---HONumber=58--> 