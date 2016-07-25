<properties
   pageTitle="Infraestrutura de Atualização do Red Hat (RHUI) | Microsoft Azure"
   description="Saiba mais sobre a Infraestrutura de Atualização do Red Hat (RHUI) para as instâncias sob demanda do Red Hat Enterprise Linux no Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/11/2016"
   ms.author="borisb"/>

# Infraestrutura de Atualização do Red Hat (RHUI) para as VMs do Red Hat Enterprise Linux sob demanda no Azure

As máquinas virtuais criadas a partir das imagens disponíveis do Red Hat Enterprise Linux (RHEL) sob demanda no Azure Marketplace são registradas para acessar a Infraestrutura de Atualização do Red Hat (RHUI) implantada no Azure. As instâncias do RHEL sob demanda terão acesso a um repositório yum regional e serão capazes de receber atualizações incrementais.

A lista de repositórios yum, que é gerenciada pelo RHUI, é configurada na sua instância RHEL durante o provisionamento. Você não precisa fazer nenhuma configuração adicional: basta executar `yum update` depois da instância do RHEL estar em execução para obter as atualizações mais recentes.

## Visão geral de RHUI
A [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) oferece uma solução altamente escalonável para gerenciar o conteúdo do repositório yum para instâncias de nuvem do Red Hat Enterprise Linux hospedadas por provedores de nuvem certificados pelo Red Hat. Com base no projeto Pulp upstream, o RHUI permite que provedores de nuvem espelhem o conteúdo do repositório hospedado pelo Red Hat localmente, criem repositórios personalizados com seu próprio conteúdo e disponibilizem esses repositórios para um grande grupo de usuários finais por meio de um sistema de entrega de conteúdo com balanceamento de carga.

## Regiões em que o RHUI está disponível
O RHUI está disponível em todas as regiões onde as imagens do RHEL sob demanda estão disponíveis. Atualmente, inclui todas as regiões públicas listadas na página [Painel de status do Azure](https://azure.microsoft.com/status/). O acesso do RHUI para as VMs provisionadas a partir das imagens do RHEL sob demanda está incluído em seu preço. A disponibilidade de nuvem regional/nacional será atualizada quando expandirmos a disponibilidade do RHEL sob demanda no futuro.

> [AZURE.NOTE] O acesso ao RHUI hospedado pelo Azure é limitado às VMs nos [Intervalos de IP do Data Center do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## Obter atualizações de outro repositório de atualizações

Se você precisar obter atualizações de um repositório de atualizações diferente (em vez do RHUI hospedado pelo Azure), precisará cancelar o registro de suas instâncias do RHUI e registrá-las novamente na infraestrutura de atualização desejada (como Red Hat Satellite ou CDN do Portal do Cliente do Red Hat). Você precisará das devidas assinaturas do Red Hat para esses serviços e o registro para o [Acesso em Nuvem do Red Hat no Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para cancelar o registro do RHUI e registrar novamente na infraestrutura de atualização, siga as etapas abaixo.

1.	Edite /etc/yum.repos.d/rh-cloud.repo e altere todos os `enabled=1` para `enabled=0`. Por exemplo:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Edite /etc/yum/pluginconf.d/rhnplugin.conf e altere `enabled=0` para `enabled=1`.
3.	Então, registre-se na infraestrutura desejada, como o Portal do Cliente do Red Hat. Siga o guia de solução do Red Hat em [como registrar e assinar um sistema para o Portal do Cliente do Red Hat](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] O acesso ao RHUI hospedado pelo Azure é incluído no preço de imagem Pré-Pago (PAYG) do RHEL. Cancelar o registro de uma VM PAYG do RHEL a partir do RHUI hospedado pelo Azure não converte a máquina virtual na VM do tipo Traga Sua Própria Licença (BYOL), portanto, você pode ser cobrado duas vezes se registrar a mesma VM em outra fonte de atualizações.
> 
> Se você precisar usar consistentemente uma infraestrutura de atualização diferente do RHUI hospedado pelo Azure, considere criar e implantar suas próprias imagens (do tipo BYOL), como descrito no artigo [Criar e Carregar uma máquina virtual baseada no Red Hat para o Azure](virtual-machines-linux-redhat-create-upload-vhd.md).

## Próximas etapas
Para criar uma VM do Red Hat Enterprise Linux a partir da imagem Pré-Paga do Azure Marketplace e aproveitar o RHUI hospedado pelo Azure, acesse o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Você poderá usar `yum update` em sua instância do RHEL sem nenhuma configuração adicional.

<!---HONumber=AcomDC_0713_2016-->