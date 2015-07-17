<properties 
   pageTitle="Como migrar de grupos de afinidades para uma rede virtual regional (VNet)"
   description="Saiba como migrar de grupos de afinidades para VNets regionais"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/29/2015"
   ms.author="telmos" />

# Como migrar de grupos de afinidades para uma rede virtual regional (VNet)

Você pode usar um grupo de afinidades para garantir que recursos criados dentro do mesmo grupo de afinidades sejam hosts fisicamente por servidores que estejam próximos uns dos outros, permitindo que esses recursos se comuniquem mais rapidamente. Antigamente, os grupos de afinidades eram um requisito para a criação de redes virtuais (VNets). Na época, o serviço gerenciador de rede que gerenciava as VNets podia funcionar somente dentro de um conjunto de servidores físicos ou unidade de escala. Melhorias de arquitetura recentes aumentaram o escopo de gerenciamento de rede para uma região.

Como resultado dessas melhorias na arquitetura, os grupos de afinidade não são mais recomendados ou obrigatórios para redes virtuais. O uso de grupos de afinidades para VNets está sendo substituído por regiões. As VNets que estão associadas a regiões são chamadas de VNets regionais.

Além disso, recomendamos que você não use grupos de afinidades em geral. Tirando o requisito para VNets, os grupos de afinidades também foram importantes para garantir que recursos como computação e armazenamento fossem colocados próximos uns dos outros. No entanto, com a arquitetura atual de rede do Azure, esses requisitos de colocação não são mais necessários. Confira [Grupos de afinidades e VMs](#Affinity-groups-and-VMs) para algumas das hipóteses específicas restantes em que você possa precisar usar um grupo de afinidades.

## Criando e migrando para VNets regionais

No futuro, ao criar novas VNets, use *Região*. Você verá isso como uma opção no Portal de Gerenciamento. Observe que, no arquivo de configuração de rede, isso aparece como *Local*.

>[AZURE.IMPORTANT]Embora ainda seja tecnicamente possível criar uma rede virtual associada a um grupo de afinidades, não há nenhum motivo relevante para fazê-lo. Muitos novos recursos, como grupos de segurança de rede, somente estão disponíveis com o uso de uma VNet regional e não estão disponíveis para redes virtuais associadas a grupos de afinidades.

### Sobre VNets atualmente associadas a grupos de afinidades

As VNets que estão associadas a grupos de afinidades serão habilitadas para a migração para VNets regionais em um futuro próximo. Atualizaremos esta página para que você saiba que o processo está em andamento e quando poderá executar as próximas etapas para concluir a migração.

Observe que os grupos de afinidades ainda existirão, embora não recomendemos o seu uso com VNets. Por isso, você pode escolher não migrar sua VNet ou atrasar a migração. A sua VNet ainda funcionará. Mas você pode se deparar com limitações e não poderá tirar proveito de qualquer um dos muitos recursos novos que exigem uma VNet regional. Recomendamos sinceramente que você migre sua VNet de grupo de afinidades para uma VNet regional quando ela estiver habilitada.

### Migração inicial

A migração inicial está disponível aos clientes que possuem um contrato de suporte. Para se qualificar para a migração inicial, siga estas etapas:

1. No **Portal de Gerenciamento**, na página VNet, no canto superior direito, clique em **Contate o suporte da Microsoft**.

	![Solicitação de migração para VNet regional](./media/virtual-networks-migrate-to-regional-vnet/IC790447.png)

1. Na página **Contate o suporte da Microsoft**, selecione o seguinte: Assinatura: se você tiver mais de uma assinatura, selecione a assinatura que corresponde à VNet que você deseja migrar. Tipo de suporte: técnico Tipo de produto: redes virtuais (VNet) Tipo de problema: migrar a rede virtual para a rede virtual regional

	- **Assinatura:** se você tiver mais de uma assinatura, selecione a assinatura que corresponde à VNet que você deseja migrar.

	- **Tipo de suporte:** *técnico*

	- **Tipo de produto:** *redes virtuais (VNet)*

	- **Tipo de problema:** *migrar a rede virtual para a rede virtual regional*

1. Pré-requisito: a sua VNet deve ser estar habilitada para a migração a fim de executar as etapas a seguir. Você saberá que a sua VNet foi habilitada quando receber a confirmação (no caso de uma solicitação de migração inicial) ou quando esta página for atualizada indicando que todas as VNets estão habilitadas para a migração.

1. Exportar o arquivo de configuração de rede. Você pode usar o PowerShell ou o Portal de Gerenciamento. Para obter instruções sobre como usar o Portal de gerenciamento, confira [Configurar a sua VNet usando um arquivo de configuração de rede](../virtual-networks-using-network-configuration-file/).

1. Edite o arquivo de configuração de rede substituindo os valores antigos pelos novos valores.

	> [AZURE.NOTE]O **Local** é a região que você especificou para o grupo de afinidades que está associado a sua VNet. Por exemplo, se a VNet estiver associada a um grupo de afinidades que está localizado no Oeste dos EUA, quando você migrar, a sua localização deve apontar para o oeste dos EUA.
	
	Edite as seguintes linhas no arquivo de configuração de rede substituindo os valores pelos seus:

	**Valor antigo:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG">

	**Novo valor:** \<VirtualNetworkSitename="VNetUSWest" Location="West US">

1. Salve suas alterações e [importe](../virtual-networks-using-network-configuration-file/) a configuração de rede para o Azure.

## Grupos de afinidades e VMs

Conforme mencionado anteriormente, os grupos de afinidades geralmente não são recomendados para máquinas virtuais. Você somente deve usar um grupo de afinidades quando um conjunto de VMs tiver que ter a menor latência de rede possível entre as máquinas virtuais. Ao colocar VMs em um grupo de afinidades, as VMs estarão todas na mesma unidade de escala ou cluster de computação.

É importante observar que o uso de um grupo de afinidades pode ter duas consequências possivelmente negativas:

- O conjunto de tamanhos de máquina virtual será limitado ao conjunto de tamanhos das máquinas virtuais oferecido pela unidade de escala de computação.

- Há uma probabilidade maior de não se conseguir alocar uma nova VM. Isso acontece quando a unidade de escala específica do grupo de afinidades não tem capacidade insuficiente.

### O que fazer se você tiver uma máquina virtual em um grupo de afinidades

Máquinas virtuais que estão atualmente em um grupo de afinidades não precisam ser removidas do grupo de afinidades.

Quando uma máquina virtual é implantada, ela é implantada em uma única unidade de escala. Os grupos de afinidades pode restringir o conjunto de tamanhos de VM disponíveis para uma nova implantação de VM, mas qualquer VM existente já implantada fica restrita ao conjunto de tamanhos de VM disponíveis na unidade de escala em que a VM estiver implantada. Por isso, a remoção de uma máquina virtual do grupo de afinidades não terá consequências.
 

<!---HONumber=62-->