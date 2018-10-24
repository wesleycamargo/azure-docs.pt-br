---
title: Ações de nó de unidade de dimensionamento no Azure Stack | Microsoft Docs
description: Saiba como exibir o status do nó e usar a ativação em, desligar, Esvaziar e retomar as ações de nó em um sistema integrado do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: f299cca57e3eaddd2a9d4868f3bae822392e45a0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944929"
---
# <a name="scale-unit-node-actions-in-azure-stack"></a>Ações de nó de unidade de escala no Azure Stack

*Aplica-se a: sistemas integrados do Azure Stack*

Este artigo descreve como exibir o status de uma unidade de escala e seus nós associados e como usar as ações de nó disponível. Ações de nó incluem ativação, desativar, drenar, retomam e reparem. Normalmente, você usa essas ações de nó durante a substituição de campo de partes de ou para cenários de recuperação de nó.

> [!Important]  
> Todas as ações de nó descritas neste artigo devem apenas destino um nó por vez.


## <a name="view-the-node-status"></a>Exibir o status do nó

No portal do administrador, você pode facilmente exibir o status de uma unidade de escala e seus nós associados.

Para exibir o status de uma unidade de escala:

1. Sobre o **gerenciamento de região** bloco, selecione a região.
2. À esquerda, sob **recursos de infraestrutura**, selecione **unidades de escala**.
3. Nos resultados, selecione a unidade de escala.
 
Aqui, você pode exibir as informações a seguir:

- nome da região. O nome da região é referenciado com **-local** no módulo do PowerShell.
- tipo de sistema
- total de núcleos lógicos
- total de memória
- A lista de nós individuais e seus status; qualquer um dos **em execução** ou **interrompido**

![Bloco de unidade de escala, mostrando o status de execução para cada nó](media/azure-stack-node-actions/ScaleUnitStatus.PNG)

## <a name="view-node-information"></a>Exibir informações de nó

Se você selecionar um nó individual, você pode exibir as informações a seguir:

- Nome da região
- modelo de servidor
- Endereço IP do baseboard management controller (BMC)
- estado operacional
- Número total de núcleos
- quantidade total de memória
 
![Bloco de unidade de escala, mostrando o status de execução para cada nó](media/azure-stack-node-actions/NodeActions.PNG)

Você também pode executar ações de nó de unidade de escala a partir daqui.

## <a name="scale-unit-node-actions"></a>Ações de nó de unidade de escala

Quando você exibe informações sobre um nó de unidade de escala, você também pode executar ações de nó, como:

- Esvaziar e retomar
- Reparar

O estado operacional do nó determina quais opções estão disponíveis.

### <a name="power-off"></a>Desligar

O **desligue** ação desativa o nó. É o mesmo como se você pressionar o botão de energia. Ele faz **não** enviar um sinal de desligamento para o sistema operacional. Para operações de desligar planejado, verifique se que você drenar um nó de unidade de escala pela primeira vez.

Essa ação normalmente é usada quando um nó está em um estado suspenso e não responde às solicitações.

> [!Important] 
> Essa funcionalidade só está disponível por meio do PowerShell. Ele estará disponível no portal do administrador do Azure Stack novamente mais tarde.


Para executar o desligamento de ação por meio do PowerShell:

````PowerShell
  Stop-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
```` 

No caso improvável de que a ação de desligar não funciona, use a interface da web do BMC.

### <a name="power-on"></a>Ligue

O **ligar** ação ativa o nó. É o mesmo como se você pressionar o botão de energia. 

> [!Important] 
> Essa funcionalidade só está disponível por meio do PowerShell. Ele estará disponível no portal do administrador do Azure Stack novamente mais tarde.

Para executar a potência em ação por meio do PowerShell:

````PowerShell
  Start-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
````

No caso improvável de que a ação de ligar não funciona, use a interface da web do BMC.

### <a name="drain"></a>Esvaziar

O **drenar** ação evacua todas as cargas de trabalho ativas por distribuí-los entre os nós restantes dessa unidade de escala específica.

Essa ação normalmente é usada durante a substituição de campo de partes, como a substituição de um nó inteiro.

> [!IMPORTANT]  
> Certifique-se de que você drenar um nó somente durante uma janela de manutenção planejada, onde os usuários foram notificados. Sob algumas condições, cargas de trabalho ativas podem enfrentar interrupções.

Para executar a ação de esvaziar por meio do PowerShell:

  ````PowerShell
  Disable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="resume"></a>Continuar

O **retomar** ação retoma um drenagem de nó e a marca Active Directory para o posicionamento da carga de trabalho. As cargas de trabalho anteriores que estavam em execução no nó não realizar o failback. (Se você drenar um nó e, em seguida, desligue, quando você liga o nó de volta, ele não está marcado como ativo para o posicionamento da carga de trabalho. Quando estiver pronto, você deve usar a ação de continuação para marcar o nó como o Active Directory.)

Para executar a ação de continuação por meio do PowerShell:

  ````PowerShell
  Enable-AzsScaleUnitNode -Location <RegionName> -Name <NodeName>
  ````

### <a name="repair"></a>Reparar

O **reparo** ação repara um nó. Use-o somente para qualquer um dos seguintes cenários:

- Substituição de nó completo (com ou sem novos discos de dados)
- Após a falha de componente de hardware e substituição (se for avisado na documentação de FRU (unidade) do campo).

> [!IMPORTANT]  
> Consulte a documentação de FRU do seu fornecedor hardware de OEM para obter as etapas exatas quando você precisar substituir um nó ou componentes de hardware individuais. A documentação de FRU especificará se você precisa executar a ação de reparo após a substituição de um componente de hardware.  

Quando você executa a ação de reparo, você precisa especificar o endereço IP do BMC. 

Para executar a ação de reparo por meio do PowerShell:

  ````PowerShell
  Repair-AzsScaleUnitNode -Location <RegionName> -Name <NodeName> -BMCIPAddress <BMCIPAddress>
  ````

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o módulo de administrador de malha do Azure Stack, confira [Azs.Fabric.Admin](https://docs.microsoft.com/powershell/module/azs.fabric.admin/?view=azurestackps-1.4.0).