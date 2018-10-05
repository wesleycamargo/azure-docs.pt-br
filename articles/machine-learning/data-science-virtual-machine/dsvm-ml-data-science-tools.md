---
title: Ferramentas de ciência de dados e aprendizado de máquina – Azure | Microsoft Docs
description: Ferramentas de ciência de dados e aprendizado de máquina
keywords: ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 9a9dc868c4f22f95ca5027e3c95513d176c69eac
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47392390"
---
# <a name="machine-learning-and-data-science-tools"></a>Ferramentas de ciência de dados e aprendizado de máquina
A DSVM (Máquina Virtual de Ciência de Dados) tem um rico conjunto de ferramentas e bibliotecas para aprendizado de máquina disponível em linguagens populares como Python, R, Julia. 

Aqui estão algumas da ferramentas e bibliotecas de aprendizado de máquina na DSVM. 

## <a name="azure-machine-learning-servicehttpsdocsmicrosoftcomazuremachine-learningserviceoverview-what-is-azure-ml-sdk"></a>SDK do [Serviço Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/overview-what-is-azure-ml)
|    |           |
| ------------- | ------------- |
| O que é?   |   O Azure Machine Learning é um serviço de nuvem que você pode usar para desenvolver e implantar modelos de machine learning.  Você pode acompanhar seus modelos ao criar, treinar, dimensionar e gerenciá-los usando o SDK Python. Implante modelos como contêineres e execute-os na nuvem, localmente ou no IoT Edge.   |
| Edições do DSVM com suporte     | Windows (ambiente do Conda: AzureML), Linux (ambiente do Conda: py36)    |
| Usos típicos      | Plataforma de ML geral      |
| Como é configurado/instalado no DSVM?      |  Instalada com o suporte a GPU   |
| Como usar/executar?      | Com o SDK Python e a AZ CLI (ferramenta de linha de comando do Azure). Ative para o ambiente do Conda `AzureML` na edição do Windows ou para o `py36` na edição do Linux.      |
| Links para exemplos      | Exemplos Jupyter Notebooks estão incluídos no diretório `AzureML` em blocos de anotações  |
| Ferramentas relacionadas ao DSVM      | Visual Studio Code, Jupyter   |

## <a name="xgboost"></a>XGBoost 
|    |           |
| ------------- | ------------- |
| O que é?   |    Biblioteca de aumento de gradiente rápida, portátil e distribuída (GBDT, GBRT ou GBM) para Python, R, Java, Scala, C++ e outros. É executada em único computador, Hadoop, Spark    |
| Edições do DSVM com suporte     | Windows, Linux     |
| Usos típicos      | Biblioteca ML geral      |
| Como é configurado/instalado no DSVM?      |  Instalada com o suporte a GPU   |
| Como usar/executar?      | Como Biblioteca Python (2.7 e 3.5), pacote R e em ferramenta de linha de comando do caminho (`C:\dsvm\tools\xgboost\bin\xgboost.exe` para Windows, `/dsvm/tools/xgboost/xgboost` para Linux)    |
| Links para exemplos      | Exemplos são incluídos na VM, em `/dsvm/tools/xgboost/demo` no Linux e em `C:\dsvm\tools\xgboost\demo` no Windows   |
| Ferramentas relacionadas ao DSVM      | LightGBM, MXNet   |



