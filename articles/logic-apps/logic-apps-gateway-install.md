---
title: "Instalar gateway de dados local - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Acesse dados locais de aplicativos lógicos instalando um gateway de dados local"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 47e3024e-88a0-4017-8484-8f392faec89d
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: c300ba45cd530e5a606786aa7b2b254c2ed32fcd
ms.openlocfilehash: b9971117d5f61669a5161a28c96b11b2fd600b61
ms.lasthandoff: 04/14/2017


---
# <a name="install-an-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar gateway de dados local para Aplicativos Lógicos do Azure

O gateway de dados local dá suporte a estas conexões:

*   BizTalk Server
*   DB2  
*   Sistema de Arquivos
*   Informix
*   MQ
*   MySQL
*   Banco de dados Oracle 
*   Servidor de aplicativos SAP 
*   Servidor de mensagens SAP
*   SharePoint somente para HTTP e não para HTTPS
*   SQL Server
*   Teradata

Para obter mais informações sobre essas conexões, confira [Conectores para Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/connectors/apis-list).

## <a name="installation-and-configuration"></a>Instalação e configuração

### <a name="requirements"></a>Requisitos

Mínimo:

* .NET 4.5 Framework
* Versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

Recomendável:

* CPU de 8 núcleos
* Memória de 8 GB
* Versão de 64 bits do Windows 2012 R2 (ou posterior)

Considerações relacionadas:

* Só instale o gateway de dados local em um computador local.
Você não pode instalar o gateway em um controlador de domínio.

* Não instale o gateway em um computador que possa ser desligado, entra em suspensão ou não se conectar à Internet, pois o gateway não pode ser executado em tais circunstâncias. Além disso, o desempenho do gateway pode ser reduzido em uma rede sem fio.

* Você só pode usar um endereço de email corporativo ou de estudante no Azure, para que possa associar o gateway de dados local a sua conta baseada no Azure Active Directory.

    Se estiver usando uma conta da Microsoft, como @outlook.com, você poderá usar sua conta do Azure para   [criar um endereço de email corporativo ou de estudante](../virtual-machines/windows/create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal).

### <a name="install-the-gateway"></a>Instalar o gateway

