---
title: Ferramentas de ciência de dados e aprendizado de máquina – Azure | Microsoft Docs
description: Saiba mais sobre as ferramentas e estruturas de aprendizado de máquina pré-instaladas na Máquina Virtual de Ciência de Dados.
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: e8876306e4ffbd0fa9a8aafc6d5d757fd3c9c614
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847358"
---
# <a name="machine-learning-and-data-science-tools"></a>Ferramentas de ciência de dados e aprendizado de máquina
As Máquinas Virtuais de Ciência de Dados têm um avançado conjunto de ferramentas e bibliotecas para ML (aprendizado de máquina) disponíveis em linguagens populares como Python, R e Julia. 

A seguir, estão algumas bibliotecas e ferramentas de ML em Máquinas Virtuais de Ciência de Dados. 

## <a name="azure-machine-learninghttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>SDK do [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml)
|    |           |
| ------------- | ------------- |
| O que é?   |   O Azure Machine Learning é um serviço de nuvem que você pode usar para desenvolver e implantar modelos de ML. É possível acompanhar os modelos na medida em que você cria, treina, dimensiona e gerencia usando o SDK do Python. Implante modelos como contêineres e execute-os na nuvem, no local ou no Azure IoT Edge.   |
| Edições com suporte     | Windows (ambiente do conda: AzureML), Linux (ambiente do conda: py36)    |
| Usos típicos      | Plataforma de ML geral      |
| Como é configurado ou instalado?      |  Instalada com o suporte a GPU   |
| Como usá-lo ou executá-lo      | Como o SDK do Python e CLI do Azure. Ative para o ambiente conda `AzureML` na edição do Windows *ou* para `py36` na edição do Linux.      |
| Link para exemplos      | Exemplos Jupyter Notebooks estão incluídos no diretório `AzureML` em notebooks.  |
| Ferramentas relacionadas      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| O que é?   |    O XGBoost é uma biblioteca de gradient boosting (GBDT, GBRT ou GBM) rápida, portátil e distribuída para Python, R, Java, Scala, C++ e more. Executa em um único computador, Hadoop e Spark.    |
| Edições com suporte     | Windows, Linux     |
| Usos típicos      | Biblioteca ML geral      |
| Como é configurado ou instalado?      |  Instalada com o suporte a GPU   |
| Como usá-lo ou executá-lo      | Como biblioteca Python (2.7 e 3.5), pacote R e em ferramenta de linha de comando do caminho (`C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows, `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Links para exemplos      | Exemplos são incluídos na VM, em `/dsvm/tools/xgboost/demo` no Linux e em `C:\dsvm\tools\xgboost\demo` no Windows.   |
| Ferramentas relacionadas      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| O que é?   |   Vowpal Wabbit (também conhecido como "VW") é uma biblioteca de sistema de aprendizado fora do núcleo rápida de software livre.    |
| Edições com suporte     | Windows, Linux     |
| Usos típicos      | Biblioteca ML geral      |
| Como é configurado ou instalado?      |  Windows--msi installer, Linux--apt-get |
| Como usá-la ou executá-la      | Como uma ferramenta de linha de comando no caminho (`C:\Program Files\VowpalWabbit\vw.exe` no Windows, `/usr/bin/vw` no Linux)    |
| Link para exemplos      | [Exemplos de VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Weka é uma coleção de algoritmos de ML para tarefas de mineração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados do seu próprio código Java. Weka contém ferramentas para o pré-processamento, classificação, regressão, clustering, regras de associação e visualização de dados. |
| Edições com suporte     | Windows, Linux     |
| Usos típicos      | Ferramenta de ML geral     |
| Como usá-la ou executá-la      | No Windows, pesquise Weka no menu Iniciar. No Linux, entre com X2Go e, em seguida, vá para **Aplicativos** > **Desenvolvimento** > **Weka**. |
| Link para exemplos      | [Exemplos de Weka](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas      |LightGBM, Rattle, XGBoost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Rattle é uma interface gráfica de usuário para mineração de dados usando R.   |
| Edições com suporte     | Windows, Linux     |
| Usos típicos      | Ferramenta de mineração de dados da Interface do Usuário geral para R    |
| Como usá-lo ou executá-lo      | Ferramenta da interface do usuário. No Windows, inicie um prompt de comando, execute R e, em seguida, dentro de R, execute `rattle()`. No Linux, conecte-se ao X2Go, inicie um terminal, execute R e, em seguida, dentro de R, execute `rattle()`. |
| Link para exemplos      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| O que é?   | O LightGBM é uma estrutura de gradient boosting (GBDT, GBRT, GBM ou MART) rápida, distribuída e de alto desempenho com base em algoritmos de árvore de decisão. É usado para pontuação, classificação e muitas outras tarefas de ML.    |
| Versões com suporte      | Windows, Linux    |
| Usos típicos      | Estrutura de aumento de gradiente de finalidade geral      |
| Como é configurado ou instalado?      | No Windows, LightGBM é instalado como um pacote do Python. No Linux, o executável de linha de comando está em `/opt/LightGBM/lightgbm`, o pacote R está instalado e pacotes Python estão instalados.     |
| Link para exemplos      | [Guia LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| O que é?   | H2O é uma plataforma de AI de software livre com suporte para ML em memória, distribuído, rápido e escalonável.  |
| Versões com suporte      | Linux   |
| Usos típicos      | Uso geral de ML escalonável e distribuído   |
| Como é configurado ou instalado?      | O H2O é instalado em `/dsvm/tools/h2o`.      |
| Como usá-lo ou executá-lo      | Conecte-se à VM usando o X2Go. Inicie um novo terminal e execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Em seguida, inicie um navegador da Web e conecte-se ao `http://localhost:54321`.      |
| Link para exemplos      | Os exemplos estão disponíveis na VM em Jupyter no diretório `h2o`.      |
| Ferramentas relacionadas      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Há várias outras bibliotecas de ML em Máquinas Virtuais de Ciência de Dados como o popular pacote `scikit-learn`, fornecido como parte da distribuição do Anaconda Python instalada em Máquinas Virtuais de Ciência de Dados. Para verificar a lista de pacotes disponíveis em Python, R e Julia, execute os respectivos gerenciadores de pacotes.
