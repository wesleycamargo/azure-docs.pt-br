---
title: Habilitar a CLI do Azure para usuários do Azure Stack | Microsoft Docs
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para gerenciar e implantar recursos no Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/28/2018
ms.author: mabrigg
ms.openlocfilehash: c2827a4badd61aeb8de556795834dee39769e85e
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554496"
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Habilitar a CLI do Azure para usuários do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode fornecer o certificado da AC raiz para os usuários do Azure Stack para que eles podem usar a CLI do Azure em seus computadores de desenvolvimento. Os usuários precisarão o certificado para gerenciar os recursos por meio da CLI.

* **O certificado de raiz da autoridade de certificação do Azure Stack** será necessária se os usuários estão usando a CLI em uma estação de trabalho fora o Kit de desenvolvimento do Azure Stack.  

* **O ponto de extremidade de aliases de máquina virtual** fornece um alias, como "UbuntuLTS" ou "Win2012Datacenter", que faz referência a um editor de imagem, oferta, SKU e versão como um único parâmetro durante a implantação de VMs.  

As seções a seguir descrevem como obter esses valores.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportar o certificado de raiz da autoridade de certificação do Azure Stack

Você pode encontrar o certificado de raiz da autoridade de certificação do Azure Stack no kit de desenvolvimento e em uma máquina virtual de locatário que está em execução no ambiente do kit de desenvolvimento. Para exportar o certificado de raiz do Azure Stack no formato PEM, entre em seu kit de desenvolvimento ou a máquina virtual de locatário e execute o script a seguir:

```powershell
$label = "<Your Azure Stack CA root certificate name>"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurar o ponto de extremidade de aliases de máquina virtual

Operadores do Azure Stack devem definir um ponto de extremidade publicamente acessível que hospeda um arquivo de alias de máquina virtual. O arquivo de alias de máquina virtual é um arquivo JSON que fornece um nome comum para uma imagem. Esse nome é especificado, posteriormente, quando uma VM é implantada como um parâmetro de CLI do Azure.  

Antes de adicionar uma entrada para um arquivo de alias, certifique-se de que você [download de imagens do Azure Marketplace](azure-stack-download-azure-marketplace-item.md), ou ter [publicado sua própria imagem personalizada](azure-stack-add-vm-image.md). Se você publicar uma imagem personalizada, anote as informações de publicador, oferta, SKU e versão que você especificou durante a publicação. Se for uma imagem do marketplace, você pode exibir as informações usando o ```Get-AzureVMImage``` cmdlet.  

Um [arquivo de alias de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) com muitas imagens comuns aliases está disponível. Você pode usá-lo como um ponto de partida. Hospede esse arquivo em um espaço onde os clientes CLI podem acessá-lo. Uma maneira é hospedar o arquivo em uma conta de armazenamento de BLOBs e compartilhar com seus usuários a URL:

1. Baixe o [arquivo de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) do GitHub.
2. Crie uma nova conta de armazenamento no Azure Stack. Quando isso for feito, crie um novo contêiner de blob. Definir a política de acesso para "public".  
3. Carregar o arquivo JSON para o novo contêiner. Quando isso for concluído, você pode exibir a URL do blob selecionando o nome do blob e, em seguida, selecionando a URL das propriedades do blob.

## <a name="next-steps"></a>Próximas etapas

- [Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)
- [Conectar-se com o PowerShell](azure-stack-connect-powershell.md)
- [Gerenciar permissões de usuário](azure-stack-manage-permissions.md)
