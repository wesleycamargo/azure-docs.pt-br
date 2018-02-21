---
title: "Notas de versão do Azure ML Workbench para o sprint 3 de janeiro de 2018"
description: "Este documento fornece detalhes das atualizações para a versão do sprint 3 do Azure ML"
services: machine-learning
author: raymondlaghaeian
ms.author: raymondl
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/22/2018
ms.openlocfilehash: 266a56d5e247fd4926031e563c8be302599838eb
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="sprint-3---january-2018"></a>Sprint 3 - Janeiro 2018 

#### <a name="version-number-01171218263"></a>Número da versão: 0.1.1712.18263

>Veja como você pode [localizar o número da versão](known-issues-and-troubleshooting-guide.md).

Bem-vindo à quarta atualização do Azure Machine Learning Workbench. Veja a seguir as atualizações e melhorias neste sprint. Muitas dessas atualizações são feitas como resultado direto dos comentários do usuário. 

## <a name="notable-new-features-and-changes"></a>Alterações e novos recursos importantes
- Atualizações na pilha de autenticação forçam a seleção de logon e conta na inicialização

## <a name="detailed-updates"></a>Atualizações detalhadas
A seguir, está uma lista de atualizações detalhadas em cada área de componente do Azure Machine Learning neste sprint.

### <a name="workbench"></a>Workbench
- Capacidade de instalar/desinstalar o aplicativo em Adicionar/Remover Programas
- Atualizações na pilha de autenticação forçam a seleção de logon e conta na inicialização
- Melhor experiência de SSO (Logon Único) no Windows
- Os usuários que pertencem a vários locatários com credenciais diferentes agora poderão entrar no Workbench

#### <a name="ui"></a>Interface do usuário
- Melhorias gerais e correções de bugs

### <a name="notebooks"></a>Blocos de anotações
- Melhorias gerais e correções de bugs

### <a name="data-preparation"></a>Preparação dos dados 
- Melhorias de sugestões automáticas durante a execução de transformações Por Exemplo
- Algoritmo aprimorado para o inspetor de Frequência do Padrão
- Capacidade de enviar dados de exemplo e comentários durante a execução de transformações Por Exemplo ![Imagem do link Enviar comentários na transformação Derivar coluna](media/release-notes-sprint-3/SendFeedbackFromDeriveColumn.png)
- Melhorias no Tempo de Execução do Spark
- O Scala substituiu o Pyspark
- Correção da incapacidade de fechar Dados Não Aplicáveis no Time Series Inspector 
- Correção do tempo de travamento para a execução de Preparação de Dados no HDI

### <a name="model-management-cli-updates"></a>Atualizações da CLI de Gerenciamento de Modelos 
  - A propriedade da assinatura não é mais necessária para o provisionamento de recursos. O acesso de Colaborador ao grupo de recursos será suficiente para configurar o ambiente de implantação.
  - Configuração do ambiente local habilitada para assinaturas gratuitas 
