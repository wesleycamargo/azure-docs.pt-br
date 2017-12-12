---
title: "Habilitar a CLI do Azure para usuários de pilha do Azure | Microsoft Docs"
description: Saiba como usar a interface de linha de comando de plataforma cruzada (CLI) para gerenciar e implantar recursos na pilha do Azure
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: e2483bda5a0c6a6b270759946f146c37c5dad5b1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Habilitar a CLI do Azure para usuários de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Não existem quaisquer tarefas de operador específico de pilha do Azure que você pode executar usando a CLI do Azure. Mas, antes que os usuários podem gerenciar recursos por meio de CLI, os operadores do Azure pilha devem fornecê-los com o seguinte:

* **O certificado de raiz da autoridade de certificação do Azure pilha** é necessário se os usuários estão usando a CLI de uma estação de trabalho fora o Kit de desenvolvimento de pilha do Azure.  

* **O ponto de extremidade de aliases de máquina virtual** fornece um alias, como "UbuntuLTS" ou "Win2012Datacenter", que faz referência a um editor de imagem, oferta, SKU e versão como um único parâmetro durante a implantação de VMs.  

As seções a seguir descrevem como obter esses valores.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exporte o certificado de raiz da autoridade de certificação de pilha do Azure

O certificado de raiz da autoridade de certificação de pilha do Azure está disponível no kit de desenvolvimento e em uma máquina virtual de locatário que está sendo executado no ambiente do kit de desenvolvimento. Para exportar o certificado de raiz de pilha do Azure no formato PEM, entrar no seu kit de desenvolvimento ou a máquina virtual de locatário e execute o script a seguir:

```powershell
$label = "AzureStackSelfSignedRootCert"
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

Operadores de pilha do Azure devem configurar um ponto de extremidade publicamente acessível que hospeda um arquivo de alias de máquina virtual. O arquivo de alias de máquina virtual é um arquivo JSON que fornece um nome comum para uma imagem. Esse nome é subsequentemente especificado quando uma máquina virtual é implantada como um parâmetro de CLI do Azure.  

Antes de adicionar uma entrada para um arquivo de alias, verifique se você [download de imagens do Azure Marketplace](azure-stack-download-azure-marketplace-item.md), ou ter [publicado sua própria imagem personalizada](azure-stack-add-vm-image.md). Se você publicar uma imagem personalizada, anote as informações de publicador, oferta, SKU e versão que você especificou durante a publicação. Se for uma imagem do marketplace, você pode exibir as informações usando o ```Get-AzureVMImage``` cmdlet.  
   
Um [arquivo de alias de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) com muitos imagem comum aliases está disponível. Você pode usá-lo como um ponto de partida. Hospede esse arquivo em um espaço onde seus clientes CLI podem alcançá-lo. É uma maneira hospedar o arquivo em uma conta de armazenamento de blob e compartilhe o URL com seus usuários:

1. Baixe o [arquivo de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) do GitHub.
2. Crie uma nova conta de armazenamento na pilha do Azure. Quando for concluída, crie um novo contêiner de blob. Definir a política de acesso para "público".  
3. Carregue o arquivo JSON para o novo contêiner. Quando for concluída, você pode exibir a URL do blob clicando no nome de blob e, em seguida, selecionando a URL das propriedades do blob.


## <a name="next-steps"></a>Próximas etapas

[Implantar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)

[Conectar-se com o PowerShell](azure-stack-connect-powershell.md)

[Gerenciar permissões de usuário](azure-stack-manage-permissions.md)

