---
title: Requisitos de sistema do Microsoft Azure Data Box Gateway | Microsoft Docs
description: Saiba mais sobre o software e os requisitos de rede do Azure Data Box Gateway
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/17/2018
ms.author: alkohli
ms.openlocfilehash: da22c09a227069af0eeb42ab67a59189ae494185
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256665"
---
# <a name="azure-data-box-gateway-system-requirements-preview"></a>Requisitos de sistema de Azure Data Box Gateway (versão prévia)

Este artigo descreve os requisitos de sistema importantes para a solução Gateway do Microsoft Azure Data Box e para os clientes que se conectam ao Azure Data Box Gateway. Recomendamos que você examine as informações com atenção antes de implantar o Data Box Gateway e consulte-as, quando necessário, durante a implantação e a subsequente operação.

Os requisitos de sistema para o dispositivo virtual Data Box Gateway incluem:

- **Requisitos de software para hosts** – descreve as plataformas com suporte, os navegadores da interface do usuário de configuração local, os clientes SMB e requisitos adicionais para os hosts que se conectam ao dispositivo.
- **Requisitos de rede para o dispositivo** – fornece informações sobre os requisitos de rede da operação do dispositivo virtual.

> [!IMPORTANT]
> O Data Box Gateway está em versão prévia. Examine os [termos de uso para a versão prévia](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) antes de implantar essa solução. 

## <a name="specifications-for-the-virtual-device"></a>Especificações para o dispositivo virtual

O sistema host subjacente para o Data Box Gateway é capaz de dedicar os seguintes recursos para provisionar seu dispositivo virtual:

| Especificações                                          | DESCRIÇÃO              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |            
| Memória  | Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos| Disco do sistema operacional: 250 GB <br> Disco de dados: Mínimo de 2 TB, com provisionamento dinâmico e deve ser ativado por SSDs|
| Interfaces de rede|1 ou mais adaptadores de rede virtual|


## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operacional com suporte para clientes conectados ao dispositivo

Aqui está uma lista dos sistemas operacionais com suporte para clientes ou hosts conectados ao Data Box Gateway.

| **Sistema operacional/plataforma** | **Versões** |
| --- | --- |
| Windows Server |2012 R2 <br> 2016 |
| Windows |8, 10 |
| SUSE Linux |Enterprise Server 12 (x86_64)|
| Ubuntu |16.04.3 LTS|
| CentOS | 7.0 |

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos com suporte para clientes que acessam o dispositivo

|**Protocolo** |**Versões**   |**Observações**  |
|---------|---------|---------|
|SMB    | 2.X, 3.X      | Não há suporte para protocolos SMB 1.|
|NFS     | V3 e V4        |         |

## <a name="supported-virtualization-platforms-for-device"></a>Plataformas de virtualização com suporte para o dispositivo

| **Sistema operacional/plataforma**  |**Versões**   |**Observações**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016  |         |
|VMware ESXi     | 6,0 <br> 6.5        |Não há suporte para as ferramentas do VMware.         |


## <a name="supported-storage-accounts"></a>Contas de armazenamento com suporte

Aqui está uma lista das contas de armazenamento suportadas para o Data Box Gateway.

| **Conta de armazenamento** | **Observações** |
| --- | --- |
| Clássico | Standard |
| Uso geral  |Standard; tanto V1 quanto V2 são compatíveis. Tanto a camada de acesso frequente quanto a camada de acesso esporádico são compatíveis. |


## <a name="supported-storage-types"></a>Tipos de armazenamento com suporte

Aqui está uma lista dos tipos de armazenamento com suporte para o Data Box Gateway.

| **Formato de arquivo** | **Observações** |
| --- | --- |
| Blob de blocos do Azure | |
| Blob de páginas do Azure  | |
| Arquivos do Azure | |

## <a name="supported-browsers-for-local-web-ui"></a>Navegadores com suporte para a interface do usuário da Web local

Aqui está uma lista dos navegadores com suporte para a interface do usuário da Web local para o dispositivo virtual.

|Navegador  |Versões  |Requisitos/observações adicionais  |
|---------|---------|---------|
|Google Chrome   |Última versão         |         |
|Microsoft Edge    | Última versão        |         |
|Internet Explorer     | Última versão        |         |
|Firefox    |Última versão         |         |


## <a name="networking-requirements"></a>Requisitos de rede

A tabela a seguir lista as portas que devem ser abertas no firewall para permitir o tráfego SMB, de nuvem ou de gerenciamento. Nesta tabela, *entrada* ou *de entrada* refere-se à direção da qual as solicitações do cliente acessam o dispositivo. *Saída* ou *de saída* refere-se à direção para a qual seu dispositivo Data Box Gateway envia dados externamente, além da implantação: por exemplo, saída para a Internet.

