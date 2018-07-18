---
title: Início rápido ao Azure IoT Edge + Windows | Microsoft Docs
description: Experimente o Azure IoT Edge executando a análise em um dispositivo de borda simulado
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/24/2018
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 11b2fccf3c02555f50f48252f2cd9968c9ec90d7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38632877"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Início rápido: implantar seu primeiro módulo IoT Edge do Portal do Azure para um dispositivo Windows – versão prévia

Neste guia de início rápido, use a interface de nuvem do Azure IoT Edge para implantar remotamente um código pré-compilado em um dispositivo IoT Edge. Para realizar essa tarefa, primeiro use seu dispositivo Windows para simular um dispositivo IoT Edge, depois você pode implantar um módulo a ele.

Neste guia de início rápido, você aprende a:

1. Crie um Hub IoT.
2. Registrar um dispositivo IoT Edge em seu Hub IoT.
3. Instale e inicie o tempo de execução do IoT Edge no dispositivo.
4. Implante remotamente um módulo em um dispositivo IoT Edge e envie a telemetria para IoT Hub.

![Arquitetura do tutorial][2]

O módulo implantado neste guia de início rápido é um sensor simulado que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados simulados para obter informações de negócios. 

>[!NOTE]
>O tempo de execução do IoT Edge no Windows está em [versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita][lnk-account] antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este guia de início rápido transforma seu computador ou máquina virtual Windows em um dispositivo IoT Edge. Caso esteja executando o Windows em uma máquina virtual, habilite a [virtualização aninhada][lnk-nested] e aloque pelo menos 2 GB de memória. 

Tenha os seguintes pré-requisitos prontos no computador que você está usando para um dispositivo IoT Edge:

1. Verifique se você está usando uma versão do Windows que tem suporte:
   * Windows 10 ou mais recente
   * Windows Server 2016 ou mais recente
