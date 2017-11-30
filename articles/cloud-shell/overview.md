---
title: "Visão Geral Azure Cloud Shell | Microsoft Docs"
description: "Visão geral do Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: juluk
ms.openlocfilehash: 08ab3b38e4c1fbeb1fac67c5d1b6f6749f7a0a3e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Visão geral do Azure Cloud Shell
O Azure Cloud Shell é um shell interativo e acessível pelo navegador para o gerenciamento de recursos do Azure.
Ele dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha.
Usuários do Linux podem optar por uma experiência com o Bash, enquanto usuários do Windows podem optar pelo PowerShell.

Inicializar por meio do portal do Azure usando o ícone do Cloud Shell:

![Inicialização do portal](media/overview/portal-launch-icon.png)

Aproveite Bash ou PowerShell no menu suspenso de seletor de shell:

![Bash no Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell no Cloud Shell (versão prévia)](media/overview/overview-ps-pic.png)

## <a name="features"></a>Recursos
### <a name="browser-based-shell-experience"></a>Experiência de shell baseada no navegador
O Cloud Shell permite o acesso a uma experiência de linha de comando baseada em navegador, que foi criada tendo em mente as tarefas de gerenciamento do Azure.
Utilize o Cloud Shell para trabalhar de forma independente de um computador local, de uma maneira que somente a nuvem pode proporcionar.

### <a name="choice-of-preferred-shell-experience"></a>Opção de experiência de shell preferida
O Azure Cloud Shell dá a você a flexibilidade de escolher a experiência de shell que melhor se adequa ao modo como você trabalha.
Os usuários do Linux podem optar por Bash no Shell de nuvem, enquanto os usuários do Windows podem optar por PowerShell no Cloud Shell (visualização).

### <a name="authenticated-and-configured-azure-workstation"></a>Estação de trabalho configurada e autenticada do Azure
O Cloud Shell vem gerenciado pela Microsoft com ferramentas de linha de comando populares e suporte para idiomas já pré-instalados, para que você possa trabalhar mais rapidamente. Adicionalmente, o Cloud Shell é autenticado de maneira segura e automaticamente para cada sessão para ter acesso instantâneo a seus recursos por meio da CLI do Azure 2.0 ou de cmdlets do Azure PowerShell.

Exiba a lista completa de ferramentas da [experiência do Bash](features.md#tools) e [experiência do PowerShell (Versão prévia).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Vários pontos de acesso
Além de Cloud Shell disponível no portal do Azure, ele também pode ser acessado de:
* [Documentação de "Teste" de 2.0 do CLI do Azure](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [Aplicativo móvel do Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Extensão Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-azure-files-storage"></a>Conectar o armazenamento de arquivos do Azure
Os computadores do Cloud Shell são temporários e, assim, exigem que um compartilhamento de Arquivos do Azure seja montado como `clouddrive` para manter seu diretório $Home.
Na primeira inicialização, o Cloud Shell solicita a criação de um grupo de recursos, uma conta de armazenamento e um compartilhamento de arquivos em seu nome. Essa é uma etapa única e será anexada automaticamente para todas as sessões. Um compartilhamento de arquivo único pode ser mapeado e será usado pelo Bash e pelo PowerShell no Cloud Shell (Versão prévia).

#### <a name="create-new-storage"></a>Criar novo armazenamento
![](media/overview/basic-storage.png)

Uma conta de LRS (armazenamento com redundância local) e um compartilhamento de Arquivos do Azure podem ser criados em seu nome. O compartilhamento de Arquivos do Azure será usado para ambientes Bash e PowerShell, se você optar por usar ambos. Custos de armazenamento regulares se aplicam.

Três recursos serão criados em seu nome:
1. Um Grupo de Recursos chamado: `cloud-shell-storage-<region>`
2. Uma Conta de Armazenamento chamada: `cs<uniqueGuid>`
3. Um Compartilhamento de Arquivos chamado: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> O Bash no Cloud Shell também cria uma imagem de disco de 5 GB padrão para manter `$Home`. Todos os arquivos em seu diretório $Home como chaves SSH são mantidos em sua imagem de disco do usuário armazenada no compartilhamento de arquivo montados. Aplica as práticas recomendadas ao salvar arquivos no diretório $Home e compartilhamento de arquivos montado.

#### <a name="use-existing-resources"></a>Usar recursos existentes
![](media/overview/advanced-storage.png)

Uma opção avançada é fornecida para associar os recursos existentes ao Cloud Shell.
No prompt de configuração de armazenamento, clique em “Mostrar configurações avançadas” para mostrar as opções adicionais.
As listas suspensas são filtradas para sua região do Cloud Shell atribuída e para contas de armazenamento com redundância local/global.

[Saiba mais sobre o armazenamento do Cloud Shell, atualização de compartilhamentos de arquivos e upload/download de arquivos.](persisting-shell-storage.md)

## <a name="concepts"></a>Conceitos
* O Cloud Shell é executado em um host temporário fornecido por sessão e por usuário
* O Cloud Shell atinge o tempo limite após 20 minutos sem atividade interativa
* O Cloud Shell exige a montagem de um compartilhamento de arquivos
* O Cloud Shell usa o mesmo compartilhamento de arquivos para Bash e PowerShell
* É atribuído ao Cloud Shell um computador por conta de usuário
* As permissões são definidas da mesma forma que para um usuário normal do Linux em Bash

Saiba mais sobre os recursos em [Bash no Cloud Shell](features.md) e [PowerShell no Cloud Shell (Versão prévia)](features-powershell.md).

## <a name="examples"></a>Exemplos
* Use scripts para automatizar as tarefas de gerenciamento do Azure
* Gerenciar recursos do Azure simultaneamente por meio do portal do Azure e ferramentas de linha de comando do Azure
* Testar CLI do Azure 2.0 ou cmdlets do Azure PowerShell

Experimente esses exemplos em início rápido para [Bash no Cloud Shell](quickstart.md) e [PowerShell no Cloud Shell (Versão prévia)](quickstart-powershell.md).

## <a name="pricing"></a>Preços
O computador que hospeda o Cloud Shell é gratuito, com um pré-requisito de um compartilhamento de Arquivos do Azure montado. Custos de armazenamento regulares se aplicam.

## <a name="next-steps"></a>Próximas etapas
[Início rápido do Bash no Cloud Shell](quickstart.md) <br>
[Início rápido do PowerShell no Cloud Shell (versão prévia)](quickstart-powershell.md)