| Nº da porta| Entrada ou saída | Escopo da porta| Obrigatório|   Observações                                                             |                                                                                     |
|--------|---------|----------|--------------|----------------------|---------------|
| TCP 80 (HTTP)|Saída|WAN |Não |A porta de saída é usada para acesso à Internet para recuperar atualizações. <br>O proxy Web de saída é configurável pelo usuário. |                          
| TCP 443 (HTTPS)|Saída|WAN|SIM|A porta de saída é usada para acessar dados na nuvem.<br>O proxy Web de saída é configurável pelo usuário.|   
| UDP 53 (DNS)|Saída|WAN|Em alguns casos<br>Consulte as observações|Esta porta só será necessária se você estiver usando um servidor DNS baseado na Internet.<br>É recomendado usar o servidor DNS local. |
| UDP 123 (NTP)|Saída|WAN|Em alguns casos<br>Consulte as observações|Esta porta é necessária apenas se você estiver usando um servidor NTP baseado na Internet.  |
| UDP 67 (DHCP)|Saída|WAN|Em alguns casos<br>Consulte as observações|Essa porta será necessária apenas se você estiver usando um servidor DHCP.  |
| TCP 80 (HTTP)|No|LAN|SIM|Essa é a porta de entrada da interface do usuário local no dispositivo para gerenciamento local. <br>O acesso à interface do usuário local por HTTP será redirecionado automaticamente para HTTPS.  | 
| TCP 443 (HTTPS)|No|LAN|SIM|Essa é a porta de entrada da interface do usuário local no dispositivo para gerenciamento local. | 

## <a name="url-patterns-for-firewall-rules"></a>Padrões de URL para regras de firewall

Os administradores de rede geralmente podem configurar regras avançadas de firewall com base nos padrões de URL para filtrar o tráfego de entrada e de saída. Seu dispositivo Data Box Gateway e o serviço Data Box Gateway dependem de outros aplicativos da Microsoft, como o Barramento de Serviço do Azure, o Controle de Acesso do Azure Active Directory, contas de armazenamento e servidores do Microsoft Update. Os padrões de URL associados a esses aplicativos podem ser usados para configurar regras de firewall. É importante entender que os padrões de URL associados a esses aplicativos podem ser alterados. Isso, por sua vez, exigirá que o administrador de rede monitore e atualize as regras de firewall do Data Box Gateway conforme e quando necessário.

É recomendado que você defina suas regras de firewall para tráfego de saída, com base nos endereços IP fixos do Data Box Gateway e, na maioria dos casos, de modo flexível. No entanto, você pode usar as informações a seguir para definir regras avançadas de firewall que são necessárias para criar ambientes seguros.

> [!NOTE]
> - Os IPs do dispositivo (de origem) sempre devem estar configurados para todas as interfaces de rede habilitadas para a nuvem.
> - Os IPs de destino devem ser definidos como [Azure datacenter IP ranges](https://www.microsoft.com/download/confirmation.aspx?id=41653)(Intervalos de IP do datacenter do Azure).

|     Padrão de URL                                                                                                                                                                                                                                                                                                                                                                                                                                       |     Componente/funcionalidade                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Serviço de Azure Data Box Gateway<br>Barramento de Serviço do Azure<br>Serviço de autenticação    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    Ativação do dispositivo                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    Revogação de certificado                                                                               |
|    https://*.core.windows.net/*   https://*.data.microsoft.com   http://*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Contas de armazenamento e monitoramento do Azure                                                                |
|    http://windowsupdate.microsoft.com<br>http://*.windowsupdate.microsoft.com<br>https://*.windowsupdate.microsoft.com<br>http://*.update.microsoft.com<br>https://*.update.microsoft.com<br>http://*.windowsupdate.com<br>http://download.microsoft.com<br>http://*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*.ws.microsoft.com<br>https://*.ws.microsoft.com<br>http://*.mp.microsoft.com        |    Servidores do Microsoft Update                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    CDN do Akamai                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    Pacote de suporte                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Serviço de telemetria no Windows, confira a atualização da telemetria de experiência do usuário e de diagnóstico      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |



## <a name="internet-bandwidth"></a>Largura de banda de Internet

Os seguintes requisitos aplicam-se à largura de banda mínima da Internet disponível para seus dispositivos do Data Box Gateway.

- O Data Box Gateway tem uma largura de banda de Internet dedicada de 20 Mbps (ou mais) disponível o tempo todo. Essa largura de banda não deve ser compartilhada com outros aplicativos. 
- O Data Box Gateway tem uma largura de banda de Internet dedicada de 32 Mbps (ou mais) ao usar a limitação de rede.

## <a name="next-step"></a>Próxima etapa

* [Implantar o Azure Data Box Gateway](data-box-gateway-deploy-prep.md)

