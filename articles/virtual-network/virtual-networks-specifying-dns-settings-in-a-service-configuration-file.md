---
title: Especificando as configurações DNS em um arquivo de configuração de serviço | Microsoft Docs
description: especificando as configurações DNS personalizadas usando o arquivo de configuração de serviço para uma rede virtual
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 009206f1e0ba848538ed2c666032a63051d062e4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790739"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Especificando as configurações de DNS em um arquivo de configuração de serviço
## <a name="dns-elements"></a>Elementos DNS
Um arquivo de configuração de serviço pode conter um elemento DnsServers com uma lista de endereços IPv4 para os servidores do Sistema de Nome de Domínio (DNS) que o serviço usará. As configurações no arquivo de configuração de serviço têm precedência sobre as configurações no arquivo de configuração de rede. Para obter mais informações, consulte [Esquema de configuração de serviço do Azure (arquivo .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> O atributo **nome** no elemento **DnsServer** é usado apenas como um nome de referência. Ele não representa o nome do host para o servidor DNS. Cada valor do atributo **DnsServer** deve ser exclusivo em toda a assinatura do Microsoft Azure.
> 
> 

## <a name="see-also"></a>Veja também
[Esquema de configuração de serviço do Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuração de Rede Virtual do Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurar uma Rede Virtual usando arquivos de configuração de rede](http://go.microsoft.com/fwlink/?LinkId=248094)

[Sobre as configurações de rede virtual no Portal de Gerenciamento](http://go.microsoft.com/fwlink/?LinkId=248092)

