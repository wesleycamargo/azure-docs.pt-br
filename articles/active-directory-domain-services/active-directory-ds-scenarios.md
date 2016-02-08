<properties
	pageTitle="Visualização dos Serviços de Domínio do Active Directory do Azure: cenários de implantação | Microsoft Azure"
	description="Cenários de implantação dos Serviços de Domínio do Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>


# Cenários de implantação e casos de uso
Nesta seção, vamos dar uma olhada em alguns cenários e casos de uso que podem aproveitar os Serviços de Domínio do AD (Active Directory do Azure).

## Administração segura e fácil de máquinas virtuais do Azure
Conforme servidores e outras infraestruturas atingem o fim da vida útil, a Contoso está movendo muitos aplicativos atualmente hospedados no local para a nuvem. O padrão de TI atual exige que os servidores que hospedam aplicativos corporativos estejam ingressados no mesmo domínio e gerenciados usando a Política de Grupo. O administrador de TI da Contoso prefere ingressar as máquinas virtuais implantadas no Azure no mesmo domínio para facilitar a administração (ou seja, administradores podem fazer logon usando credenciais corporativas). A Contoso prefere não ter que implantar, monitorar e gerenciar controladores de domínio no Azure para proteger máquinas virtuais do Azure.

Alguns pontos importantes a se observar ao considerar este cenário:

- Os domínios gerenciados fornecidos pelos Serviços de Domínio do AD do Azure dão suporte a somente uma estrutura de UO (unidade organizacional) simples. Todos os computadores com ingresso no domínio residem em uma única UO simples e não há suporte para estruturas de UO hierárquicas.
- Os Serviços de Domínio do AD do Azure dão suporte à política de grupo simples na forma de um GPO interno para os usuários e um para os contêineres de computador. Você não pode definir GP por departamento/UO, realizar a filtragem WMI ou criar GPOs personalizados.
- Os Serviços de Domínio do AD do Azure dão suporte ao esquema do objeto de computador AD base. Você não pode estender o esquema do objeto de computador.


## Fazer a mudança de aplicativos locais que usam autenticação de associação LDAP para os Serviços de Infraestrutura do Azure
A Contoso tem um aplicativo local que foi comprado de um ISV há muitos anos. O aplicativo está atualmente no modo de manutenção pelo ISV e a solicitação de alterações no aplicativo é extremamente dispendioso para Contoso. Este aplicativo tem um front-end baseado na Web que coleta credenciais de usuário usando um formulário da Web e autentica usuários executando uma associação LDAP para o Active Directory corporativo. A Contoso deseja migrar esse aplicativo para os Serviços de Infraestrutura do Azure. É recomendável que o aplicativo funcione como está, sem exigir nenhuma alteração. Além disso, os usuários devem ser capazes de autenticar usando suas credenciais corporativas existentes e sem ter que ser treinados novamente para fazer coisas de forma diferente. Em outras palavras, os usuários finais não devem se lembrar de onde o aplicativo está sendo executado e a migração deve ser clara para eles.

Alguns pontos importantes a se observar ao considerar este cenário:

- Verifique se o aplicativo não precisa modificar/gravar no diretório. Não há suporte a acesso para gravação LDAP para domínios gerenciados fornecidos pelos Serviços de Domínio do Azure AD.
- Os usuários não podem alterar a senha diretamente em relação ao domínio. Os usuários podem alterar suas senhas ou usando o mecanismo de alteração de senha de autoatendimento do Azure AD ou no diretório local. Essas alterações serão automaticamente sincronizadas e estarão disponíveis no domínio gerenciado.


## Fazer a mudança de aplicativos locais que usam leitura LDAP para acessar o diretório que leva aos Serviços de Infraestrutura do Azure
A Contoso tem uma linha local de aplicativo de negócios desenvolvida quase uma década atrás. Esse aplicativo percebe diretórios e foi projetado para funcionar com o AD do Windows Server. O aplicativo usa LDAP (Lightweight Directory Access Protocol) para ler informações/atributos sobre os usuários do Active Directory. O aplicativo não modifica atributos ou gravar no diretório de forma alguma. A Contoso deseja migrar esse aplicativo para os Serviços de Infraestrutura do Azure e desativar o hardware local antigo que atualmente hospeda esse aplicativo. O aplicativo não pode ser reescrito para usar APIs de diretório modernas, como a Graph API do Azure AD baseada em REST. Portanto, uma opção de mudança é desejada por meio da qual o aplicativo possa ser migrado para executar na nuvem sem modificar o código ou reescrever o aplicativo.

Alguns pontos importantes a se observar ao considerar este cenário:

- Verifique se o aplicativo não precisa modificar/gravar no diretório. Não há suporte a acesso para gravação LDAP para domínios gerenciados fornecidos pelos Serviços de Domínio do Azure AD.
- Verifique se o aplicativo não precisa de um esquema do Active Directory estendido/personalizado. As extensões de esquema não têm suporte nos Serviços de Domínio do AD do Azure.
- Verifique se o aplicativo não exige acesso LDAPS. LDAPS não tem suporte dos Serviços de Domínio do AD do Azure.


## Migrar um aplicativo de serviço ou daemon do local para os Serviços de Infraestrutura do Azure
A Contoso tem um aplicativo personalizado de cofre que inclui um front-end da Web, um SQL Server e um servidor FTP de back-end. Eles aproveitam a autenticação integrada do Windows usando contas de serviço para autenticar o front-end da Web para o servidor FTP. Eles gostariam de mover esse aplicativo para os Serviços de Infraestrutura do Azure e preferem não ter que implantar uma máquina virtual do controlador de domínio na nuvem para dar suporte às necessidades de identidade do aplicativo. O administrador de TI da Contoso pode implantar os servidores que hospedam o front-end da Web, o SQL server e o servidor FTP para máquinas virtuais no Azure e ingressá-los em um domínio dos Serviços de Domínio do AD do Azure. Em seguida, eles podem usar a mesma conta de serviço em seu diretório para fins de autenticação do aplicativo.

Alguns pontos importantes a se observar ao considerar este cenário:

- Verifique se o aplicativo usa o nome de usuário e senha para autenticação. A autenticação com certificado/cartão inteligente não tem suporte dos Serviços de Domínio do AD do Azure.


## RemoteApp do Azure
O RemoteApp do Azure permite que o administrador do Contoso crie uma coleção associada a um domínio. Isso permite que aplicativos remotos atendidos pelo RemoteApp do Azure sejam executados em computadores associados ao domínio e acessem outros recursos usando a autenticação integrada do Windows. O Contoso pode usar os Serviços de Domínio do AD do Azure para fornecer um domínio gerenciado usado pelas coleções associadas ao domínio do RemoteApp do Azure.

Para saber mais sobre esse cenário de implantação, consulte o artigo de Blog de Serviços de Área de Trabalho Remota intitulado [Lift-and-shift your workloads with Azure RemoteApp and AD do Azure Domain Services](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx).

<!---HONumber=AcomDC_0128_2016-->