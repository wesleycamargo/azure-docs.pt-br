---
title: Gateway de dados local | Microsoft Docs
description: "Um gateway local será necessário se o servidor do Analysis Services no Azure for se conectar a fontes de dados locais."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: cd596155-b608-4a34-935e-e45c95d884a9
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 10/24/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 90584f60864589744888817ea71d0eb0d4d170ff


---
# <a name="on-premises-data-gateway"></a>Gateway de dados local
O gateway de dados local atua como uma ponte, fornecendo transferência de dados segura entre fontes de dados locais e o servidor do Azure Analysis Services na nuvem.

Um gateway é instalado em um computador em sua rede. É necessário instalar um gateway para cada servidor do Azure Analysis Services existente em sua assinatura do Azure. Por exemplo, se você tiver dois servidores em sua assinatura do Azure que se conectam a fontes de dados locais, será necessário instalar um gateway em dois computadores separados em sua rede.

## <a name="requirements"></a>Requisitos
**Requisitos mínimos:**

* .NET 4.5 Framework
* Versão de 64 bits do Windows 7/Windows Server 2008 R2 (ou posterior)

**Recomendados:**

* CPU de 8 núcleos
* Memória de 8 GB
* Versão de 64 bits do Windows 2012 R2 (ou posterior)

**Considerações importantes:**

* O gateway não pode ser instalado em um controlador de domínio.
* Apenas um gateway pode ser instalado em um computador.
* Instale o gateway em um computador que permanece ligado e não entra em suspensão. Se o computador não estiver ligado, o servidor do Azure Analysis Services não poderá se conectar a suas fontes de dados locais para atualizar os dados.
* Não instale o gateway em um computador sem fio conectado à rede. O desempenho pode ser prejudicado.
* Para alterar o nome do servidor de um gateway que já foi configurado, você precisa reinstalar e configurar um novo gateway.
* Em alguns casos, modelos de tabela que se conectam a fontes de dados usando provedores nativos, como o SQL Server Native Client (SQLNCLI11), podem retornar um erro. Para saber mais, confira [Conexões com fonte de dados](analysis-services-datasource.md).

## <a name="supported-on-premises-data-sources"></a>Fontes de dados locais com suporte
Para a versão de visualização, o gateway oferece suporte a conexões entre o servidor do Azure Analysis Services e fontes de dados locais:

* SQL Server
* SQL Data Warehouse
* APS
* Oracle
* Teradata

## <a name="download"></a>Baixar
 [Baixe o gateway](https://aka.ms/azureasgateway)

## <a name="install-and-configure"></a>Instalar e configurar
1. Execute a instalação.
2. Escolha um local de instalação e aceite os termos da licença.
3. Entre no Azure.
4. Especifique o nome do Azure Analysis Server. Você só pode especificar um servidor por gateway. Clique em **Configurar** e você estará pronto para começar.
   
    ![Entrar no Azure](./media\\analysis-services-gateway\\aas-gateway-configure-server.png)

## <a name="how-it-works"></a>Como ele funciona
O gateway é executado como um serviço do Windows, **Gateway de dados local**, em um computador na rede de sua organização. O gateway que você instala para usar com o Azure Analysis Services tem base no mesmo gateway usado para outros serviços, como o Power BI, mas com algumas diferenças de configuração.

![Como ele funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Consultas e fluxos de dados funcionam da seguinte maneira:

1. Uma consulta é criada pelo serviço de nuvem com as credenciais criptografadas para a fonte de dados local. Ela é enviada para uma fila de processamento do gateway.
2. O serviço de nuvem do gateway analisa a consulta e envia a solicitação para o [Barramento de Serviço do Azure](https://azure.microsoft.com/documentation/services/service-bus/).
3. O gateway de dados local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes.
4. O gateway obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados com essas credenciais.
5. O gateway envia a consulta à fonte de dados para execução.
6. Os resultados são enviados da fonte de dados de volta para o gateway e, em seguida, para o serviço de nuvem.

## <a name="windows-service-account"></a>Conta de Serviço do Windows
O gateway de dados local é configurado para usar *NT SERVICE\PBIEgwService* para as credenciais de logon de serviço do Windows. Por padrão, ele tem o direito de Fazer logon como um serviço; no contexto da máquina na qual você está instalando o gateway. Essa credencial não é da mesma conta usada para se conectar às fontes de dados locais ou da sua conta do Azure.  

Se você encontrar problemas com o servidor proxy devido à autenticação, convém alterar a conta de serviço do Windows para um usuário de domínio ou conta de serviço gerenciado.

## <a name="ports"></a>Portas
O gateway cria uma conexão de saída para o Barramento de Serviço do Azure. Ele se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354.  O gateway não exige portas de entrada.

Recomendamos a inclusão dos endereços IP em uma lista branca para a região de dados em seu firewall. Baixe a [lista de IPs de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). A lista é atualizada semanalmente.

> [!NOTE]
> Os Endereços IP listados na lista de IP de Datacenter do Azure estão em notação CIDR. Por exemplo, 10.0.0.0/24 significa 10.0.0.0 a 10.0.0.24. Saiba mais sobre a [notação CIDR ](http://whatismyipaddress.com/cidr).
> 
> 

Veja a seguir os nomes de domínio totalmente qualificados usados pelo gateway.

| Nomes de domínio | Portas de saída | Descrição |
| --- | --- | --- |
| *.powerbi.com |80 |HTTP usado para baixar o instalador. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Usado para testar a conectividade com a Internet se o gateway não poder ser acessado pelo serviço do Power BI. |
| *.microsoftonline p.com |443 |Usado para autenticação, dependendo da configuração. |

### <a name="forcing-https-communication-with-azure-service-bus"></a>Forçar a comunicação HTTPS com o Barramento de Serviço do Azure
Você pode forçar o gateway para se comunicar com o Barramento de Serviço do Azure usando HTTPS em vez de TCP direto; no entanto, isso pode reduzir consideravelmente o desempenho. Você precisa modificar o arquivo *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config*. Altere o valor de `AutoDetect` para `Https`. Esse arquivo está localizado, por padrão, em *C:\Arquivos de Programas\Gateway de dados local*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Solucionar problemas
Nos bastidores, o gateway de dados local usado para conectar o Azure Analysis Services às suas fontes de dados locais é o mesmo gateway usado com o Power BI.

Se você estiver enfrentando problemas para instalar e configurar um gateway, confira [Solução de problemas do Gateway do Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Se você achar que está enfrentando um problema com o seu firewall, consulte as seções de proxy ou firewall.

Se você achar que você está encontrando problemas de proxy, com o gateway, consulte [Definir as configurações de proxy para os Gateways do Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar o Analysis Services](analysis-services-manage.md)
* [Obter dados do Azure Analysis Services](analysis-services-connect.md)




<!--HONumber=Nov16_HO3-->