2. Instale o [Docker para Windows][lnk-docker] e verifique se ele está em execução.
3. Configurar o Docker para usar [contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você usa a CLI do Azure para concluir muitas das etapas neste guia de início rápido e o Azure IoT possui uma extensão para habilitar funcionalidades adicionais. 

Adicione a extensão do Azure IoT à instância do shell de nuvem.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Comece o início rápido criando o Hub IoT no portal do Azure.
![Criar o Hub IoT][3]

O nível gratuito do Hub IoT funciona para este guia de início rápido. Se você tiver usado o Hub IoT antes e já tiver um hub disponível criado, você pode usar esse Hub IoT. Cada assinatura pode ter somente um hub IoT gratuito. 

1. No Azure Cloud Shell, crie um grupo de recursos. O código a seguir cria um grupo de recursos chamado **TestResources** na região **Oeste dos EUA**. Ao colocar todos os recursos para os inícios rápidos e tutoriais em um grupo, você pode gerenciá-los juntos. 

   ```azurecli-interactive
   az group create --name TestResources --location westus
   ```

1. Crie um hub IoT em seu novo grupo de recursos. O código a seguir cria um hub **F1** disponível no grupo de recursos **TestResources**. Substitua *{hub_name}* por um nome exclusivo para o Hub IoT.

   ```azurecli-interactive
   az iot hub create --resource-group TestResources --name {hub_name} --sku F1 
   ```

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.
![Registrar um dispositivo][4]

Crie uma identidade de dispositivo para seu dispositivo simulado para que ele possa se comunicar com o hub IoT. Como os dispositivos IoT Edge se comportam e podem ser gerenciados diferentemente de dispositivos IoT comuns, declare esse para ser um dispositivo de IoT Edge desde o início. 

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

Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo IoT Edge. 
![Registrar um dispositivo][5]

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Tem três componentes. O **daemon de segurança do IoT Edge** é iniciado sempre que um dispositivo Edge é iniciado e inicializa o dispositivo inicializando o agente do IoT Edge. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. 

>[!NOTE]
>As etapas de instalação nesta seção são manuais por agora, enquanto um script de instalação está sendo desenvolvido. 

As instruções nesta seção configuram o tempo de execução do IoT Edge com contêineres do Linux. Se você deseja usar os contêineres do Windows, consulte [Instalar o tempo de execução do Azure IoT Edge no Windows para usar com contêineres do Windows](how-to-install-iot-edge-windows-with-windows.md).

### <a name="download-and-install-the-iot-edge-service"></a>Baixe e instale o serviço do IoT Edge

1. No seu dispositivo IoT Edge, execute o PowerShell como administrador.

2. Baixe o pacote de serviço do IoT Edge.

  ```powershell
  Invoke-WebRequest https://aka.ms/iotedged-windows-latest -o .\iotedged-windows.zip
  Expand-Archive .\iotedged-windows.zip C:\ProgramData\iotedge -f
  Move-Item c:\ProgramData\iotedge\iotedged-windows\* C:\ProgramData\iotedge\ -Force
  rmdir C:\ProgramData\iotedge\iotedged-windows
  $env:Path += ";C:\ProgramData\iotedge"
  SETX /M PATH "$env:Path"
  ```

3. Instale o vcruntime.

  ```powershell
  Invoke-WebRequest -useb https://download.microsoft.com/download/0/6/4/064F84EA-D1DB-4EAA-9A5C-CC2F0FF6A638/vc_redist.x64.exe -o vc_redist.exe
  .\vc_redist.exe /quiet /norestart
  ```

4. Instalar e iniciar o tempo de execução do IoT Edge.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

5. Adicione exceções de firewall para as portas usadas pelo serviço de IoT Edge.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

6. Crie um novo arquivo chamado **iotedge.reg** e abra-o com um editor de texto. 

7. Adicione o seguinte conteúdo e salve o arquivo. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

8. Navegue até o arquivo no Explorador de Arquivos e clique duas vezes para importar as alterações no Registro do Windows. 

### <a name="configure-the-iot-edge-runtime"></a>Configurar o tempo de execução do IoT Edge 

Configure o tempo de execução com sua cadeia de conexão do dispositivo IoT Edge copiada quando você registrou um novo dispositivo. Em seguida, configure a rede de tempo de execução. 

1. Abra o arquivo de configuração do IoT Edge, que está localizado em `C:\ProgramData\iotedge\config.yaml`. Este arquivo é protegido, então execute um editor de texto como o Bloco de notas como administrador, em seguida, use o editor para abrir o arquivo. 

2. Localize a seção de **provisionamento** e atualize o valor de **device_connection_string** com a cadeia de caracteres que você copiou de seu detalhes do dispositivo IoT Edge. 

3. Na janela do PowerShell de administrador, recupere o nome do host para seu dispositivo IoT Edge e copie a saída. 

   ```powershell
   hostname
   ```

4. No arquivo de configuração, localize a seção **Nome de host do dispositivo Edge**. Atualizar o valor do **nome do host** com o nome do host que você copiou do PowerShell.

3. Na janela do PowerShell de administrador, recupere o endereço IP para seu dispositivo IoT Edge. 

   ```powershell
   ipconfig
   ```

4. Copie o valor para **Endereço IPv4** na seção **vEthernet (DockerNAT)** da saída. 

5. Crie uma variável de ambiente chamada **IOTEDGE_HOST**, substituindo *\<ip_address\>* pelo endereço IP para seu dispositivo IoT Edge. 

  ```powershell
  [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
  ```
  
  Persista a variável de ambiente entre as reinicializações.

  ```powershell
  SETX /M IOTEDGE_HOST "http://<ip_address>:15580"
  ```


6. No arquivo `config.yaml`, localize a seção **Configurações de conexão**. Atualize os valores de **management_uri** e **workload_uri** com o endereço IP e as portas que você abriu na seção anterior. Substitua **\<GATEWAY_ADDRESS\>** pelo seu endereço IP. 

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

7. Localize a seção **Escutar configurações** e adicione os mesmos valores para **management_uri** e **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

8. Localize a seção **Configurações de tempo de execução de contêiner Moby** e verifique se o valor de **network** está definido como `nat`.

9. Salve o arquivo de configuração. 

10. No PowerShell, reinicie o serviço do IoT Edge.

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

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
    sort-object @{Expression="TimeCreated";Descending=$false}
   ```

3. Exiba todos os módulos em execução no seu dispositivo IoT Edge. Como o serviço acabou de ser iniciado pela primeira vez, você só verá o módulo **edgeAgent** em execução. O módulo edgeAgent é executado por padrão e ajuda a instalar e iniciar quaisquer módulos adicionais que você implantar em seu dispositivo. 

   ```powershell
   iotedge list
   ```

   ![Exibir um módulo no dispositivo](./media/quickstart/iotedge-list-1.png)

## <a name="deploy-a-module"></a>Implantar um módulo

Gerencie o dispositivo Azure IoT Edge na nuvem para implantar um módulo que enviará dados telemétricos ao Hub IoT.
![Registrar um dispositivo][6]

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

Você também pode exibir as mensagens que são recebidas pelo seu Hub IoT usando a [ferramenta exploradora do Hub IoT][lnk-iothub-explorer] ou a [extensão Kit de ferramentas do Azure IoT para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). 

## <a name="clean-up-resources"></a>Limpar recursos

Você pode usar o dispositivo simulado que você configurou neste guia de início rápido para testar os tutoriais de IoT Edge. Se você quiser interromper o envio dados para o Hub IoT pelo módulo tempSensor, use o seguinte comando para parar o serviço de IoT Edge e exclua os contêineres que foram criados no seu dispositivo. Quando você quiser usar seu computador como um dispositivo IoT Edge novamente, lembre-se de iniciar o serviço. 

   ```powershell
   Stop-Service iotedge -NoWait
   docker rm -f $(docker ps -aq)
   ```

Quando você não precisar mais dos recursos do Azure que você criou, você pode usar o seguinte comando para excluir o grupo de recursos que você criou e todos os recursos associados a ele:

   ```azurecli-interactive
   az group delete --name TestResources
   ```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo de teste que gera dados brutos sobre seu ambiente. 

Você está pronto para prosseguir para um dos outros tutoriais para saber como o Azure IoT Edge pode ajudá-lo a transformar esses dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Filtrar dados de sensor usando uma função do Azure](tutorial-deploy-function.md)


<!-- Images -->
[1]: ./media/quickstart/cloud-shell.png
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png


<!-- Links -->
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-account]: https://azure.microsoft.com/free
[lnk-portal]: https://portal.azure.com
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-delete]: https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az_iot_hub_delete

<!-- Anchor links -->
[anchor-register]: #register-an-iot-edge-device
