<properties
   pageTitle="Criar o FQDN para uma VM no portal do Azure | Microsoft Azure"
   description="Saiba como criar um nome de domínio totalmente qualificado, ou FQDN, para uma máquina virtual baseada no Gerenciador de Recursos no portal do Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/23/2016"
   ms.author="iainfou"/>

# Criar um nome de domínio totalmente qualificado no portal do Azure
Quando você cria uma VM (máquina virtual) no [portal do Azure](https://portal.azure.com) usando o modelo de implantação do Gerenciador de Recursos, um recurso de IP público para a máquina virtual é criado automaticamente. Use esse endereço IP para acessar a VM remotamente. Embora o portal não crie um [nome de domínio totalmente qualificado](https://en.wikipedia.org/wiki/Fully_qualified_domain_name), ou FQDN, por padrão, você pode adicionar um depois que a VM for criada. Este artigo apresenta as etapas para criar um nome DNS ou FQDN.

[AZURE.INCLUDE [virtual-machines-common-portal-create-fqdn](../../includes/virtual-machines-common-portal-create-fqdn.md)]

Agora você pode se conectar à VM remotamente usando esse nome DNS, assim como com `ssh adminuser@testdnslabel.centralus.cloudapp.azure.com`.

## Próximas etapas
Agora que sua VM tem um IP público e um nome DNS, é possível implantar estruturas comuns do aplicativo ou serviços, como o nginx, MongoDB, Docker, etc.

Leia mais sobre como [usar o Gerenciador de Recursos](../resource-group-overview.md) para obter dicas sobre a criação de implantações do Azure.

<!---HONumber=AcomDC_0824_2016-->