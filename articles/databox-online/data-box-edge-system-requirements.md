---
title: Requisitos de sistema do Microsoft Azure Data Box Edge | Microsoft Docs
description: Aprender sobre os requisitos de software e de rede para seu Azure Data Box Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/14/2019
ms.author: alkohli
ms.openlocfilehash: 05f6208788060459e67a787507a56adeed980e2d
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54318987"
---
# <a name="azure-data-box-edge-system-requirements-preview"></a>Requisitos do sistema do Azure Data Box Edge (versão prévia)

Este artigo descreve os requisitos de sistema importantes para a solução do Microsoft Azure Data Box Edge e para os clientes que se conectam ao Azure Data Box Edge. É recomendável que você examine as informações com atenção antes de implantar o Data Box Edge. Você pode consultar novamente a essas informações conforme necessário durante a implantação e operação subsequente.

São requisitos de sistema do Data Box Edge:

- **Requisitos de software para hosts** – descreve as plataformas com suporte, os navegadores da interface do usuário de configuração local, os clientes SMB e requisitos adicionais para os clientes que acessam o dispositivo.
- **Requisitos de rede para o dispositivo** – fornece informações sobre os requisitos de rede da operação do dispositivo físico.

> [!IMPORTANT]
> O Data Box Edge está em versão prévia. Examine os [termos de uso para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução.

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operacional com suporte para clientes conectados ao dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos com suporte para clientes que acessam o dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores com suporte para a interface do usuário da Web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="port-configuration-for-data-box-edge"></a>Configuração de porta para Data Box Edge

A tabela a seguir lista as portas que devem ser abertas no firewall para permitir o tráfego SMB, de nuvem ou de gerenciamento. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *Saída* ou *de saída* referem-se à direção para a qual seu dispositivo do Data Box Edge envia dados externamente, além da implantação, por exemplo, saída para a internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="port-configuration-for-iot-edge"></a>Configuração de porta para IoT Edge

O Azure IoT Edge permite a comunicação de saíde de um dispositivo do Edge local com a nuvem do Azure usando os protocolos do Hub IoT com suporte. A comunicação de entrada só é necessária para cenários específicos em que o Hub IoT do Azure precisa enviar mensagens por push para o dispositivo do Azure IoT Edge (por exemplo, mensagens de nuvem a dispositivo).

Use a tabela a seguir para a configuração de porta dos servidores que hospedam o tempo de execução do IoT Edge:

| Nº da porta | Entrada ou saída | Escopo da porta | Obrigatório | Diretrizes |
|----------|-----------|------------|----------|----------|
| TCP 5671 (AMQP)| Saída       | WAN        | SIM      | Protocolo de comunicação padrão do IoT Edge. Precisa estar aberta quando o Azure IoT Edge não está configurado para outros protocolos com suporte ou quando o AMQP é o protocolo de comunicação desejado. <br>Não há suporte para o 5672 para AMQP no IoT Edge. <br>Bloqueie essa porta quando o Azure IoT Edge usar outro protocolo do Hub IoT com suporte. |
| TCP 443 (HTTPS)| Saída       | WAN        | SIM      | Saída aberta para o provisionamento do IoT Edge. Se você tiver um gateway transparente com dispositivos de folha que possam enviar solicitações de método. Nesse caso, a porta 443 não precisa estar aberta para redes externas para conectar-se ao Hub IoT ou fornecer serviços do Hub IoT por meio do Azure IoT Edge. Assim, a regra de entrada pode ser restrita para abrir somente a entrada da rede interna. |
| TCP 5671 (AMQP) | No        |            | Não        | As conexões de entrada devem ser bloqueadas.|
| TCP 443 (HTTPS) | No        |            | Em alguns casos, veja os comentários | As conexões de entrada devem ser abertas somente para cenários específicos. Se protocolos não HTTP, como AMQP e MQTT, não puderem ser configurados, as mensagens poderão ser enviadas por meio de WebSockets usando a porta 443. |

Para todas as informações, acesse [Regras de configuração de firewall e de porta para implantação do IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. Seu dispositivo do Data Box Edge e o serviço dependem de outros aplicativos da Microsoft, como o Barramento de Serviço do Azure, o Controle de Acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Essas mudanças exigem que o administrador de rede monitore e atualize as regras de firewall do Data Box Edge conforme e quando necessário.

É recomendado que você defina suas regras de firewall para tráfego de saída, com base nos endereços IP fixos do Data Box Edge e, na maioria dos casos, de modo flexível. No entanto, você pode usar as informações a seguir para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (de origem) sempre devem estar configurados para todas as interfaces de rede habilitadas para a nuvem.
> - Os IPs de destino devem ser configurados como [Intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Padrões de URL para o recurso de gateway

|    Padrão de URL                                                                                                                                                                                                                                                                                                                                                                                                                                                      |    Componente ou funcionalidade                                                                           |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://\*.databoxedge.azure.com/\*<br>https://\*.servicebus.windows.net/\*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                                                                           |    Serviço do Azure Data Box Edge<br>Barramento de Serviço do Azure<br>Serviço de autenticação                           |
|    http://\*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                                |    Ativação do dispositivo                                                                                    |
|    http://crl.microsoft.com/pki/\*<br>http://www.microsoft.com/pki/\*                                                                                                                                                                                                                                                                                                                                                                                                  |    Revogação de certificado                                                                               |
|    https://\*.core.windows.net/\*<br>https://\*.data.microsoft.com<br>http://\*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                            |    Contas de armazenamento e monitoramento do Azure                                                                |
|    http://windowsupdate.microsoft.com<br>http://\*.windowsupdate.microsoft.com<br>https://\*.windowsupdate.microsoft.com<br>http://\*.update.microsoft.com<br>https://\*.update.microsoft.com<br>http://\*.windowsupdate.com<br>http://download.microsoft.com<br>http://\*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://\*.ws.microsoft.com<br>https://\*.ws.microsoft.com<br>http://\*.mp.microsoft.com |    Servidores do Microsoft Update                                                                             |
|    http://\*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                                          |    CDN do Akamai                                                                                           |
|    https://\*.partners.extranet.microsoft.com/\*                                                                                                                                                                                                                                                                                                                                                                                                                    |    Pacote de suporte                                                                                      |
|    http://\*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                                     |    Serviço de telemetria no Windows, confira a atualização da telemetria de experiência do usuário e de diagnóstico      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |                                                                                                         |


### <a name="url-patterns-for-compute-feature"></a>Padrões de URL para o recurso de computação

| Padrão de URL                      | Componente ou funcionalidade                     |   |
|----------------------------------|---------------------------------------------|---|
| https://mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registro de Contêiner da Microsoft (obrigatório)               |   |
| https://\*.azurecr.io                     | Registros de contêiner pessoal e de terceiros (opcional) |   |
| https://\*.azure-devices.net              | Acesso do Hub IoT (obrigatório)                             |   |

## <a name="internet-bandwidth"></a>Largura de banda de Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Próxima etapa

* [Implante seu Azure Data Box Edge](data-box-Edge-deploy-prep.md)
