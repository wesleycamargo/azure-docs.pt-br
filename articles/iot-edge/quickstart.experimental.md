---
title: Simular o Azure IoT Edge no Windows | Microsoft Docs
description: Instalar o tempo de execução do Azure IoT Edge em um dispositivo simulado no Windows e implantar seu primeiro módulo
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: elioda
ms.date: 06/08/2018
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5863a8edbb20b2b0c231834259f1bb7b0423a8f6
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37033470"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-from-the-azure-portal-to-a-windows-device---preview"></a>Início rápido: implantar seu primeiro módulo IoT Edge do Portal do Azure para um dispositivo Windows – versão prévia

O Azure IoT Edge permite executar análise e processamento de dados em seus dispositivos em vez de enviar por push todos os dados para a nuvem. Os tutoriais do IoT Edge demonstram como implantar diferentes tipos de módulos, mas primeiro você precisa de um dispositivo para teste. 

Neste guia de início rápido, você aprende a:

1. Crie um Hub IoT
2. Registrar um dispositivo IoT Edge
3. Iniciar o tempo de execução do IoT Edge
4. Implantar um módulo

![Arquitetura do tutorial][2]

O módulo implantado neste guia de início rápido é um sensor simulado que gera dados de temperatura, umidade e pressão. Os outros tutoriais do Azure IoT Edge se baseiam no trabalho feito aqui com a implantação de módulos que analisam os dados simulados para obter informações de negócios. 

>[!NOTE]
>O tempo de execução do IoT Edge no Windows está em [versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita][lnk-account] antes de começar.

## <a name="prerequisites"></a>pré-requisitos

Este início rápido pressupõe que você esteja usando um computador ou uma máquina virtual que execute o Windows para simular um dispositivo de IoT. Caso esteja executando o Windows em uma máquina virtual, habilite a [virtualização aninhada][lnk-nested] e aloque pelo menos 2 GB de memória. 

Tenha os seguintes pré-requisitos prontos no computador que você está usando para um dispositivo IoT Edge:

1. Verifique se você está usando uma versão do Windows que tem suporte:
   * Windows 10 ou mais recente
   * Windows Server 2016 ou mais recente
