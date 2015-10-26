<properties 
    pageTitle="Posso depender do AD do Azure para habilitar o logon único (SSO) para todos os meus aplicativos | Microsoft Azure" 
    description="Saiba como o Active Directory do Azure ajuda a estender o escopo de uma identidade e seu gerenciamento." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/12/2015" 
    ms.author="markvi" />


# Posso depender do AD do Azure para habilitar o logon único (SSO) para todos os meus aplicativos
Este artigo destina-se a tomadores de decisões de TI que desejam conhecer o valor comercial que o Active Directory do Azure fornece aos aplicativos que sua organização usa.

## Aplicativos em execução em um ambiente baseado em nuvem  

Hoje, em um mundo baseado na nuvem, você pode encontrar milhares de aplicativos para fazer qualquer trabalho. Os departamentos de TI muitas vezes nem conhecem todos os aplicativos SaaS que são usados em suas organizações. Em muitos casos, o uso do aplicativo é conhecido, mas obter algum nível de controle e de segurança é caro e demorado. Muitos dos aplicativos não oferecem suporte a integração com sistemas de identidade corporativa. Em outros casos, integrações únicas são evitadas devido ao custo e à complexidade.

Como resultado,

- Muitas organizações têm preocupações com o acesso não autorizado a dados corporativos, perda de dados e outros riscos de segurança inerentes ao aplicativo introduzido por aplicativos não gerenciados. Como elas ainda não sabem quantos ou quais aplicativos estão sendo usados, começar a criar um plano para lidar com esses riscos parece preocupante.
- Os administradores precisam gerenciar individualmente todos os provedores de serviços IAM introduzidos por aplicativos gerenciados no seu ambiente. Isso inclui tarefas como criar e excluir usuários e grupos, bem como conceder ou revogar acesso a esses aplicativos.
- Os usuários precisam memorizar várias credenciais para acessar os aplicativos com que precisam trabalhar. Senhas esquecidas representam um enorme impacto nos custos operacionais de muitas organizações. O que todos esses problemas têm em comum é que eles têm um impacto negativo na produtividade dos usuários e nos custos operacionais.  
 
## Como o Active Directory do Azure ajuda?
O Active Directory do Azure ajuda a resolver esses problemas, permitindo que você facilmente estenda sua infraestrutura existente de identidade para a nuvem e com isso:

- Estenda o alcance de seus usuários móveis para qualquer aplicativo 
- Estenda o alcance do gerenciamento de acesso para qualquer aplicativo em nuvem 
- Detecte os aplicativos usados acessados pelos usuários


### Estenda o alcance de sua identidade para qualquer aplicativo 

Por padrão, o Active Directory do Azure fornece SSO para aplicativos que são hospedados no Azure e em outros serviços online da Microsoft como o Office 365, o CRM Online e o Intune.

Além disso, ele pode ser seu agente de identidade para logon único para aplicativos em qualquer nuvem pública, como Google, Amazon, IBM e outras. <br> Se um aplicativo é amplamente conhecido ou popular, iremos pré-integrá-lo à galeria de aplicativos do Active Directory do Azure. Hoje, você pode encontrar em torno de 2.500 aplicativos pré-integrados na galeria de aplicativos e o número está crescendo continuamente. <br> Basta alguns cliques para configurar o SSO para qualquer um dos aplicativos pré-integrados da galeria de aplicativos. E se meu aplicativo não estiver na galeria de aplicativos ainda? Você pode integrar qualquer aplicativo público ao seu Active Directory do Azure usando um assistente e, assim, habilitar o SSO para eles. Para obter mais detalhes, consulte [Implantando o logon único usando o Active Directory do Azure para aplicativos SaaS recém-adquiridos](active-directory-single-sign-on-newly-acquired-saas-apps.md) e [Integrar o SSO do Active Directory do Azure com aplicativos existentes](active-directory-sso-integrate-existing-apps.md)<br> E quanto aos aplicativos desenvolvidos pela sua organização? Podemos fornecer a documentação relacionada que permite que os desenvolvedores integrem facilmente aplicativos desenvolvidos pela sua organização ao Active Directory do Azure. Se você quiser saber mais sobre isso, consulte [AD do Azure e aplicativos: orientando desenvolvedores](active-directory-applications-guiding-developers-for-lob-applications.md).

Fornecendo suporte aos aplicativos pré-integrados, a aplicativos públicos que não estão na galeria de aplicativos e aplicativos que sua organização desenvolveu, o Active Directory do Azure permite oferecer o SSO para todos os seus aplicativos em nuvem.

