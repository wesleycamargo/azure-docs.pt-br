---
title: "Usar o Visual Studio em uma máquina virtual do Azure | Microsoft Docs"
description: "Usar o Visual Studio em uma máquina virtual do Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: PhilLee-MSFT
manager: sacalla
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.prod: vs-devops-alm
ms.date: 01/30/2018
ms.author: phillee
keywords: visualstudio
ms.openlocfilehash: 599a890be4d014d22bae899be4cf6e281c4109d4
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a id="top"> </a> Imagens do Visual Studio no Azure
Usar o Visual Studio executando em uma VM (máquina virtual) pré-configurada do Azure é a maneira mais fácil e rápida de partir do nada para um ambiente de desenvolvimento atualizado.  As imagens do sistema com diferentes configurações do Visual Studio estão disponíveis no [Azure Marketplace](https://portal.azure.com/). Basta inicializar uma VM e pronto.

Você é novo no Azure? [Crie uma conta gratuita do Azure](https://azure.microsoft.com/free).

## <a name="what-configurations-and-versions-are-available"></a>Quais configurações e versões estão disponíveis?
No Azure Marketplace você encontra imagens para as principais versões mais recentes: Visual Studio 2017 e Visual Studio 2015.  Para cada versão principal, você consulta a versão originalmente liberada (aka 'RTW') e as versões atualizadas “mais recentes”.  Para cada uma dessas versões diferentes, você encontra as edições Visual Studio Enterprise e Visual Studio Community.  Essas imagens são atualizadas pelo menos uma vez por mês para incluir as atualizações mais recentes do Visual Studio e do Windows.  Embora os nomes das imagens permaneçam os mesmos, a descrição de cada imagem inclui a versão do produto instalada e a data inicial da imagem.

|               Versão de lançamento              |          Edições            |     Versão do produto     |
|:------------------------------------------:|:----------------------------:|:-----------------------:|
| Visual Studio 2017 - Última (versão 15.5) |    Enterprise, Community     |      Versão 15.5.3     |
|         Visual Studio 2017 - RTW           |    Enterprise, Community     |      Versão 15.0.7     |
|   Visual Studio 2015 - Última (Atualização 3)   |    Enterprise, Community     |  Versão 14.0.25431.01  |
|         Visual Studio 2015 - RTW           |              Nenhum            | (Expirado para manutenção) |

> [!NOTE]
> De acordo com a política de atendimento da Microsoft, a versão original (aka 'RTW') do Visual Studio 2015 expirou para manutenção.  Portanto, o Visual Studio 2015 Atualização 3 é a única versão restante oferecida para a linha de produtos Visual Studio 2015.

Para obter mais informações, consulte a [Política de Atendimento do Visual Studio](https://www.visualstudio.com/en-us/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Quais recursos são instalados?
Cada imagem contém o conjunto de recursos recomendado para essa edição do Visual Studio.  Geralmente, a instalação inclui:

* Todas as cargas de trabalho disponíveis, incluindo os componentes opcionais recomendados da carga de trabalho
* .NET 4.6.2 e .NET 4.7 SDKs, Pacotes de Destino, e Ferramentas para Desenvolvedores
* Visual F#
* Extensão do GitHub para Visual Studio
* Ferramentas do LINQ to SQL

Esta é a linha de comando que usamos para instalar o Visual Studio ao compilar as imagens:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Se as imagens não incluem um recurso do Visual Studio que você precisa, forneça esse comentário através da ferramenta de comentários (canto superior direito da página).

## <a name="what-size-vm-should-i-choose"></a>Qual tamanho de VM devo escolher?
O provisionamento de uma nova máquina virtual é fácil e o Azure oferece uma gama completa de tamanhos das máquinas virtuais.  Tal como acontece com qualquer aquisição de hardware, você deseja equilibrar o desempenho em relação ao custo.  Uma vez que o Visual Studio é um aplicativo multi-thread avançado, você deseja um tamanho de VM que inclua, pelo menos, dois processadores e 7 GB de memória.  Estes são os tamanhos de VM recomendados para as imagens do Visual Studio:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Para obter mais informações sobre os tamanhos das máquinas mais recentes, consulte [Tamanhos das máquinas virtuais do Windows no Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes).

Com o Azure, você não fica preso com sua primeira escolha - é possível reequilibrar sua escolha inicial redimensionando a VM.  É possível fornecer uma nova VM com um tamanho mais apropriado ou você pode redimensionar sua VM existente para hardware subjacente diferente.  Para obter mais informações, consulte [Redimensionar uma VM do Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/resize-vm).

## <a name="after-i-get-the-vm-running-then-what"></a>Depois de obter a VM em execução, o que acontece em seguida?
O Visual Studio acompanha o modelo "traga sua própria licença" no Azure.  Portanto, de forma semelhante a uma instalação em hardware proprietário, uma das primeiras etapas é o licenciamento da instalação do Visual Studio.  Você pode desbloquear o Visual Studio fazendo logon com uma conta da Microsoft associada a uma assinatura do Visual Studio, ou pode desbloquear o Visual Studio com a chave do produto com a compra inicial.  Para obter mais informações, consulte [Conectando-se ao Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/signing-in-to-visual-studio) e [Como desbloquear o Visual Studio](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="after-i-build-out-the-dev-vm-how-do-i-save-capture-it-for-future-or-team-use"></a>Após compilar o desenvolvimento da VM, como faço para salvá-lo (capturar) para uso futuro ou da equipe?

O espectro de ambientes de desenvolvimento é imenso e há custos reais associados à compilação de ambientes mais complexos.  No entanto, independentemente da configuração do seu ambiente, o Azure torna a preservação desse investimento fácil ao salvar/capturar sua VM perfeitamente configurada como uma "imagem base" para uso futuro – para você e/ou outros membros da sua equipe.  Então, ao inicializar uma nova VM, provisione-a a partir da imagem base e não da imagem do Marketplace.

Como um resumo rápido, será necessária a execução de sysprep e o desligamento da VM em execução e, em seguida, *capturar (Figura 1)* a VM como uma imagem através da interface do usuário do Portal do Azure.  O Azure salva o arquivo `.vhd` que contém a imagem, na conta de armazenamento de sua escolha.  Então, a nova imagem aparecerá como um recurso de imagem na lista de recursos da sua assinatura.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal’s UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Figura 1) Capturar uma imagem através da interface do usuário do Portal do Azure.*</center>

Para obter mais informações, consulte [Capturar uma VM para uma imagem](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource).

  **Lembrete:**  Não se esqueça de executar sysprep da VM!  Se essa etapa for ignorada, o Azure não poderá fornecer uma VM da imagem.

> [!NOTE]
> Você ainda tem algum custo para o armazenamento da(s) imagem(ns), mas esse custo incremental é provavelmente insignificante em comparação com os custos de mão de obra para recompilar a VM a partir do zero – para cada pessoa em sua equipe que precisar de uma VM.  Por exemplo, há o custo de alguns dólares para criar e armazenar uma imagem de 127 GB por um mês que é reutilizável por todos os membros de sua equipe.  No entanto, esses custos são insignificantes em comparação com as horas que cada funcionário investe para compilar e validar um desenvolvimento de caixa devidamente configurado para o uso individual.

Além disso, suas tarefas ou tecnologias de desenvolvimento podem precisar de mais escala – como variedades de configurações de desenvolvimento e múltiplas configurações de computadores.  Você pode usar o Azure DevTest Labs para criar _receitas_ que automatizam a construção de sua "imagem dourada" e gerenciar políticas para as VMs em execução da sua equipe.  [Usar Azure DevTest Labs para desenvolvedores](https://docs.microsoft.com/en-us/azure/devtest-lab/devtest-lab-developer-lab) é a melhor fonte para obter mais informações sobre DevTest Labs.

## <a name="next-steps"></a>Próximas etapas
Agora que você conhece as imagens pré-configuradas do Visual Studio, a próxima etapa é criar uma nova VM:

* [Criar uma VM através do Portal do Azure](quick-create-portal.md)
* [Visão geral de Máquinas Virtuais do Windows](overview.md)
