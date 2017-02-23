---
title: Publicar aplicativos em redes e locais separados usando grupos de conectores no Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Aborda como criar e gerenciar grupos de conectores no Proxy de Aplicativo do Azure AD.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: d169720d01d543bb199a118aea7e34d9da45b2c1
ms.openlocfilehash: 2fafc207826db48d3fea01bb2cac9689b9784060


---

# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publicar aplicativos em redes e locais separados usando grupos de conectores
> [!div class="op_single_selector"]
> * [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
> * [Portal clássico do Azure](active-directory-application-proxy-connectors.md)
>

## <a name="azure-ad-application-proxy-and-connector-groups"></a>Proxy de Aplicativo do Azure e grupos de conectores

Os clientes utilizam o Proxy de Aplicativo do Azure AD para cada vez mais cenários e aplicativos. Então, tornamos o Proxy de Aplicativo ainda mais flexível por meio de mais topologias. Você pode criar grupos de conectores de Proxy de Aplicativo – um novo recurso para atribuir conectores específicos a fim de atender a aplicativos específicos. Esse recurso gera muitos casos de uso para o Proxy de Aplicativo que antes não eram possíveis. Durante a fase de visualização privada, vimos clientes de grande porte habilitar os grupos de conectores a fim de impulsionar suas implantações do Proxy de Aplicativo. 

O conceito básico é que cada conector do Proxy de Aplicativo é atribuído a um grupo de conectores. Todos os conectores que pertencem ao mesmo grupo de conectores agem como um grupo separado com relação à alta disponibilidade e ao balanceamento de carga. Por padrão, todos os conectores pertencem a um grupo padrão. O administrador pode criar novos grupos e alterar essas atribuições no Portal do Azure. 

Por padrão, todos os aplicativos são atribuídos a um grupo de conector padrão. Se o seu administrador não alterar nada, o sistema continuará se comportando como fazia antes. Se você não alterar nada, todos os aplicativos atribuídos ao grupo de conectores padrão incluirão todos os conectores. Mas se você organizar seus conectores em grupos, poderá configurar cada aplicativo para trabalhar com um grupo específico de conectores. Nesse caso, apenas os conectores nesse grupo atenderão ao aplicativo mediante solicitação.


>[!NOTE] 
>Como os novos conectores são automaticamente atribuídos a um grupo de conectores padrão, para implantações de grande porte, recomendamos que você não atribua os aplicativos ao grupo padrão. Portanto, uma vez instalados, novos conectores não receberão qualquer tráfego ativo. Somente após a atribuição do conector a um dos grupos ativos, ele poderá começar a servir o tráfego ativo. Isso também permite que você coloque conectores em um modo ocioso a fim de permitir a manutenção.
>

## <a name="prerequisite-create-your-connector-groups"></a>Pré-requisito: criar seus grupos de conectores
Para agrupar seus conectores, você precisa assegurar-se de ter [instalado vários conectores](active-directory-application-proxy-enable.md). Quando você instala um novo conector, ele automaticamente se junta ao grupo de conector **Padrão** .

## <a name="step-1-create-connector-groups"></a>Etapa 1: criar grupos de conectores
Você pode criar quantos grupos de conectores desejar. A criação de grupo de Conectores é feita no [portal do Azure](https://portal.azure.com).

1. Selecione **Azure Active Directory** para ir para o painel de gerenciamento para seu diretório. Aqui, selecione **Aplicativos empresariais** > **Proxy de aplicativo**.
2. Selecione o botão **Grupos de Conector** . A folha Novo Grupo de Conectores é exibida.
3. Nomeie o novo grupo de conectores e use o menu suspenso para selecionar quais conectores pertencem a esse grupo.
4. Selecione **Salvar** quando seu Grupo de conectores estiver concluído.

## <a name="step-2-assign-applications-to-your-connector-groups"></a>Etapa 2: atribuir aplicativos aos grupos de conectores
A última etapa é atribuir cada aplicativo ao grupo de conectores que vai servi-lo.

1. No painel de gerenciamento do seu diretório, selecione **Aplicativos empresariais** > **Todos os aplicativos** > o aplicativo que você deseja atribuir a um grupo de conectores > **Proxy de Aplicativo**.
2. Em **Grupo de conectores**, use o menu suspenso para selecionar o grupo que você deseja que o aplicativo use.
3. Clique em **Salvar** para aplicar a alteração.

## <a name="use-cases-for-connector-groups"></a>Casos de uso para grupos de conectores 

Os grupos de conectores são úteis para diversos cenários, incluindo:

###<a name="sites-with-multiple-interconnected-datacenters"></a>Sites com vários datacenters interconectados

Muitas organizações têm um número de datacenters interconectados. Nesses casos, você deseja manter o tráfego dentro do datacenter o máximo possível, porque links entre datacenters são caros e lentos. Você pode implantar conectores em cada datacenter para servir apenas os aplicativos que residem dentro do datacenter. Essa abordagem minimiza os links entre datacenters e fornece uma experiência totalmente transparente para os usuários.

### <a name="applications-installed-on-isolated-networks"></a>Aplicativos instalados em redes isoladas

Os aplicativos podem ser hospedados em redes que não fazem parte da rede corporativa principal. Você pode usar grupos de conectores para instalar conectores dedicados em redes isoladas para isolar também os aplicativos para a rede. Isso geralmente acontece quando um fornecedor de terceiros mantém um aplicativo específico para sua organização. 

Os grupos de conectores permitem que você instale conectores dedicados para essas redes que publicam apenas aplicativos específicos, facilitando e protegendo a terceirização do gerenciamento de aplicativos para os fornecedores de terceiros.

### <a name="applications-installed-on-iaas"></a>Aplicativos instalados no IaaS 

Para aplicativos instalados no IaaS para acesso à nuvem, os grupos de conector fornecem um serviço comum para proteger o acesso a todos os aplicativos. Os grupos de conectores não criam dependência adicional em sua rede corporativa nem fragmentam a experiência de aplicativo. Conectores podem ser instalados em cada datacenter na nuvem e servir apenas os aplicativos que residem nessa rede. Você pode instalar vários conectores para obter alta disponibilidade.

Nesse caso, a organização diversas máquinas virtuais conectadas à própria rede virtual de IaaS hospedada. Para permitir que os funcionários usem esses aplicativos, essas redes privadas são conectadas à rede corporativa usando VPN site a site. Isso proporciona uma boa experiência para os funcionários locais. Mas, talvez não seja ideal para funcionários remotos, pois exige uma infraestrutura local adicional, como você pode ver no diagrama a seguir:

![Rede IaaS do Azure](./media/application-proxy-publish-apps-separate-networks/application-proxy-iaas-network.png)
  
Isso pode se tornar um problema, pois muitas organizações usam vários fornecedores de nuvem, uma vez que seus aplicativos residem em vários datacenters. Com os grupos de conectores do Proxy de Aplicativo do Azure AD, você pode permitir que um serviço comum proteja o acesso a todos os aplicativos sem criar dependências adicionais em sua rede corporativa:

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-publish-apps-separate-networks/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Várias florestas – grupos de conectores diferentes para cada floresta

A maioria dos clientes que implantou o Proxy de Aplicativo usa seus recursos de SSO (Logon Único) executando a KCD (Delegação restrita de Kerberos). Para conseguir isso, as máquinas do conector precisam ser associadas a um domínio que possa delegar os usuários para o aplicativo. O KCD oferece suporte a recursos entre florestas. Porém, para as empresas que possuem ambientes diferentes de várias florestas sem qualquer relação de confiança entre eles, é possível usar um único conector para todas as florestas. 

Nesse caso, é possível implantar conectores específicos por floresta e configurá-los para atender aos aplicativos publicados para atender apenas aos usuários dessa floresta específica. Cada grupo de conectores representa uma floresta diferente. Embora o locatário, e a maior parte da experiência, seja unificado para todas as florestas, os usuários podem ser atribuídos aos seus aplicativos de floresta usando os grupos do Azure AD.
 
### <a name="disaster-recovery-sites"></a>Sites de Recuperação de Desastre

Há duas abordagens diferentes que podem ser executadas com um site de DR (recuperação de desastres), dependendo de como os sites foram implementados:

* Se o seu site de DR for criado no modo ativo-ativo, no qual ele é exatamente como o site principal e tem as mesmas configurações de rede e do AD, você poderá criar os conectores no site de DR no mesmo grupo de conectores do site principal. Isso permite que o Azure AD detecte failovers para você.
* Se o seu site de DR for separado do site principal, você poderá criar um grupo de conectores diferente no site de DR e ter 1) mais aplicativos ou 2) desviar manualmente o aplicativo existente para o grupo de conectores de DR, conforme o necessário.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Atender a várias empresas de um único locatário

