---
title: Perguntas frequentes relacionadas ao servidor do Windows do Azure Stack | Microsoft Docs
description: Lista de perguntas frequentes do Marketplace do Azure Stack para Windows Server
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/12/2018
ms.openlocfilehash: 03a6f649f15f6a4905433d6e2ec292a901340929
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249673"
---
# <a name="windows-server-in-azure-stack-marketplace-faq"></a>Windows Server no Azure Stack Marketplace perguntas Frequentes

Este artigo responde a algumas perguntas frequentes sobre as imagens do Windows Server no [Marketplace do Azure Stack](azure-stack-marketplace.md).

## <a name="marketplace-items"></a>Itens do Marketplace

### <a name="how-do-i-update-to-a-newer-windows-image"></a>Como atualizar para uma imagem mais recente do Windows?

Primeiro, determine se os modelos do Azure Resource Manager se referir a versões específicas. Nesse caso, atualize esses modelos ou manter as versões mais antigas de imagem. É melhor usar **versão: mais recente**.

Em seguida, se quaisquer conjuntos de dimensionamento de máquina Virtual se referir a uma versão específica, pense sobre se eles mais tarde serão dimensionados e decida se deseja manter as versões mais antigas. Se nenhuma dessas condições se aplicar, exclua imagens mais antigas no Marketplace antes de baixar as mais novas. Use o gerenciamento de mercado para fazer isso, se for como original foi baixado. Em seguida, baixe a versão mais recente.

### <a name="what-are-the-licensing-options-for-windows-server-marketplace-images-on-azure-stack"></a>Quais são as opções de licenciamento para imagens do Windows Server Marketplace no Azure Stack?

A Microsoft oferece duas versões de imagens do Windows Server por meio do Marketplace do Azure Stack:

- **Pague à medida que você use**: Essas imagens executar os medidores de Windows do preço total. 
   Quem deve usar: Os clientes do Enterprise Agreement (EA) que usam o *modelo de cobrança de consumo*; CSPs que não desejam usar o licenciamento de SPLA.
- **Traga sua própria licença (BYOL)**: Essas imagens execute medidores básicos.
   Quem deve usar: Clientes do EA com uma licença do Windows Server; CSPs que usam o licenciamento de SPLA.

Não há suporte para o benefício de uso de híbrido do Azure (AHUB) no Azure Stack. Os clientes que licenciam por meio do modelo de "Capacidade" devem usar a imagem BYOL. Se você estiver testando com o Azure Stack desenvolvimento ASDK (Kit), você pode usar qualquer uma dessas opções.

### <a name="what-if-i-downloaded-the-wrong-version-to-offer-my-tenantsusers"></a>E se eu baixei a versão incorreta para oferecer Meus locatários/usuários?

Exclua a versão incorreta pela primeira vez por meio do gerenciamento do Marketplace. Aguarde até que ela seja concluída completamente (Observe as notificações de conclusão, não a folha de gerenciamento do Marketplace). Em seguida, baixe a versão correta.

### <a name="what-if-my-user-incorrectly-checked-the-i-have-a-license-box-in-previous-windows-builds-and-they-dont-have-a-license"></a>E se meu usuário incorretamente marcada a caixa "Eu tenho uma licença" no Windows anterior compila e eles não tiverem uma licença?

