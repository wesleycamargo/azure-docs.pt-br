---
title: Instalar o Azure IoT Edge no Windows | Microsoft Docs
description: Instruções de instalação do Azure IoT Edge no Windows 10, Windows Server e Windows IoT Core
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: e48ab075264423479e792848af522a890736a403
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65152700"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Instalar o tempo de execução do IoT Edge no Windows

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O tempo de execução pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o tempo de execução do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele. 

Para saber mais sobre o tempo de execução do IoT Edge, consulte [Reconhecer o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para instalar o tempo de execução do Azure IoT Edge em seu Windows x64 (Intel/AMD) usando contêineres do Windows do sistema.

> [!NOTE]
> Um problema conhecido do sistema operacional Windows impede que a transição para o modo de suspensão e hibernação estados de energia quando módulos do IoT Edge (processo isolado contêineres do Windows Nano Server) estiverem em execução. Esse problema afeta a duração da bateria do dispositivo.
>
> Como alternativa, use o comando `Stop-Service iotedge` para interromper todos os módulos do IoT Edge em execução antes de usar esses estados de energia. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

O uso de contêineres do Linux no sistema Windows não é uma configuração de produção com suporte ou recomendada para o Azure IoT Edge. No entanto, pode ser utilizado para fins de desenvolvimento e teste. Para obter mais informações, consulte [Use IoT Edge no Windows para executar contêineres do Linux](how-to-install-iot-edge-windows-with-linux.md).

Para obter informações sobre o que está incluído na versão mais recente do IoT Edge, consulte [do Azure IoT Edge libera](https://github.com/Azure/azure-iotedge/releases).

## <a name="prerequisites"></a>Pré-requisitos

Use esta seção para verificar se o dispositivo Windows pode dar suporte ao IoT Edge e prepará-lo para um mecanismo de contêiner antes da instalação. 

### <a name="supported-windows-versions"></a>Versões do Windows com suporte

Para cenários de desenvolvimento e teste, o Azure IoT Edge com contêineres do Windows pode ser instalado em qualquer versão do Windows 10 ou Windows Server de 2019 (build 17763) que suporta o recurso de contêineres. Para obter informações sobre quais sistemas operacionais têm suporte atualmente para cenários de produção, consulte [sistemas com suporte do Azure IoT Edge](support.md#operating-systems). 

### <a name="prepare-for-a-container-engine"></a>Preparar para um mecanismo de contêiner 

O Azure IoT Edge depende de um mecanismo de contêiner [compatível com OCI](https://www.opencontainers.org/). Para cenários de produção, use o mecanismo Moby incluído no script de instalação para executar contêineres do Windows no dispositivo Windows. 

## <a name="install-iot-edge-on-a-new-device"></a>Instalar o IoT Edge em um novo dispositivo

>[!NOTE]
>Os pacotes de software do Azure IoT Edge estão sujeitos aos termos de licença localizados nos pacotes (no diretório LICENSE). Leia os termos de licença antes de utilizar o pacote. A instalação e o uso do pacote constitui a aceitação desses termos. Se você não concorda com os termos de licença, não utilize o pacote.

Um script do PowerShell baixa e instala o daemon de segurança do Azure IoT Edge. Em seguida, o daemon de segurança inicia o primeiro dos dois módulos de tempo de execução, o agente do IoT Edge, que permite implantações remotas de outros módulos. 

Quando você instala o tempo de execução do IoT Edge pela primeira vez em um dispositivo, é necessário provisionar o dispositivo com uma identidade de um hub IoT. Um único dispositivo IoT Edge pode ser provisionado manualmente usando uma cadeia de conexão do dispositivo fornecida pelo IoT Hub. Ou, você também pode usar o Serviço de Provisionamento de Dispositivos para provisionar dispositivos automaticamente, o que é útil quando há muitos dispositivos para configurar. Dependendo de sua escolha de provisionamento, escolha o script de instalação apropriado. 

As seções a seguir descrevem os casos de uso comuns e os parâmetros do script de instalação do IoT Edge em um novo dispositivo. 

### <a name="option-1-install-and-manually-provision"></a>Opção 1: Instalar e provisionar manualmente

Nessa primeira opção, você deve fornecer um **cadeia de conexão do dispositivo** geradas pelo IoT Hub para provisionar o dispositivo. 

Este exemplo demonstra uma instalação manual com contêineres do Windows:

1. Se você ainda não fez isso, registre um novo dispositivo IoT Edge e recuperar o **cadeia de conexão do dispositivo**. Copie a cadeia de conexão para usar posteriormente nesta seção. Você pode concluir esta etapa usando as seguintes ferramentas:

   * [Portal do Azure](how-to-register-device-portal.md)
   * [CLI do Azure](how-to-register-device-cli.md)
   * [Visual Studio Code](how-to-register-device-vscode.md)

2. Execute o PowerShell como administrador.

3. O **IoTEdge implantar** comando verifica seu computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução moby e o tempo de execução do IoT Edge. O comando assumirá como padrão o uso de contêineres do Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste ponto, dispositivos de IoT Core poderá ser reiniciado automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar que você reinicie. Nesse caso, reinicie seu dispositivo agora. Depois que o dispositivo está pronto, execute novamente o PowerShell como administrador.

5. O **IoTEdge Initialize** comando configura o tempo de execução do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. Quando solicitado, forneça a cadeia de caracteres de conexão de dispositivo que você recuperou na etapa 1. A cadeia de caracteres de conexão do dispositivo associa o dispositivo físico com uma ID de dispositivo no IoT Hub. 

   A cadeia de caracteres de conexão do dispositivo recebe o seguinte formato e não deve incluir as aspas: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. Use as etapas em [verificar a instalação bem-sucedida](#verify-successful-installation) para verificar o status do IoT Edge em seu dispositivo. 

Ao instalar e provisionar um dispositivo manualmente, é possível usar parâmetros adicionais para modificar a instalação, incluindo:
* Tráfego direto para passar por um servidor proxy
* Apontar o instalador para um diretório offline
* Declarar uma imagem de contêiner específica do agente e fornecer credenciais se estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, pule para saber mais sobre [todos os parâmetros de instalação](#all-installation-parameters).

### <a name="option-2-install-and-automatically-provision"></a>Opção 2: Instalar e provisionar automaticamente

Nesta segunda opção, você provisiona o dispositivo usando o Serviço de Provisionamento de Dispositivos no Hub IoT. Forneça a **ID do Escopo** de uma instância de Serviço de Provisionamento de Dispositivos e a **ID de Registro** do seu dispositivo.

O exemplo a seguir demonstra uma instalação automática com contêineres do Windows:

1. Siga as etapas em [Crie e provisione um dispositivo TPM Edge simulado no Windows](how-to-auto-provision-simulated-device-windows.md) para configurar o Serviço de Provisionamento de Dispositivo e recuperar seu **ID do escopo**, simular um dispositivo TPM e recuperar seu **Registro ID** e crie uma inscrição individual. Depois que o dispositivo é registrado no hub IoT, continue com estas etapas de instalação.  

   >[!TIP]
   >Mantenha a janela que está executando o simulador do TPM aberta durante a instalação e teste. 

2. Execute o PowerShell como administrador.

3. O **IoTEdge implantar** comando verifica seu computador Windows está em uma versão com suporte, ativa o recurso de contêineres e, em seguida, baixa o tempo de execução moby e o tempo de execução do IoT Edge. O comando assumirá como padrão o uso de contêineres do Windows. 

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. Neste ponto, dispositivos de IoT Core poderá ser reiniciado automaticamente. Outros dispositivos Windows 10 ou Windows Server podem solicitar que você reinicie. Nesse caso, reinicie seu dispositivo agora. Depois que o dispositivo está pronto, execute novamente o PowerShell como administrador.

6. O **IoTEdge Initialize** comando configura o tempo de execução do IoT Edge em seu computador. O comando assumirá como padrão o provisionamento manual com contêineres do Windows. Use o `-Dps` sinalizador para usar o serviço de provisionamento de dispositivos em vez de provisionamento manual.

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps
   ```

7. Quando solicitado, forneça a ID do escopo do seu serviço de provisionamento de dispositivos e a ID de registro do dispositivo, o que você deve ter recuperado na etapa 1.

8. Use as etapas em [verificar a instalação bem-sucedida](#verify-successful-installation) para verificar o status do IoT Edge em seu dispositivo. 

Ao instalar e provisionar um dispositivo manualmente, é possível usar parâmetros adicionais para modificar a instalação, incluindo:
* Tráfego direto para passar por um servidor proxy
* Apontar o instalador para um diretório offline
* Declarar uma imagem de contêiner específica do agente e fornecer credenciais se estiver em um registro privado

Para obter mais informações sobre essas opções de instalação, continue lendo este artigo ou ignore para saber mais sobre [Todos os parâmetros de instalação](#all-installation-parameters).

## <a name="offline-installation"></a>Instalação offline

Durante a instalação do dois arquivos são baixados: 
* Cab do Microsoft Azure IoT Edge, que contém o daemon de segurança do IoT Edge (iotedged), o mecanismo do contêiner Moby e Moby CLI.
* MSI do pacote redistribuível do Visual C++ (tempo de execução de VC)

Você pode baixar um ou ambos os arquivos antes do tempo para o dispositivo e o script de instalação do ponto no diretório que contém os arquivos. O instalador primeiro verifica o diretório e, em seguida, baixa apenas os componentes que não são localizados. Se todos os arquivos ficam disponíveis offline, você poderá instalar com nenhuma conexão de internet. Você também pode usar esse recurso para instalar uma versão específica dos componentes.  

Para os arquivos de instalação mais recente do IoT Edge, juntamente com versões anteriores, consulte [do Azure IoT Edge libera](https://github.com/Azure/azure-iotedge/releases).

Para instalar com componentes offline, use o `-OfflineInstallationPath` parâmetro como parte da implantação-IoTEdge de comando e forneça o caminho absoluto para o diretório de arquivos. Por exemplo,

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

Você também pode usar o parâmetro de caminho de instalação offline com o comando Update-IoTEdge, apresentado mais adiante neste artigo. 

## <a name="verify-successful-installation"></a>Verifique se a instalação bem-sucedida

Verifique o status do serviço do IoT Edge. Ele deverá estar listada como em execução.  

```powershell
Get-Service iotedge
```

Examine os logs de serviço pelos últimos 5 minutos usando. Se você acabou de instalar o tempo de execução do IoT Edge, você poderá ver uma lista de erros do que o tempo entre execução **Deploy IoTEdge** e **Initialize IoTEdge**. Esses erros são esperados, como o serviço está tentando iniciar antes que está sendo configurado. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Módulos de execução da lista. Após uma nova instalação, o único módulo que você verá é a execução **edgeAgent**. Depois que você [implantar módulos do IoT Edge](how-to-deploy-modules-portal.md), você verá que outras pessoas. 

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>Gerenciar contêineres do módulo

O serviço do IoT Edge requer um mecanismo de contêiner em execução no seu dispositivo. Quando você implanta um módulo em um dispositivo, o tempo de execução do IoT Edge usa o mecanismo de contêiner para puxar a imagem de contêiner de um registro na nuvem. O serviço do IoT Edge permite que você interaja com seus módulos e recuperar os logs, mas, às vezes, você talvez queira usar o mecanismo de contêiner para interagir com o contêiner em si. 

Para obter mais informações sobre os conceitos de módulo, consulte [módulos de compreender o Azure IoT Edge](iot-edge-modules.md). 

Se você estiver executando contêineres do Windows no seu dispositivo Windows IoT Edge, a instalação do IoT Edge incluído o mecanismo de contêiner Moby. O mecanismo Moby foi baseado nos padrões do mesmos que o Docker e foi projetado para ser executado em paralelo no mesmo computador como área de trabalho do Docker. Por esse motivo, se você quiser os contêineres de destino gerenciados pelo mecanismo Moby, você precisa especificamente que o mecanismo em vez de Docker de destino. 

Por exemplo, para listar todas as imagens do Docker, use o seguinte comando:

```powershell
docker images
```

Para listar todas as imagens de Moby, modifique o mesmo comando com um ponteiro para o mecanismo de Moby: 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

O mecanismo de URI é listado na saída do script de instalação, ou você pode encontrá-lo na seção de configurações de tempo de execução do contêiner para o arquivo config. YAML. 

![moby_runtime uri no config. YAML](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

Para obter mais informações sobre os comandos que você pode usar para interagir com contêineres e imagens em execução em seu dispositivo, consulte [interfaces de linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/docker/).

## <a name="update-an-existing-installation"></a>Atualizar uma instalação existente

Se você já instalou o tempo de execução do IoT Edge em um dispositivo antes e provisionou com uma identidade do IoT Hub, você pode atualizar o tempo de execução sem precisar reinserir suas informações de dispositivo. 

Para obter mais informações, consulte [Atualize o daemon de segurança e o tempo de execução do IoT Edge](how-to-update-iot-edge.md).

Este exemplo mostra uma instalação que aponta para um arquivo de configuração existente e usa contêineres do Windows: 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Update-IoTEdge
```

Quando você atualiza do IoT Edge, você pode usar parâmetros adicionais para modificar a atualização, incluindo:
* Direcionar o tráfego para passar por um servidor proxy, ou
* Apontar o instalador para um diretório offline 
* Reiniciar sem um aviso se necessário

Você não pode declarar uma imagem de contêiner do agente do IoT Edge com os parâmetros do script porque essa informação já está definida no arquivo de configuração da instalação anterior. Se você quiser modificar a imagem de contêiner do agente, faça isso no arquivo config.yaml. 

Para obter mais informações sobre essas opções de atualização, use o comando `Get-Help Update-IoTEdge -full` ou consulte [todos os parâmetros de instalação](#all-installation-parameters).

## <a name="uninstall-iot-edge"></a>Desinstalar o IoT Edge

Se você quiser remover a instalação do IoT Edge do dispositivo Windows, use o seguinte comando em uma janela administrativa do PowerShell. Esse comando remove o tempo de execução do IoT Edge, juntamente com a configuração existente e os dados do mecanismo Moby. 

```powershell
Uninstall-IoTEdge
```

O comando de desinstalação IoTEdge não funciona no Windows IoT Core. Para remover o IoT Edge de dispositivos do Windows IoT Core, você precisará reimplantar sua imagem do Windows IoT Core. 

Para obter mais informações sobre opções de desinstalação, use o comando `Get-Help Uninstall-IoTEdge -full`. 

## <a name="all-installation-parameters"></a>Todos os parâmetros de instalação

As seções anteriores introduziram cenários comuns de instalação com exemplos de como usar parâmetros para modificar o script de instalação. Esta seção fornece as tabelas de referência dos parâmetros comuns usados para instalar, atualizar ou desinstalar o IoT Edge. 

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

O comando implantar IoTEdge baixa e implanta o Daemon de segurança do IoT Edge e suas dependências. O comando de implantação aceita esses parâmetros comuns, entre outros. Para obter a lista completa, use o comando `Get-Help Deploy-IoTEdge -full`.  

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema de operacional do contêiner for especificado, o Windows é o valor padrão.<br><br>Para contêineres do Windows, o IoT Edge usa o mecanismo de contêiner de moby incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se esse parâmetro for incluído, o instalador verificará a pasta listada para o cab do IoT Edge e os arquivos MSI do tempo de execução de VC necessários para instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estão no diretório, você pode instalar o IoT Edge sem uma conexão de internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeeded** | Nenhum | Esse sinalizador permite que o script de implantação ao reiniciar a máquina sem aviso, se necessário. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando de inicialização IoTEdge configura do IoT Edge com sua cadeia de conexão do dispositivo e os detalhes operacionais. Muitas das informações geradas por este comando, em seguida, é armazenada no arquivo iotedge\config.yaml. O comando de inicialização aceita esses parâmetros comuns, entre outros. Para obter a lista completa, use o comando `Get-Help Initialize-IoTEdge -full`. 

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Manual** | Nenhum | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, o manual é o valor padrão.<br><br>Declara que você fornecerá uma cadeia de conexão de dispositivo para provisionar o dispositivo manualmente |
| **Dps** | Nenhum | **Parâmetro de opção**. Se nenhum tipo de provisionamento for especificado, o manual é o valor padrão.<br><br>Declara que você fornecerá uma ID do escopo do DPS (Serviço de Provisionamento de Dispositivos) e a ID de Registro do dispositivo para provisionar por meio do DPS.  |
| **DeviceConnectionString** | Uma cadeia de conexão de um dispositivo do IoT Edge registrado em um Hub IoT, entre aspas simples | **Necessário** para instalação manual. Se você não fornecer uma cadeia de conexão nos parâmetros de script, durante a instalação será solicitado que você forneça uma. |
| **ScopeId** | Uma ID do escopo de uma instância do Serviço de Provisionamento de Dispositivos associada ao Hub IoT. | **Necessário** para instalação de DPS. Se você não fornecer uma ID do escopo nos parâmetros do script, durante a instalação será solicitado que você forneça uma. |
| **RegistrationId** | Uma ID de registro gerada pelo dispositivo | **Necessário** para instalação de DPS. Se você não fornecer uma ID de registro nos parâmetros de script, durante a instalação será solicitado que você forneça uma. |
| **ContainerOs** | **Windows** ou **Linux** | Se nenhum sistema de operacional do contêiner for especificado, o Windows é o valor padrão.<br><br>Para contêineres do Windows, o IoT Edge usa o mecanismo de contêiner de moby incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **AgentImage** | URI de imagem do agente do IoT Edge | Por padrão, uma nova instalação do IoT Edge usa a marca sem interrupção mais recente para a imagem do agente do IoT Edge. Use esse parâmetro para definir uma marca específica para a versão da imagem ou para fornecer a própria imagem de agente. Para obter mais informações, consulte [Reconhecer as marcas do IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de Usuário** | Nome de usuário do registro de contêiner | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça um nome de usuário com acesso ao registro. |
| **Senha** | Cadeia de caracteres de senha de segurança | Use esse parâmetro somente se você definir o parâmetro -AgentImage como um contêiner em um registro privado. Forneça a senha para acessar o registro. | 

### <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** ou **Linux** | Se não houver contêiner que sistema operacional for especificado, o Windows é o valor padrão. Para contêineres do Windows, um mecanismo de contêiner será incluído na instalação. Para contêineres do Linux, será necessário instalar um mecanismo de contêiner antes de iniciar a instalação. |
| **Proxy** | URL do Proxy | Inclua esse parâmetro se o dispositivo precisar passar por um servidor proxy para acessar a Internet. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **InvokeWebRequestParameters** | Tabela de hash de parâmetros e valores | Durante a instalação, várias solicitações da Web serão feitas. Use esse campo para definir parâmetros para essas solicitações da Web. Esse parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [Configurar um dispositivo IoT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se esse parâmetro for incluído, o instalador verificará a pasta listada para o cab do IoT Edge e os arquivos MSI do tempo de execução de VC necessários para instalação. Todos os arquivos não encontrados no diretório são baixados. Se ambos os arquivos estão no diretório, você pode instalar o IoT Edge sem uma conexão de internet. Você também pode usar esse parâmetro para usar uma versão específica. |
| **RestartIfNeeded** | Nenhum | Esse sinalizador permite que o script de implantação ao reiniciar a máquina sem aviso, se necessário. |


### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parâmetro | Valores aceitos | Comentários |
| --------- | --------------- | -------- |
| **Force** | Nenhum | Este sinalizador de força a desinstalação caso a tentativa anterior de desinstalação foi bem-sucedida. 
| **RestartIfNeeded** | Nenhum | Esse sinalizador permite que o script de desinstalação reiniciar a máquina sem aviso, se necessário. |


## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com a instalação correta do IoT Edge, consulte a página de [solução de problemas](troubleshoot.md).

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o daemon de segurança e o tempo de execução do IoT Edge](how-to-update-iot-edge.md).
