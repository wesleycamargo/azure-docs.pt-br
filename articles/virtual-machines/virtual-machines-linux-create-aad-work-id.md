<properties
   pageTitle="Criar uma identidade corporativa ou de estudante no AAD | Microsoft Azure"
   description="Saiba como criar uma identidade corporativa ou de estudante no Azure Active Directory para usar com suas máquinas virtuais Linux."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# Criando uma identidade Corporativa ou de Estudante no Azure Active Directory para usar com VMs Linux

Se você tiver criado uma conta pessoal do Azure ou tiver uma assinatura pessoal do MSDN e criou a conta do Azure para aproveitar os créditos Azure no MSDN, você usou uma identidade da *conta da Microsoft* para criá-la. Para que muitos recursos excelentes do Azure (como os [modelos de grupo de recursos](../resource-group-overview.md)) funcionem, é necessário ter uma conta corporativa ou de estudante (uma identidade gerenciada pelo Active Directory do Azure). Você pode seguir as instruções abaixo para criar uma nova conta corporativa ou de estudante, porque, felizmente, uma das melhores coisas sobre sua conta pessoal do Azure pessoal é que ela vem com um domínio do Active Directory do Azure padrão, que você pode usar para criar uma nova corporativa ou de estudante, que pode usar com os recursos do Azure que necessitam dela.

No entanto, alterações recentes possibilitam gerenciar sua assinatura com qualquer tipo de conta do Azure usando o `azure login` método de logon interativo descrito [aqui](../xplat-cli-connect.md). Você pode usar esse mecanismo, ou seguir as instruções abaixo. Você também pode [criar uma identidade Corporativa ou de Estudante no Azure Active Directory para usar com VMs do Windows](virtual-machines-windows-create-aad-work-id.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

[AZURE.INCLUDE [virtual-machines-common-create-aad-work-id](../../includes/virtual-machines-common-create-aad-work-id.md)]

<!---HONumber=AcomDC_0824_2016-->