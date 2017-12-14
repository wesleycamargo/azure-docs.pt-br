---
title: "Notas de versão do Azure ML Workbench para o sprint 2 de dezembro de 2017"
description: "Este documento fornece detalhes das atualizações para a versão do sprint 2 do Azure ML"
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: 6fede727a36b0f3243975bf23df2ef53baf1b15c
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2017
---
# <a name="sprint-2---december-2017"></a>Sprint 2 - Dezembro de 2017 

#### <a name="version-number-01171115263"></a>Número da versão: 0.1.1711.15263

>Veja como você pode [localizar o número da versão](https://docs.microsoft.com/azure/machine-learning/preview/known-issues-and-troubleshooting-guide).

Bem-vindo à terceira atualização do Azure Machine Learning Workbench. Esta atualização inclui melhorias no aplicativo workbench, a CLI (Interface de Linha de Comando) e os serviços de back-end. Agradecemos imensamente por nos enviar sorrisos e rostos tristes. Muitas das atualizações a seguir foram feitas como resultado direto de seus comentários. 

## <a name="notable-new-features"></a>Novos recursos importantes
- [Suporte para SQL Server e Azure SQL DB como uma fonte de dados](https://docs.microsoft.com/azure/machine-learning/preview/data-prep-appendix2-supported-data-sources#types) 
- [Aprendizagem profunda no Spark com suporte de GPU utilizando MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Todos os contêineres AML são compatíveis com os dispositivos do Azure IoT Edge quando implantados (sem etapas extras necessárias)](http://aka.ms/aml-iot-edge-blog)
- Lista de modelos registrados e exibições de detalhes disponíveis no Portal do Azure
- Acessando destinos de computação utilizando autenticação baseada em chave SSH, além de acesso baseado em senha/nome de usuário. 
- Novo Inspetor de Frequência de Padrão na experiência de preparação de dados. 

## <a name="detailed-updates"></a>Atualizações detalhadas
A seguir, está uma lista de atualizações detalhadas em cada área de componente do Azure Machine Learning neste sprint.

### <a name="installer"></a>Instalador
- O instalador pode se atualizar automaticamente, de modo que as correções de bugs e novos recursos possam ser suportados sem que o usuário tenha que reinstalá-lo

### <a name="workbench-authentication"></a>Autenticação do Workbench
- Correções múltiplas para o sistema de autenticação. Informe-nos caso ainda esteja com problemas de logon.
- Alterações de UI que facilitam a localização das configurações do Gerenciador de Proxy.

### <a name="workbench"></a>Workbench
- A exibição de arquivo somente leitura agora tem plano de fundo azul claro
- Botão Editar movido para a direita para torná-lo melhor detectável.
- Os formatos de arquivo "dsource", "dprep" e "ipynb" agora podem ser renderizados em formato de texto
- O Workbench agora possui uma nova experiência de edição que guia os usuários para o uso de IDEs externos para editar scripts e use o Workbench somente para editar tipos de arquivos com uma experiência de edição avançada (como Notebooks, Fontes de dados, Pacotes de preparação de dados)
- Carregar a lista de espaços de trabalho e projetos aos quais o usuário tenha acesso, agora está significativamente mais rápido

### <a name="data-preparation"></a>Preparação dos dados 
- Um Inspector de Frequência de Padrão para exibir os padrões de sequência de cadeia de caracteres em uma coluna. Também é possível filtrar seus dados utilizando esses padrões. Isso mostra uma exibição semelhante ao inspetor de Contagem de Valores. A diferença é que a Frequência de Padrão mostra as contagens dos padrões exclusivos dos dados, em vez das contagens de dados exclusivos. Além disso, também é possível filtrar dentro ou fora todas as linhas que se encaixam em um determinado padrão.

![Imagem do inspetor de frequência de padrão no Número do Produto](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- Melhorias de desempenho ao recomendar casos de borda para revisão na transformação 'derivar colunas por exemplo'

- [Suporte para SQL Server e Azure SQL DB como uma fonte de dados](https://docs.microsoft.com/azure/machine-learning/preview/data-prep-appendix2-supported-data-sources#types) 

![Imagem da criação de uma nova fonte de dados do SQL Server](media/release-notes-sprint-2/sql-server-data-source.png)

- Habilitado exibição "Visão rápida" das contagens de linhas e colunas

![Imagem da visão rápida da contagem de coluna da linha](media/release-notes-sprint-2/row-col-count.png)

- A preparação de dados está habilitada em todos os contextos de computação
- As fontes de dados que utilizam um banco de dados do SQL Server estão habilitadas em todos os contextos de computação
- As colunas de grade de preparação de dados podem ser filtradas pelo tipo de dados
- Problema corrigido com a conversão de várias colunas até a data
- Problema corrigido, o qual o usuário poderia selecionar a coluna de saída como fonte em Derivar Coluna por Exemplo, se o usuário alterasse o nome da coluna de saída no modo avançado.

### <a name="job-execution"></a>Execução do trabalho
Agora é possível criar e acessar um destino de computação tipo cluster ou remotedocker utilizando a autenticação baseada em chave SSH, seguindo essas etapas:
- Anexar um destino de computação utilizando o seguinte comando na CLI

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] -k (ou a opção --use-azureml-ssh-key) no comando especifica para gerar e usar a chave SSH.

- O Azure ML Workbench irá gerar uma chave pública e emitirá isso em seu console. Faça logon no destino de computação utilizando o mesmo nome de usuário e acrescente o arquivo ~/.ssh/authorized_keys com essa chave pública.

- É possível pode preparar esse destino de computação e utilizá-lo para execução e o Azure ML Workbench usará essa chave para autenticação.  

Para obter mais informações sobre como criar alvos de computação, consulte [Configurando o Serviço de Experimentação do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/preview/experimentation-service-configuration)

### <a name="visual-studio-tools-for-ai"></a>Ferramentas do Visual Studio para IA
- Adicionado suporte para [Ferramentas do Visual Studio para AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Interface de Linha de Comando (CLI)
- O comando `az ml datasource create` adicionado permite criar um arquivo de fonte de dados a partir da linha de comando

### <a name="model-management-and-operationalization"></a>Gerenciamento de modelos e operacionalização
- [Todos os contêineres do AML são compatíveis com os dispositivos do Azure IoT Edge quando operacionalizados (sem etapas extras necessárias)](http://aka.ms/aml-iot-edge-blog) 
- Aprimoramentos de mensagens de erro na CLI o16n
- Correções de bug no portal de gerenciamento de modelo UX  
- Capitalização consistente para atributos de gerenciamento de modelo na página de detalhes
- O tempo limite de chamadas em tempo real é definido como 60 segundos
- Lista de modelos registrados e exibições de detalhes disponíveis no Portal do Azure

![detalhes do modelo no portal](media/release-notes-sprint-2/model-list.jpg)

![visão geral do modelo no portal](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Aprendizagem profunda no Spark com [Suporte de GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Suporte para modelos Gerenciador de Recursos para fácil implantação de recursos
- Suporte para o ecossistema de SparklyR
- [Integração de AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Projetos de exemplo
- Amostras [Íris](https://github.com/Azure/MachineLearningSamples-Iris) e [MMLSpark](https://github.com/Azure/mmlspark) atualizadas com a nova versão do SDK do ML do Azure

## <a name="breaking-changes"></a>ALTERAÇÕES SIGNIFICATIVAS
- Promoveu `--type` a alternar em `az ml computetarget attach` para um subcomando. 

    - `az ml computetarget attach --type remotedocker` agora é `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` agora é `az ml computetarget attach cluster`
