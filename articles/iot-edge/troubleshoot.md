---
title: Solucionar problemas do Azure IoT Edge | Microsoft Docs
description: Resolver problemas comuns e aprender habilidades para solução de problemas do Azure IoT Edge
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ecd19acdeba57a29a28187d42783bbf146095190
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001898"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Problemas comuns e resoluções para o Azure IoT Edge

Se você tiver problemas com o Azure IoT Edge no seu ambiente, use este artigo como um guia para a solução desses problemas. 

## <a name="standard-diagnostic-steps"></a>Etapas de diagnóstico padrão 

Quando você encontrar um problema, saiba mais sobre o estado do dispositivo do IoT Edge examinando os logs de contêiner e as mensagens que entram e saem do dispositivo. Use as ferramentas e os comandos desta seção para coletar informações. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Verifique o status do Gerenciador de Segurança do IoT Edge e seus logs:

No Linux:
- Para ver o status do Gerenciador de Segurança do IoT Edge:

   ```bash
   sudo systemctl status iotedge
   ```

- Para ver os logs do Gerenciador de Segurança do IoT Edge:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Para ver mais detalhadamente os logs do Gerenciador de Segurança do IoT Edge:

   - Edite as configurações de daemon iotedge:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Atualize as seguintes linhas:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Reinicie o daemon de segurança do IoT Edge:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

No Windows:
- Para ver o status do Gerenciador de Segurança do IoT Edge:

   ```powershell
   Get-Service iotedge
   ```

- Para ver os logs do Gerenciador de Segurança do IoT Edge:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false}
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Se o Gerenciador de segurança de IoT Edge não está em execução, verifique se o arquivo de configuração yaml

> [!WARNING]
> Arquivos YAML não podem conter guias como recuo. Use 2 espaços no lugar.

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Verifique os logs de contêiner de problemas

Depois que o Daemon de segurança de IoT Edge está em execução, examine os logs de contêineres para detectar problemas. Comece com os contêineres implantados, examine os contêineres que compõem o tempo de execução do IoT Edge: Agente do Edge e Hub do Edge. Os logs do Agente do Edge normalmente fornecem informações sobre o ciclo de vida de cada contêiner. Os logs do Hub do Edge fornecem informações sobre mensagens e roteamento. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Exibir as mensagens enviadas por meio do hub Edge

Veja as mensagens que passam pelo Hub do Edge e colete informações sobre atualizações de propriedades do dispositivo nos logs detalhados dos contêineres de tempo de execução de edgeAgent e edgeHub. Para ativar os logs detalhados nesses contêineres, defina `RuntimeLogLevel` em seu arquivo de configuração yaml. Para abrir o arquivo:

No Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

No Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Por padrão, o elemento `agent` terá a seguinte aparência:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Substitua `env: {}` por:

> [!WARNING]
> Arquivos YAML não podem conter guias como recuo. Use 2 espaços no lugar.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

Salve o arquivo e reinicie o gerenciador de segurança do IoT Edge.

Você também pode verificar as mensagens que estão sendo enviadas entre os dispositivos do Hub IoT e do IoT Edge. Veja essas mensagens usando a extensão [Kit de ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) do Visual Studio Code. Para obter instruções, consulte [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/) (Ferramenta útil ao desenvolver com o Azure IoT).

### <a name="restart-containers"></a>Reinicie os contêineres
Após investigar os logs e as mensagens para obter informações, você poderá tentar reiniciar contêineres:

```
iotedge restart <container name>
```

Reiniciar contêineres de tempo de execução do IoT Edge:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Reinicie o gerenciador de segurança do IoT Edge

Se o problema ainda é persistente, você pode tentar reiniciar o Gerenciador de segurança de IoT Edge.

No Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

No Windows:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>O Agente do Edge é interrompido após cerca de um minuto

O Agente do Edge a é iniciado e executado com êxito por cerca de um minuto e, em seguida, é interrompido. Os logs indicam que o Agente do Edge tenta se conectar ao Hub IoT com o AMQP e, aproximadamente 30 segundos mais tarde, tenta se conectar usando o AMQP com websocket. Quando isso falha, o Agente do Edge é encerrado. 

Logs do Agente do Edge de exemplo:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Causa raiz
Uma configuração de rede na rede do host está impedindo que o Agente do Edge alcance a rede. O agente tenta se conectar com AMQP (porta 5671) primeiro. Se isso falha, ele tenta websockets (porta 443).

