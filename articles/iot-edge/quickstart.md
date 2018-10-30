---
title: Início rápido ao Azure IoT Edge + Windows | Microsoft Docs
description: Experimente o Azure IoT Edge executando a análise em um dispositivo de borda simulado
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/02/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 50c430d22f58ce9d2c122c630df1689c18e50aad
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946157"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Início rápido: implantar seu primeiro módulo IoT Edge do Portal do Azure para um dispositivo Windows – versão prévia

Neste guia de início rápido, use a interface de nuvem do Azure IoT Edge para implantar remotamente um código pré-compilado em um dispositivo IoT Edge. Para realizar essa tarefa, primeiro use seu dispositivo Windows para simular um dispositivo IoT Edge, depois você pode implantar um módulo a ele.

Neste guia de início rápido, você aprende a:

1. Crie um Hub IoT.
2. Registrar um dispositivo IoT Edge em seu Hub IoT.
3. Instale e inicie o tempo de execução do IoT Edge no dispositivo.
4. Implante remotamente um módulo em um dispositivo IoT Edge e envie a telemetria para IoT Hub.

![Arquitetura do início rápido](./media/quickstart/install-edge-full.png)

O módulo implantado neste guia de início rápido é um sensor simulado que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados simulados para obter informações de negócios.

>[!NOTE]
>O tempo de execução do IoT Edge no Windows está em [versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você usa a CLI do Azure para concluir muitas das etapas neste guia de início rápido e o Azure IoT possui uma extensão para habilitar funcionalidades adicionais.

Adicione a extensão do Azure IoT à instância do shell de nuvem.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prerequisites"></a>Pré-requisitos

Recursos de nuvem:

* Um grupo de recursos para gerenciar todos os recursos que você usará neste início rápido.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus
   ```

Dispositivo IoT Edge:

* Um computador ou uma máquina virtual Windows para atuar como dispositivo IoT Edge. Use uma versão com suporte para Windows:
  * Windows 10 ou mais recente
  * Windows Server 2016 ou mais recente
* Se for um computador Windows, verifique se ele atende a [requisitos de sistema](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements) para Hyper-V.
* Caso seja uma máquina virtual, habilite a [virtualização aninhada](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) e aloque pelo menos 2 GB de memória.
* Instale o [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) e verifique se ele está em execução.

> [!TIP]
> Há uma opção durante a instalação do Docker para usar contêineres do Windows ou Linux. Este início rápido descreve como configurar o tempo de execução do IoT Edge para uso com contêineres do Linux.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Comece o início rápido criando o Hub IoT com a CLI do Azure.

![Criar Hub IoT](./media/quickstart/create-iot-hub.png)

O nível gratuito do Hub IoT funciona para este guia de início rápido. Se você tiver usado o Hub IoT antes e já tiver um hub disponível criado, você pode usar esse Hub IoT. Cada assinatura pode ter somente um hub IoT gratuito.

O código a seguir cria um hub **F1** gratuito no grupo de recursos **IoTEdgeResources**. Substitua *{hub_name}* por um nome exclusivo para o Hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1
   ```

   Se você receber um erro porque já exsite um hub gratuito na sua assinatura, altere o SKU para **S1**.

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.
![Registrar um dispositivo](./media/quickstart/register-device.png)

Crie uma identidade de dispositivo para seu dispositivo simulado para que ele possa se comunicar com o hub IoT. A identidade do dispositivo reside na nuvem e você usa uma cadeia de conexão de dispositivo exclusiva para associar um dispositivo físico a uma identidade do dispositivo.

Como os dispositivos IoT Edge se comportam e podem ser gerenciados diferentemente de dispositivos IoT comuns, declare esse para ser um dispositivo de IoT Edge desde o início.

1. No Azure Cloud Shell, digite o seguinte comando para criar um dispositivo denominado **myEdgeDevice** no seu hub.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --hub-name {hub_name} --edge-enabled
   ```

1. Recupere a cadeia de conexão para o seu dispositivo, o que vincula o dispositivo físico à sua identidade no Hub IoT.

   ```azurecli-interactive
   az iot hub device-identity show-connection-string --device-id myEdgeDevice --hub-name {hub_name}
   ```

1. Copie a cadeia de conexão e salve-a. Você usará esse valor para configurar o tempo de execução de IoT Edge na próxima seção.

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o tempo de execução do IoT Edge

Instale o tempo de execução do Azure IoT Edge no dispositivo IoT Edge e configure-o com uma cadeia de conexão do dispositivo.
![Registrar um dispositivo](./media/quickstart/start-runtime.png)

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Tem três componentes. O **daemon de segurança do IoT Edge** é iniciado sempre que um dispositivo Edge é iniciado e inicializa o dispositivo inicializando o agente do IoT Edge. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT.

Durante a instalação do tempo de execução, você precisará fornecer uma cadeia de conexão do dispositivo. Use a cadeia de caracteres que você recuperou da CLI do Azure. Essa cadeia de caracteres associa seu dispositivo físico à identidade do dispositivo IoT Edge no Azure.

As instruções nesta seção configuram o tempo de execução do IoT Edge com contêineres do Linux. Se você deseja usar os contêineres do Windows, consulte [Instalar o tempo de execução do Azure IoT Edge no Windows para usar com contêineres do Windows](how-to-install-iot-edge-windows-with-windows.md).

Conclua as etapas a seguir no computador ou na VM Windows que você preparou para funcionar como um dispositivo IoT Edge.

### <a name="download-and-install-the-iot-edge-service"></a>Baixe e instale o serviço do IoT Edge

Use o PowerShell para baixar e instalar o tempo de execução do IoT Edge. Use a cadeia de conexão do dispositivo que você recuperou do Hub IoT para configurar o dispositivo.

1. No seu dispositivo IoT Edge, execute o PowerShell como administrador.

2. Baixe e instale o serviço IoT Edge em seu dispositivo.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Install-SecurityDaemon -Manual -ContainerOs Linux
   ```

3. Quando solicitado a fornecer uma **DeviceConnectionString**, forneça a cadeia de caracteres que você copiou na seção anterior. Não inclua aspas na cadeia de conexão.

### <a name="view-the-iot-edge-runtime-status"></a>Veja o status do tempo de execução do IoT Edge

Verifique se o tempo de execução foi instalado e configurado com êxito.

1. Verifique o status do serviço do IoT Edge.

   ```powershell
   Get-Service iotedge
   ```

2. Se você precisar solucionar problemas do serviço, recupere os logs de serviço.

   ```powershell
   # Displays logs from today, newest at the bottom.

   Get-WinEvent -ea SilentlyContinue `
    -FilterHashtable @{ProviderName= "iotedged";
      LogName = "application"; StartTime = [datetime]::Today} |
    select TimeCreated, Message |
    sort-object @{Expression="TimeCreated";Descending=$false} |
    format-table -autosize -wrap
   ```

3. Exiba todos os módulos em execução no seu dispositivo IoT Edge. Como o serviço acabou de ser iniciado pela primeira vez, você só verá o módulo **edgeAgent** em execução. O módulo edgeAgent é executado por padrão e ajuda a instalar e iniciar quaisquer módulos adicionais que você implantar em seu dispositivo.

   ```powershell
   iotedge list
   ```

   ![Exibir um módulo no dispositivo](./media/quickstart/iotedge-list-1.png)

Seu dispositivo IoT Edge agora está configurado. Ele está pronto para executar os módulos implantados na nuvem.

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Registrar um dispositivo](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Exibir os dados gerados

Neste guia de início rápido, você criou um novo dispositivo IoT Edge e instalou o tempo de execução de IoT Edge nele. Em seguida, você usou o Portal do Azure para enviar por push um módulo do IoT Edge para ser executado no dispositivo sem precisar fazer alterações no próprio dispositivo. Nesse caso, o módulo enviado por push cria dados de ambiente que podem ser usados para os tutoriais.

Confirme se o módulo implantado da nuvem está em execução no seu dispositivo IoT Edge.

```powershell
iotedge list
```

   ![Exibir três módulos no seu dispositivo](./media/quickstart/iotedge-list-2.png)

Exiba as mensagens que estão sendo enviadas do módulo tempSensor para a nuvem.

```powershell
iotedge logs tempSensor -f
```

  ![Exibir os dados do seu módulo](./media/quickstart/iotedge-logs.png)

Você também pode exibir as mensagens que são recebidas pelo seu Hub IoT usando a [extensão Kit de Ferramentas do Azure IoT para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Limpar recursos

Se você deseja prosseguir para os tutoriais do IoT Edge, pode usar o dispositivo registrado e configurado neste guia de início rápido. Caso contrário, é possível excluir os recursos do Azure que você criou e remover o tempo de execução do IoT Edge do seu dispositivo.

### <a name="delete-azure-resources"></a>Excluir recursos do Azure

Se você tiver criado a sua máquina virtual e o Hub IoT em um novo grupo de recursos, é possível excluir esse grupo e todos os recursos associados. Se houver alguma coisa dentro desse grupo de recursos que você deseje manter, então exclua somente os recursos específicos que você deseja apagar.

Remova o grupo **IoTEdgeResources**.

   ```azurecli-interactive
   az group delete --name IoTEdgeResources
   ```

### <a name="remove-the-iot-edge-runtime"></a>Remover o tempo de execução do IoT Edge

Se você planeja usar o dispositivo IoT Edge para testes futuros, mas deseja impedir o módulo tempSensor de enviar dados para o hub IoT quando não estiver em uso, utilize o comando a seguir para interromper o serviço IoT Edge.

   ```powershell
   Stop-Service iotedge -NoWait
   ```

Você pode reiniciar o serviço quando você estiver pronto para começar a testar novamente

   ```powershell
   Start-Service iotedge
   ```

Se você deseja remover as instalações do dispositivo, use os comandos a seguir.  

Remova o tempo de execução do IoT Edge.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Uninstall-SecurityDaemon
   ```

Quando o tempo de execução do IoT Edge for removido, os contêineres criados por ele são interrompidos, mas ainda existem no seu dispositivo. Visualizar todos os contêineres.

   ```powershell
   docker ps -a
   ```

Exclua os contêineres que foram criados no seu dispositivo pelo tempo de execução do IoT Edge. Altere o nome do contêiner tempSensor se você deu um outro nome para ele.

   ```powershell
   docker rm -f tempSensor
   docker rm -f edgeHub
   docker rm -f edgeAgent
   ```
   
## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo de teste que gera dados brutos sobre seu ambiente.

Você está pronto para prosseguir para um dos outros tutoriais para saber como o Azure IoT Edge pode ajudá-lo a transformar esses dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Filtrar dados de sensor usando uma função do Azure](tutorial-deploy-function.md)
