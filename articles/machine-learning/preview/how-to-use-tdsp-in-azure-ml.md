---
title: "Estruturar projetos com o Modelo de Processo de Ciência de Dados da Equipe | Microsoft Docs"
description: "Como criar uma instância de modelos de Processo de Ciência de Dados da Equipe (TDSP) no Azure ML que estrutura projetos para colaboração."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 2e03b6bcb1bc5a617234c7801f22f8207a9f5a4e
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="structure-projects-with-team-data-science-process-template"></a>Estruturar projetos com Modelo de Processo de Ciência de Dados da Equipe

Este documento apresenta instruções sobre como criar projetos de ciência de dados no Azure Machine Learning com modelos de TDSP (Processo de Ciência de Dados da Equipe) que estruturam projetos para colaboração e reprodução. 


## <a name="what-is-team-data-science-process"></a>O que é o Processo de Ciência de Dados da Equipe?
O Processo de Ciência de Dados da Equipe é um processo de ciência de dados ágil e iterativo para executar e fornecer soluções de análise avançada. É projetado para melhorar a colaboração e a eficiência das equipes de ciência de dados em organizações empresariais. Ele dá suporte a esses objetivos com quatro componentes principais:

1. Uma definição de [ciclo de vida de ciência de dados](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md) padrão.
2. [Modelos de relatório e documentação de projeto](https://github.com/Azure/Azure-TDSP-ProjectTemplate) de uma estrutura de projeto padronizada
3. Infraestrutura e recursos para execução do projeto, como infraestrutura de computação e armazenamento e repositórios de código.
4. [Ferramentas e utilitários](https://github.com/Azure/Azure-TDSP-Utilities) para tarefas de projeto de ciência de dados, como controle colaborativo de versão e revisão de código, exploração e modelagem de dados e planejamento de trabalho.

Para uma discussão mais completa sobre TDSP, consulte [Visão geral do Processo de Ciência de Dados da Equipe](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/README.md).

## <a name="why-should-you-use-tdsp-structure-and-templates"></a>Por que você deve usar a Estrutura e os Modelos de TDSP?
A padronização da estrutura, do ciclo de vida e da documentação de projetos de ciência de dados são essenciais para facilitar a colaboração eficaz em equipes de ciência de dados. A criação de projetos do Azure Machine Learning com o modelo TDSP oferece uma estrutura para trabalho em equipe coordenado.

Lançamos anteriormente um [repositório do GitHub para a estrutura e os modelos do projeto TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para ajudar a atingir esses objetivos. Mas não foi possível, até agora, instanciar a estrutura TDSP e os modelos em uma ferramenta de ciência de dados. Agora é possível criar um projeto do Azure Machine Learning que instancie os modelos de estrutura e documentação do TDSP. 

## <a name="things-to-note-before-creating-a-new-project"></a>O que observar *antes* de criar um novo projeto
Estes são os itens que você deve observar ou examinar *antes* de criar um novo projeto:
* [Modelo](https://aka.ms/tdspamlgithubrepo) do Azure Machine Learning do TDSP.
* O conteúdo (que não seja o que está na pasta 'docs') deve ser menor que 25 Mb. Consulte a **OBSERVAÇÃO** a seguir.
* A pasta de dados de\_exemplo é somente para arquivos de dados pequenos (menos de 5 Mb) com os quais você pode testar seu código ou fazer o desenvolvimento inicial.
* Armazenar arquivos, como Office Word, PowerPoint etc., pode aumentar substancialmente o tamanho da pasta 'docs'. Aconselhamos que você encontre um Wiki de colaboração, [SharePoint](https://products.office.com/en-us/sharepoint/collaboration) ou outros recursos de colaboração para armazenar esses arquivos.
* Para lidar com arquivos e saídas grandes no Azure Machine Learning, leia [isto](http://aka.ms/aml-largefiles).

> [!NOTE]
> Verifique se arquivos que não readme.md, todo o conteúdo relacionado à documentação (texto, markdowns, imagens, outros arquivos de documento) que NÃO sejam usados durante a execução do projeto estão na pasta chamada 'docs' (todas as letras minúsculas). Esta é uma pasta especial ignorada pela execução do Azure Machine Learning para que o conteúdo nessa pasta não seja copiado para o destino de computação desnecessariamente. Os objetos nesta pasta também não são considerados para o limite de 25 MB para tamanho de projeto, assim, você pode armazenar arquivos de imagem grandes necessários na sua documentação, por exemplo. Eles ainda são acompanhados pelo Git por meio de Histórico de Execução. 

## <a name="instantiating-tdsp-structure-and-templates-from-the-azure-machine-learning-template-gallery"></a>Instanciar a estrutura e os modelos TDSP da Galeria de Modelos do Azure Machine Learning
Para criar um novo projeto com os modelos de estrutura e documentação do Processo de Ciência de Dados da Equipe, conclua os procedimentos a seguir: 

### <a name="click-on-new-project"></a>Clique em "Novo projeto"
Abra o Azure Machine Learning. Em **Projetos** na parte superior esquerda, clique em **+** e selecione **Novo Projeto** para criar um novo projeto.

![Novo Projeto](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="creating-a-new-tdsp-structured-project"></a>Criar um novo projeto estruturado TDSP
Especifique os parâmetros e as informações nas caixas relevantes:

- Nome do projeto
- Diretório do projeto
- Descrição do projeto
- Um caminho do repositório Git vazio
- Nome do espaço de trabalho

Então, na caixa de *Pesquisa*, digite **TDSP**. Quando a opção **Estruturar um projeto com TDSP** for exibido, clique nela para selecionar o modelo. Em seguida, clique no botão **Criar** para criar o novo projeto com a estrutura TDSP. Se você fornecer um repositório Git vazio durante a criação do projeto (na caixa adequada), então esse repositório será preenchido com a estrutura e o conteúdo do projeto após a criação do projeto.

![Criar um projeto TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Examinar a Estrutura do Projeto TDSP
Depois que o novo projeto for criado, você pode examinar sua estrutura (painel esquerdo na figura abaixo). Contém todos os aspectos da documentação padronizada para compreensão de negócios, estágios do ciclo de vida do TDSP, local de dados, definição e arquitetura de nesse modelo de documentação. Essa estrutura é derivada da estrutura do TDSP publicada [aqui](https://github.com/Azure/Azure-TDSP-ProjectTemplate), com algumas modificações. Por exemplo, vários modelos de documento são mesclados em um markdown, ou seja, [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Estrutura da Pasta de Projeto
O modelo de projeto TDSP contém as pastas de nível superior a seguir:
1. **código**: contém código
2. **docs**: contém a documentação necessária sobre o projeto (por exemplo, arquivos de Markdown, mídia relacionada etc.)
3. **sample_data**: contém **EXEMPLO (pequeno)** dados que podem ser usados para teste ou desenvolvimento inicial. Normalmente, não mais que vários (5) Mbs. Não para conjuntos de dados completos ou grandes.

![Dados de amostra](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="using-the-tdsp-structure-and-templates"></a>Como usar a Estrutura e os Modelos de TDSP
A estrutura e os modelos precisam ser preenchidos com informações específicas do projeto. Você deve preenchê-los com o código e as informações necessárias para executar e entregar seu projeto. O arquivo [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) é um modelo que deve ser modificado diretamente com informações relevantes para seu projeto. Ele vem com um conjunto de perguntas que o ajudam a preencher as informações para cada um dos quatro estágios do [ciclo de vida do Processo de Ciência de Dados da Equipe](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md).

Um exemplo da aparência de uma estrutura de projeto durante a execução ou após a conclusão é apresentado a seguir (painel esquerdo na figura abaixo). Isso é do [Projeto de exemplo de Processo de Ciência de Dados da Equipe: classificar rendas dos dados de censo dos EUA no Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).

![Exemplo de estrutura de projeto](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="documenting-your-project"></a>Como documentar seu projeto
Consulte [Modelos de documentação TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate) para documentar seu projeto. No modelo de documentação TDSP do Azure Machine Learning atual, recomendamos que você inclua todas as informações no arquivo [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). Esse modelo deve ser preenchido com informações específicas do seu projeto. 

Nós também fornecemos outro modelo de [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) para incluir quaisquer informações não incluídas no documento do projeto primário, mas que ainda são úteis de documentar. 

### <a name="example-project-report"></a>Exemplo de relatório de projeto
Um exemplo de relatório de projeto pode ser encontrado [aqui](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliveralbe_docs/ProjectReport.md). Este é o relatório de projeto para o [Projeto de exemplo de Classificação de Renda do EUA](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome), que mostra como o modelo TDSP pode ser instanciado e usado para um projeto de ciência de dados.

## <a name="next-steps"></a>Próximas etapas
Para facilitar a sua compreensão de como a estrutura TDSP e os modelos podem ser usados em projetos de do Azure Machine Learning, podemos fornecer vários exemplos de projetos trabalhados na documentação do Azure Machine Learning.

- Para obter um exemplo de como criar um projeto TDSP no Azure Machine Learning, consulte [Projeto de exemplo do Processo de Ciência de Dados da Equipe: classificar rendas dos dados de censo dos EUA no Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) 
- Para obter um exemplo que use Aprendizado Profundo em NLP em um projeto instanciado por TDSP no Azure Machine Learning, consulte [Reconhecimento de entidade biomédica usando Processamento de Linguagem Natural com Aprendizado Profundo](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)

