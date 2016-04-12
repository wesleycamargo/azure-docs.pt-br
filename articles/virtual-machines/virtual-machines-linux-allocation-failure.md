<properties
	pageTitle="Solucionar problemas de falhas de alocação de VM do Linux| Microsoft Azure"
	description="Solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar uma VM do Linux no Azure"
	services="virtual-machines-linux, azure-resource-manager"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue,azure-resourece-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="na"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/02/2016"
	ms.author="cjiang"/>

# Solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar VMs do Linux no Azure

Quando você cria uma VM, reinicia VMs paradas (desalocadas) ou redimensiona uma VM, o Microsoft Azure aloca recursos de computação para sua assinatura. Eventualmente, você pode receber mensagens de erro durante a execução dessas operações, antes de alcançar os limites da assinatura do Azure. Este artigo explica as causas das falhas de alocação mais comuns e sugere possíveis correções. As informações também poderão ser úteis caso você pretenda implantar serviços.

A seção "Etapas gerais de solução de problemas" lista as etapas para solucionar problemas comuns. A seção "Etapas detalhadas da solução de problemas" fornece as etapas de resolução por mensagem de erro específica. Antes de iniciar, aqui estão algumas informações básicas para entender como funciona a alocação e por que a falha de alocação acontece. Também é possível [solucionar problemas de falhas de alocação ao criar, reiniciar ou redimensionar VMs do Windows no Azure](virtual-machines-windows-allocation-failure.md).


[AZURE.INCLUDE [virtual-machines-common-allocation-failure](../../includes/virtual-machines-common-allocation-failure.md)]

<!-----------HONumber=AcomDC_0330_2016-->