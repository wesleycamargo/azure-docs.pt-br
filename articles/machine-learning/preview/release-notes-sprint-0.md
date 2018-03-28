---
title: "Notas de versão do Azure ML Workbench para o sprint 0 de outubro de 2017"
description: "Este documento detalha as atualizações para a versão de sprint 0 do Azure ML"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 10/12/2017
ms.openlocfilehash: 37e0a4461e8a0de631a6194a1eb8cc16b610954f
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2017
---
# <a name="sprint-0---october-2017"></a>Sprint 0 - Outubro 2017 

**Número de versão: 0.1.1710.04013**

Bem-vindo à primeira atualização ao Workbench de Machine Learning do Azure seguindo nossa visualização pública inicial na conferência Microsoft Ignite 2017. As principais atualizações nesta versão são correções de confiabilidade e estabilização.  Alguns dos problemas críticos que abordamos incluem:

## <a name="new-features"></a>Novos recursos
- Agora há suporte para macOS High Sierra

## <a name="bug-fixes"></a>Correções de bug
### <a name="workbench-experience"></a>Experiência de Workbench
- Arrastar e soltar um arquivo no Workbench faz o Workbench falhar.
- A janela do terminal no código VS configurado como um IDE para o Workbench não reconhece comandos _az ml_.

### <a name="workbench-authentication"></a>Autenticação do Workbench
Fizemos várias atualizações para melhorar vários problemas de logon e autenticação relatados.
- A janela de autenticação continua aparecendo, especialmente quando a conexão de Internet não é estável.
- Melhoramos problemas de confiabilidade relativos à expiração do token de autenticação.
- Em alguns casos, a janela de autenticação aparece duas vezes.
- A janela principal do Workbench ainda exibe a mensagem "autenticando" quando o processo de autenticação, na verdade, já foi concluído e a caixa de diálogo pop-up já foi descartada.
- Se não houver conexão com a Internet, a caixa de diálogo de autenticação surgirá com uma tela em branco.

### <a name="data-preparation"></a>Preparação dos dados 
- Quando um valor específico é filtrado, erros e valores ausentes também são filtrados.
- Alterar uma estratégia de amostragem remove as operações de junção existentes subsequentes.
- Substituir a transformação de Valor Ausente não leva NaN em consideração.
- Inferência de tipos de data gera exceção quando o valor nulo é encontrado.

### <a name="job-execution"></a>Execução do trabalho
- Não há mensagem de erro clara quando a execução do trabalho falha em carregar a pasta do projeto porque ela excedeu o limite de tamanho.
- Se o script Python do usuário alterar o diretório de trabalho, os arquivos gravados nas pastas de saída não serão rastreados. 
- Se a assinatura ativa do Azure for diferente daquela à qual o projeto atual pertence, o envio do trabalho resultará em um erro 403.
- Quando o Docker não está presente, nenhuma mensagem de erro clara será retornada se o usuário tentar usar o Docker como um destino de execução.
- O arquivo .runconfig não é salvo automaticamente quando o usuário clica no botão _Executar_.

### <a name="jupyter-notebook"></a>Bloco de anotações do Jupyter
- O servidor de notebook não conseguirá iniciar se o usuário o utilizar com certos tipos de logon.
- Mensagens de erro de servidor de notebook não ocorrem em logs visíveis ao usuário.

### <a name="azure-portal"></a>Portal do Azure
- Selecionar o tema escuro do portal do Azure faz com que a folha de Gerenciamento de Modelos seja exibida como uma caixa preta.

### <a name="operationalization"></a>Operacionalização
- Reutilizar um manifesto para atualizar um serviço Web faz com que uma nova imagem do Docker seja criada com um nome aleatório.
- Não possível recuperar logs de serviço Web do cluster Kubernetes.
- Mensagem de erro enganosas são impressas quando o usuário tenta criar uma conta de Gerenciamento de Modelos ou uma conta de Computação ML e encontra problemas de permissões.
