---
title: Guia para instalar e implantar o agente do Linux C da Central de segurança do Azure para IoT agente Preview | Microsoft Docs
description: Saiba como instalar a Central de segurança para o agente do IoT do Azure no Linux de 32 bits e 64 bits.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 147813ae096114b4dfc1a20d2e0a70639aa82445
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754446"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Implantar a Central de segurança do Azure para o agente de segurança com base em C do IoT para Linux

> [!IMPORTANT]
> Central de segurança do Azure para IoT está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia explica como instalar e implantar o Azure segurança ASC (Central) para o agente de segurança com base em IoT C no Linux.

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solucionar problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e versões do agente, consulte [escolha o agente de segurança correta](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador do qual que você deseja instalar em (sudo).

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para instalar e implantar o agente de segurança, faça o seguinte:


1. Baixe a versão mais recente no seu computador de [Github](https://aka.ms/iot-security-github-c).

1. Extraia o conteúdo do pacote e navegue até a _/instalar_ pasta.

1. Adicione permissões de execução para o **InstallSecurityAgent script** executando o seguinte:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Em seguida, execute: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Ver [como configurar autenticação](concept-security-agent-authentication-methods.md) para obter mais informações sobre parâmetros de autenticação.

Esse script faz o seguinte:

1. Instala pré-requisitos.

2. Adiciona um usuário de serviço (com logon interativo desabilitado).

3. Instala o agente como um **Daemon** -pressupõe que o dispositivo usa **systemd** para gerenciamento de serviços.

4. Configura o agente com os parâmetros de autenticação fornecidos. 

Para obter ajuda adicional, execute o script com o parâmetro – ajuda: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com –-desinstalar o parâmetro:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>solução de problemas
Verifique o status da implantação, executando:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Próximas etapas
- Leia o ASC para o serviço IoT [visão geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Habilitar o [serviço](quickstart-onboard-iot-hub.md)
- Leia o [perguntas Frequentes](resources-frequently-asked-questions.md)
- Entender [alertas de segurança](concept-security-alerts.md)