2. Instale o [Docker para Windows][lnk-docker] e verifique se ele está em execução.
3. Configurar o Docker para usar [contêineres do Linux](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Comece o início rápido criando o Hub IoT no portal do Azure.
![Criar o Hub IoT][3]

[!INCLUDE [iot-hub-create-hub](../../includes/iot-hub-create-hub.md)]

## <a name="register-an-iot-edge-device"></a>Registrar um dispositivo IoT Edge

Registre um dispositivo IoT Edge no Hub IoT recém-criado.
![Registrar um dispositivo][4]

[!INCLUDE [iot-edge-register-device](../../includes/iot-edge-register-device.md)]

## <a name="install-and-start-the-iot-edge-runtime"></a>Instalar e iniciar o tempo de execução do IoT Edge

Instale e inicie o tempo de execução do Azure IoT Edge no dispositivo IoT Edge. 
![Registrar um dispositivo][5]

O tempo de execução do IoT Edge é implantado em todos os dispositivos IoT Edge. Ele tem três componentes. O **daemon de segurança do IoT Edge** é iniciado sempre que um dispositivo Edge é iniciado e inicializa o dispositivo inicializando o agente do IoT Edge. O **agente do IoT Edge** facilita a implantação e o monitoramento de módulos no dispositivo IoT Edge, incluindo o hub do IoT Edge. O **hub IoT Edge** gerencia a comunicação entre os módulos no dispositivo IoT Edge e entre o dispositivo e o Hub IoT. 

>[!NOTE]
>As etapas de instalação nesta seção são manuais por agora, enquanto um script de instalação está sendo desenvolvido. 

As instruções nesta seção configuram o tempo de execução do IoT Edge com contêineres do Linux. Se você deseja usar os contêineres do Windows, consulte [Instalar o tempo de execução do Azure IoT Edge no Windows para usar com contêineres do Windows](how-to-install-iot-edge-windows-with-windows.md).

### <a name="download-and-install-the-iot-edge-service"></a>Baixe e instale o serviço do IoT Edge

1. No seu dispositivo IoT Edge, execute o PowerShell como administrador.

2. Baixe o pacote de serviço do IoT Edge.

   ```powershell
   Invoke-WebRequest https://conteng.blob.core.windows.net/iotedged/iotedge.zip -o .\iotedge.zip
   Expand-Archive .\iotedge.zip C:\ProgramData\iotedge -f
   $env:Path += ";C:\ProgramData\iotedge"
   SETX /M PATH "$env:Path"
   ```

3. Instalar e iniciar o tempo de execução do IoT Edge.

   ```powershell
   New-Service -Name "iotedge" -BinaryPathName "C:\ProgramData\iotedge\iotedged.exe -c C:\ProgramData\iotedge\config.yaml"
   Start-Service iotedge
   ```

4. Adicione exceções de firewall para as portas usadas pelo serviço de IoT Edge.

   ```powershell
   New-NetFirewallRule -DisplayName "iotedged allow inbound 15580,15581" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 15580-15581 -Program "C:\programdata\iotedge\iotedged.exe" -InterfaceType Any
   ```

5. Crie um novo arquivo chamado **iotedge.reg** e abra-o com um editor de texto. 

6. Adicione o seguinte conteúdo e salve o arquivo. 

   ```input
   Windows Registry Editor Version 5.00
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
   "CustomSource"=dword:00000001
   "EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
   "TypesSupported"=dword:00000007
   ```

7. Navegue até o arquivo no Explorador de Arquivos e clique duas vezes para importar as alterações no Registro do Windows. 

### <a name="configure-the-iot-edge-runtime"></a>Configurar o tempo de execução do IoT Edge 

Configure o tempo de execução com sua cadeia de conexão do dispositivo IoT Edge copiada quando você registrou um novo dispositivo. Em seguida, configure a rede de tempo de execução. 

1. Abra o arquivo de configuração do IoT Edge, que está localizado em `C:\ProgramData\iotedge\config.yaml`. Este arquivo é protegido, então execute um editor de texto como o Bloco de notas como administrador, em seguida, use o editor para abrir o arquivo. 

2. Localize a seção de **provisionamento** e atualize o valor de **device_connection_string** com a cadeia de caracteres que você copiou de seu detalhes do dispositivo IoT Edge. 

3. Na janela do PowerShell de administrador, recupere o nome do host para seu dispositivo IoT Edge e copie a saída. 

   ```powershell
   hostname
   ```

4. No arquivo de configuração, localize a seção **Nome de host do dispositivo Edge**. Atualizar o valor do **nome do host** com o nome do host que você copiou do PowerShell.

5. Na janela do PowerShell de administrador, recupere o endereço IP para seu dispositivo IoT Edge. 

   ```powershell
   ipconfig
   ```

6. Copie o valor para **Endereço IPv4** na seção **vEthernet (DockerNAT)** da saída. 

7. Crie uma variável de ambiente chamada **IOTEDGE_HOST**, substituindo *\<ip_address\>* pelo endereço IP para seu dispositivo IoT Edge. 

   ```powershell
   [Environment]::SetEnvironmentVariable("IOTEDGE_HOST", "http://<ip_address>:15580")
   ```

8. No arquivo `config.yaml`, localize a seção **Configurações de conexão**. Atualize os valores de **management_uri** e **workload_uri** com o endereço IP no lugar de **\<GATEWAY_ADDRESS\>** e das portas que você abriu na seção anterior. 

   ```yaml
   connect: 
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

9. Localize a seção **Escutar configurações** e adicione os mesmos valores para **management_uri** e **workload_uri**. 

   ```yaml
   listen:
     management_uri: "http://<GATEWAY_ADDRESS>:15580"
     workload_uri: "http://<GATEWAY_ADDRESS>:15581"
   ```

10. Localize a seção **Configurações de tempo de execução de contêiner Moby**. Remova a marca de comentário da linha **network** e verifique se o valor está definido como `nat`.

   ```yaml
   moby_runtime:
     uri: "npipe://./pipe/docker_engine"
     network: "nat"
   ```

11. Salve o arquivo de configuração. 

12. No PowerShell, reinicie o serviço do IoT Edge.

   ```powershell
   Stop-Service iotedge
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
  select TimeCreated, Message | Sort-Object -Descending
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

Quando não precisar mais do Hub IoT criado, você pode usar o comando portal do Azure para remover o recurso e quaisquer dispositivos associados a ele. Navegue até a página de visão geral do seu Hub IoT e selecione **Excluir**. 

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo dispositivo IoT Edge e usou a interface de nuvem do Azure IoT Edge para implantar código no dispositivo. Agora, você tem um dispositivo de teste que gera dados brutos sobre seu ambiente. 

Você está pronto para prosseguir para um dos outros tutoriais para saber como o Azure IoT Edge pode ajudá-lo a transformar esses dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Filtrar dados de sensor usando uma função do Azure](tutorial-deploy-function.md)

<!-- Images -->
[2]: ./media/quickstart/install-edge-full.png
[3]: ./media/quickstart/create-iot-hub.png
[4]: ./media/quickstart/register-device.png
[5]: ./media/quickstart/start-runtime.png
[6]: ./media/quickstart/deploy-module.png

<!-- Links -->
[lnk-nested]: https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization
[lnk-docker]: https://docs.docker.com/docker-for-windows/install/ 
[lnk-python]: https://www.python.org/downloads/
[lnk-docker-containers]: https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10#2-switch-to-windows-containers
[lnk-iothub-explorer]: https://github.com/azure/iothub-explorer
[lnk-install-iotcore]: how-to-install-iot-core.md
[lnk-account]: https://azure.microsoft.com/free
