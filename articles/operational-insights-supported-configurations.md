<properties 
   pageTitle="Configurações com suporte do Insights Operacionais"
   description="Saiba mais sobre as configurações necessárias do Insights Operacionais"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" /> <tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/30/2015"
   ms.author="banders" />

# Configurações com suporte do Insights Operacionais

[AZURE.INCLUDE [operational-insights-note-moms](../includes/operational-insights-note-moms.md)]

O que você precisa para usar o Insights Operacionais? Confira as informações a seguir para se preparar para o Insights Operacionais.


## Configuração do System Center 2012 Operations Manager

Você pode usar o Insights Operacionais como um serviço anexado no Operations Manager no System Center 2012 R2 ou no System Center 2012 SP1 R2. Isso permite que você use o console de Operações no Operations Manager para exibir alertas do Insights Operacionais e as informações de configuração. Quando você usa o Insights Operacionais como um serviço anexado no Operations Manager, o agente se comunica diretamente com o servidor de gerenciamento que, por sua vez, se comunica com o serviço do Insights Operacionais.

Usar o Insights Operacionais como um serviço anexado tem os seguintes pré-requisitos:


- A integração entre o Operations Manager do System Center 2012 SP1 e o Insights Operacionais requer pacotes de gerenciamento atualizados que são incluídos no [Conector do Insights Operacionais para o Operations Manager](https://www.microsoft.com/pt-br/download/details.aspx?id=38199). Você pode baixar e instalar os pacotes de gerenciamento no [Conector do Insights Operacionais para o Operations Manager](https://www.microsoft.com/pt-br/download/details.aspx?id=38199).

- System Center 2012 SP1: Pacote Cumulativo de Atualizações 6 do Operations Manager, embora o Pacote Cumulativo de Atualizações 7 seja preferido. Esta atualização precisa ser aplicada ao servidor de gerenciamento, agentes e console de Operações do Insights Operacionais como um cenário de serviço anexado.

- System Center 2012 R2: Pacote Cumulativo de Atualizações 2 do Operations Manager, embora o Pacote Cumulativo de Atualizações 3 seja preferido. Esta atualização precisa ser aplicada ao servidor de gerenciamento, agentes e console de Operações do Insights Operacionais como um cenário de serviço anexado.

- Para exibir os dados de gerenciamento da capacidade, você deve habilitar a conectividade do Operations Manager com o Virtual Machine Manager (VMM). Para obter informações adicionais sobre como conectar os sistemas, consulte [Como conectar o VMM ao Operations Manager](https://technet.microsoft.com/pt-br/library/hh882396.aspx).

Consulte [Exibir Alertas do Insights Operacionais](http://go.microsoft.com/fwlink/?LinkID=293793) para obter instruções sobre a instalação e configuração.

Se você quiser exibir alertas do Insights Operacionais sobre o SharePoint Server 2010, Lync Server 2013, Lync Server 2010 ou System Center 2012 SP1 - Virtual Machine Manager, precisará configurar uma conta Executar Como para essas cargas de trabalho. Consulte as seguintes informações:


- [Definir a Conta Executar Como para o SharePoint](operational-insights-run-as.md)

- [Definir a Conta Executar Como para o Lync Server](operational-insights-run-as.md)

- [Definir a Conta Executar Como para o Virtual Machine Manager (VMM)](operational-insights-run-as.md)

### Sistemas operacionais do Operations Manager

Os agentes do Operations Manager têm suporte em vários computadores. Consulte [Requisitos do Sistema: Operations Manager do System Center 2012 R2](https://technet.microsoft.com/library/dn249696.aspx) para obter detalhes sobre o suporte do agente.

### Software necessário para o Operations Manager

Para exibir os dados de gerenciamento da capacidade, você deve habilitar a conectividade do Operations Manager com o VMM. Para obter informações adicionais sobre como conectar os sistemas, consulte [Como conectar o VMM ao Operations Manager](https://technet.microsoft.com/pt-br/library/hh882396.aspx).

## Agentes conectando diretamente ao Insights  Operacionais

O agente usado para se conectar diretamente ao serviço é o agente de Monitoramento da Microsoft. Seus requisitos de sistema são listados na página [Centro de Download da Microsoft](https://www.microsoft.com/pt-br/download/details.aspx?id=40316&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True).

## Navegadores

Você pode usar qualquer um dos seguintes navegadores com o Insights Operacionais:

- Windows Internet Explorer 11, Internet Explorer 10, Internet Explorer 9, Internet Explorer 8 ou Internet Explorer 7

- Mozilla Firefox 3.5 ou posterior

Independentemente do navegador usado, você também deve instalar o Microsoft Silverlight 4.

## Tecnologias que você pode analisar

A Avaliação de Configuração do Insights Operacionais analisa as cargas de trabalho a seguir:

- Windows Server 2012 e Microsoft Hyper-V Server 2012

- Windows Server 2008 e Windows Server 2008 R2, inclusive:
    - Active Directory
	- Host Hyper-V
	- Sistema operacional geral

- SQL Server 2012, SQL Server 2008 R2, SQL Server 2008
    - Mecanismo do Banco de Dados do SQL Server

- Microsoft SharePoint 2010

- Microsoft Exchange Server 2010 e Microsoft Exchange Server 2013

- Microsoft Lync Server 2013 e Lync Server 2010

- System Center 2012 SP1 – Virtual Machine Manager

Para o SQL Server, as seguintes edições de 32 e 64 bits têm suporte para a análise:

- SQL Server 2008 e 2008 R2 Enterprise

- SQL Server 2008 e 2008 R2 Standard

- SQL Server 2008 e 2008 R2 Workgroup

- SQL Server 2008 e 2008 R2 Web

- SQL Server 2008 e 2008 R2 Express

Além disso, a edição de 32 bits do SQL Server tem suporte ao ser executada na implementação do WOW64.


<!--HONumber=54-->