1.    [Baixe o instalador para o gateway de dados local aqui](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

2.    Especifique **Gateway de dados local** como o modo.

3. Entre com sua conta corporativa ou de estudante do Azure. 

4. Configure um novo gateway, ou você pode migrar, restaurar ou assumir um gateway existente.

    Para configurar um gateway, forneça um nome para o gateway e uma chave de recuperação e escolha **Configurar**.
  
    Especifique uma chave de recuperação que contém pelo menos oito caracteres e guarde-a em um local seguro. Você precisa dessa chave para migrar, restaurar ou assumir o gateway.

    Para migrar, restaurar ou assumir um gateway existente, forneça a chave de recuperação que foi especificada quando o gateway foi criado.

### <a name="restart-the-gateway"></a>Reinicie o gateway

O gateway é executado como um serviço do Windows, assim, da mesma forma como qualquer outro serviço do Windows, você pode iniciar e parar o serviço de várias maneiras. Por exemplo, você pode abrir um prompt de comando com permissões elevadas no computador em que o gateway está em execução e executar um destes comandos:

* Para parar o serviço, execute este comando:
  
    `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:
  
    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configure um firewall ou proxy

Para fornecer informações de proxy para o gateway, confira [Definir configurações de proxy](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy/).

Você pode verificar se o firewall ou proxy pode bloquear conexões executando o comando a seguir em um prompt do PowerShell. Esse comando testa a conectividade com o Barramento de Serviço do Azure e apenas a conectividade de rede, para que o comando não tenha nada a ver com o serviço de servidor de nuvem ou o gateway. Esse teste ajuda a determinar se o computador pode realmente se conectar à Internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Os resultados devem semelhantes a este exemplo. Se **TcpTestSucceeded** não for true, você poderá estar sendo bloqueado por um firewall.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Para uma verificação completa, substitua os valores de **ComputerName** e **Porta** por aqueles listados em [Configurar portas](#configure-ports) neste tópico.

O firewall também pode bloquear conexões que o Barramento de Serviço do Azure faz para os data centers do Azure. Nesse caso, aprove (desbloqueie) todos os endereços IP para os data centers em sua região.
Você pode obter uma lista de [endereços IP do Azure aqui](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configure portas
O gateway cria uma conexão de saída para o Barramento de Serviço do Azure e se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354. O gateway não exige portas de entrada.

Saiba mais sobre [soluções híbridas](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMES DE DOMÍNIO | PORTAS DE SAÍDA | DESCRIÇÃO |
| --- | --- | --- |
| *.analysis.windows.net | 443 | HTTPS | 
| *.login.windows.net | 443 | HTTPS | 
| *.servicebus.windows.net | 5671-5672 | Advanced Message Queuing Protocol (AMQP) | 
| *.servicebus.windows.net | 443, 9350-9354 | Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) | 
| *.frontend.clouddatahub.net | 443 | HTTPS | 
| *.core.windows.net | 443 | HTTPS | 
| login.microsoftonline.com | 443 | HTTPS | 
| *.msftncsi.com | 443 | Usado para testar a conectividade com a Internet quando o gateway não puder ser acessado pelo serviço do Power BI. | 

Se precisar aprovar endereços IP em vez dos domínios, você poderá baixar e usar a [lista de intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Em alguns casos, as conexões de Barramento de Serviço do Azure são feitas com o Endereço IP, em vez de nomes de domínio totalmente qualificados.

### <a name="sign-in-accounts"></a>Contas de entrada

Você pode entrar com uma conta corporativa ou de estudante, que é a conta de sua organização. Se você se inscreveu para uma oferta do Office 365 e não forneceu seu email de trabalho real, o endereço de entrada pode ser semelhante a jeff@contoso.onmicrosoft.com. Sua conta, em um serviço de nuvem, é armazenada em um locatário no Azure AD (Azure Active Directory). Geralmente, o UPN de sua conta do Azure AD corresponde ao endereço de email.

### <a name="windows-service-account"></a>Conta de serviço do Windows

Para a credencial de logon do serviço do Windows, o gateway de dados local é configurado para usar NT SERVICE\PBIEgwService. Por padrão, o gateway tem o direito de "Fazer logon como um serviço", no contexto da máquina em que você instala o gateway.

Essa conta de serviço não é a mesma conta usada para se conectar a fontes de dados locais, nem a conta corporativa ou de estudante que você usa para entrar em serviços de nuvem.

## <a name="how-the-gateway-works"></a>Como funciona o gateway
Quando outros usuários interagem com um elemento que está conectado a uma fonte de dados local:

1. O serviço de nuvem cria uma consulta, juntamente com as credenciais criptografadas para a fonte de dados e envia a consulta para a fila de processamento do gateway.
2. O serviço analisa a consulta e envia a solicitação para o Barramento de Serviço do Azure.
3. O gateway de dados local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes.
4. O gateway obtém a consulta, descriptografa as credenciais e conecta-se à fonte de dados com essas credenciais.
5. O gateway envia a consulta à fonte de dados para execução.
6. Os resultados são enviados da fonte de dados para o gateway e para o serviço de nuvem. O serviço usa os resultados.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="general"></a>Geral

**Pergunta**: preciso ter um gateway para fontes de dados na nuvem, como o SQL Azure? <br/>
**Resposta**: Não. Um gateway conecta-se apenas a fontes de dados locais.

**Pergunta**: Qual é o nome real do serviço do Windows?<br/>
**Resposta**: Em Serviços, o gateway é chamado de Serviço de Gateway Corporativo do Power BI.

**Pergunta**: existem conexões de entrada para o gateway da nuvem? <br/>
**Resposta**: Não. O gateway usa conexões de saída para o Barramento de Serviço do Azure.

**Pergunta**: e se eu bloquear conexões de saída? O que preciso abrir? <br/>
**Resposta**: Verifique as portas e os hosts que o gateway usa.

**Pergunta**: o gateway precisa ser instalado no mesmo computador que a fonte de dados? <br/>
**Resposta**: Não. O gateway se conecta à fonte de dados usando as informações de conexão que foram fornecidas. Considere o gateway como um aplicativo de cliente nesse sentido. O gateway precisa apenas da capacidade de se conectar ao nome do servidor que foi fornecido.

**Pergunta**: qual é a latência para execução de consultas para uma fonte de dados do gateway? Qual é a melhor arquitetura? <br/>
**Resposta**: Para reduzir a latência de rede, instale o gateway o mais próximo possível da fonte de dados. Se você puder instalar o gateway na fonte de dados real, essa proximidade minimizará a latência introduzida. Considere também os data centers. Por exemplo, se o serviço usar o datacenter Oeste dos EUA e você tiver o SQL Server hospedado em uma VM do Azure, a VM do Azure também deverá estar no Oeste dos EUA. Essa proximidade minimiza a latência e evita encargos de saída na VM do Azure.

**Pergunta**: há algum requisito de largura de banda de rede? <br/>
**Resposta**: recomendamos que sua conexão de rede tenha uma boa taxa de transferência. Cada ambiente é diferente, e a quantidade de dados enviados afeta os resultados. Usar a Rota Expressa pode ajudar a garantir um nível de taxa de transferência entre os dada centers locais e do Azure.

Você pode usar um aplicativo de Teste de Velocidade do Azure de terceiros para avaliar sua taxa de transferência.

**Pergunta**: o serviço do Windows do gateway pode ser executado com uma conta do Azure Active Directory? <br/>
**Resposta**: Não. O serviço do Windows deve ter uma conta válida do Windows. Por padrão, o serviço é executado com o SID de Serviço, NT SERVICE\PBIEgwService.

**Pergunta**: como os resultados são enviados para a nuvem? <br/>
**Resposta**: os resultados são enviados por meio do Barramento de Serviço do Azure.

**Pergunta**: onde minhas credenciais são armazenadas? <br/>
**Resposta**: as credenciais que você inserir para uma fonte de dados são criptografadas e armazenadas no serviço de nuvem do gateway. Elas são descriptografadas no gateway local.

### <a name="high-availabilitydisaster-recovery"></a>Alta disponibilidade/recuperação de desastres
**Pergunta**: há planos para habilitar cenários de alta disponibilidade com o gateway? <br/>
**Resposta**: esses cenários são previstos, mas ainda não temos uma linha do tempo.

**Pergunta**: quais opções estão disponíveis para recuperação de desastres? <br/>
**Resposta**: Você pode usar a chave de recuperação para restaurar ou mover um gateway. Ao instalar o gateway, especifique a chave de recuperação.

**Pergunta**: qual é o benefício da chave de recuperação? <br/>
**Resposta**: a chave de recuperação fornece uma maneira de migrar ou recuperar as configurações de gateway após um desastre.

## <a name="troubleshooting"></a>Solucionar problemas

**Pergunta**: onde estão os logs do gateway? <br/>
**Resposta**: Consulte a seção Ferramentas mais adiante neste tópico.

**Pergunta**: como ver quais consultas estão sendo enviadas à fonte de dados local? <br/>
**Resposta**: você pode habilitar o acompanhamento de consulta, que inclui as consultas que são enviadas. Lembre-se de alterar o rastreamento de consulta de volta para o valor original quando concluir a solução de problemas. Deixar o acompanhamento de consulta ativado cria logs maiores.

Você também pode examinar ferramentas de rastreamento de consultas disponibilizadas por sua fonte de dados. Por exemplo, você pode usar o Extended Events ou o SQL Profiler para SQL Server e Analysis Services.

### <a name="update-to-the-latest-version"></a>Obtenha a versão mais recente

Muitos problemas podem surgir quando a versão do gateway fica desatualizada. Como prática geral recomendada, use a última versão. Se você não atualiza o gateway há um mês ou mais, convém instalar a versão mais recente do gateway e verificar se o problema pode ser reproduzido.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Erro: falha ao adicionar usuário ao grupo. (-2147463168 PBIEgwService Performance Log Users)

Você poderá obter esse erro se tentar instalar o gateway em um controlador de domínio, o que não tem suporte. Implante o gateway em um computador que não seja um controlador de domínio.

## <a name="tools"></a>Ferramentas
### <a name="collect-logs-from-the-gateway-configurer"></a>Coletar logs do configurador de gateway

É possível coletar vários logs para o gateway. Sempre comece com os logs!

#### <a name="installer-logs"></a>Logs do instalador

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Logs de configuração

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Logs de serviço do gateway corporativo

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Logs de eventos

Você pode encontrar os logs do Gateway de Gerenciamento de Dados e do PowerBIGateway em **Logs de Aplicativos e Serviços**.

### <a name="fiddler-trace"></a>Rastreamento do Fiddler

[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik que monitora o tráfego HTTP. Você pode ver esse tráfego com o serviço do Power BI da máquina cliente. Esse serviço pode mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Próximas etapas
    
* [Conectar-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md)
* [Recursos de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores de Aplicativos Lógicos do Azure](../connectors/apis-list.md)