O tempo de execução do IoT Edge configura uma rede para que cada um dos módulos se comunique. No Linux, esta rede é uma rede de ponte. No Windows, ele usa o NAT. Esse problema é mais comum em dispositivos Windows com contêineres do Windows que usam a rede NAT. 

### <a name="resolution"></a>Resolução
Verifique se existe uma rota para a Internet para os endereços IP atribuídos a esta rede de ponte/NAT. Às vezes, uma configuração de VPN no host substitui a rede do IoT Edge. 

## <a name="edge-hub-fails-to-start"></a>O Hub do Edge não pode ser iniciado

O Hub do Edge não é iniciado e imprime a seguinte mensagem nos logs: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Causa raiz
Algum outro processo no computador host tem a porta 443 associada. O Hub do Edge mapeia as portas 5671 e 443 para usar em cenários de gateway. Esse mapeamento de porta falhará se outro processo já estiver associado a essa porta. 

### <a name="resolution"></a>Resolução
Localize e interrompa o processo que está usando a porta 443. Esse processo geralmente é um servidor Web.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>O Agente do Edge não pode acessar a imagem de um módulo (403)
Falha na execução de um contêiner e os logs do Agente do Edge mostram um erro 403. 

### <a name="root-cause"></a>Causa raiz
O Agente do Edge não tem permissões para acessar a imagem de um módulo. 

### <a name="resolution"></a>Resolução
Certifique-se de que suas credenciais de registro estão especificadas corretamente em seu manifesto de implantação

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>O daemon de segurança de IoT Edge falhará com um nome de host inválido

O comando `sudo journalctl -u iotedge` falha e imprime a mensagem a seguir: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Causa raiz
O tempo de execução de IoT Edge só pode oferecer suporte a nomes de host com menos de 64 caracteres. Geralmente, isso não é um problema para máquinas físicas, mas poderá ocorrer quando você configurar o tempo de execução em uma máquina virtual. Os nomes de host gerados automaticamente para máquinas virtuais do Windows hospedadas no Azure, em particular, tendem a ser longos. 

### <a name="resolution"></a>Resolução
Quando você vir esse erro, você pode resolvê-lo a configurar o nome DNS de sua máquina virtual e, em seguida, definindo o nome DNS de nome de host no comando de instalação.

1. No portal do Azure, navegue até a página de visão geral de sua máquina virtual. 
2. Selecione **configurar** em nome DNS. Caso sua máquina virtual já tenha um nome DNS configurado, você não precisará configurar um novo. 

   ![Configurar nome DNS](./media/troubleshoot/configure-dns.png)

3. Forneça um valor para **rótulo do nome DNS** e selecione **Salvar**.
4. Copie o novo nome DNS, que deve estar no formato **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**.
5. Dentro da máquina virtual, use o comando a seguir para configurar o tempo de execução do IoT Edge com seu nome DNS:

   - No Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - No Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Problemas de estabilidade em dispositivos com restrição de recursos 
Talvez você encontre problemas de estabilidade em dispositivos com restrição como o Raspberry Pi, principalmente quando usados como um gateway. Os sintomas incluem exceções fora da memória no módulo de hub do edge, os dispositivos downstream não podem se conectar ou o dispositivo padra de enviar mensagens de telemetria após algumas horas.

### <a name="root-cause"></a>Causa raiz
O hub do edge, que faz parte do tempo de execução do edge, tem o desempenho otimizado, por padrão, e tenta alocar grandes partes de memória. Isso não é ideal para dispositivos edge com restrição e pode causar problemas de estabilidade.

### <a name="resolution"></a>Resolução
Para o hub do edge, defina uma variável de ambiente **OptimizeForPerformance** como **false**. Há duas maneiras de fazer isso:

Na interface do usuário: 

No portal, em *Detalhes do dispositivo*->*Definir módulos*->*Definir configurações avançadas do Tempo de Execução do Edge*, crie uma variável de ambiente chamada *OptimizeForPerformance* definida como *false* para o *Hub do Edge*.

![optimizeforperformance][img-optimize-for-perf]

**OR**

No manifesto de implantação:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```

## <a name="next-steps"></a>Próximas etapas
Você acha que encontrou um bug na plataforma IoT Edge? [Envie um problema](https://github.com/Azure/iotedge/issues) para que possamos continuar a melhorar. 

<!-- Images -->
[img-optimize-for-perf]: ./media/troubleshoot/OptimizeForPerformanceFalse.png
