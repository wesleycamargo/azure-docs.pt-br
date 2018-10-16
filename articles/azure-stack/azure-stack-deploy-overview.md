---
title: Avaliar o Kit de desenvolvimento do Azure Stack | Microsoft Docs
description: Saiba como implantar o Kit de desenvolvimento do Azure Stack para fins de avaliação.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: bf07fe56c65e53f5485b9927e0d704f80842cf3a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338686"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Guia de início rápido: avaliar o Kit de desenvolvimento do Azure Stack

O [Kit de desenvolvimento na pilha do Azure (ASDK)](.\asdk\asdk-what-is.md) é um ambiente de teste e desenvolvimento que você pode implantar para avaliar e demonstrar os recursos do Azure Stack e serviços. Para obter uma introdução a ASDK, você precisa preparar o host de hardware do computador e, em seguida, executar alguns scripts (instalação leva várias horas). Depois disso, você pode entrar portais do administrador ou usuário para começar a usar o Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="asdk-host-computer-requirements"></a>Requisitos do computador de host ASDK

Antes de instalar o ASDK, você precisa preparar o computador que hospedará o kit de desenvolvimento. O computador de host do kit de desenvolvimento deve atender o hardware, software e requisitos de rede descritos em  **[examine as considerações de planejamento de implantação de ASDK](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> Você pode usar o [ferramenta de verificação de requisitos de implantação do Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) depois de instalar o sistema operacional no computador de host do kit de desenvolvimento para confirmar se o seu hardware atende aos requisitos de todos os.

### <a name="account-requirements"></a>Requisitos de conta

Você também precisa escolher entre usar o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS) como a solução de identidade para sua implantação. Examine os requisitos de conta no  **[considerações de planejamento de implantação](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Baixe e extraia o pacote de implantação

Depois de preparar o computador de host do kit de desenvolvimento, baixe e extraia o pacote de implantação ASDK. O pacote de implantação inclui o arquivo Cloudbuilder.vhdx, que é uma unidade de disco rígida virtual (VHD) com um sistema operacional inicializável, e os arquivos de instalação do Azure Stack.

Você pode baixar o pacote de implantação para o host do kit de desenvolvimento ou em outro computador. Os arquivos de implantação extraídos levarem até 60 GB de espaço livre em disco, portanto, usando outro computador pode ajudar a reduzir os requisitos de armazenamento no host do kit de desenvolvimento.

**[Baixe e extraia o Azure Stack desenvolvimento ASDK (Kit de)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Preparar o computador host

Antes de instalar o ASDK, o ambiente de host deve ser preparado e o sistema configurado para inicialização a partir do VHD do kit de desenvolvimento. Quando você reiniciar o host, ele é inicializado CloudBuilder.vhdx e você pode começar a implantar o ASDK.

**[Preparar o computador de host ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Instalar o ASDK no computador host

Depois que o computador host inicializa do VHD, você pode implantar o kit de desenvolvimento para o ambiente virtual Cloudbuilder. Você pode implantar o ASDK usando a interface gráfica do usuário (GUI), fornecida ao executar o script do PowerShell asdk installer.ps1 ou de [da linha de comando do PowerShell](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> Após inicializar o host da imagem Cloudbuilder.vhdx, você tem a opção de configuração [as configurações de telemetria do Azure Stack](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *antes de* instalando o ASDK.

**[Instalar o Kit de desenvolvimento do Azure Stack ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Executar configurações pós-implantação

Depois de instalar o ASDK, há algumas verificações de pós-instalação recomendadas e as alterações de configuração que devem ser feitas.

**Ferramentas**

Instalar as ferramentas do Azure Stack PowerShell e o GitHub e verificar o êxito da instalação usando o cmdlet test-AzureStack.

**Solução de identidade**

Para uma implantação que usa o Azure AD, você deve ativar ambos os portais de administrador e locatário do Azure Stack. Essa ativação dá consentimento para oferecendo o portal do Azure Stack e o Azure Resource Manager as permissões corretas (listadas na página de consentimento) para todos os usuários do diretório.

**Expiração de senha**

Você deve redefinir a política de expiração de senha para certificar-se de que a senha para o host do kit de desenvolvimento não expire antes de seu período de avaliação terminar.

> [!NOTE]
> Você também tem a opção de configuração [as configurações de telemetria do Azure Stack](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *depois* instalando o ASDK.

**[Tarefas de implantação de POST-ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registre-se com o Azure

Você deve registrar o Azure Stack com o Azure para que você possa [baixar itens do marketplace do Azure](.\asdk\asdk-marketplace-item.md) para o Azure Stack.

**[Registre-se a pilha do Azure com o Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Próximas etapas

Parabéns! Concluindo as etapas neste guia de início rápido, você tem um ambiente ASDK com um [administrator](https://adminportal.local.azurestack.external) portal e uma [usuário](https://portal.local.azurestack.external) portal.
