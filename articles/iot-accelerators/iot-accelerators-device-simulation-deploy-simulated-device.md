---
title: IoT implanta dispositivos simulados personalizados - Azure | Microsoft Docs
description: Este guia de instruções mostra como implantar dispositivos simulados personalizados no acelerador de solução de Simulação de Dispositivo.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/14/2018
ms.topic: conceptual
ms.openlocfilehash: e51d0330c3ed804bff8cdb06265bb8c39141733f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346755"
---
# <a name="deploy-a-new-simulated-device"></a>Implantar um novo dispositivo simulado

Ambos os aceleradores de solução de Simulação de Dispositivo e Monitoramento Remoto permitem definir seus próprios dispositivos simulados. Este artigo mostra como implantar um tipo de dispositivo de resfriador personalizado e um novo tipo de dispositivo de lâmpada no acelerador de solução de Simulação de Dispositivo.

As etapas neste artigo assumem que você concluiu o guia de instruções [Criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md) e tem os arquivos que definem o resfriador e novos tipos de dispositivos de lâmpada personalizados.

As etapas deste guia de instruções mostram como:

1. Use o SSH para acessar o sistema de arquivos da máquina virtual que hospeda o acelerador de solução de Simulação de Dispositivo.

1. Configure o Docker para carregar os modelos de dispositivos de um local fora do contêiner do Docker.

1. Execute uma simulação usando arquivos de modelo de dispositivo personalizados.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Para concluir as etapas neste guia de instruções, é necessário ter uma assinatura ativa do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, é necessário ter:

- Uma instância implantada do [Acelerador de solução de Simulação de Dispositivo](https://www.azureiotsolutions.com/Accelerators#solutions/types/DS).
- Um shell de **bash** para executar os comandos `ssh` e `scp`. No Windows, uma maneira fácil de instalar o **bash** é instalar o [git](https://git-scm.com/download/win).
- Os arquivos de modelo de dispositivo personalizado, como os descritos em [Criar e testar um novo dispositivo simulado](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configurar o Docker

Nesta seção, você configura o Docker para carregar os arquivos de modelo de dispositivo da pasta **/tmp/devicemodels** na máquina virtual em vez de dentro do contêiner do Docker. Execute os comandos nesta seção em um shell de **bash** no computador local:

1. Use o SSH para conectar a máquina virtual no Azure a partir da máquina local. O comando a seguir assume que o endereço IP público da máquina virtual **vm-vikxv** é **104.41.128.108** -- substitua esse valor pelo endereço IP público da máquina virtual da seção anterior:

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Siga as instruções para entrar na máquina virtual com a senha que você definiu na seção anterior.

1. Configure o serviço de simulação de dispositivo para carregar os modelos do dispositivo de fora do contêiner. Primeiro, abra o arquivo de configuração do Docker:

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Localize as configurações do contêiner **devicesimulation** e edite a configuração de **volumes**, conforme mostrado no trecho de código a seguir:

    ```yml
    # To mount custom device models, upload the files to the VM, edit and uncomment the following line:
          - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    # To mount a custom service configuration, create a custom file, edit and uncomment the following line:
    #     - /app/custom-device-simulation-appsettings.ini:/app/webservice/appsettings.ini:ro
    ```

    Salve as alterações.

1. Crie as pastas para armazenar os arquivos de script e JSON:

    ```sh
    sudo mkdir -p /tmp/devicemodels/scripts
    ```

    Mantenha a janela de **bash** com a sessão SSH aberta.

1. Copie os arquivos de modelo de dispositivo personalizado na máquina virtual. Execute esse comando em outro shell de **bash** na máquina em que você criou os modelos de dispositivo personalizado. Primeiro, navegue até a pasta local que contém os arquivos JSON do modelo do dispositivo. Os seguintes comandos assumem que o endereço IP público da máquina virtual é **104.41.128.108** -- substitua esse valor pelo endereço IP público da máquina virtual. Quando solicitado, insira a senha da máquina virtual:

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Reinicie o contêiner do Docker de simulação de dispositivo para usar os novos modelos de dispositivo. Execute os seguintes comandos no shell de **bash** com a sessão SSH aberta para a máquina virtual:

    ```sh
    sudo /app/start.sh
    ```

    Se você quiser ver o status dos contêineres do Docker em execução e as IDs do contêiner, use o comando a seguir:

    ```sh
    docker ps
    ```

    Se você quiser ver o log do contêiner de simulação de dispositivo, execute o seguinte comando. Substitua a ID do contêiner pela ID do contêiner de simulação de dispositivo:

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Executar simulação

Agora, é possível executar uma simulação usando os modelos de dispositivo personalizado:

1. Inicialize a interface do usuário da Web de Simulação de Dispositivo dos [Aceleradores de Solução de IoT do Microsoft Azure](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Use a interface do usuário da Web para configurar e executar uma simulação usando um dos modelos de dispositivo personalizado.

1. Ao executar uma simulação, clique em **Exibir métricas do Hub IoT no portal do Azure** para monitorar a simulação. Como alternativa, é possível usar o seguinte comando da CLI do Azure para monitorar o dispositivo para tráfego de nuvem. Substitua o nome do Hub IoT pelo nome do seu hub:

    ```azurecli-interactive
    az iot hub monitor-events -n contoso-simulation9dc75
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja explorar ainda mais, deixe o acelerador de solução de simulação de dispositivo implantado.

Caso não precise mais do acelerador de solução, exclua-o da página [Soluções provisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard) selecionando-o e clicando em **Excluir solução**.

## <a name="next-steps"></a>Próximas etapas

Este guia mostrou como implantar modelos de dispositivo personalizado no acelerador de solução de Simulação de Dispositivo. A próxima etapa sugerida é saber mais sobre o [esquema de modelo de dispositivo](iot-accelerators-device-simulation-device-schema.md).