O valor do Active Directory do Azure vai além de "simples" aplicativos em nuvem. Com o Active Directory do Azure, você também pode estender o alcance de identidades do Azure, fornecendo acesso remoto seguro aos aplicativos locais por meio do SSO. Isso significa acesso remoto que não exige tecnologias como VPNs ou outras infraestruturas tradicionais de acesso remoto para acessar aplicativos Web locais. O Active Directory do Azure oferece essa funcionalidade por meio de recursos de proxy de aplicativo.

O modelo tradicional de SSO é baseado em um mapeamento de duas contas individuais em dois repositórios de identidades. Com o Active Directory do Azure, você pode mapear até mesmo uma conta individual com uma conta corporativa compartilhada, como sua conta do Twitter corporativa. Ao implementar o SSO para suas contas compartilhadas corporativas, não é necessário compartilhar as credenciais da conta com os usuários, o que melhora significativamente a proteção dessas contas. Para saber mais sobre isso, veja [Compartilhando contas com o AD do Azure](active-directory-sharing-accounts.md)

Ao estender o alcance de suas identidades, uma única senha fornece acesso a milhares de aplicativos.



### Estendendo o alcance do gerenciamento de acesso para qualquer aplicativo em nuvem

Gerenciar o acesso a seus aplicativos em nuvem é caro se você tiver que fazer isso manualmente para cada aplicativo. Com o Active Directory do Azure, você pode gerenciar o acesso aos seus aplicativos em nuvem com base em grupos de um ponto central - o portal do Azure. Você pode atribuir acesso para usuários individuais ou grupos. Para obter mais detalhes, consulte [Gerenciar o acesso aos aplicativos](active-directory-managing-access-to-apps.md).

Alguns aplicativos, como Salesforce, Box, Google Apps e Concur, fornecem interfaces de automação para criação e remoção ou desativação das contas. Se um provedor oferece uma interface desse tipo, ele é aproveitado pelo Active Directory do Azure. Em outras palavras, o Active Directory do Azure fornece suporte para o provisionamento automatizado de usuários para aplicativos que oferecem uma interface de automação relacionada.

Com o provisionamento automatizado de usuários:

- Quando um usuário tiver recebido acesso a um aplicativo relacionado, o Active Directory do Azure provisionará automaticamente a conta de SSO necessária ao aplicativo.
- Quando uma conta de usuário no Active Directory do Azure for excluída, a conta no aplicativo é desativada ou excluída. A configuração do provisionamento automatizado de usuários oferece os seguintes benefícios:
- 	Reduz os custos operacionais porque automatiza tarefas administrativas que precisam ser executadas por sua equipe de TI.
- Melhora a segurança do seu ambiente, pois diminui a chance de que exista acesso desnecessário a aplicativos.

Para obter mais detalhes sobre o provisionamento automatizado de usuários, consulte [Provisionamento e desprovisionamento automatizados de usuários para aplicativos SaaS com o Active Directory do Azure](active-directory-saas-app-provisioning.md).


### Detectando os aplicativos usados acessados pelos usuários

Com o provisionamento de conta e o SSO, o Active Directory do Azure fornece mecanismos poderosos para aprimorar como os usuários e administradores podem trabalhar com aplicativos em seu ambiente dos quais você tem conhecimento. No entanto, nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos de nuvem que são usados. Com o Cloud App Discovery, o Active Directory do Azure fornece também uma solução para detectar esses aplicativos. Com o Cloud App Discovery é possível:

- Descobrir aplicativos em uso e medir o uso pelo número de usuários, volume de tráfego ou número de solicitações Web ao aplicativo.
- Identificar os usuários que estão usando um aplicativo.
- Exportar dados para adição de análise offline.
- Priorizar aplicativos a serem colocados sob o controle da TI e integrá-los facilmente para habilitar o gerenciamento de usuário e logon único.

Para obter mais detalhes sobre o Cloud App Discovery, consulte [Como descobrir aplicativos na nuvem não aprovados, usados em minha organização](active-directory-cloudappdiscovery-whatis.md)


## Como posso começar?

Primeiro, se você ainda não estiver usando o AD do Azure e for um administrador de TI:

- [Experimente!](https://azure.microsoft.com/trial/get-started-active-directory) - você pode se inscrever em uma avaliação gratuita de 30 dias hoje mesmo e implantar sua primeira solução de nuvem em menos de 5 minutos usando este link

<!---HONumber=Oct15_HO3-->