## <a name="vowpal-wabbit"></a>Vowpal Wabbit
|    |           |
| ------------- | ------------- |
| O que é?   |   Vowpal Wabbit (também conhecido como "VW") é uma biblioteca de sistema de aprendizado fora do núcleo rápida de software livre    |
| Edições do DSVM com suporte     | Windows, Linux     |
| Usos típicos      | Biblioteca ML geral      |
| Como é configurado/instalado no DSVM?      |  Windows – msi installer, Linux – apt-get |
| Como usar/executar?      | Como uma ferramenta de linha de comando no caminho (`C:\Program Files\VowpalWabbit\vw.exe` no Windows, `/usr/bin/vw` no Linux)    |
| Links para exemplos      | [Exemplos de VowPal Wabbit](https://github.com/JohnLangford/vowpal_wabbit/wiki/Examples) |
| Ferramentas relacionadas ao DSVM      |LightGBM, MXNet, XGBoost   |


## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| O que é?   |  Weka é uma coleção de algoritmos de aprendizado de máquina para tarefas de mineração de dados. Os algoritmos podem ser aplicados diretamente a um conjunto de dados ou chamados do seu próprio código Java. Weka contém ferramentas para o pré-processamento, classificação, regressão, clustering, regras de associação e visualização de dados. |
| Edições do DSVM com suporte     | Windows, Linux     |
| Usos típicos      | Ferramenta de ML geral     |
| Como usar/executar?      | No Windows, pesquise Weka no Menu Iniciar. No Linux, faça logon com X2Go e, em seguida, navegue até Aplicativos -> Desenvolvimento -> Weka. |
| Links para exemplos      | [Exemplos de Weka](http://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Ferramentas relacionadas ao DSVM      |LightGBM, Rattle, XGBooost   |

## <a name="rattle"></a>Rattle
|    |           |
| ------------- | ------------- |
| O que é?   |   Uma Interface Gráfica do Usuário para Mineração de Dados usando R   |
| Edições do DSVM com suporte     | Windows, Linux     |
| Usos típicos      | Ferramenta de Mineração de Dados da Interface do Usuário Geral para R    |
| Como usar/executar?      | Ferramenta da interface do usuário. No Windows, inicie um Prompt de Comando, execute R e, em seguida, dentro de R, execute `rattle()`. No Linux, conecte-se ao X2Go, inicie um terminal, execute R e, em seguida, dentro de R, execute `rattle()`. |
| Links para exemplos      | [Rattle](https://togaware.com/onepager/) |
| Ferramentas relacionadas ao DSVM      |LightGBM, Weka, XGBoost   |

## <a name="lightgbm"></a>LightGBM
|    |           |
| ------------- | ------------- |
| O que é?   | Uma estrutura de reforço de gradiente de alto desempenho, distribuída e rápida (GBDT, GBRT, GBM ou MART) com base em algoritmos de árvore de decisão, usada para priorização, classificação e muitas outras tarefas de aprendizado de máquina.    |
| Versões do DSVM com suporte      | Windows, Linux    |
| Usos típicos      | Estrutura de aumento de gradiente de finalidade geral      |
| Como é configurado/instalado no DSVM?      | No Windows, LightGBM é instalado como um pacote do Python. No Linux, o executável de linha de comando está em `/opt/LightGBM/lightgbm`, o pacote R está instalado e pacotes Python estão instalados.     |
| Links para exemplos      | [Guia LightGBM](https://github.com/Microsoft/LightGBM/tree/master/examples/python-guide)   |
| Ferramentas relacionadas ao DSVM      | MXNet, XgBoost  |

## <a name="h2o"></a>H2O
|    |           |
| ------------- | ------------- |
| O que é?   | Uma plataforma de AI de software livre com suporte para aprendizado de máquina em memória, distribuído, rápido e escalonável  |
| Versões do DSVM com suporte      | Linux   |
| Usos típicos      | ML escalonável distribuída de Finalidade Geral   |
| Como é configurado/instalado no DSVM?      | O H2O é instalado em `/dsvm/tools/h2o`.      |
| Como usar/executar?      | Conectar-se à VM usando X2Go. Inicie um novo terminal e execute `java -jar /dsvm/tools/h2o/current/h2o.jar`. Em seguida, inicie um navegador da Web e conecte-se ao `http://localhost:54321`.      |
| Links para exemplos      | Os exemplos estão disponíveis na VM em Jupyter no diretório `h2o`.      |
| Ferramentas relacionadas ao DSVM      | Apache Spark, MXNet, XGBoost, Sparkling Water, Deep Water    |

Existem várias outras bibliotecas ML na DSVM, como o popular pacote `scikit-learn`, fornecido como parte da distribuição do Anaconda Python instalada na DSVM. Confira a lista de pacotes disponíveis em Python, R e Julia executando os respectivos gerenciadores de pacote. 