Há várias maneiras de implementar um modelo no qual um único provedor de serviço implanta e mantém serviços relacionados ao Azure AD para diversas empresas. Os grupos de conectores ajudam o administrador a separar os conectores e os aplicativos em grupos diferentes. Uma maneira, que é adequada para pequenas empresas, é ter um único locatário do Azure AD, e cada empresas têm seu próprio nome de domínio e redes. Isso também vale para cenários de fusão e aquisição, e situações nas quais uma única divisão de TI atende a várias empresas por motivos regulatórios ou comerciais. 

## <a name="sample-configurations"></a>Exemplos de configuração

Entre os exemplos que você pode implementar, estão os grupos de conectores a seguir.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Configuração padrão – não usa grupos de conectores

Se você não usar grupos de conector, sua configuração terá esta aparência:

![AzureAD Sem grupos de conectores](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-1.png)
 
Essa configuração é suficiente para testes e pequenas implantações. Ela também funcionará bem se a sua organização tiver uma topologia de rede simples.
 
### <a name="default-configuration-and-an-isolated-network"></a>Configuração padrão e uma rede isolada

Essa configuração é uma evolução da padrão, pois há um aplicativo específico que é executado em uma rede isolada, como a rede virtual IaaS: 

![AzureAD Sem grupos de conectores](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Configuração recomendada – vários grupos específicos e um grupo padrão para ociosidade

A configuração recomendada para organizações complexas e de grande porte é ter o grupo de conectores padrão como um grupo que não atende aos aplicativos e é usado para conectores ociosos ou recém-instalados. Todos os aplicativos são atendidos usando grupos de conectores personalizados. Isso permite toda a complexidade dos cenários descritos acima.

No exemplo abaixo, a empresa tem dois data centers, A e B, com dois conectores que atendem a cada site. Cada site possui aplicativos diferentes em execução. 

![AzureAD Sem grupos de conectores](./media/application-proxy-publish-apps-separate-networks/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Próximas etapas
* [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
* [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
* [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
* [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)




<!--HONumber=Feb17_HO1-->


