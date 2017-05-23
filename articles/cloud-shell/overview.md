---
title: "Visão geral do Azure Cloud Shell (versão prévia) | Microsoft Docs"
description: "Visão geral do Azure Cloud Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: 63f1c468b5f8f4b0bb298cb67adea8c01b065427
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017

---
# <a name="overview-of-azure-cloud-shell-preview"></a>Visão geral do Azure Cloud Shell (versão prévia)
O Azure Cloud Shell é um shell interativo e acessível pelo navegador para o gerenciamento de recursos do Azure.

![](media/startup.gif)

## <a name="features"></a>Recursos
### <a name="browser-based-shell-experience"></a>Experiência de shell baseada no navegador
O Cloud Shell permite o acesso a uma experiência de linha de comando baseada em navegador, que foi criada tendo em mente as tarefas de gerenciamento do Azure. Utilize o Cloud Shell para trabalhar de forma independente de um computador local, de uma maneira que somente a nuvem pode proporcionar.

### <a name="pre-configured-azure-workstation"></a>Estação de trabalho pré-configurada do Azure
O Cloud Shell vem pré-instalado com ferramentas de linha de comando populares e suporte para idiomas, para que você possa trabalhar mais rapidamente.

[Exiba a lista completa de ferramentas para o Azure Cloud Shell aqui.](features.md#tools)

### <a name="automatic-authentication"></a>Autenticação automática
O Cloud Shell é autenticado de forma segura e automática a cada sessão para ter acesso instantâneo a seus recursos por meio da CLI do Azure 2.0.

### <a name="connect-your-azure-file-storage"></a>Conectar o armazenamento de arquivos do Azure
As máquinas do Cloud Shell são temporárias e, consequentemente, exigem que um compartilhamento de arquivos do Azure seja montado como `clouddrive` para manter seu diretório $Home.
Na primeira inicialização, o Cloud Shell solicita a criação de um grupo de recursos, uma conta de armazenamento e um compartilhamento de arquivos em seu nome. Essa é uma etapa única e será anexada automaticamente para todas as sessões. 

![](media/storage-prompt.png)

Uma conta de armazenamento LRS é criada em seu nome com um compartilhamento de arquivos do Azure que contém uma imagem de disco de 5 GB padrão. O compartilhamento de arquivos é montado como `clouddrive` para interação de compartilhamento de arquivos com a imagem do disco sendo usada para sincronizar e manter seu diretório $Home. Custos de armazenamento regulares se aplicam.

Três recursos serão criados em seu nome:
1. Um Grupo de Recursos chamado: `cloud-shell-storage-<region>`
2. Uma Conta de Armazenamento chamada: `cs-uniqueGuid`
3. Um Compartilhamento de Arquivos chamado: `cs-<user>-<domain>-com-uniqueGuid`

> [!Note]
> Todos os arquivos em seu diretório $Home como chaves SSH são mantidos em sua imagem de disco do usuário armazenada no compartilhamento de arquivo montados. Aplica as práticas recomendadas ao salvar arquivos no diretório $Home e compartilhamento de arquivos montado.

[Saiba mais sobre o armazenamento de Cloud Shell, atualização de compartilhamentos de arquivos e upload/download de arquivos.] (persisting-shell-storage.md).

## <a name="concepts"></a>Conceitos
* O Cloud Shell é executado em um computador temporário fornecido por sessão e por usuário
* O Cloud Shell atinge o tempo limite após 10 minutos sem atividade interativa
* O Cloud Shell só pode ser acessado com um compartilhamento de arquivos anexado
* É atribuído ao Cloud Shell um computador por conta de usuário
* As permissões são definidas da mesma forma que para um usuário normal do Linux

[Saiba mais sobre todos os recursos do Cloud Shell.](features.md)

## <a name="examples"></a>Exemplos
* Criar ou editar scripts para gerenciar recursos do Azure em qualquer navegador
* Gerenciar recursos simultaneamente por meio do portal do Azure e da CLI do Azure 2.0
* Testar a CLI do Azure 2.0

[Teste todos esses exemplos no início rápido do Cloud Shell.](quickstart.md)

## <a name="pricing"></a>Preços
O computador que hospeda o Cloud Shell é gratuito,, com o pré-requisito de um compartilhamento de arquivos do Azure montado para persistir seu diretório $Home. Custos de armazenamento regulares se aplicam.

## <a name="supported-browsers"></a>Navegadores com suporte
O Cloud Shell é recomendado para Chrome, Edge e Safari. Embora tenha suporte para Chrome, Firefox, Safari, IE e Edge, o Cloud Shell está sujeito a configurações específicas do navegador.
Para ver as limitações conhecidas específicas, visite [Limitações do Cloud Shell](limitations.md).
