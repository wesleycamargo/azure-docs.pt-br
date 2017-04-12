---
title: "Solucionar problemas na criação de coleções híbridas do RemoteApp | Microsoft Docs"
description: "Saiba como solucionar problemas de falhas de criação de coleção híbrida do RemoteApp"
services: remoteapp
documentationcenter: 
author: vkbucha
manager: mbaldwin
ms.assetid: b32033ee-8d52-4e74-bb78-86ca873c34e2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a486dcb3f994cd78311ee86521a6792a4d57438e
ms.lasthandoff: 03/31/2017


---
# <a name="troubleshoot-creating-azure-remoteapp-hybrid-collections"></a>Solucionar problemas na criação de coleções híbridas do RemoteApp do Azure
> [!IMPORTANT]
> O Azure RemoteApp será descontinuado até 31 de agosto de 2017. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Uma coleção híbrida é hospedada e armazena os dados na nuvem do Azure, mas também permite aos usuários acessarem dados e recursos armazenados em sua rede local. Os usuários podem acessar aplicativos ao efetuar logon com suas credenciais corporativas sincronizadas ou federadas com o Active Directory do Azure. Você pode implantar uma coleção híbrida que usa uma Rede Virtual existente do Azure, ou você pode criar uma nova rede virtual. Recomendamos que você crie ou use uma sub-rede da rede virtual com uma variedade CIDR grande o suficiente para futuro crescimento estimado para o RemoteApp do Azure.

Ainda não criou sua coleção? Confira [Criar uma coleção híbrida](remoteapp-create-hybrid-deployment.md) para obter as etapas.

Se estiver enfrentando problemas para criar sua coleção, ou se a coleção não está funcionando como você acha que deveria, verifique as informações a seguir.

## <a name="your-image-is-invalid"></a>A imagem é inválida
Se você receber uma mensagem como “GoldImageInvalid” quando estiver aguardando enquanto o Azure provisiona sua coleção, isso significa que a sua imagem de modelo não atende aos [requisitos de imagem definidos](remoteapp-imagereqs.md). Portanto, leia esses [requisitos](remoteapp-imagereqs.md), corrija sua imagem e tente criar novamente sua coleção.

## <a name="does-your-vnet-have-network-security-groups-defined"></a>A sua VNET tem grupos de segurança de rede definidos?
Se você tiver grupos de segurança de rede definidos na sub-rede que você está usando para sua coleção, verifique se essas [URLs e portas](remoteapp-ports.md) estão acessíveis de dentro da sub-rede.

Você pode adicionar grupos de segurança de rede adicionais às máquinas virtuais implantadas por você na sub-rede para um controle mais rigoroso.

## <a name="are-you-using-your-own-dns-servers-and-are-they-accessible-from-your-vnet-subnet"></a>Você está usando seus próprios servidores DNS? Eles são acessíveis da sua sub-rede VNET?
> [!NOTE]
> Você precisa verificar se os servidores DNS na sua rede virtual estão sempre ativados e sempre capazes de resolver os problemas das máquinas virtuais hospedadas na rede virtual. Não use o DNS do Google para isso.
> 
> 

Para coleções híbridas, use seus próprios servidores DNS. Você os especifica no seu esquema de configuração de rede ou por meio do portal de gerenciamento ao criar a rede virtual. Os servidores DNS são usados na ordem em que eles forem especificados em uma forma de failover (em vez de round robin).  
Confira [Resolução de nomes de VMs e instâncias de função](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para verificar se os servidores DNS estão configurados corretamente.

Verifique se os servidores DNS de sua coleção estão acessíveis e disponíveis da sub-rede VNET especificada para essa coleção.

Por exemplo:

    <VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
    </VirtualNetworkConfiguration>

![Definir o DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

## <a name="are-you-using-an-active-directory-domain-controller-in-your-collection"></a>Você está usando um controlador de domínio do Active Directory em sua coleção?
No momento, somente um domínio do Active Directory pode ser associado ao RemoteApp do Azure. A coleção híbrida dá suporte somente às contas do Active Directory do Azure que foram sincronizadas usando a ferramenta DirSync de uma implantação do Active Directory do Windows Server. Mais especificamente, sincronizado com a opção de Sincronização de Senha ou com federação dos Serviços de Federação do Active Directory (AD FS) configurada. Você precisa criar um domínio personalizado que coincide com o sufixo de domínio para seu domínio local e configurar a integração de diretório.

Confira [Configurando o Active Directory para o RemoteApp do Azure](remoteapp-ad.md) para obter informações de planejamento.

Verifique se os detalhes do domínio fornecidos são válidos e se o controlador de domínio está acessível da VM criada na sub-rede usada para o RemoteApp do Azure. Verifique também se as credenciais da conta de serviço fornecidas têm permissões para adicionar computadores ao domínio fornecido e se o nome do AD fornecido pode ser resolvido a partir do DNS fornecido na VNET.

## <a name="what-domain-name-did-you-specify-when-you-created-your-collection"></a>Que nome de domínio você especificou quando criou a sua coleção?
O nome de domínio criado ou adicionado deve ser um nome de domínio interno (não o seu nome de domínio do Azure AD) e deve estar no formato DNS resolvível (contoso. local). Por exemplo, você tem um nome interno do Active Directory (Contoso) e um UPN do Active Directory (contoso.com): deve, então, usar o nome interno ao criar sua coleção.


