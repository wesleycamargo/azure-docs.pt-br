---
title: Recursos do Azure Cloud Shell | Microsoft Docs
description: Visão geral dos recursos do Bash no Azure Cloud Shell
services: Azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2018
ms.author: juluk
ms.openlocfilehash: 09c3ca23aafc8519b9e3ad57d030f066bb153e26
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056184"
---
# <a name="features--tools-for-azure-cloud-shell"></a>Recursos e ferramentas do Azure Cloud Shell

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

O Azure Cloud Shell é executado no `Ubuntu 16.04 LTS`.

## <a name="features"></a>Recursos

### <a name="secure-automatic-authentication"></a>Autenticação automática segura

O Cloud Shell protege e autentica automaticamente o acesso de conta para o Azure PowerShell e a CLI do Azure 2.0.

### <a name="home-persistence-across-sessions"></a>Persistência de $Home entre as sessões

Para persistir arquivos entre as sessões, o Cloud Shell orienta você pela anexação de um compartilhamento de arquivos do Azure na primeira inicialização.
Após a conclusão, o Cloud Shell anexará automaticamente o armazenamento (montado como `$Home\clouddrive`) para todas as sessões futuras.
Além disso, seu diretório `$Home` é mantido como .img em seu compartilhamento de arquivos do Azure.
Os arquivos fora de `$Home` e do estado da máquina não são mantidos entre sessões. Use as práticas recomendadas ao armazenar segredos, como chaves SSH. Serviços como o [Azure Key Vault têm tutoriais para a instalação](https://docs.microsoft.com/azure/key-vault/key-vault-manage-with-cli2#prerequisites).

[Saiba mais sobre como manter arquivos no Cloud Shell](persisting-shell-storage.md)

### <a name="azure-drive-azure"></a>Unidade do Azure (Azure:)

PowerShell no Cloud Shell (versão prévia) começa com você no drive do Azure (`Azure:`).
A unidade do Azure permite a fácil detecção e navegação de recursos do Azure, como computação, rede, armazenamento e etc. de forma semelhante à navegação de sistema de arquivos.
Você pode continuar usando os [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure) comuns para gerenciar esses recursos, independentemente da unidade em que estiver.
Todas as alterações feitas nos recursos do Azure, diretamente no portal do Azure ou por meio de cmdlets do Azure PowerShell, são refletidas na unidade do Azure.  Você pode executar `dir -Force` para atualizar seus recursos.

![](media/features-powershell/azure-drive.png)

### <a name="deep-integration-with-open-source-tooling"></a>Ampla integração com ferramentas de software livre

O Cloud Shell inclui autenticação pré-configurada para ferramentas de software livre, como Terraform, Ansible e Chef InSpec. Experimente-o a partir do passo a passo de exemplo.

## <a name="tools"></a>Ferramentas

|Categoria   |NOME   |
|---|---|
|Ferramentas do Linux            |bash<br> zsh<br> sh<br> tmux<br> dig<br>               |
|Ferramentas do Azure            |CLI do Azure [1.0](https://github.com/Azure/azure-cli) e [2.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [CLI do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cli) |
|Editores de texto           |vim<br> nano<br> emacs       |
|Controle do código-fonte         |git                    |
|Ferramentas de build            |make<br> maven<br> npm<br> pip         |
|Contêineres             |[CLI do Docker](https://github.com/docker/cli)/[Computador do Docker](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [Helm](https://github.com/kubernetes/helm)<br> [CLI DO DC/OS](https://github.com/dcos/dcos-cli)         |
|Bancos de dados              |Cliente do MySQL<br> Cliente do PostgreSql<br> [Utilitário SQLCMD](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|Outros                  |Cliente do iPython<br> [CLI do Cloud Foundry](https://github.com/cloudfoundry/cli)<br> [Terraform](https://www.terraform.io/docs/providers/azurerm/)<br> [Ansible](https://www.ansible.com/microsoft-azure)<br> [Chef InSpec](https://www.chef.io/inspec/)| 

## <a name="language-support"></a>Suporte ao idioma

|Linguagem   |Versão   |
|---|---|
|.NET Core  |2.0.0       |
|Go         |1.9        |
|Java       |1.8        |
|Node.js    |8.9.4      |
|PowerShell |[6.0.2](https://github.com/PowerShell/powershell/releases)       |
|Python     |2.7 e 3.5 (padrão)|

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Bash no Cloud Shell](quickstart.md) <br>
[Início rápido do PowerShell no Cloud Shell (versão prévia)](quickstart-powershell.md) <br>
[Saiba mais sobre a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre o Azure PowerShell](https://docs.microsoft.com/powershell/azure/) <br>
