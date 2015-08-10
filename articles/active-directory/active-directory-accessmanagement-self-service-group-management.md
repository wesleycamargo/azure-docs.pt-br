< propriedades pageTitle = "Configurando o AD do Azure para management| de acesso do aplicativo de autoatendimento Descrição do Microsoft Azure"="um tópico que explica como gerenciar grupos no AD do Azure."serviços ="active directory"documentationCenter =" "autores ="femila"manager ="swadhwa"" editor = "" tags = "portal de clássico do azure" / >

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/13/2015" 
	ms.author="femila"/>

#Configurando o AD do Azure para gerenciamento de acesso de aplicativos de autoatendimento

O gerenciamento de grupo de autoatendimento permite aos usuários criar e gerenciar grupos de segurança no Microsoft Azure Active Directory (AD) e oferece aos usuários a possibilidade de associações de grupo de segurança à solicitação, que podem ser subsequentemente aprovadas ou negadas pelo proprietário do grupo. Usando recursos de gerenciamento de grupo de autoatendimento, o controle diário de associação de grupo pode ser delegado à pessoa que compreender o contexto dos negócios naquela associação.

O gerenciamento de grupo de autoatendimento atualmente está composto de dois cenários essenciais:gerenciamento de grupo delegado e gerenciamento de grupo de autoatendimento.


- **Gerenciamento de grupo delegado** veja o exemplo de um administrador que está gerenciando o acesso a um aplicativo SaaS que sua empresa está usando. O gerenciamento desses direitos de acesso está se tornando inconveniente, portanto esse administrador solicita ao proprietário de negócios para criar um novo grupo. O administrador agora atribui acesso para o aplicativo para um novo grupo que o proprietário da empresa acabou de criar e coloca todas as pessoas que atualmente têm acesso ao aplicativo dentro desse grupo. O proprietário da empresa, em seguida, pode adicionar mais usuários e os usuários são automaticamente provisionados em momentos do aplicativo. O proprietário da empresa não precisa esperar que o administrador faça o trabalho, mas pode gerenciar ele mesmo o acesso para seus usuários. O administrador pode fazer a mesma coisa com um assistente administrativo para um grupo de negócios diferentes e o proprietário da empresa e o assistente administrativo agora pode gerenciar o acesso para seus usuários – sem ser capaz de tocar ou ver uns dos outros usuários. O administrador ainda pode ver todos os usuários que têm acesso ao aplicativo e direitos de acesso de bloco, se necessário.


- **Gerenciamento de grupo de autoatendimento**- veja o exemplo de dois usuários que possuem sites do SharePoint Online que eles configuram independentemente, mas eles realmente gostariam de facilitar o fornecimento de acesso de entre as equipes. Portanto, eles criam um grupo no AD do Azure e no SharePoint Online cada um deles seleciona esse mesmo grupo para fornecer acesso a seus sites. Quando alguém deseja acesso, solicita do painel de acesso, e depois da aprovação obtêm acesso a ambos os sites do SharePoint Online automaticamente. Posteriormente, um deles decide que todas as pessoas que acessam o site também devem obter acesso a um determinado aplicativo SaaS. Ele solicita ao administrador de seu aplicativo SaaS para adicionar direitos de acesso para este aplicativo a seu site. Daí em seguida diante, quaisquer solicitações que ele aprovar dará acesso para os dois sites SharePoint Online e também para este aplicativo de SaaS.



##Disponibilização de um grupo para o usuário final de autoatendimento

No Portal de gerenciamento do Azure, na guia Configuração, defina a opção de gerenciamento de grupo delegado como Habilitada e, em seguida, defina a opção Usuários podem criar de grupos como Habilitada.

Quando a opção **Usuários podem criar grupos** é definida como**Habilitada**, todos os usuários em seu diretório têm permissão para criar novos grupos de segurança e adicionar membros a esses grupos. Observe que esses novos grupos também deverão aparecer no Painel de acesso para todos os outros usuários e que outros usuários podem criar solicitações para ingressar nesses grupos, se a configuração de política de grupo permitir isso. Se essa opção for definida como desativado, os usuários não poderão criar grupos e não será possível alterar os grupos existentes que são proprietários, mas ainda podem gerenciar os membros desses grupos e aprovar solicitações de outros usuários para ingressar em seus grupos.

Você também pode usar os usuários que podem usar o autoatendimento para a opção de grupos de segurança para obter um controle de acesso mais refinado sobre os recursos de gerenciamento de grupo de autoatendimento para seus usuários. Quando a opção Usuários podem criar grupos está definida como Habilitada, todos os usuários em seu diretório têm permissão para criar novos grupos de segurança e adicionar membros a esses grupos. Definindo também os usuários que podem usar autoatendimento para grupos de segurança para alternar para Alguns, você está restringindo o gerenciamento de grupo de segurança para apenas um grupo limitado de usuários. Quando essa opção estiver definida para Alguns, um grupo chamado SSGMSecurityGroupsUsers é criado no diretório e apenas aos usuários que você associou a esse grupo podem criar novos grupos de segurança e adicionar membros a esses grupos em seu diretório. Definindo os usuários que podem usar autoatendimento para alternar de grupos de segurança para todos, você permite que todos os usuários em seu diretório criem novos grupos de segurança.

Você também pode usar o grupo que pode usar o autoatendimento para o campo de grupos de segurança (definido pelo padrão para SSGMSecurityGroupsUsers para especificar seu próprio nome personalizado para um grupo que irá conter todos os usuários com a capacidade de usar o autoatendimento e criar novos grupos de segurança em seu diretório.

Estes são alguns tópicos que fornecem informações adicionais sobre o Active Directory do Azure

* [Gerenciando acesso a recursos com grupos do Active Directory do Azure](active-directory-manage-groups.md)

* [O que é o Active Directory do Azure?](active-directory-whatis.md)

* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

<!---HONumber=July15_HO5-->