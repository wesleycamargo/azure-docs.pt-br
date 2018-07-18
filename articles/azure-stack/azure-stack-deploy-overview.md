---
title: Avaliar o Kit de desenvolvimento de pilha do Azure | Microsoft Docs
description: Saiba como implantar o Kit de desenvolvimento de pilha do Azure para fins de avaliação.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/04/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: a0e742ab3ac43cc7977761dd94c9689e3a7c2e0b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235178"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Início rápido: avaliar o Kit de desenvolvimento de pilha do Azure

O [Kit de desenvolvimento na pilha do Azure (ASDK)](.\asdk\asdk-what-is.md) é um ambiente de teste e desenvolvimento que pode ser implantado para avaliar e demonstrar os recursos de pilha do Azure e serviços. Para começar a usar o ASDK, você precisa preparar o host do hardware do computador e, em seguida, executar alguns scripts (instalação leva várias horas). Depois disso, você pode entrar portais de administrador ou usuário para começar a usar a pilha do Azure.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="asdk-host-computer-requirements"></a>Requisitos do computador host ASDK

Antes de instalar o ASDK, você precisa preparar o computador que hospedará o kit de desenvolvimento. O computador de host do kit de desenvolvimento deve atender o hardware, software e requisitos de rede descritos em  **[examine a considerações de planejamento de implantação ASDK](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> Você pode usar o [ferramenta de verificação de requisitos de implantação do Azure pilha](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) depois de instalar o sistema operacional no computador de host do kit de desenvolvimento para confirmar se o hardware atende todos os requisitos.

### <a name="account-requirements"></a>Requisitos de conta

Você também precisa escolher entre usar o Azure Active Directory (AD do Azure) ou os serviços de Federação do Active Directory (AD FS) como a solução de identidade para sua implantação. Examine os requisitos de conta em  **[considerações de planejamento de implantação](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Baixe e extraia o pacote de implantação

Depois de preparar o computador de host do kit de desenvolvimento, baixe e extraia o pacote de implantação ASDK. O pacote de implantação inclui o arquivo Cloudbuilder.vhdx, que é uma unidade de disco rígida virtual (VHD) com um sistema operacional inicializável, e os arquivos de instalação de pilha do Azure.

Você pode baixar o pacote de implantação para o host do kit de desenvolvimento ou para outro computador. Os arquivos extraídos implantação levarem até 60 GB de espaço livre em disco, para que usar outro computador pode ajudar a reduzir os requisitos de armazenamento no host do kit de desenvolvimento.

**[Baixe e extraia o Kit de desenvolvimento de pilha do Azure (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Preparar o computador host

Antes de instalar o ASDK, o ambiente de host deve ser preparado e o sistema é configurado para inicialização do VHD do kit de desenvolvimento. Quando você reiniciar o host, ele é inicializado CloudBuilder.vhdx e você pode começar a implantar o ASDK.

**[Preparar o computador de host ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Instalar o ASDK no computador host

Depois que o computador host inicializa a partir do VHD, você pode implantar o kit de desenvolvimento para o ambiente virtual Cloudbuilder. Você pode implantar o ASDK usando a interface gráfica do usuário (GUI), fornecida ao executar o script do PowerShell asdk installer.ps1 ou de [da linha de comando do PowerShell](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> Após inicializar o host da imagem Cloudbuilder.vhdx, você tem a opção de configuração [as configurações de telemetria do Azure pilha](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *antes de* instalando o ASDK.

**[Instalar o Kit de desenvolvimento de pilha do Azure (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Executar as configurações de pós-implantação

Depois de instalar o ASDK, há algumas verificações de pós-instalação recomendadas e alterações de configuração que devem ser feitas.

**Ferramentas**

Instalar as ferramentas do PowerShell do Azure pilha e GitHub e verificar o êxito da instalação usando o cmdlet test-AzureStack.

**Solução de identidade**

Para uma implantação que usa o AD do Azure, você deve ativar ambos os portais de administrador e locatário pilha do Azure. Essa ativação consente fornecendo o portal de pilha do Azure e o Azure Resource Manager as permissões corretas (listadas na página de consentimento) para todos os usuários do diretório.

**Expiração de senha**

Você deve redefinir a política de expiração de senha para certificar-se de que a senha para o host do kit de desenvolvimento não expira antes de seu período de avaliação terminar.

> [!NOTE]
> Você também tem a opção de configuração [as configurações de telemetria do Azure pilha](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *depois* instalando o ASDK.

**[Tarefas de implantação de POST-ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrar com o Azure

Você deve registrar a pilha do Azure com o Azure para que você possa [baixar itens do marketplace do Azure](.\asdk\asdk-marketplace-item.md) a pilha do Azure.

**[Registrar a pilha do Azure com o Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Próximas etapas

Parabéns! Ao concluir as etapas neste guia de início rápido você tiver um ambiente ASDK um [administrador](https://adminportal.local.azurestack.external) portal e um [usuário](https://portal.local.azurestack.external) portal.
