---
title: Baixe e extraia o Kit de desenvolvimento de pilha do Azure (ASDK) | Microsoft Docs
description: Descreve como baixar e extrair o Kit de desenvolvimento de pilha do Azure (ASDK).
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
ms.openlocfilehash: 0cf389c9443a9cff477b884c277d72de27769afc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
ms.locfileid: "29976416"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Baixe e extraia o Kit de desenvolvimento de pilha do Azure (ASDK)
Depois de garantir que o computador de host do kit de desenvolvimento atenda aos requisitos básicos para instalar o ASDK, a próxima etapa é fazer o download e extraia o pacote de implantação para obter o Cloudbuilder.vhdx ASDK.

## <a name="download-the-asdk"></a>Baixe o ASDK
1. Antes de iniciar o download, certifique-se de que o computador atende aos seguintes pré-requisitos:

  - O computador deve ter pelo menos 60 GB de espaço livre em disco disponível em quatro separados, idênticos discos rígidos lógicos além para o disco do sistema operacional.
  - [.NET framework 4.6 (ou uma versão posterior)](https://aka.ms/r6mkiy) deve ser instalado.

2. [Vá para a página de Introdução](https://azure.microsoft.com/overview/azure-stack/try/?v=try) onde você pode baixar o Kit de desenvolvimento de pilha do Azure, forneça os detalhes e, em seguida, clique em **enviar**.
3. Baixe e execute o [Verificador de implantação do Kit de desenvolvimento de pilha do Azure](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) script Verificador de pré-requisitos. Esse script autônomo mostra as verificações de pré-requisitos feitas pela instalação para o Kit de desenvolvimento de pilha do Azure. Ele fornece uma maneira para confirmar que você cumpra os requisitos de hardware e software, antes de baixar o pacote maior para o Kit de desenvolvimento de pilha do Azure.
4. Em **baixar o software**, clique em **Kit de desenvolvimento de pilha do Azure**.

  > [!NOTE]
  > O download ASDK (AzureStackDevelopmentKit.exe) é de aproximadamente 10GB.

## <a name="extract-the-asdk"></a>Extrair o ASDK
1. Após a conclusão do download, clique em **executar** para iniciar o Self-extractor ASDK (AzureStackDevelopmentKit.exe).
2. Leia e aceite o contrato de licença exibidos a **contrato de licença** página do Assistente de Self-Extractor e depois clique em **próximo**.
3. Revise as informações de declaração de privacidade exibidas no **Aviso importante** página do Assistente de Self-Extractor e depois clique em **próximo**.
4. Selecione o local dos arquivos de instalação de pilha do Azure a ser extraído no **Selecionar local de destino** página do Assistente de Self-Extractor e depois clique em **próximo**. O local padrão é *pasta atual*\Azure Kit de desenvolvimento de pilha. 
5. Revise o resumo de local de destino a **pronto para extrair** página do Assistente de Self-Extractor e depois clique em **extrair** para extrair o CloudBuilder.vhdx (aproximadamente 25 GB) e Arquivos de ThirdPartyLicenses.rtf. Esse processo leva algum tempo para concluir.
6. Copiar ou mover o arquivo CloudBuilder.vhdx para a raiz da unidade C:\ (C:\CloudBuilder.vhdx) no computador host ASDK.

> [!NOTE]
> Depois de extrair os arquivos, você pode excluir o. EXE e. Arquivos BINÁRIOS para recuperar espaço no disco rígido. Ou, você pode fazer backup desses arquivos para que você não precisa baixar os arquivos novamente, se você precisar reimplantar o ASDK.


## <a name="next-steps"></a>Próximas etapas
[Preparar o computador de host ASDK](asdk-prepare-host.md)