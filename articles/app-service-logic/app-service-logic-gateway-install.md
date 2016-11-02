<properties
   pageTitle="Instalação de Aplicativos Lógicos no gateway de dados local | Microsoft Azure"
   description="Informações sobre como instalar o gateway de dados local para uso em um Aplicativo Lógico."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>


# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Instalar gateway de dados local para Aplicativos Lógicos

## <a name="installation-and-configuration"></a>Instalação e Configuração

### <a name="prerequisites"></a>Pré-requisitos

Mínimo:

* .NET 4.5 Framework
* Versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

Recomendável:

* CPU de 8 núcleos
* Memória de 8 GB
* Versão de 64 bits do Windows 2012 R2 (ou posterior)

Considerações relacionadas:

* Você não pode instalar um gateway em um controlador de domínio.
* Você não deve instalar o gateway em um computador como um laptop, que pode ser desligado, entrar suspensão ou não estar conectado à Internet, pois o gateway não pode ser executado nessas circunstâncias. Além disso, o desempenho do gateway pode ser reduzido em uma rede sem fio.

### <a name="install-a-gateway"></a>Instale um gateway

Você pode obter o [instalador para o gateway de dados local aqui](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Especifique **Gateway de dados local** como o modo, entre com sua conta corporativa ou escolar e, em seguida, configure um novo gateway ou migre, restaure ou assuma um gateway existente.

* Para configurar um gateway, digite um **nome** para ele e uma **chave de recuperação**, e, em seguida, clique ou toque em **Configurar**.

    Especifique uma chave de recuperação que contém pelo menos oito caracteres e guarde-a em um local seguro. Você precisará dessa chave para migrar, restaurar ou assumir seu gateway.

* Para migrar, restaurar ou assumir um gateway existente, forneça a chave de recuperação que foi especificada quando o gateway foi criado.

### <a name="restart-the-gateway"></a>Reinicie o gateway

O gateway é executado como um serviço do Windows e, assim como acontece com qualquer outro serviço do Windows, você pode iniciá-lo e pará-lo de várias maneiras. Por exemplo, você pode abrir um prompt de comando com permissões elevadas no computador onde o gateway está em execução e executar um destes comandos:

* Para parar o serviço, execute este comando:

    `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configure um firewall ou proxy

Para saber como fornecer informações de proxy para o gateway, consulte [Definir configurações de proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Você pode verificar se o firewall ou proxy pode estar bloqueando conexões executando o comando a seguir em um prompt do PowerShell. Ele testará a conectividade do Barramento de Serviço do Azure. O comando apenas testa a conectividade de rede e não está relacionado ao serviço de nuvem do servidor ou ao gateway. Ele ajudará a determinar se seu computador pode acessar a Internet.

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

Para uma verificação completa, substitua os valores de **ComputerName** e **Porta** por aqueles listados em [Configurar portas](#configure-ports) mais adiante neste tópico.

O firewall também pode estar bloqueando as conexões que o Barramento de Serviço do Azure cria para data centers do Azure. Se esse é o caso, o ideal é colocar na lista de permissões (desbloquear) todos os endereços IP da região para desses data centers. Você pode obter uma lista de [endereços IP do Azure aqui](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configure portas

O gateway cria uma conexão de saída para o Barramento de Serviço do Azure. Ele se comunica nas portas de saída: TCP 443 (padrão), 5671, 5672, 9350 a 9354. O gateway não exige portas de entrada.

Saiba mais sobre [soluções híbridas](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMES DE DOMÍNIO | PORTAS DE SAÍDA | DESCRIÇÃO |
| ----- | ------ | ------ |
| *.analysis.windows.net | 443 | HTTPS |
| *.login.windows.net | 443 | HTTPS |
| *.servicebus.windows.net |5671-5672 | Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net | 443, 9350-9354 | Ouvintes de Retransmissão do Barramento de Serviço por meio de TCP (requer 443 para aquisição de token de Controle de Acesso) |
| *.frontend.clouddatahub.net | 443 | HTTPS |
| *.core.windows.net | 443 | HTTPS |
| login.microsoftonline.com | 443 | HTTPS |
| *.msftncsi.com | 443 | Usado para testar a conectividade com a Internet se o gateway não poder ser acessado pelo serviço do Power BI. |

Se precisar colocar endereços IP na lista de permissões em vez de domínios, você poderá baixar e usar a [lista de intervalos de IP de Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Em alguns casos, as conexões de Barramento de Serviço do Azure serão realizada com o endereço IP em vez dos nomes de domínio totalmente qualificados.

### <a name="sign-in-account"></a>Conta de logon

Os usuários entrarão com uma conta corporativa ou escolar. Essa é a conta da sua organização. Se você se inscreveu para uma oferta do Office 365 e não forneceu seu email corporativo real, ele pode ser do tipo jeff@contoso.onmicrosoft.com. Sua conta, em um serviço de nuvem, é armazenada em um locatário no AAD (Azure Active Directory). Na maioria dos casos, o UPN da sua conta do AAD corresponderá ao endereço de email.

### <a name="windows-service-account"></a>Conta de Serviço do Windows

O gateway de dados local é configurado para usar NT SERVICE\PBIEgwService para as credenciais de logon de serviço do Windows. Por padrão, ela tem o direito de Log como um serviço. Ela está no contexto do computador no qual você está instalando o gateway.

Ela não é a conta usada para se conectar a fontes de dados locais ou à conta corporativa ou escolar com a qual você entra em serviços de nuvem.

##<a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="general"></a>Geral

**Pergunta**: A quais fontes de dados o gateway oferece suporte?<br/>
**Resposta**: No momento em que este artigo foi escrito, apenas o SQL Server.

**Pergunta**: preciso ter um gateway para fontes de dados na nuvem, como o SQL Azure? <br/>
**Resposta**: Não. Um gateway conecta-se apenas a fontes de dados locais.

**Pergunta**: Qual é o nome real do serviço do Windows?<br/>
**Resposta**: Em Serviços, o gateway é chamado de Serviço de Gateway Corporativo do Power BI.

**Pergunta**: existem conexões de entrada para o gateway da nuvem? <br/>
**Resposta**: Não. O gateway usa conexões de saída para o Barramento de Serviço do Azure.

**Pergunta**: e se eu bloquear conexões de saída? O que preciso abrir? <br/>
**Resposta**: Verifique as portas e os hosts que o gateway usa.


**Pergunta**: o gateway precisa ser instalado no mesmo computador que a fonte de dados? <br/>
**Resposta**: Não. O gateway será conectado à fonte de dados usando as informações de conexão que foram fornecidas. Considere o gateway como um aplicativo de cliente nesse sentido. Ele apenas precisa ser capaz de se conectar ao nome do servidor que foi fornecido.


**Pergunta**: qual é a latência para execução de consultas para uma fonte de dados do gateway? Qual é a melhor arquitetura? <br/>
**Resposta**: Para reduzir a latência de rede, instale o gateway o mais próximo possível da fonte de dados. Se puder instalar o gateway na própria fonte de dados, isso minimizará a latência apresentada. Considere também os dada centers. Por exemplo, se o serviço estiver usando o data center do Oeste dos EUA e tiver o SQL Server hospedado em uma VM do Azure, o ideal será ter a VM do Azure também no Oeste dos EUA. Isso minimizará a latência e evitará encargos de saída na VM do Azure.


**Pergunta**: há algum requisito de largura de banda de rede? <br/>
**Resposta**: É recomendável ter uma boa taxa de transferência para a conexão de rede. Cada ambiente é diferente e a quantidade de dados sendo enviados afeta os resultados. Usar a Rota Expressa pode ajudar a garantir um nível de taxa de transferência entre os dada centers locais e do Azure.

Você pode usar um aplicativo de teste de velocidade do Azure de terceiros para avaliar qual é a taxa de transferência.


**Pergunta**: o serviço do Windows do gateway pode ser executado com uma conta do Azure Active Directory? <br/>
**Resposta**: Não. O serviço do Windows deve ter uma conta válida do Windows. Por padrão, ele será executado com o SID de serviço NT SERVICE\PBIEgwService.


**Pergunta**: como os resultados são enviados para a nuvem? <br/>
**Resposta**: Isso é feito por meio do Barramento de Serviço do Azure. Para obter mais informações, veja como ele funciona.


**Pergunta**: onde minhas credenciais são armazenadas? <br/>
**Resposta**: As credenciais que você inserir para uma fonte de dados são armazenadas criptografadas no serviço de nuvem do gateway. Elas são descriptografadas no gateway local.

### <a name="high-availability/disaster-recovery"></a>Alta disponibilidade/recuperação de desastres

**Pergunta**: há planos para habilitar cenários de alta disponibilidade com o gateway? <br/>
**Resposta**: Isso está em nossos planos, mas ainda não temos um cronograma.


**Pergunta**: quais opções estão disponíveis para recuperação de desastres? <br/>
**Resposta**: Você pode usar a chave de recuperação para restaurar ou mover um gateway. Ao instalar o gateway, especifique a chave de recuperação.


**Pergunta**: qual é o benefício da chave de recuperação? <br/>
**Resposta**: Ela fornece uma maneira de migrar ou recuperar as configurações do gateway após um desastre.

### <a name="troubleshooting"></a>Solucionar problemas

**Pergunta**: onde estão os logs do gateway? <br/>
**Resposta**: Consulte a seção Ferramentas mais adiante neste tópico.


**Pergunta**: como ver quais consultas estão sendo enviadas à fonte de dados local? <br/>
**Resposta**: Você pode habilitar o rastreamento de consultas, que incluirá as consultas que estão sendo enviadas. Lembre-se de alterá-lo para o valor original quando terminar de solucionar o problema. Deixar o rastreamento de consultas habilitado fará com que os logs sejam maiores.

Você também pode examinar ferramentas de rastreamento de consultas disponibilizadas por sua fonte de dados. Por exemplo, você pode usar o Extended Events ou o SQL Profiler para SQL Server e Analysis Services.

## <a name="how-the-gateway-works"></a>Como funciona o gateway

Quando um usuário interage com um elemento que está conectado a uma fonte de dados local:

1. O serviço de nuvem cria uma consulta, juntamente com as credenciais criptografadas para a fonte de dados e envia a consulta para a fila de processamento do gateway.
1. O serviço analisa a consulta e envia a solicitação para o Barramento de Serviço do Azure.
1. O gateway de dados local sonda o Barramento de Serviço do Azure para verificar solicitações pendentes.
1. O gateway obtém a consulta, descriptografa as credenciais e conecta-se às fontes de dados com essas credenciais.
1. O gateway envia a consulta à fonte de dados para execução.
1. Os resultados são enviados da fonte de dados para o gateway e, em seguida, para o serviço de nuvem. O serviço usa os resultados.

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="update-to-the-latest-version"></a>Obtenha a versão mais recente

Muitos problemas podem surgir quando a versão do gateway está desatualizada.  É uma boa prática geral garantir que sua versão seja sempre a mais recente.  Se você não atualiza o gateway há um mês ou mais, convém instalar a versão mais recente do gateway e verificar se o problema pode ser reproduzido.

### <a name="error:-failed-to-add-user-to-group.-(-2147463168-pbiegwservice-performance-log-users-)"></a>Erro: falha ao adicionar usuário ao grupo. (-2147463168 PBIEgwService Performance Log Users )

Você poderá receber esse erro se estiver tentando instalar o gateway em um controlador de domínio, que não tem suporte. Será preciso implantar o gateway em uma máquina que não seja um controlador de domínio.

## <a name="tools"></a>Ferramentas

### <a name="collecting-logs-from-the-gateway-configurator"></a>Coletando logs do configurador do gateway

É possível coletar vários logs para o gateway. Sempre comece com os logs!

#### <a name="installer-logs"></a>Logs do instalador

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Logs de configuração

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Logs de serviço do gateway corporativo

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Logs de eventos

Os logs do Gateway de Gerenciamento de Dados e do PowerBIGateway se encontram em **Logs de Aplicativos e Serviços**.

### <a name="fiddler-trace"></a>Rastreamento do Fiddler

[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik que monitora o tráfego HTTP.  Você pode ver as movimentações com o serviço do Power BI pelo computador cliente. Isso pode mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Próximas etapas
- [Criar uma conexão local com Aplicativos Lógicos](app-service-logic-gateway-connection.md)
- [Recursos de integração corporativa](app-service-logic-enterprise-integration-overview.md)
- [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)


<!--HONumber=Oct16_HO2-->


