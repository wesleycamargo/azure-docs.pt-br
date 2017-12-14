---
title: "Monitorar os serviços e os aplicativos do Azure que usam o Grafana | Microsoft Docs"
description: "Encaminhe dados do Azure Monitor e do Application Insights para que você possa exibi-los no Grafana."
services: monitoring-and-diagnostics
keywords: 
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: article
ms.service: monitoring-and-diagnostics
ms.openlocfilehash: c189e67c481239a8a68f2e2b30d05bb615cfa24e
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorar os serviços do Azure no Grafana
Também monitore serviços e aplicativos do Azure no [Grafana](https://grafana.com/) usando o [plug-in de fonte de dados do Azure Monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). O plug-in reúne dados de desempenho do aplicativo coletados pelo SDK do Application Insights, bem como dados de infraestrutura fornecidos pelo Azure Monitor. Em seguida, exiba esses dados no painel do Grafana.

Atualmente, o plug-in está em versão prévia.

Use as etapas a seguir para configurar um servidor do Grafana por meio do Azure Marketplace e criar painéis para métricas no Azure Monitor e Application Insights.

## <a name="set-up-a-grafana-instance"></a>Configurar uma instância do Grafana
1. Acesse o Azure Marketplace e escolha Grafana da Grafana Labs.

2. Preencha os nomes e os detalhes. Crie um novo grupo de recursos. Acompanhe os valores escolhidos para o nome de usuário da VM, senha da VM e senha do administrador do servidor do Grafana.  

3. Escolha um tamanho de VM e uma conta de armazenamento.

4. Configure as definições de configuração de rede.

5. Exiba o resumo e selecione **Criar** depois de aceitar os termos de uso.

## <a name="log-in-to-grafana"></a>Fazer logon no Grafana
1. Após a conclusão da implantação, selecione **Ir para o Grupo de Recursos**. Você verá uma lista de recursos recém-criados. 

    ![Objetos do grupo de recursos do Grafana](.\media\monitor-how-to-grafana\grafana1.png) 

    Se você selecionar o grupo de segurança de rede (*grafana-nsg*, nesse caso), poderá ver que a porta 3000 é usada para acessar o servidor do Grafana. 

2. Volte para a lista de recursos e selecione **Endereço IP público**. Usando os valores encontrados nessa tela, digite *http://<IP address>:3000* ou *<DNSName>:3000* no navegador. Você deverá ver uma página de logon para o servidor do Grafana recém-criado.
    
    ![Tela de logon do Grafana](.\media\monitor-how-to-grafana\grafana2.png) 

3. Faça logon com o nome de usuário como *admin* e a senha de administrador do servidor do Grafana criada anteriormente. 

## <a name="configure-data-source-plugin"></a>Configurar o plug-in de fonte de dados

Depois de fazer logon com êxito, você deverá ver que o plug-in de fonte de dados do Azure Monitor já está incluído.

![Grafana mostra plug-in do Azure Monitor](.\media\monitor-how-to-grafana\grafana3.png) 

1. Selecione **Adicionar fonte de dados** para configurar o Azure Monitor e o Application Insights. 
    
2. Escolha um nome para a fonte de dados e selecione **Azure Monitor** como a fonte de dados na lista suspensa.
    
    
## <a name="create-a-service-principal"></a>Criar uma entidade de serviço 

O Grafana usa uma entidade de serviço do Azure Active Directory para se conectar às APIs do Azure Monitor e coletar dados de métricas. Você deve criar uma entidade de serviço para gerenciar o acesso aos recursos do Azure.

1. Consulte [estas instruções](../azure-resource-manager/resource-group-create-service-principal-portal.md) para criar uma entidade de serviço. Copie e salve a ID do locatário, a ID do cliente e um segredo do cliente.

2. Consulte [Atribuir aplicativo à função](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) para atribuir a função de leitor ao aplicativo Azure Active Directory.     

3. Se você usar o Application Insights, também poderá incluir a API do Application Insights e a ID do aplicativo para coletar métricas baseadas no Application Insights. Para obter mais informações, consulte [Obtendo sua chave de API e a ID do Aplicativo](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Depois de inserir todas essas informações, selecione **Salvar** e o Grafana testará a API. Você deverá ver uma mensagem semelhante à mostrada a seguir.  

    ![Grafana mostra plug-in do Azure Monitor](.\media\monitor-how-to-grafana\grafana4.png) 
    
## <a name="build-a-grafana-dashboard"></a>Criar um painel do Grafana

1. Acesse a Página Inicial e selecione **Novo Painel**.

2. No novo painel, selecione o **Gráfico**. Você pode tentar outras opções de gráficos, mas este artigo usa *Gráfico* como exemplo. 

    ![Novo Painel do Grafana](.\media\monitor-how-to-grafana\grafana5.png) 

3. Um gráfico em branco é mostrado no painel. 

4. Clique no título do painel e selecione **Editar** para inserir os detalhes dos dados que você deseja plotar nesse gráfico.
    
5. Depois de selecionar todas as VMs corretas, comece a exibir as métricas no painel. 

Veja a seguir um painel simples com dois gráficos. O gráfico à esquerda mostra o percentual de CPU de duas VMs. O gráfico à direita mostra as transações em uma conta de Armazenamento do Azure discriminadas pelo tipo de API de Transação.
    
![Exemplo de dois gráficos do Grafana](.\media\monitor-how-to-grafana\grafana6.png) 
    

## <a name="optional-create-dashboard-playlists"></a>Opcional: Criar playlists do painel

Um dos muitos recursos úteis do Grafana é a playlist do painel. Você pode criar vários painéis e adicioná-los à playlist configurando um intervalo a ser mostrado para cada painel. Selecione **Reproduzir** para ver os painéis serem percorridos. Talvez você deseje exibi-los em um monitor grande de parede para fornecer um “quadro de status” para o grupo. 
    
![Exemplo de playlist do Grafana](.\media\monitor-how-to-grafana\grafana7.png) 


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcional: Monitorar suas métricas personalizadas no mesmo servidor do Grafana

Também instale o Telegraf e o InfluxDB para coletar e plotar as métricas personalizadas e baseadas em agente na mesma instância do Grafana. Há muitos plug-ins de fonte de dados que você pode usar para reunir essas métricas em um painel. 
    
Também reutilize essa configuração para incluir métricas do servidor do Prometheus. Use o plug-in de fonte de dados do Prometheus na galeria de plug-ins do Grafana.
    
Estes são bons artigos de referência sobre como usar o Telegraf, InfluxDB, Prometheus e Docker
 - [Como monitorar as métricas do sistema com a Pilha TICK no Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitorar as métricas de recurso do Docker com o Grafana, InfluxDB e Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Uma solução de monitoramento para hosts, contêineres e serviços em contêineres do Docker](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Esta é uma imagem de um painel completo do Grafana que contém métricas do Azure Monitor e do Application Insights.
![Métricas de exemplo do Grafana](.\media\monitor-how-to-grafana\grafana8.png) 


## <a name="clean-up-resources"></a>Limpar recursos

Você é cobrado quando as VMs são executadas, independentemente de você estar usando ou não as VMs. Para evitar incorrer em encargos adicionais, limpe o grupo de recursos criado neste artigo. 

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e, em seguida, em **Grafana**. 
2. Na página do grupo de recursos, clique em **Excluir**, digite **Grafana** na caixa de texto e, em seguida, clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas
* [Visão geral das métricas do Azure Monitor](monitoring-overview-metrics.md)


