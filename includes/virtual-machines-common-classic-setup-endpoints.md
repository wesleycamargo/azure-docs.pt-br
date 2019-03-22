---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 10/23/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: ee5faedd4f59aa791424a1f178f0462922f21d28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58115891"
---
Cada ponto de extremidade tem uma *porta pública* e uma *porta privada*:

* A porta pública é usada pelo balanceador de carga do Azure para ouvir o tráfego de entrada da internet com destino à máquina virtual.
* A porta privada é usada pela máquina virtual para ouvir o tráfego de entrada normalmente com destino a um aplicativo ou serviço em execução na máquina virtual.

Os valores padrão para o protocolo IP e as portas TCP ou UDP para protocolos conhecidos são fornecidos quando você cria pontos de extremidade com o portal do Azure. Para pontos de extremidade personalizados, especifique o protocolo IP correto (TCP ou UDP) e as portas públicas e privadas. Para distribuir o tráfego de entrada aleatoriamente entre várias máquinas virtuais, crie um conjunto com balanceamento de carga composto de vários pontos de extremidade.

Depois de criar um ponto de extremidade, você pode usar uma lista de controle de acesso (ACL) para definir regras que permitam ou neguem o tráfego de entrada na porta pública do ponto de extremidade com base em seu endereço IP de origem. No entanto, se a máquina virtual estiver em uma rede virtual do Azure, use grupos de segurança de rede. Para saber mais, confira [Sobre grupos de segurança de rede](../articles/virtual-network/security-overview.md).

> [!NOTE]
> A configuração de firewall para máquinas virtuais do Azure é feita automaticamente para as portas associadas com pontos de extremidade de conectividade remota que o Azure configura automaticamente. Para portas especificadas para todos os outros pontos de extremidade, nenhuma configuração é feita automaticamente para o firewall da máquina virtual. Ao criar um ponto de extremidade para a máquina virtual, garanta que o firewall da máquina virtual também permita tráfego para o protocolo e a porta privada correspondente à configuração do ponto de extremidade. Para configurar o firewall, consulte a documentação ou a Ajuda online para o sistema operacional em execução na máquina virtual.
>
>

## <a name="create-an-endpoint"></a>Criar um ponto de extremidade
1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Máquinas virtuais**e, em seguida, selecione a máquina virtual que você deseja configurar.

3. Selecione **Pontos de extremidade** no grupo **Configurações**. A página **Pontos de Extremidade** é exibida e lista todos os pontos de extremidade atuais para a máquina virtual. (Este exemplo é para uma VM do Windows. Uma VM do Linux por padrão mostrará um ponto de extremidade para SSH.)

   <!-- ![Endpoints](./media/virtual-machines-common-classic-setup-endpoints/endpointswindows.png) -->
   ![Pontos de extremidade](./media/virtual-machines-common-classic-setup-endpoints/endpointsblade.png)


4. Na barra de comandos acima das entradas de ponto de extremidade, selecione **Adicionar**. A página **Adicionar ponto de extremidade** é exibida.

5. Em **Nome**, insira um nome para o ponto de extremidade.

6. Em **Protocolo**, escolha **TCP** ou **UDP**.

7. Em **Porta pública**, insira o número de porta para o tráfego de entrada da internet. 

8. Em **Porta privada**, insira o número da porta em que a máquina virtual está escutando. Os números de porta pública e privada podem ser diferentes. Certifique-se de que o firewall na máquina virtual tenha sido configurado para permitir o tráfego correspondente para o protocolo e a porta privada.

9. Selecione **OK**.

O novo ponto de extremidade é listado na página **Pontos de extremidade**.

![Criação de ponto de extremidade com êxito](./media/virtual-machines-common-classic-setup-endpoints/endpointcreated.png)

## <a name="manage-the-acl-on-an-endpoint"></a>Gerenciar a ACL em um ponto de extremidade
Para definir o conjunto de computadores que pode enviar tráfego, a ACL em um ponto de extremidade pode restringir o tráfego com base no endereço IP de origem. Siga estas etapas para adicionar, modificar ou remover uma ACL em uma empresa.

> [!NOTE]
> se o ponto de extremidade é parte de um conjunto com balanceamento de carga, quaisquer alterações feitas na ACL em um ponto de extremidade são aplicadas a todos os pontos de extremidade no conjunto.
>
>

Se a máquina virtual estiver em uma rede virtual do Azure, use grupos de segurança de rede de ACLs. Para saber mais, confira [Sobre grupos de segurança de rede](../articles/virtual-network/security-overview.md).

1. Entre no Portal do Azure.

2. Selecione **Máquinas virtuais** e, em seguida, selecione nome da máquina virtual que você deseja configurar.

3. Selecione **Pontos de extremidade**. Na lista de pontos de extremidade, selecione o ponto de extremidade apropriado. A lista ACL é na parte inferior da página.

   ![Detalhes específicos de ACL](./media/virtual-machines-common-classic-setup-endpoints/aclpreentry.png)

4. Use linhas na lista para adicionar, excluir ou editar regras de uma ACL e alterar sua ordem. O valor de **SUB-REDE REMOTA** é um intervalo de endereços IP para o tráfego de entrada da internet que o balanceador de carga do Azure usa a fim de permitir ou negar o tráfego com base no endereço IP de origem. Especifique o intervalo de endereços IP no formato CIDR (Roteamento entre Domínios sem Classificação), também conhecido como formato de prefixo de endereço. Por exemplo, `10.1.0.0/8`.

   ![Nova entrada ACL](./media/virtual-machines-common-classic-setup-endpoints/newaclentry.png)


Você pode usar regras para permitir somente o tráfego de computadores específicos correspondentes aos seus computadores na internet ou negar o tráfego de intervalos de endereços específicos e conhecidos.

As regras são avaliadas em ordem, começando com a primeira regra e terminando com a última regra. Portanto, as regras devem ser ordenadas das menos restritivas para as mais restritivas. Para obter mais informações, veja [O que é uma Lista de Controle de Acesso de Rede](../articles/virtual-network/virtual-networks-acl.md).