Ver [converter VMs do Windows Server com o benefício de volta para pré-pago](../virtual-machines/windows/hybrid-use-benefit-licensing.md#powershell-1).

### <a name="what-if-i-have-an-older-image-and-my-user-forgot-to-check-the-i-have-a-license-box-or-we-use-our-own-images-and-we-do-have-enterprise-agreement-entitlement"></a>E se eu tiver uma imagem mais antiga e meu usuário se esqueceu de verificar a caixa "Eu tenho uma licença", ou podemos usar nossas próprio imagens e temos o direito de Enterprise Agreement?

Ver [converter uma VM existente usando Azure híbrido benefício para o Windows Server](../virtual-machines/windows/hybrid-use-benefit-licensing.md#convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server). Observe que o benefício híbrido do Azure não se aplica ao Azure Stack, mas o efeito dessa configuração é aplicável.

### <a name="what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server"></a>O que outras VMs que usam o Windows Server, como SQL ou o servidor do Machine Learning?

Essas imagens são aplicáveis a **licenseType** parâmetro, para que sejam pagamento, como você usa. Você pode definir esse parâmetro (consulte as perguntas frequentes sobre anterior resposta). Isso se aplica somente ao software do Windows Server, não para em camadas produtos como SQL, que exigem que você traga sua própria licença. Pagamento conforme você usa o licenciamento não é aplicável aos produtos de software em camadas.

### <a name="i-have-an-enterprise-agreement-ea-and-will-be-using-my-ea-windows-server-license-how-do-i-make-sure-images-are-billed-correctly"></a>Tenho um Enterprise Agreement (EA) e usará minha licença do Windows Server de EA; como tornar-se de que as imagens são cobradas corretamente?

Você pode adicionar **licenseType: Windows_Server** em um modelo do Azure Resource Manager. Essa configuração deve ser adicionada para cada bloco de recurso de máquina virtual.

## <a name="activation"></a>Ativação

Para ativar uma máquina de virtual do Windows Server no Azure Stack, as seguintes condições devem ser verdadeiras:

- O OEM foi definido marcador BIOS apropriado em cada sistema de host no Azure Stack.
- Devem usar o Windows Server 2012 R2 e Windows Server 2016 [Automatic Virtual Machine Activation](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)). Serviço de gerenciamento de chaves (KMS) e outros serviços de ativação não têm suporte no Azure Stack.

### <a name="how-can-i-verify-that-my-virtual-machine-is-activated"></a>Como verificar que a minha máquina virtual está ativada?

Execute o seguinte comando em um prompt de comando elevado: 

```shell
slmgr /dlv
``` 

Se ele é ativado corretamente, você verá isso claramente indicadas e o nome do host é exibido no `slmgr` saída. Não dependem de marcas d'água na tela conforme eles talvez não seja atualizados ou estão sendo exibidos em uma máquina virtual diferente por trás de sua.

### <a name="my-vm-is-not-set-up-to-use-avma-how-can-i-fix-it"></a>Minha VM não está configurado para usar a AVMA, como posso corrigir o erro?

Execute o seguinte comando em um prompt de comando elevado: 

```shell
slmgr /ipk <AVMA key> 
```

Consulte o artigo [Automatic Virtual Machine Activation](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) para as chaves a ser usado para sua imagem.

### <a name="i-create-my-own-windows-server-images-how-can-i-make-sure-they-use-avma"></a>Posso criar minhas próprias imagens do Windows Server, como pode posso ter certeza de usar a AVMA?

É recomendável que você execute as `slmgr /ipk` linha de comando com a chave apropriada antes de executar o `sysprep` comando. Ou então, incluir a chave AVMA em qualquer arquivo de instalação Unattend.exe.

### <a name="i-am-trying-to-use-my-windows-server-2016-image-created-on-azure-and-it-is-not-activating-or-using-kms-activation"></a>Estou tentando usar minha imagem do Windows Server 2016 criada no Azure e não está ativando ou usando a ativação do KMS.

Execute o comando `slmgr /ipk`. Imagens do Azure podem não corretamente voltar a AVMA, mas se eles podem acessar o sistema KMS do Azure, eles serão ativados. É recomendável que você verifique se que essas VMs são definidas para usar a AVMA.

### <a name="i-have-performed-all-of-these-steps-but-my-virtual-machines-are-still-not-activating"></a>Eu executou todas essas etapas, mas minhas máquinas virtuais ainda não estão sendo ativados.

Entre em contato com seu fornecedor de hardware para verificar se os marcadores de BIOS corretos foram instalados.

### <a name="what-about-earlier-versions-of-windows-server"></a>E versões anteriores do Windows Server?

[Ativação automática de máquina Virtual](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11)) não tem suporte em versões anteriores do Windows Server. Você precisará ativar as VMs manualmente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos:

- [Visão geral do Azure Stack Marketplace](azure-stack-marketplace.md)
- [Baixar itens do marketplace do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md)
