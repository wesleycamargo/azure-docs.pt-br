---
title: Perguntas Frequentes do Log Analytics | Microsoft Docs
description: "Respostas para perguntas frequentes sobre o serviço Azure Log Analytics."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 9ee8f4aafcc35e43c4fcba5a3a72b043dd9fc32c
ms.openlocfilehash: 4695669dc20b4b4b90ccdaf4db06df2cfcba2167
ms.lasthandoff: 02/21/2017


---
# <a name="log-analytics-faq"></a>Perguntas frequentes do Log Analytics
Essas Perguntas Frequentes da Microsoft são uma lista de perguntas frequentes sobre o Log Analytics no Microsoft Operations Management Suite (OMS). Se você tiver perguntas adicionais sobre o Log Analytics, vá para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e poste suas perguntas. Alguém da sua comunidade o ajudará a obter respostas. Se uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## <a name="general"></a>Geral
**P. Quais verificações são executadas pelo AD e soluções de Avaliação de SQL?**

R. A consulta a seguir mostra uma descrição de todas as verificações executadas no momento:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados podem então ser exportados para o Excel para análise adicional.

**P: Por que eu vejo algo diferente de *OMS* na Administração do SCOM?**

R: Dependendo do pacote cumulativo de atualizações do SCOM em que você estiver, você poderá ver um nó para *System Center Advisor*, *Insights Operacionais* ou *Log Analytics*.

A atualização de cadeia de caracteres de texto para *OMS* está incluída em um pacote de gerenciamento, que deve ser importado manualmente. Siga as instruções no artigo KB do pacote cumulativo de atualizações SCOM e atualize o console do OMS para ver as atualizações mais recentes no nó do *OMS* .

**P: Há uma versão *local* do OMS?**

R: não. O Log Analytics processa e armazena quantidades de dados muito grandes. Como um serviço de nuvem, o Log Analytics é capaz de escalar verticalmente se necessário e evitar qualquer impacto de desempenho ao seu ambiente.

Além disso, ser um serviço de nuvem significa que você não precisa manter a infraestrutura do Log Analytics funcionando e pode receber correções e atualizações de recursos frequentes.

## <a name="configuration"></a>Configuração
**P. Posso alterar o nome do contêiner de tabela/blob usado para ler do Diagnóstico do Azure (WAD)?**  

R.    Não, isso não é possível no momento, mas está planejado para uma versão futura.

**P. Quais endereços IP os serviços do OMS usam? Como garantir que o firewall permita apenas o tráfego para os serviços do OMS?**  

R. O serviço Log Analytics é criado no Azure e os pontos de extremidade recebem IPs que estão nos [Intervalos de IP de Datacenter do Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653).

Conforme as implantações de serviço são feitas, os endereços IP reais dos serviços do OMS são alterados. Os nomes DNS a permitir através do firewall estão documentados em [Definir as configurações de proxy e firewall no Log Analytics](log-analytics-proxy-firewall.md).

**P. Eu uso a Rota Expressa para me conectar ao Azure. O meu tráfego do Log Analytics usará minha conexão ExpressRoute?**  

