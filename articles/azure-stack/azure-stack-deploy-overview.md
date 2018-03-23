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
ms.date: 03/22/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: 1deabcf64b3fbf3cbc89232c340a8882cd2591e8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Início rápido: Avaliar o Kit de desenvolvimento de pilha do Azure
O [Kit de desenvolvimento na pilha do Azure (ASDK)](.\asdk\asdk-what-is.md) é um ambiente de teste e desenvolvimento que pode ser implantado para avaliar e demonstrar os recursos de pilha do Azure e serviços. Para começar a usar o ASDK, você precisa preparar o host do hardware do computador e, em seguida, executar alguns scripts (instalação leva várias horas). Depois disso, você pode entrar portais de administrador e usuário para começar a usar a pilha do Azure.

## <a name="prerequisites"></a>Pré-requisitos 
Antes de instalar o ASDK, você precisa preparar o computador que hospedará o kit de desenvolvimento (o host do kit de desenvolvimento). O computador de host do kit de desenvolvimento deve atender aos requisitos de rede, mínimos de hardware e software. 

Você também precisa escolher entre usar o Azure Active Directory (AD do Azure) ou os serviços de Federação do Active Directory (AD FS) como a solução de identidade para sua implantação. 

Certifique-se de que o host do kit de desenvolvimento atende aos requisitos mínimos de hardware e que você fez a decisão de solução de identidade feita antes de iniciar a implantação para que o processo de instalação seja executado sem problemas. 

**[Examine a considerações de planejamento de implantação de ASDK](.\asdk\asdk-deploy-considerations.md)**

> [!TIP]
> Você pode usar o [ferramenta de verificação de requisitos de implantação do Azure pilha](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) depois de instalar o sistema operacional no computador de host do kit de desenvolvimento para confirmar se o hardware atende todos os requisitos.

## <a name="download-and-extract-the-deployment-package"></a>Baixe e extraia o pacote de implantação
Depois de garantir que o computador de host do kit de desenvolvimento atenda aos requisitos básicos para instalar o ASDK, a próxima etapa é fazer o download e extraia o pacote de implantação ASDK. O pacote de implantação inclui o arquivo Cloudbuilder.vhdx, que é um disco rígido virtual que inclui um sistema operacional inicializável e os arquivos de instalação de pilha do Azure.

Você pode baixar o pacote de implantação para o host do kit de desenvolvimento ou para outro computador. Os arquivos extraídos implantação levarem até 60 GB de espaço livre em disco, por isso usando outro computador pode ajudar a reduzir os requisitos de hardware para o host do kit de desenvolvimento.

**[Baixe e extraia o Kit de desenvolvimento de pilha do Azure (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-development-kit-host-computer"></a>Preparar o computador de host do kit de desenvolvimento
Antes de instalar o ASDK no computador host, o ambiente deve ser preparado e o sistema é configurado para inicialização do VHD. Depois que o computador de host do kit de desenvolvimento foi preparado, ele é inicializado do disco rígido CloudBuilder.vhdx máquina virtual para que você possa começar a implantação ASDK.

**[Preparar o computador de host ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Instalar o ASDK no computador host
Depois de preparar o computador de host do kit de desenvolvimento, o ASDK pode ser implantado na imagem CloudBuilder.vhdx. O ASDK pode ser implantado usando uma interface gráfica do usuário (GUI) fornecida baixando e executando o script do PowerShell asdk installer.ps1 ou completamente de [a linha de comando](.\asdk\asdk-deploy-powershell.md). 

> [!NOTE]
> Opcionalmente, depois que o computador host foi inicializado para o CloudBuilder.vhdx, você pode configurar [as configurações de telemetria do Azure pilha](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *antes de* instalando o ASDK.


**[Instalar o Kit de desenvolvimento de pilha do Azure (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Executar as configurações de pós-implantação
Depois de instalar o ASDK, há algumas verificações de pós-instalação recomendadas e alterações de configuração que devem ser feitas. Você pode validar a instalação tiver sido instalado com êxito usando o cmdlet test-AzureStack e instalar as ferramentas do PowerShell do Azure pilha e GitHub. 

Depois de implantações que usam o AD do Azure, você deve ativar ambos os portais de administrador e locatário pilha do Azure. Essa ativação consente fornecendo o portal de pilha do Azure e o Azure Resource Manager as permissões corretas (listadas na página de consentimento) para todos os usuários do diretório.

Você também deve redefinir a política de expiração de senha para certificar-se de que a senha para o host do kit de desenvolvimento não expira antes de seu período de avaliação terminar.

> [!NOTE]
> Opcionalmente, você também pode configurar [as configurações de telemetria do Azure pilha](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *depois* instalando o ASDK.

**[Tarefas de implantação de POST-ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrar com o Azure
Você deve registrar a pilha do Azure com o Azure para que você possa [baixar itens do marketplace do Azure](.\asdk\asdk-marketplace-item.md) a pilha do Azure.

**[Registrar a pilha do Azure com o Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Próximas etapas
Parabéns! Depois de concluir essas etapas, você terá um ambiente de kit de desenvolvimento com [administrador](https://adminportal.local.azurestack.external) e [usuário](https://portal.local.azurestack.external) portais. 
