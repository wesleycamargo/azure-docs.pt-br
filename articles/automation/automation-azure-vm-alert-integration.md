<properties
    pageTitle="Corrigir alertas de VM do Azure com runbooks de Automação | Microsoft Azure"
    description="Este artigo demonstra como integrar os alertas de Máquina Virtual do Azure com runbooks de Automação do Azure e corrigir automaticamente os problemas"
    services="automation"
    documentationCenter=""
    authors="csand-msft"
    manager="stevenka"
    editor="tysonn" />    
<tags
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="04/11/2016"
    ms.author="csand;magoedte" />

# Solução de Automação do Azure: corrigir alertas de VM do Azure

As Máquinas Virtuais do Azure e a Automação do Azure lançaram um novo recurso que permite que você configure os alertas de VM (Máquina Virtual) para executar runbooks de Automação. Essa nova funcionalidade permite que você execute a correção standard automaticamente em resposta a alertas de VM, como reiniciar ou parar a VM.

Anteriormente, durante a criação de regra de alerta de VM você podia [especificar um webhook de Automação](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) para um runbook para executar o runbook sempre que o alerta fosse disparado. No entanto, isso exigia que você realizasse o trabalho de criar o runbook, criar o webhook para o runbook e, em seguida, copiasse e colasse o webhook durante a criação da regra de alerta. Com essa nova versão, o processo é muito mais fácil, porque você pode escolher diretamente um runbook em uma lista durante a criação da regra de alerta e pode escolher uma conta de Automação que executará o runbook ou facilmente criar uma conta.

Neste artigo, mostraremos como é fácil configurar um alerta de VM do Azure e configurar um runbook de Automação para ser executado sempre que o alerta for disparado. Os cenários de exemplo incluem reiniciar uma VM quando o uso de memória exceder algum limite devido a um aplicativo na VM com um vazamento de memória ou parar uma VM quando o tempo de usuário da CPU foi abaixo de 1% na hora anterior e não está em uso. Explicaremos também como a criação automatizada de uma entidade de serviço em sua conta de Automação simplifica o uso de runbooks na correção de alerta do Azure.

## Criar um alerta em uma VM

Execute as seguintes etapas para configurar um alerta para iniciar um runbook quando o limite for atingido.

>[AZURE.NOTE] Com esta versão, damos suporte apenas a máquinas virtuais V2 e o suporte para VMs clássicas será adicionado em breve.

1. Faça logon no portal do Azure e clique em **Máquinas Virtuais**.  
2. Selecione uma das máquinas virtuais. A folha do painel da máquina virtual será exibida e a folha **Configurações** à sua direita.  
3. Na folha **Configurações**, na seção Monitoramento, selecione **Regras de alerta**.
4. Na folha **Regras de alerta**, clique em **Adicionar alerta**.

Isso abrirá a folha **Adicionar uma regra de alerta**, na qual você pode configurar as condições para o alerta e escolher entre uma ou todas essas opções: enviar email para alguém, use um webhook para encaminhar o alerta para outro sistema de e/ou executar um runbook de Automação na tentativa de resposta para corrigir o problema.

## Configurar um runbook

Para configurar um runbook para ser executado quando o limite de alerta da VM for atingido, selecione **Runbook de Automação**. Na folha **Configurar runbook**, você pode selecionar o runbook para ser executado e a conta de Automação na qual executar o runbook.

![Configurar um runbook de Automação e criar uma nova conta de Automação](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

>[AZURE.NOTE] Para esta versão, você pode escolher entre três runbooks que o serviço fornece: Reiniciar VM, Parar VM ou Remover VM (excluí-la). A capacidade de selecionar outros runbooks ou um de seus próprios runbooks estará disponível em uma versão futura.

![Runbooks dentre os quais escolher](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

Depois de selecionar um dos três runbooks disponíveis, a lista suspensa **Conta de Automação** é exibida e você pode selecionar uma conta de automação como a qual o runbook será executado. Os runbooks precisam ser executados no contexto de uma [Conta de Automação](automation-security-overview.md) que esteja na sua assinatura do Azure. Você pode selecionar uma conta de Automação que já criou ou pode ter uma nova conta de Automação criada para você.

Os runbooks fornecidos se autenticam no Azure usando uma entidade de serviço. Se você optar por executar o runbook em uma de suas contas de Automação existentes, criaremos automaticamente a entidade de serviço para você. Se você optar por criar uma nova conta de Automação, criaremos automaticamente a conta e a entidade de serviço. Em ambos os casos, também serão criados dois ativos na conta de Automação: um ativo de certificado chamado **AzureRunAsCertificate** e um ativo de conexão chamado **AzureRunAsConnection**. Os runbooks usarão o **AzureRunAsConnection** para se autenticar com o Azure para executar a ação de gerenciamento na VM.

>[AZURE.NOTE] A entidade de serviço é criada no escopo de assinatura e é atribuída à função de Colaborador. Essa função é necessária para a conta ter permissão para executar runbooks de Automação para gerenciar VMs do Azure. A criação de uma conta de Automação e/ou entidade de serviço é um evento único. Depois que elas forem criadas, você poderá usar essa conta para executar runbooks para outros alertas de VM do Azure.

Quando você clica em **OK**, o alerta é configurado e, se você selecionou a opção para criar uma nova conta de automação, ela é criada junto com a entidade de serviço. Isso pode levar alguns segundos para ser concluído.

![Runbook sendo configurado](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

Após a configuração ser concluída, você verá o nome do runbook aparecer na folha **Adicionar uma regra de alerta**.

![Runbook configurado](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

Clique em **OK** na folha **Adicionar uma regra de alerta** e a regra de alerta será criada e ativada se a máquina virtual estiver em estado de execução.

### Habilitar ou desabilitar um runbook

Se você tiver um runbook configurado para um alerta, poderá desabilitá-lo sem remover a configuração do runbook. Isso permite que você mantenha o alerta em execução e talvez teste algumas das regras de alerta e depois habilite novamente o runbook.

## Resumo

Quando você configura um alerta em uma VM do Azure, agora você tem a capacidade de configurar facilmente um runbook de Automação para executar a ação de correção automaticamente quando o alerta for disparado. Para esta versão, você pode escolher dentre runbooks para reiniciar, parar ou excluir uma VM dependendo do cenário de alerta. Isso é apenas o começo da habilitação de cenários em que você controla as ações (notificação, solução de problemas, correção) que serão executadas automaticamente quando um alerta for disparado.

## Próximas etapas

- Para começar a usar runbooks gráficos, veja [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
- Para começar a usar runbooks de fluxo de trabalho do PowerShell, veja [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
- Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)

<!---HONumber=AcomDC_0420_2016-->