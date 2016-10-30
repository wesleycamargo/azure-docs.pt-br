<properties 
    pageTitle="Registro em log de serviços Web de Aprendizado de Máquina | Microsoft Azure" 
    description="Saiba como habilitar o registro em log de serviços Web de Aprendizado de Máquina. O registro em log fornece informações adicionais para ajudar a solucionar problemas com as APIs." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>


# <a name="enable-logging-for-machine-learning-web-services"></a>Habilitar o log de serviços Web de Aprendizado de Máquina  

Este documento fornece informações sobre o recurso de registro em log de serviços Web clássicos. Habilitar o log em serviços Web fornece informações adicionais, além de apenas um número de erro e uma mensagem, o que pode ajudar a solucionar suas chamadas para as APIs de Machine Learning.  

Para habilitar o log nos serviços Web no portal clássico do Azure:   

1.  Entre no [portal clássico do Azure](https://manage.windowsazure.com/)
2.  Na coluna de recursos à esquerda, clique em **MACHINE LEARNING**.
3.  Clique em seu espaço de trabalho e, em seguida, em **SERVIÇOS WEB**.
4.  Na lista de serviços Web, clique no nome do serviço Web.
5.  Na lista de pontos de extremidade, clique no nome do ponto de extremidade.
6.  Clique em **CONFIGURAR**.
7.  Defina o **DIAGNOSTICS TRACE LEVEL** como *Erro* ou *Todos* e, em seguida, clique em **SALVAR**.

Para habilitar o registro em log no portal de serviços Web do Azure Machine Learning.

1. Entre no [portal de serviços Web do Azure Machine Learning](https://services.azureml.net).
2. Clique em Classic Web Services.
3.  Na lista de serviços Web, clique no nome do serviço Web.
4.  Na lista de pontos de extremidade, clique no nome do ponto de extremidade.
5.  Clique em **Configurar**.
6.  Defina **Registro em log** como *Erro* ou *Todos*, e, em seguida, clique em **SALVAR**.

## <a name="the-effects-of-enabling-logging"></a>Os efeitos de habilitar o registro em log

Quando o registro em log estiver habilitado, todos os diagnósticos e erros do ponto de extremidade selecionado serão registrados na Conta de Armazenamento do Azure vinculada ao espaço de trabalho do usuário. Você pode ver essa conta de armazenamento no modo de exibição do Painel do portal clássico do Azure (parte inferior da seção Visão Rápida) do seu espaço de trabalho.  

Os logs podem ser exibidos usando qualquer uma das várias ferramentas disponíveis para 'explorar' uma Conta de Armazenamento do Azure. A maneira mais fácil pode ser simplesmente navegar até a Conta de Armazenamento no portal clássico do Azure e, então, clicar em **CONTÊINERES**. Em seguida, você verá um Contêiner chamado **ml-diagnostics**. Esse contêiner armazena todas as informações de diagnóstico para todos os pontos de extremidade do serviço da Web para todos os espaços de trabalho associados a esta Conta de Armazenamento. 
 
## <a name="log-blob-detail-information"></a>Informações detalhadas do log blob

Cada blob no contêiner mantém as informações de diagnóstico para exatamente o seguinte:

-   Uma execução do método Batch-Execution  
-   Uma execução do método Request-Response  
-   Inicialização de um contêiner de Request-Response
  
O nome de cada blob tem um prefixo da seguinte forma: 

{ID do espaço de trabalho}-{ID do serviço Web}-{ID do ponto de extremidade}/{Tipo de log}  

Sendo que Tipo de log assume um dos seguintes valores:  

- lote  
- pontuação/solicitações  
- pontuação/init  


<!--HONumber=Oct16_HO2-->


