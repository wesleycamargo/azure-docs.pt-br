<properties
    pageTitle="Automatizar a remoção de Grupos de Recursos | Microsoft Azure"
    description="Versão do Fluxo de Trabalho do PowerShell de um cenário de Automação do Azure, incluindo runbooks para remover todos os Grupos de Recursos de sua assinatura."
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor=""
	/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="magoedte"/>

# Cenário da Automação do Azure - automatize a remoção de grupos de recursos

Muitos clientes criam mais de um grupo de recursos, em que alguns são dedicados ao gerenciamento de aplicativos de produção e outros podem ser a ambientes de desenvolvimento, teste e de preparo. A automatização da implantação desses recursos é uma coisa, mas poder encerrar um grupo de recursos com um clique do botão é outra. O uso da Automação para lidar com isso é um caso de uso perfeito e uma oportunidade de simplificar uma tarefa de gerenciamento comum como essa. Isso também será útil se você estiver trabalhando com uma assinatura do Azure com um limite de gastos por meio de uma oferta de membro como o MSDN ou o programa Microsoft Partner Network Cloud Essentials, por exemplo.

Este cenário se baseia em um runbook do PowerShell e foi projetado para remover da sua assinatura um ou mais grupos de recursos especificados por você. O runbook dá suporte a testes antes de prosseguir, que é o valor padrão. Dessa forma, você não o excluirá acidentalmente sem ter certeza absoluta de que está pronto para concluir este procedimento.

## Obtendo o cenário

Este cenário consiste em um runbook do PowerShell que você pode baixar da [Galeria do PowerShell](https://www.powershellgallery.com/packages/Remove-ResourceGroup/1.0/DisplayScript) ou que pode importar diretamente da [Galeria de Runbooks](automation-runbook-gallery.md) no portal do Azure.<br><br>

Runbook | Descrição|
----------|------------|
Remove-ResourceGroup | Remove um ou mais grupos de recursos do Azure e seus recursos da assinatura.  
<br> Os parâmetros de entrada a seguir são definidos para este runbook:

Parâmetro | Descrição|
----------|------------|
NameFilter (Obrigatório) | Permite que você especifique um filtro de nome para limitar os grupos de recursos que você pretende excluir. Você pode passar vários valores usando uma lista separada por vírgulas.<br>O filtro não diferencia maiúsculas de minúsculas e corresponderá a qualquer grupo de recursos que contenha a cadeia de caracteres.|
PreviewMode (opcional) | Execute o runbook para ver quais grupos de recursos seriam excluídos, mas não realize nenhuma ação.<br>O padrão é **true** para ajudar a evitar a exclusão acidental de um ou mais grupos de recursos passados para o runbook.  

## Instalando e configurando esse cenário

### Pré-requisitos

Esse runbook se autentica usando a [conta Executar como do Azure](automation-sec-configure-azure-runas-account.md).

### Instalar e publicar os runbooks

Depois de baixar o runbook, você poderá importá-lo usando o procedimento em [Procedimentos para importar runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation). Publique o runbook depois que ele for importado com êxito em sua conta de Automação.


## Usando o runbook

As etapas a seguir orientarão você pela execução deste runbook e o ajudarão a se familiarizar com o funcionamento dele. Nós só testaremos o runbook neste exemplo, não excluiremos o grupo de recursos.

1. No Portal do Azure, abra sua conta da Automação e clique no bloco **Runbooks**.
2. Selecione o runbook **Remove-ResourceGroup** e clique em **Iniciar**.
3. Quando você inicia o runbook, a folha **Iniciar Runbook** é aberta e você pode configurar valores a seguir para os parâmetros. Insira o nome de um ou mais grupos de recursos em sua assinatura que você deseja testar e isso não causará danos se for acidentalmente excluído.<br> ![Parâmetros de Remove-ResouceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-input-parameters.png)
    
    >[AZURE.NOTE] Verifique se a opção **Previewmode** está definida como **true** para evitar a exclusão dos grupos de recursos selecionados. **Observe** que este runbook não removerá o grupo de recursos com a conta da Automação que está executando esse runbook.

4. Depois de configurar todos os valores de parâmetro, clique em **OK** e o runbook será enfileirado para execução.

Para exibir os detalhes do trabalho do runbook **Remove-ResourceGroup** no Portal do Azure, selecione o bloco **Jobs** do runbook. O resumo do trabalho exibe os parâmetros de entrada e o fluxo de saída, além de informações gerais sobre o trabalho e todas as exceções, caso tenham ocorrido.<br> ![Status de trabalho do runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-status.png).

O **Resumo do Trabalho** inclui mensagens de fluxos de saída, de aviso e de erro. Selecione o bloco de **Output** para exibir os resultados detalhados da execução do runbook.<br> ![Resultados da saída do runbook Remove-ResourceGroup](media/automation-scenario-remove-resourcegroup/remove-resourcegroup-runbook-job-output.png)

## Próximas etapas

- Para começar a criar seu próprio runbook, confira [Criando ou importando um runbook na Automação do Azure](automation-creating-importing-runbook.md)
- Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)

<!---HONumber=AcomDC_0928_2016-->