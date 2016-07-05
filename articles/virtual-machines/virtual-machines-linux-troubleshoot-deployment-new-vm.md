<properties
   pageTitle="Solucionar problemas de implantação do RM de VM Linux | Microsoft Azure"
   description="Solucionar problemas de implantação do Resource Manager ao criar uma nova máquina virtual Linux no Azure"
   services="virtual-machines-linux, azure-resource-manager"
   documentationCenter=""
   authors="jiangchen79"
   manager="felixwu"
   editor=""
   tags="top-support-issue, azure-resource-manager"/>

<tags
  ms.service="virtual-machines-linux"
  ms.workload="na"
  ms.tgt_pltfrm="vm-linux"
  ms.devlang="na"
  ms.topic="article"
  ms.date="06/20/2016"
  ms.author="cjiang"/>

# Solucionar problemas de implantação do Resource Manager com a criação de uma nova máquina virtual Linux no Azure

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-selectors](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-selectors-include.md)]

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## Coletar logs de auditoria

Para iniciar a solução de problemas, colete os logs de auditoria para identificar o erro associado ao problema. Os links a seguir contêm informações detalhadas sobre o processo a ser seguido.

[Solucionar problemas de implantações de grupos de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)

[Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md)

[AZURE.INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[AZURE.INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** se o sistema operacional for Linux generalizado e ele for carregado e/ou capturado com a configuração generalizada, não haverá erros. Da mesma forma, se o sistema operacional for Linux especializado e ele for carregado e/ou capturado com a configuração especializada, não haverá erros.

**Erros de upload:**

**N<sup>1</sup>:** se o sistema operacional for Linux generalizado e ele for carregado como especializado, você receberá um erro de tempo limite de provisionamento, pois a VM estará paralisada no estágio de provisionamento.

**N<sup>2</sup>:** se o sistema operacional for Linux especializado e ele for carregado como generalizado, você receberá um erro de falha no provisionamento, pois a nova VM estará em execução com o nome do computador, nome de usuário e senha originais.

**Resolução:**

Para resolver ambos os erros, carregue o VHD original, disponível no local, com a mesma configuração usada para o sistema operacional (generalizado/especializado). Para carregar como generalizado, lembre-se de executar -deprovision primeiro.

**Erros de captura:**

**N<sup>3</sup>:** se o sistema operacional for Linux generalizado e ele for capturado como especializado, você receberá um erro de tempo limite de provisionamento, pois a VM original não será utilizável, já que estará marcada como generalizada.

**N<sup>4</sup>:** se o sistema operacional for Linux especializado e ele for capturado como generalizado, você receberá um erro de falha no provisionamento, pois a nova VM estará em execução com o nome do computador, nome de usuário e senha originais. Além disso, a VM original não será utilizável, já que estará marcada como especializada.

**Resolução:**

Para resolver ambos os erros, exclua a imagem atual do portal e [recapture-a dos VHDs atuais](virtual-machines-linux-capture-image.md) com a mesma configuração usada para o sistema operacional (generalizado/especializado).

## Problema: imagem personalizada/da galeria/do Marketplace; falha de alocação
Esse erro ocorre em situações nas quais a nova solicitação de VM é fixada em um cluster que não tem suporte para o tamanho da VM sendo solicitado ou não tem espaço livre disponível para acomodar a solicitação.

**Causa 1:** o cluster não dá suporte ao tamanho de VM solicitado.

**Resolução 1:**

- Repita a solicitação com um tamanho de VM menor.
- Se o tamanho da VM solicitada não puder ser alterado:
  - Pare todas as VMs no conjunto de disponibilidade. Clique em **Grupos de recursos** > *seu grupo de recursos* > **Recursos** > *seu conjunto de disponibilidade* > **Máquinas Virtuais** > *sua máquina virtual* > **Parar**.
  - Depois de parar todas as máquinas virtuais, crie a nova VM no tamanho desejado.
  - Inicie a nova VM primeiro e, em seguida, selecione cada uma das VMs paradas e clique em **Iniciar**.

**Causa 2:** o cluster não tem recursos livres.

**Resolução 2:**

- Repita a solicitação mais tarde.
- Se a nova VM puder ser parte de um conjunto de disponibilidade diferente
  - Crie uma nova VM em um conjunto de disponibilidade diferente (na mesma região).
  - Adicione a nova VM à mesma rede virtual.

## Próximas etapas
Se você encontrar problemas ao iniciar uma VM do Linux parada ou redimensionar uma VM do Linux existente no Azure, consulte [Solucionar problemas de implantação do Resource Manager ao reinicializar ou redimensionar uma máquina virtual Linux existente no Azure](virtual-machines-linux-restart-resize-error-troubleshooting.md).

<!---HONumber=AcomDC_0622_2016-->