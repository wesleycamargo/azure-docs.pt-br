---
title: Habilitar a CLI do Azure para usuários do Azure Stack | Microsoft Docs
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para gerenciar e implantar recursos no Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 01/15/2019
ms.author: mabrigg
ms.lastreviewed: 01/15/2019
ms.openlocfilehash: 34a33c82813a0059cc286a7cb2d1fe984a5bf6d2
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56452396"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Habilitar a CLI do Azure para usuários do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode fornecer o certificado da AC raiz para os usuários do Azure Stack para que eles podem usar a CLI do Azure em seus computadores de desenvolvimento. Os usuários precisam o certificado para gerenciar os recursos por meio da CLI.

 - **O certificado de raiz da autoridade de certificação do Azure Stack** será necessária se os usuários estão usando a CLI em uma estação de trabalho fora o Kit de desenvolvimento do Azure Stack.  

 - **O ponto de extremidade de aliases de máquina virtual** fornece um alias, como "UbuntuLTS" ou "Win2012Datacenter", que faz referência a um editor de imagem, oferta, SKU e versão como um único parâmetro durante a implantação de VMs.  

As seções a seguir descrevem como obter esses valores.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportar o certificado de raiz da autoridade de certificação do Azure Stack

Se você estiver usando um sistema integrado, você não precisa exportar o certificado de raiz da autoridade de certificação. Você precisará exportar o certificado de autoridade de certificação raiz em um Azure Stack desenvolvimento ASDK (Kit).

Para exportar o certificado de raiz ASDK no formato PEM, entrar e execute o seguinte script:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Write-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurar o ponto de extremidade de aliases de máquina virtual

Operadores do Azure Stack devem definir um ponto de extremidade publicamente acessível que hospeda um arquivo de alias de máquina virtual. O arquivo de alias de máquina virtual é um arquivo JSON que fornece um nome comum para uma imagem. Quando você implanta uma VM como um parâmetro de CLI do Azure, você usará o nome.  

Antes de adicionar uma entrada para um arquivo de alias, certifique-se de que você [download de imagens do Azure Marketplace](azure-stack-download-azure-marketplace-item.md) ou ter [publicado sua própria imagem personalizada](azure-stack-add-vm-image.md). Se você publicar uma imagem personalizada, anote as informações de publicador, oferta, SKU e versão que você especificou durante a publicação. Se for uma imagem do marketplace, você pode exibir as informações usando o ```Get-AzureVMImage``` cmdlet.  

Um [arquivo de alias de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) com muitas imagens comuns aliases está disponível. Você pode usá-lo como um ponto de partida. Hospede esse arquivo em um espaço onde os clientes CLI podem acessá-lo. Uma maneira é hospedar o arquivo em uma conta de armazenamento de BLOBs e compartilhar com seus usuários a URL:

1. Baixe o [arquivo de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) do GitHub.
2. Crie uma conta de armazenamento no Azure Stack. Quando isso for feito, crie um contêiner de blob. Definir a política de acesso para "public".  
3. Carregar o arquivo JSON para o novo contêiner. Quando isso for concluído, você pode exibir a URL do blob. Selecione o nome do blob e, em seguida, selecionando a URL das propriedades do blob.

## <a name="next-steps"></a>Próximas etapas

- [Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
- [Conectar-se com o PowerShell](azure-stack-connect-powershell.md)
- [Gerenciar permissões de usuário](azure-stack-manage-permissions.md)
