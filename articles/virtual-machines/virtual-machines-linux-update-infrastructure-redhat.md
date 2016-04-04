<properties
   pageTitle="Uma infraestrutura de atualização para imagens do Red Hat Enterprise Linux | Microsoft Azure"
   description="Apresenta o serviço de atualização yum para uma instância do Red Hat Enterprise Linux sob demanda no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="kyliel"/>

# Uma infraestrutura de atualização para imagens do Red Hat Enterprise Linux

Usando o RHUI (Red Hat Update Infrastructure) no Azure, você pode gerenciar o conteúdo do repositório yum para as imagens do RHEL (Red Hat Enterprise Linux) do Azure. As instâncias do RHEL sob demanda que você criou no Azure Marketplace terão acesso ao repositório yum regional. Isso permite que instâncias RHEL recebam atualizações incrementais.

A lista de repositórios yum, que é gerenciada pelo RHUI, é configurada na sua instância RHEL durante o provisionamento. Por isso, você não precisa fazer nenhuma configuração adicional: basta executar `yum update` depois que a instância do RHEL estiver em execução.

## Visão geral de RHUI
A [Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) oferece uma solução altamente escalonável para gerenciar o conteúdo do repositório yum para instâncias de nuvem do Red Hat Enterprise Linux hospedadas por provedores de nuvem certificados pelo Red Hat. Com base no projeto Pulp upstream, o RHUI permite que provedores de nuvem espelhem o conteúdo do repositório hospedado pelo Red Hat localmente, criem repositórios personalizados com seu próprio conteúdo e disponibilizem esses repositórios para um grande grupo de usuários finais por meio de um sistema de entrega de conteúdo com balanceamento de carga.

## Regiões em que o RHUI está disponível
O RHUI está disponível em todas as regiões públicas listadas no [Painel de status do Azure](https://azure.microsoft.com/status/). Isso significa que você pode obter o serviço de atualização yum sem nenhum custo adicional nessas regiões. Essa informação será atualizada no futuro.

## Obter atualizações do outro repositório de atualização (como o Red Hat Network Satellite)

Para obter atualizações do outro repositório de atualização, você precisa ter uma licença do Red Hat Cloud Access e uma assinatura existente do Red Hat no Azure.

Em seguida, você precisa cancelar o registro do RHUI e registrá-lo novamente em sua infraestrutura de atualização. Abaixo estão as etapas detalhadas.

1.	Edite /etc/yum.repos.d/rh-cloud.repo e altere todos os `enabled=1` para `enabled=0`. Por exemplo:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.	Edite /etc/yum/pluginconf.d/rhnplugin.conf e altere `enabled=0` para `enabled=1`.
3.	Em seguida, registre-se com o RHN (Red Hat Network).

        rhn_register

    ou

        rhnreg_ks


> [AZURE.NOTE] Transferências de dados de saída serão cobradas (consulte os [detalhes de preços](http://azure.microsoft.com/pricing/details/data-transfers/)). Recomendamos usar o RHUI padrão para obter atualizações incrementais sem incorrer em encargos adicionais.

## Próximas etapas
Agora que você conhece o RHUI no Azure, poderá criar uma imagem do RHEL do [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) e usar `yum update` na sua instância do RHEL.

<!---HONumber=AcomDC_0323_2016-->