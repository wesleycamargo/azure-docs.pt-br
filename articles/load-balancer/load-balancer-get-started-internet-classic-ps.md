<properties 
   pageTitle="Introdução à criação de balanceador de carga para a Internet no modo clássico usando o PowerShell | Microsoft Azure"
   description="Saiba como criar um balanceador de carga para a Internet no modo clássico usando o PowerShell"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2015"
   ms.author="joaoma" />

# Introdução à criação de um balanceador de carga para a Internet (clássico) no PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação clássico. Também é possível [Saber como criar um balanceador de carga para a Internet usando o Gerenciador de Recursos do Azure](load-balancer-get-started-internet-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## Configurar o balanceador de carga usando o PowerShell

Para configurar um balanceador de carga usando o powershell, siga as etapas abaixo:

1. Se você nunca usou o Azure PowerShell, veja [Como instalar e configurar o Azure PowerShell](powershell-install-configure.md) e siga as instruções até o final para entrar no Azure e selecionar sua assinatura.


2. Depois de criar uma máquina virtual, você pode usar os cmdlets do PowerShell para adicionar um balanceador de carga a uma máquina virtual no mesmo serviço de nuvem.

No exemplo a seguir, você adicionará um conjunto de balanceadores de carga chamado "webfarm" ao serviço de nuvem "mytestcloud" (ou myctestcloud.cloudapp.net), adicionando os pontos de extremidade para o balanceador de carga às máquinas virtuais chamadas "web1" e "web2". O balanceador de carga recebe tráfego de rede na porta 80 e faz o balanceamento de carga entre as máquinas virtuais definidas pelo ponto de extremidade local (nesse caso, na porta 80) usando TCP.


### Etapa 1
Criar um ponto de extremidade com balanceamento de carga para a primeira VM "web1"

	Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

### Etapa 2 

Criar outro ponto de extremidade para a segunda VM "web2" usando o mesmo nome do conjunto de balanceadores de carga

	Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM

## Remover uma máquina virtual de um balanceador de carga

Você pode usar Remove-AzureEndpoint para remover um ponto de extremidade de máquina virtual do balanceador de carga

	Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM

## Próximas etapas

[Introdução à configuração de um balanceador de carga interno](load-balancer-internal-getstarted.md)

[Configurar um modo de distribuição do balanceador de carga](load-balancer-distribution-mode.md)

[Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1203_2015-->