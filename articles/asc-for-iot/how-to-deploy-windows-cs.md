---
title: Instalação do Windows da Central de segurança do Azure para IoT agente versão prévia | Microsoft Docs
description: Saiba mais sobre como instalar a Central de segurança do Azure para o agente do IoT em dispositivos Windows de 32 bits ou 64 bits.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 7e3cb1f44711a8eedb248320db8bce5dfd0eaf0c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754600"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Implantar uma central de segurança do Azure para IoT C#-com base em agente de segurança para Windows

> [!IMPORTANT]
> Central de segurança do Azure para IoT está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este guia explica como instalar o Centro de segurança do Azure (ASC) para IoT C#-com base em agente de segurança no Windows.

Neste guia, você aprenderá a: 
> [!div class="checklist"]
> * Instalar
> * Verificar implantação
> * Desinstalar o agente
> * Solucionar problemas 

## <a name="prerequisites"></a>Pré-requisitos

Para outras plataformas e versões do agente, consulte [escolha o agente de segurança correta](how-to-deploy-agent.md).

1. Direitos de administrador local no computador que você deseja instalar em. 

1. [Criar um módulo de segurança](quickstart-create-security-twin.md) para o dispositivo.

## <a name="installation"></a>Instalação 

Para instalar o agente de segurança, faça o seguinte:

1. Para instalar o ASC para Windows IoT C# agent no dispositivo, baixe a versão mais recente no seu computador do ASC para IoT [repositório GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Extraia o conteúdo do pacote e navegue até a pasta /Install.

3. Abra o Windows PowerShell como administrador. 
    1. Adicione permissões de execução para o script InstallSecurityAgent, executando ```Unblock-File .\InstallSecurityAgent.ps1```
    
        e execute:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Por exemplo: 
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Ver [como configurar autenticação](concept-security-agent-authentication-methods.md) para obter mais informações sobre parâmetros de autenticação.

Esse script faz o seguinte:

- Instala pré-requisitos.

- Adiciona um usuário de serviço (com logon interativo desabilitado).

- Instala o agente como um **serviço do sistema**.

- Configura o agente com os parâmetros de autenticação fornecido.


Para obter ajuda adicional, use o comando Get-Help no PowerShell <br>Exemplo de Get-Help:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verificar o status da implantação

- Verifique o status de implantação do agente executando:<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Desinstalar o agente

Para desinstalar o agente:

1. Execute o seguinte script do PowerShell com o **-modo** parâmetro definido como **desinstalar**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>solução de problemas

Se o agente não for iniciado, ative o log (registro em log é *desativar* por padrão) para obter mais informações.

Para ativar o registro em log:

1. Abra o arquivo de configuração (General.config) para edição usando um editor de arquivos padrão.

1. Edite os valores a seguir:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > É recomendável ativar o registro em log **desativar** após a conclusão da solução de problemas. Deixando o registro em log **em** aumentos de uso de dados e tamanho de arquivo de log. 

1. Reinicie o agente executando o PowerShell ou linha de comando a seguir:

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   ou o

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Examine o arquivo de log para obter mais informações sobre a falha.

   Local do arquivo de log: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Próximas etapas
- Leia o ASC para o serviço IoT [visão geral](overview.md)
- Saiba mais sobre o ASC para IoT [arquitetura](architecture.md)
- Habilitar o [serviço](quickstart-onboard-iot-hub.md)
- Leia o [perguntas Frequentes](resources-frequently-asked-questions.md)
- Entender [alertas](concept-security-alerts.md)