---
title: Baixe e extraia o Azure Stack desenvolvimento ASDK (Kit) | Microsoft Docs
description: Descreve como baixar e extrair o Azure Stack desenvolvimento ASDK (Kit).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: b55bc7f6aab522a6313498b6fdccc88870796224
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213020"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Baixe e extraia o Azure Stack desenvolvimento ASDK (Kit de)
Depois de garantir que seu computador de host do kit de desenvolvimento atende aos requisitos básicos para instalar o ASDK, a próxima etapa é baixar e extrair o pacote de implantação para obter o Cloudbuilder.vhdx ASDK.

## <a name="download-the-asdk"></a>Baixe o ASDK
1. Antes de iniciar o download, certifique-se de que seu computador atende aos seguintes pré-requisitos:

  - O computador deve ter pelo menos 60 GB de espaço livre em disco disponível em quatro separados, idêntico discos rígidos lógicos além para o disco do sistema operacional.
  - [.NET framework 4.6 (ou uma versão posterior)](https://aka.ms/r6mkiy) deve ser instalado.

2. [Vá para a página de Introdução](https://azure.microsoft.com/overview/azure-stack/try/?v=try) onde você pode baixar o Kit de desenvolvimento do Azure Stack, fornecer seus detalhes e, em seguida, clique em **enviar**.
3. Baixe e execute o [Verificador de implantação do Kit de desenvolvimento do Azure Stack](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) script Verificador de pré-requisitos. Esse script autônomo percorre as verificações de pré-requisitos feitas pela instalação para o Kit de desenvolvimento do Azure Stack. Ele fornece uma maneira para confirmar que você está atendendo aos requisitos de hardware e software, antes de baixar o pacote maior para o Kit de desenvolvimento do Azure Stack.
4. Sob **baixar o software**, clique em **Kit de desenvolvimento do Azure Stack**.

  > [!NOTE]
  > O download ASDK (AzureStackDevelopmentKit.exe) é de aproximadamente 10GB.

## <a name="extract-the-asdk"></a>Extrair o ASDK
1. Após a conclusão do download, clique em **executar** para iniciar o Self-extractor ASDK (AzureStackDevelopmentKit.exe).
2. Examine e aceite o contrato de licença exibidos a **contrato de licença** página do Assistente de Self-Extractor e depois clique em **próxima**.
3. Examine as informações de declaração de privacidade exibidas na **Aviso importante** página do Assistente de Self-Extractor e depois clique em **próxima**.
4. Selecione o local para arquivos de instalação do Azure Stack a ser extraído para ativado a **Selecionar local de destino** página do Assistente de Self-Extractor e depois clique em **próxima**. O local padrão é *pasta atual*\Azure Kit de desenvolvimento de pilha. 
5. Examine o resumo de local de destino a **pronto para extrair** página do Assistente de Self-Extractor e depois clique em **extrair** para extrair o CloudBuilder.vhdx (cerca de 25 GB) e ThirdPartyLicenses.rtf arquivos. Esse processo leva algum tempo para concluir.
6. Copiar ou mover o arquivo CloudBuilder.vhdx na raiz da unidade C:\ (C:\CloudBuilder.vhdx) no computador host ASDK.

> [!NOTE]
> Depois de extrair os arquivos, você pode excluir o. EXE e. Arquivos BINÁRIOS para recuperar espaço no disco rígido. Ou, você pode fazer backup desses arquivos para que você não precisa baixar os arquivos novamente se for necessário reimplantar o ASDK.


## <a name="next-steps"></a>Próximas etapas
[Preparar o computador de host ASDK](asdk-prepare-host.md)