---
title: Exemplos de modelo do Azure Resource Manager – Instâncias de Contêiner do Azure
description: Exemplos de modelo do Azure Resource Manager para Instâncias de Contêiner do Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259464"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Modelos do Azure Resource Manager para Instâncias de Contêiner do Azure

Os seguintes modelos de exemplo implantam instâncias de contêiner em várias configurações.

Para opções de implantação, consulte a [Implantação](#deployment). Se você quiser criar seus próprios modelos, a referência de modelo do Gerenciador de Recursos das [Instâncias de Contêiner do Azure][ref] detalha o formato do moelo e as propriedades disponíveis.

## <a name="sample-templates"></a>Modelos de exemplo

| | |
|-|-|
| **Aplicativos** ||
| [Wordpress][app-wp] | Cria um site de WordPress e seu banco de dados MySQL em uma instância de contêiner. O conteúdo do site WordPress e o banco de dados MySQL persistem em um compartilhamento do Azure Files. |
| [NAV MS com SQL Server e IIS][app-nav] | Implanta um único contêiner do Windows com um ambiente Dynamics NAV autossuficiente totalmente equipado / Dynamics 365 Business Central. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Implanta dois contêineres do Linux que compartilham um volume emptyDir. |
| [gitRepo][vol-gitrepo] | Implanta um contêiner do Linux que clona um repositório GitHub e monta-o como um volume. |
| [segredo][vol-secret] | Implanta um contêiner do Linux com um certificado PFX montado como um volume de segredo. |
| **Rede** ||
| [Contêiner expostos de UDP][net-udp] | Implanta um contêiner do Windows ou Linux que expõe uma porta UDP. |
| [Contêiner do Linux com o IP público][net-publicip] | Implanta um único contêiner do Linux acessível por meio de um IP público. |
| **Recursos do Azure** ||
| [Criar uma conta de Armazenamento do Microsoft Azure e o compartilhamento de arquivos][az-files] | Usa a CLI do Azure em uma instância de contêiner para criar uma conta de armazenamento e um compartilhamento do Azure Files.

## <a name="deployment"></a>Implantação

Você tem várias opções para a implantação de recursos com os modelos do Gerenciador de Recursos:

[CLI do Azure][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Portal do Azure][deploy-portal]

[API REST][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups