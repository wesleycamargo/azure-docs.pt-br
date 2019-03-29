---
title: Guia para instalar e implantar o Linux C# agente de ASC para versão prévia do IoT | Microsoft Docs
description: Saiba como instalar o ASC para agente do IoT em Linux de 32 bits e 64 bits.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: d6b4e6065b0ef198ad583b3760124730e658fe0b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619901"
---
# <a name="deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Implantar o ASC para IoT C#-com base em agente de segurança para Linux

> [!IMPORTANT]
> ASC para IoT está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia explica como instalar e implantar o ASC para IoT C#-com base em agente de segurança no Linux.

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solucionar problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e versões do agente, consulte [escolha o agente de segurança correta](how-to-deploy-agent.md).

1. Para implantar o agente de segurança, são necessários direitos de administrador local no computador do qual em que você deseja instalar. 

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para implantar o agente de segurança, faça o seguinte:

1. Baixe a versão mais recente no seu computador de [Github](https://aka.ms/iot-security-github-cs).

1. Extraia o conteúdo do pacote e navegue até a _/instalar_ pasta.

1. Adicione permissões de execução para o **InstallSecurityAgent script** executando `chmod +x InstallSecurityAgent.sh` 

1. Em seguida, execute: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Ver [como configurar autenticação](concept-security-agent-authentication-methods.md) para obter mais informações sobre parâmetros de autenticação.

Esse script faz o seguinte:

- Instala pré-requisitos.

- Adiciona um usuário de serviço (com logon interativo desabilitado).

- Instala o agente como um **Daemon** -isso pressupõe que o dispositivo usa **systemd** para gerenciamento de serviços.

- Configura **sudoers** para permitir que o agente realizar certas tarefas como raiz.

- Configura o agente com os parâmetros de autenticação fornecido.


Para obter ajuda adicional, execute o script com o parâmetro – ajuda: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente, execute o script com o parâmetro – u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Desinstalação não remove todos os pré-requisitos ausentes que foram instalados durante a instalação.

## <a name="troubleshooting"></a>solução de problemas  

1. Verifique o status da implantação, executando:

    `systemctl status ASCIoTAgent.service`

2. Habilite registro em log.  
   Se o agente não conseguir iniciar, ative o log para obter mais informações.

   Ative o registro em log por:

   1. Abra o arquivo de configuração para edição em qualquer editor de Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Edite os valores a seguir: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       O **logFilePath** valor é configurável. 

       > [!NOTE]
       > É recomendável ativar o registro em log **desativar** após a conclusão da solução de problemas. Deixando o registro em log **em** aumentos de uso de dados e tamanho de arquivo de log.

   1. Reinicie o agente executando:

       `systemctl restart ASCIoTAgent.service`

   1. Exiba o arquivo de log para obter mais informações sobre a falha.  

       Local do arquivo de log é: `/var/ASCIoTAgent/IotAgentLog.log`

       Altere o caminho do local do arquivo acordo com o nome que você escolheu para o **logFilePath** na etapa 2. 

## <a name="next-steps"></a>Próximas etapas

- Leia o ASC para o serviço IoT [visão geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Habilitar o [serviço](quickstart-onboard-iot-hub.md)
- Leia o [perguntas Frequentes](resources-frequently-asked-questions.md)
- Entender [alertas](concept-security-alerts.md)