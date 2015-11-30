<properties 
   pageTitle="Tipos de runbook da Automação do Azure"
   description="Descreve as diferenças entre os tipos de runbook que você pode usar na Automação do Azure e as considerações que devem ser levadas em conta ao determinar qual tipo usar."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/13/2015"
   ms.author="bwren" />

# Tipos de runbook da Automação do Azure

A Automação do Azure dá suporte a três tipos de runbook descritos brevemente na tabela a seguir. As seções abaixo fornecem mais informações sobre cada tipo, incluindo considerações sobre quando usar cada um.


| Tipo | Descrição |
|:---|:---|
| [Gráfico](#graphical-runbooks) | Com base no Fluxo de Trabalho do Windows PowerShell e criado e editado completamente no editor gráfico do portal do Azure. | 
| [Fluxo de Trabalho do PowerShell](#powershell-workflow-runbooks) | Runbook de texto com base no Fluxo de Trabalho do Windows PowerShell. |
| [PowerShell](#powershell-runbooks) | Runbook de texto com base no script do Windows PowerShell. |

## Runbooks gráficos

Os [runbooks gráficos](automation-runbook-types.md#graphical-runbooks) são criados e editados com o editor gráfico do portal do Azure. Você pode exportá-los para um arquivo e depois importá-los em outra conta de automação, mas não pode criá-los nem editá-los com outra ferramenta. Os runbooks gráficos geram código do Fluxo de Trabalho do PowerShell, mas você não pode exibir nem modificar diretamente o código. Os runbooks gráficos não podem ser convertidos em um dos [formatos de texto](automation-runbook-types.md), nem um runbook de texto pode ser convertido em formato gráfico.

### Vantagens

- Crie runbooks com conhecimento mínimo do [Fluxo de Trabalho do PowerShell](automation-powershell-workflow.md).
- Represente visualmente os processos de gerenciamento.
- Use os [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook em caso de erro.
- Use o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias atividades paralelamente.
- Pode incluir runbooks Gráficos e runbooks de Fluxo de Trabalho do PowerShell como runbooks filho para criar fluxos de trabalho de alto nível.


### Limitações

- Não é possível editar um runbook fora do portal do Azure.
- Pode exigir um [Controle de Script de Fluxo de Trabalho](automation-powershell-workflow.md#activities) contendo código de Fluxo de Trabalho do PowerShell para executar lógica complexa.
- Não é possível exibir ou editar diretamente o código de Fluxo de Trabalho do PowerShell criado pelo fluxo de trabalho gráfico. Observe que você pode exibir o código em todas as atividades de Fluxo de Trabalho de Script.
- O runbook demora mais para iniciar do que os runbooks do PowerShell pois precisa ser compilado antes da execução.
- Os runbooks do PowerShell só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.


## Runbooks do Fluxo de Trabalho do PowerShell

Os runbooks do Fluxo de Trabalho do PowerShell são runbooks de texto baseados no [Fluxo de Trabalho do Windows PowerShell](automation-powershell-workflow.md). Você edita o código do runbook diretamente usando o editor de texto do portal do Azure. Também é possível usar qualquer editor de texto offline e [importar o runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) na Automação do Azure.

### Vantagens

- Implemente toda a lógica complexa com o código do Fluxo de Trabalho do PowerShell.
- Use os [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook em caso de erro.
- Use o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações paralelamente.
- Pode incluir runbooks Gráficos e runbooks de Fluxo de Trabalho do PowerShell como runbooks filho para criar fluxos de trabalho de alto nível.


### Limitações

- O autor deve estar familiarizado com o Fluxo de Trabalho do PowerShell.
- O runbook deve lidar com a complexidade adicional do Fluxo de Trabalho do PowerShell, como [objetos desserializados](automation-powershell-workflow.md#code-changes).
- O runbook demora mais para iniciar do que os runbooks do PowerShell pois precisa ser compilado antes da execução.
- Os runbooks do PowerShell só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.


## Runbooks do PowerShell

Os runbooks do PowerShell se baseiam no Windows PowerShell. Você edita o código do runbook diretamente usando o editor de texto do portal do Azure. Também é possível usar qualquer editor de texto offline e [importar o runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx) na Automação do Azure.

### Vantagens

- Implemente toda a lógica complexa com o código do PowerShell sem as complexidades adicionais do Fluxo de Trabalho do PowerShell. 
- O runbook inicia mais rápido do que os runbooks Gráficos ou de Fluxo de Trabalho do PowerShell, uma vez que não precisa ser compilado antes da execução.

### Limitações

- Exige familiarização com a criação de script do PowerShell.
- Não é possível usar o [processamento paralelo](automation-powershell-workflow.md#parallel-processing) para executar várias ações paralelamente.
- Não é possível usar os [pontos de verificação](automation-powershell-workflow.md#checkpoints) para retomar o runbook em caso de erro.
- Os runbooks de Fluxo de Trabalho e Gráficos do PowerShell só podem ser incluídos como runbooks filho usando o cmdlet Start-AzureAutomationRunbook, que cria um novo trabalho.

### Problemas conhecidos
Veja a seguir problemas conhecidos atuais com os runbooks do PowerShell.

- Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) não criptografado com um valor nulo.
- Os runbooks do PowerShell não podem recuperar um [ativo variável](automation-variables.md) com *~* no nome.
- Get-Process em um loop em um runbook do PowerShell pode falhar após cerca de 80 iterações. 
- Um runbook do PowerShell poderá falhar se tentar gravar uma grande quantidade de dados no fluxo de saída de uma vez. Geralmente, você pode contornar esse problema emitindo apenas as informações que precisa ao trabalhar com objetos grandes. Por exemplo, em vez de gerar algo como *Get-Process*, você pode gerar apenas os campos obrigatórios com *Get-Process | Select ProcessName, CPU*.

## Considerações

Você deve levar em conta as considerações adicionais a seguir ao determinar qual tipo usar para um runbook específico.

- Você não pode converter runbooks de um tipo em outro.
- Há limitações ao usar runbooks de tipos diferentes, como um runbook filho. Veja [Runbooks filho na Automação do Azure](automation-child-runbooks.md) para obter mais informações.



  
## Artigos relacionados

- [Criação gráfica na Automação do Azure](automation-graphical-authoring-intro.md)
- [Aprendendo sobre o fluxo de trabalho do Windows PowerShell](automation-powershell-workflow.md)
- [Criando ou Importando um Runbook](http://msdn.microsoft.com/library/azure/dn643637.aspx)

<!---HONumber=Nov15_HO4-->