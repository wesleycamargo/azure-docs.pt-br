---
title: "Perguntas frequentes — Serviços do domínio do Azure Active Directory | Microsoft Docs"
description: "Perguntas frequentes sobre os Serviços de Domínio do Active Directory do Azure"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2016
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 5e6bab265b2b6eabd1a878492588c4eb39d1b332
ms.openlocfilehash: 89dfabb8feafffee2ed8143c372b53d02033d582


---
# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Azure Active Directory Domain Services: perguntas frequentes
Esta página responde a perguntas frequentes sobre o Azure Active Directory Domain Services. Continue verificando as atualizações.

### <a name="troubleshooting-guide"></a>Guia de Solução de Problemas
Consulte nosso [Guia de solução de problemas](active-directory-ds-troubleshooting.md) a fim de obter soluções para os problemas comuns encontrados ao configurar ou administrar os Serviços de Domínio do AD do Azure.

### <a name="configuration"></a>Configuração
#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>Posso criar vários domínios para um único diretório do AD do Azure?
Não. Você só pode criar um único domínio atendido pelos Serviços de Domínio do AD do Azure para um único diretório do AD do Azure.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Posso habilitar os Serviços de Domínio do Azure AD em uma rede virtual do Azure Resource Manager?
Não. Os Serviços de Domínio do Azure AD só podem ser habilitados em uma rede virtual clássica do Azure. Você pode conectar a rede virtual clássica a uma rede virtual do Gerenciador de Recursos usando o emparelhamento de rede virtual para usar seu domínio gerenciado em uma rede virtual do Gerenciador de Recursos.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Posso disponibilizar os Azure AD Domain Services em várias redes virtuais na minha assinatura?
O próprio serviço não oferece suporte diretamente para esse cenário. Os Azure AD Domain Services só estão disponíveis em uma rede virtual de cada vez. No entanto, você pode configurar a conectividade entre várias redes virtuais para expor os Azure AD Domain Services a outras redes virtuais. Este artigo descreve como você pode [conectar redes virtuais no Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Posso habilitar os Serviços de Domínio do AD do Azure usando o PowerShell?
A implantação do PowerShell/automatizada dos Serviços de Domínio do AD do Azure não está disponível no momento.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>Os Serviços de Domínio do AD do Azure estão disponíveis no novo portal do Azure?
Não. Os Serviços de Domínio do Azure AD podem ser configurados somente no [Portal Clássico do Azure](https://manage.windowsazure.com). Esperamos estender o suporte para o [Portal do Azure](https://portal.azure.com) no futuro.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Posso adicionar controladores de domínio a um domínio gerenciado dos Serviços de Domínio do AD do Azure?
Não. O domínio fornecido pelo Azure AD Domain Services é um domínio gerenciado. Você não precisa provisionar, configurar ou gerenciar de outra forma os controladores de domínio para este domínio. Essas atividades de gerenciamento são fornecidas como um serviço pela Microsoft. Portanto, você não pode adicionar outros controladores de domínio (leitura-gravação ou somente leitura) ao domínio gerenciado.

### <a name="administration-and-operations"></a>Administração e operações
#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Posso conectar ao controlador de domínio para meu domínio gerenciado usando a Área de Trabalho Remota?
Não. Você não tem permissões para se conectar aos controladores de domínio do domínio gerenciado usando a Área de Trabalho Remota. Os membros do grupo “AAD DC Administrators” podem administrar o domínio gerenciado usando ferramentas de administração do AD como o ADAC (Active Directory Administration Center) ou o AD PowerShell. Essas ferramentas são instaladas usando o recurso “Ferramentas de Administração de Servidor Remoto” em um servidor Windows ingressado no domínio gerenciado.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Habilitei os Serviços de Domínio do AD do Azure. Qual conta de usuário eu posso usar para ingressar computadores no domínio para este domínio?
Os usuários que você adicionou ao grupo administrativo (por exemplo, "Administradores de DC do AAD") podem ingressar computadores no domínio. Além disso, os usuários desse grupo recebem acesso de área de trabalho remoto para computadores que ingressaram no domínio.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>Posso exercer privilégios de administrador de domínio para o domínio fornecido pelos Serviços de Domínio do AD do Azure?
Não. Você não recebe privilégios administrativos no domínio gerenciado. Os privilégios de "Administrador do Domínio" e de "Administrador Corporativo" não estarão disponíveis no domínio. O administrador de domínio existente ou os grupos de administradores corporativos no seu diretório do Azure AD também não receberão privilégios de administrador do domínio/administrador corporativo para o domínio.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>Posso modificar associações de grupo usando o LDAP ou outras ferramentas administrativas do AD em domínios fornecidos pelos Serviços de Domínio do AD do Azure?
Não. Associações de grupo não podem ser modificadas em domínios atendidos pelos Serviços de Domínio do AD do Azure. O mesmo se aplica a atributos de usuário. No entanto, é possível alterar associações de grupo ou atributos de usuário no AD do Azure ou em seu domínio local. Essas alterações serão sincronizadas automaticamente nos Azure AD Domain Services.

#### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo demora para que as alterações que eu fiz ao meu diretório do Azure AD fiquem visíveis em meu domínio gerenciado?
As alterações feitas ao seu diretório do Azure AD usando a interface do usuário do Azure AD ou o PowerShell são sincronizadas com o domínio gerenciado. Esse processo de sincronização ocorre em segundo plano. Após a conclusão da sincronização inicial do seu diretório, que só ocorre uma vez, geralmente demora cerca de 20 minutos para que as alterações feitas ao Azure AD sejam refletidas em seu domínio gerenciado.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>Posso estender o esquema do domínio fornecido pelos Serviços de Domínio do AD do Azure?
Não. O esquema é administrado pela Microsoft para o domínio gerenciado. As extensões de esquema não têm suporte dos Serviços de Domínio do AD do Azure.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Posso modificar ou adicionar registros DNS em meu domínio gerenciado?
Sim. Usuários que pertencem ao grupo "Administradores de DC do AAD" recebem privilégios de "Administrador de DNS" para modificar os registros de DNS no domínio gerenciado. Esses usuários podem usar o console do Gerenciador de DNS em um computador que executa o Windows Server ingressado no domínio gerenciado para gerenciar o DNS. Para usar o console do Gerenciador DNS, instale as "Ferramentas de Servidor DNS", que fazem parte do recurso opcional de "Ferramentas de Administração de Servidor Remoto" no servidor. Mais informações sobre os [utilitários para administrar, monitorar e solucionar problemas de DNS](https://technet.microsoft.com/library/cc753579.aspx) estão disponíveis no TechNet.

### <a name="billing-and-availability"></a>Disponibilidade e cobrança
#### <a name="is-azure-ad-domain-services-a-paid-service"></a>Os Azure AD Domain Services são um serviço pago?
Sim. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Há uma avaliação gratuita para o serviço?
Esse serviço está incluído na avaliação gratuita do Azure. Você pode se inscrever para uma [avaliação gratuita de um mês do Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Posso obter os Serviços de Domínio do AD do Azure como parte do EMS (Enterprise Mobility Suite)? Preciso do Azure AD Premium para usar os Serviços de Domínio do Azure AD?
Não. Os Serviços de Domínio do Azure AD é um serviço pré-pago do Azure e não faz parte do EMS. O Azure AD Domain Services pode ser usado em todas as edições do Azure AD (Free, Basic e Premium). Você será cobrado por hora, dependendo do uso.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Em quais regiões do Azure o serviço está disponível?
Consulte a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para conhecer as regiões do Azure nas quais os Serviços de Domínio do Azure AD estão disponíveis.



<!--HONumber=Jan17_HO2-->