R. Os diferentes tipos de tráfego de Rota Expressa estão descritos na [Documentação da Rota Expressa](../expressroute/expressroute-faqs.md#supported-services).

O tráfego para o Log Analytics usa o circuito de Rota Expressa de emparelhamento público.

**P. Há alguma maneira simples e fácil de mover um espaço de trabalho existente do Log Analytics para outra assinatura do Azure/espaço de trabalho do Log Analytics?**  Temos vários espaços de trabalho do OMS do cliente que estávamos testando e avaliando em nossa assinatura do Azure, eles estão sendo movidos para a produção e, portanto, queremos movê-los para sua própria assinatura do Azure/OMS.  

R. O cmdlet `Move-AzureRmResource` permitirá que você mova um espaço de trabalho do Log Analytics, além de uma conta de automação de uma assinatura do Azure para outra. Para obter mais informações, consulte [Move-AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Essa alteração também pode ser feita no portal do Azure.

Você não pode mover dados de um espaço de trabalho do Log Analytics para outro, ou alterar a região na qual os dados do Log Analytics estão armazenados.

**P: como posso adicionar o OMS ao SCOM?**

R: Atualizar para o pacote cumulativo de atualizações mais recente e importar pacotes de gerenciamento permitirá conectar o SCOM ao Log Analytics.

Observe que a conexão do SCOM para o Log Analytics só está disponível para o SCOM 2012 SP1 e posterior.

**P: como posso confirmar que um agente é capaz de se comunicar com o Log Analytics?**

R: Para garantir que o agente possa se comunicar com o OMS, vá para: Painel de Controle, Segurança e Configurações, **Microsoft Monitoring Agent**.

Sob a guia **Azure Log Analytics (OMS)** , procure por uma marca de seleção verde. Um ícone de marca de seleção verde confirma que o agente é capaz de se comunicar com o serviço OMS.

Um ícone de aviso amarelo significa que o agente está tendo problemas de comunicação com o OMS. Uma razão comum é o serviço Microsoft Monitoring Agent foi interrompido e precisa ser reiniciado.

**P: como posso impedir que um agente se comunique com o Log Analytics?**

R: no SCOM, remova o computador da lista de gerenciados pelo OMS. Isso interrompe todas as comunicações por meio do SCOM para esse agente. Para os agentes conectados diretamente ao OMS, você pode impedi-los de se comunicar com o OMS por meio de: Painel de Controle, Segurança e Configurações, **Microsoft Monitoring Agent**.
Em **Azure Log Analytics (OMS)**, remova todos os espaços de trabalho listados.

**P: Por que estou recebendo um erro ao tentar mover meu espaço de trabalho de uma assinatura do Azure para outra?**

R: quando você adiciona uma solução, o Azure cria um recurso na assinatura do Azure em que o espaço de trabalho está contido.

Normalmente, a pessoa adicionando a assinatura é um administrador ou colaborador da *assinatura do Azure*. Ser um administrador ou colaborador no portal do OMS não será suficiente se o usuário também não tiver as mesmas permissões no Portal do Azure para a assinatura do Azure.


## <a name="agent-data"></a>Dados do agente
**P. Que quantidade de dados posso enviar por meio do agente para o Log Analytics? Há uma quantidade máxima de dados por cliente?**  
R. R. O plano gratuito define um limite diário de 500 MB por espaço de trabalho. Os planos standard e premium não têm limite para a quantidade de dados carregados. Como um serviço de nuvem, o Log Analytics em OMS é projetado para escalar verticalmente, de modo automático, até o identificador de volume proveniente de um cliente, mesmo que seja terabytes por dia.

O agente do Log Analytics foi projetado para garantir que ele tenha dimensões reduzidas e faça alguma compactação básica de dados. Um de nossos clientes, na verdade, escreveu um blog sobre os testes que eles executaram em nosso agente e quão impressionados ficaram. O volume de dados vai variar com base nas soluções habilitadas pelos seus clientes. Você pode encontrar informações detalhadas sobre o volume de dados e ver a divisão por solução sob o bloco **Uso** na página de visão geral do OMS.

Para obter mais informações, você pode ler um [blog do cliente](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sobre as dimensões reduzidas do agente OMS.

**P. Quanta largura de banda de rede é usada pelo MMA (Agente de Gerenciamento da Microsoft) ao enviar dados para o Log Analytics?**

R. A largura de banda é uma função na quantidade de dados enviados. Dados são compactados conforme eles são enviados pela rede.

**P. Qual quantidade de dados é enviada por agente?**

R. Isso depende muito:

* das soluções que você tiver habilitado
* do número de logs e contadores de desempenho sendo coletados
* do volume de dados nos logs

O tipo de preço gratuito é uma boa maneira de integrar vários servidores e medir o volume de dados típico. O uso geral é mostrado na página **Uso** .
Para computadores que são capazes de executar o agente WireData, você pode ver quantos dados estão sendo enviados usando a seguinte consulta:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```



## <a name="next-steps"></a>Próximas etapas
* [Introdução ao Log Analytics](log-analytics-get-started.md) para saber mais sobre o Log Analytics e colocá-lo em funcionamento em minutos.

