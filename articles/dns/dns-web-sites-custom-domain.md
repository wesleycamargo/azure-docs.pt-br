---
title: Tutorial - Criar registros DNS personalizados para um aplicativo Web
description: Neste tutorial, você aprenderá como criar registros DNS de domínio personalizado para o aplicativo Web usando o DNS do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 2/19/2019
ms.author: victorh
ms.openlocfilehash: 9ed0c8763835add485d6c60a43f4e4113ecde12e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429274"
---
# <a name="tutorial-create-dns-records-in-a-custom-domain-for-a-web-app"></a>Tutorial: Criar registros DNS em um domínio personalizado para um aplicativo Web 

Você pode configurar o DNS do Azure para hospedar um domínio personalizado em seus aplicativos Web. Por exemplo, você pode criar um aplicativo Web do Azure e ter seu acesso a usuários usando www.contoso.com ou contoso.com como um nome de domínio totalmente qualificado (FQDN).

> [!NOTE]
> Contoso.com é usado como um exemplo ao longo deste tutorial. Substitua seu próprio nome de domínio para contoso.com.

Para isso, você precisa criar dois registros:

* Um registro "A" raiz que aponta para contoso.com
* Uma raiz do Registro "TXT" para verificação
* Um registro "CNAME" para o nome www que aponta para o registro A

Tenha em mente que, se você criar um registro A para um aplicativo Web no Azure, o registro A deve ser manualmente atualizado se o endereço IP subjacente para o aplicativo Web for alterado.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um registro A e TXT para seu domínio personalizado
> * Criar um registro CNAME para seu domínio personalizado
> * Testar os novos registros
> * Adicionar nomes de host personalizados a seu aplicativo Web
> * Testar os nomes de host personalizados


Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- [Crie um aplicativo do Serviço de Aplicativo](../app-service/app-service-web-get-started-html.md) ou use um aplicativo que você criou para outro tutorial.

- Crie uma zona DNS no DNS do Azure e delegue a zona no registrador para o DNS do Azure.

   1. Para criar uma zona DNS, siga as etapas em [Criar uma zona DNS](dns-getstarted-create-dnszone.md).
   2. Para delegar sua zona ao DNS do Azure, siga as etapas em [Delegação de domínio DNS](dns-domain-delegation.md).

Após criar uma zona e delegá-la ao DNS do Azure, você pode criar registros para seu domínio personalizado.

## <a name="create-an-a-record-and-txt-record"></a>Criar um registro A e um registro TXT

Um registro A é usado para mapear um nome para seu endereço IP. No exemplo a seguir, atribua “\@” como um registro A usando o endereço IPv4 do aplicativo Web. \@ normalmente representa o domínio raiz.

### <a name="get-the-ipv4-address"></a>Obter o endereço IPv4

No painel de navegação à esquerda da página dos Serviços de Aplicativos no portal do Azure, selecione **Domínios personalizados**. 

![Menu de domínio personalizado](../app-service/./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

Na página **Domínios personalizados**, copie o endereço IPv4 do aplicativo:

![Navegação no Portal para o aplicativo do Azure](../app-service/./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="create-the-a-record"></a>Criar um conjunto A de registros

```powershell
New-AzDnsRecordSet -Name "@" -RecordType "A" -ZoneName "contoso.com" `
 -ResourceGroupName "MyAzureResourceGroup" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -IPv4Address "<your web app IP address>")
```

### <a name="create-the-txt-record"></a>Criar o registro TXT

Os Serviços de Aplicativos usam esse registro somente durante o tempo de configuração, para verificar se você possui seu próprio domínio personalizado. Você poderá excluir esse registro TXT depois que o domínio personalizado for validado e configurado no Serviço de Aplicativo.

```powershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup `
 -Name "@" -RecordType "txt" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -Value  "contoso.azurewebsites.net")
```

## <a name="create-the-cname-record"></a>Criar um registro CNAME

Se o domínio já é gerenciado pelo DNS do Azure (confira [Delegação de domínio ao DNS](dns-domain-delegation.md)), você pode usar o exemplo a seguir para criar um registro CNAME para contoso.azurewebsites.net.

Abra o Azure PowerShell e crie um novo registro CNAME. Este exemplo cria um tipo de conjunto de registros CNAME com uma "vida útil" de 600 segundos na zona DNS denominada "contoso.com" com o alias para o aplicativo Web contoso.azurewebsites.net.

### <a name="create-the-record"></a>Criar o registro

```powershell
New-AzDnsRecordSet -ZoneName contoso.com -ResourceGroupName "MyAzureResourceGroup" `
 -Name "www" -RecordType "CNAME" -Ttl 600 `
 -DnsRecords (New-AzDnsRecordConfig -cname "contoso.azurewebsites.net")
```

O exemplo a seguir é a resposta:

```
    Name              : www
    ZoneName          : contoso.com
    ResourceGroupName : myresourcegroup
    Ttl               : 600
    Etag              : 8baceeb9-4c2c-4608-a22c-229923ee185
    RecordType        : CNAME
    Records           : {contoso.azurewebsites.net}
    Tags              : {}
```

## <a name="test-the-new-records"></a>Testar os novos registros

Você pode validar se os registros foram criados corretamente consultando “www.contoso.com” e “contoso.com” usando o nslookup, conforme mostrado abaixo:

```
PS C:\> nslookup
Default Server:  Default
Address:  192.168.0.1

> www.contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    <instance of web app service>.cloudapp.net
Address:  <ip of web app service>
Aliases:  www.contoso.com
contoso.azurewebsites.net
<instance of web app service>.vip.azurewebsites.windows.net

> contoso.com
Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
Name:    contoso.com
Address:  <ip of web app service>

> set type=txt
> contoso.com

Server:  default server
Address:  192.168.0.1

Non-authoritative answer:
contoso.com text =

        "contoso.azurewebsites.net"
```
## <a name="add-custom-host-names"></a>Adicionar nomes de host personalizados

Agora você pode adicionar os nomes de host personalizados ao seu aplicativo Web:

```powershell
set-AzWebApp `
 -Name contoso `
 -ResourceGroupName MyAzureResourceGroup `
 -HostNames @("contoso.com","www.contoso.com","contoso.azurewebsites.net")
```
## <a name="test-the-custom-host-names"></a>Testar os nomes de host personalizados

Abra um navegador e navegue até `http://www.<your domainname>` e `http://<you domain name>`.

> [!NOTE]
> Certifique-se de incluir o prefixo `http://`, caso contrário, seu navegador pode tentar prever uma URL para você!

Você deve ver a mesma página para ambas as URLs. Por exemplo: 

![Serviço de aplicativo da Contoso](media/dns-web-sites-custom-domain/contoso-app-svc.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando não precisar mais dos recursos criados neste tutorial, você poderá excluir o grupo de recursos **myresourcegroup**.

## <a name="next-steps"></a>Próximas etapas

Saiba como criar zonas DNS no DNS do Azure.

> [!div class="nextstepaction"]
> [Introdução às zonas privadas do DNS do Azure usando o PowerShell](private-dns-getstarted-powershell.md)
