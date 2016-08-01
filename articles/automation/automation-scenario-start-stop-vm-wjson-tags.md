<properties
   pageTitle="Usando marcas formatadas JSON para criar um agendamento para inicialização e desligamento de VM do Azure | Microsoft Azure"
   description="Este artigo demonstra como usar cadeias de caracteres JSON em marcas para automatizar o agendamento do desligamento e inicialização de VM."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/18/2016"
   ms.author="magoedte;paulomarquesc" />

# Cenário de Automação do Azure: usando marcas formatadas JSON para criar um agendamento para inicialização e desligamento de VM do Azure

Normalmente, os clientes desejam agendar a inicialização e desligamento de máquinas virtuais para ajudar a reduzir os custos de assinatura ou dar suporte a requisitos técnicos e de negócios.

O cenário a seguir permite configurar a inicialização e o desligamento automatizados de suas VMs usando uma marcação chamada de Agendamento no nível do grupo de recursos ou da máquina virtual no Azure. Esse agendamento pode ser configurado de domingo a sábado com hora de inicialização e de desligamento. Embora existam algumas opções predeterminadas, como usar [Conjuntos de Escala de Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) com Configurações de Autoescala que permitem reduzir ou aumentar horizontalmente e o serviço [DevTest Labs](../devtest-lab/devtest-lab-overview.md), que tem a funcionalidade interna para agendamento de inicialização e desligamento, essas opções dão suporte apenas a cenários específicos e não podem ser aplicadas às VMs de IaaS.

Quando a marcação Agendamento for aplicada a um grupo de recursos, ele será aplicado a todas as máquinas virtuais dentro daquele grupo de recursos. Se um agendamento também for aplicado diretamente a uma VM, o último agendamento terá precedência na seguinte ordem:

1.  Agendamento aplicado a um grupo de recursos
2.  Agendamento aplicado a um grupo de recursos e à máquina virtual no grupo de recursos
3.  Agendamento aplicado a uma máquina virtual

Essencialmente, esse cenário usa uma cadeia de caracteres JSON com um formato especificado e adicioná-a como um valor de uma marcação chamada Agendamento. Em seguida, um runbook lista todos os grupos de recursos e máquinas virtuais e identifica os agendamentos para cada VM com base nos cenários listados acima, percorrendo essas VMs com agendamentos anexados e avalia a ação que deve ser tomada, a qual pode ser interrompida, desligada ou ignorada.

Esses runbooks são autenticados usando a [Conta Executar como do Azure](../automation/automation-sec-configure-azure-runas-account.md).

## Obtendo o cenário

Este cenário consiste em quatro runbooks de Fluxo de Trabalho do PowerShell que podem ser baixados da [Galeria do TechNet](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7) ou do repositório do [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) para esse projeto.

Runbook | Descrição 
----------|----------
Test-ResourceSchedule | Verifica o agendamento de cada Máquina Virtual (executa o desligamento ou a inicialização de máquinas virtuais dependendo do agendamento)
Add-ResourceSchedule | Adiciona a marcação Agendamento a uma VM ou grupo de recursos
Update-ResourceSchedule | Modifica uma marcação Agendamento existente substituindo-a por uma nova
Remove-ResourceSchedule | Remove a marcação Agendamento de uma VM ou grupo de recursos 


## Instalando e configurando esse cenário

### Instalar e publicar os runbooks

Depois de baixar os runbooks, você poderá importá-los usando o procedimento em [Procedimentos para importar runbooks](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-Azure-Automation). Publica cada runbook depois que eles foram importados com êxito para sua conta de Automação.


### Adicionar um agendamento para Test-ResourceSchedule

Siga estas etapas para habilitar o agendamento para o runbook Test-ResourceSchedule. Esse é o runbook que testa qual máquina virtual será iniciada, desligada ou deixada como está.

1. No Portal do Azure, abra sua conta da Automação e clique no bloco **Runbooks**.
2. Na folha **Test-ResourceSchedule**, clique no bloco **Agendamentos**.
3. Na folha **Agendamentos**, clique em **Adicionar um agendamento**.
4. Na folha **Agendamento**, selecione **Vincular um agendamento ao seu runbook** e na folha **Agendamento**, selecione **Criar um novo agendamento**.
5.  Na folha **Novo agendamento**, digite o nome deste agendamento. Por exemplo, HourlyExecution.
6. Para o agendamento **Iniciar** agendamento, defina a hora de início para um incremento de hora arredondada.
7. Selecione **Recorrência** e **Ocorrer novamente a cada** e selecione o intervalo de **1 hora**.
8. Verifique se **Definir expiração** está definido como **Não** e clique em **Criar** para salvar sua nova agenda.
9. Na folha de opções **Runbook de Agendamento**, selecione a opção **Configurações de parâmetros e de execuções** e, na folha do Test-ResourceSchedule **Parâmetros**, insira o nome da sua assinatura no campo **SubscriptionName**. Esse é o único parâmetro necessário para o runbook. Clique em **OK** após concluir.
   

O agendamento de runbook deverá parecer com o seguinte quando concluído:<br> ![Runbook de Test-ResourceSchedule configurado](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## Formato JSON de Agendamento

Essa solução basicamente usa uma cadeia de caracteres JSON com um formato especificado e adiciona-a como um valor de uma marcação chamada Agendamento. Depois disso, um runbook lista todos os grupos de recursos e máquinas virtuais e identifica os agendamentos para cada máquina virtual, contornando as máquinas virtuais com agendamentos anexados e verificando qual ação deve ser tomada. A seguir há um exemplo de como isso deve ser formatado.

    {
       "TzId": "Eastern Standard Time", 
        "0": {  
           "S": "11",
           "E": "17" 
        },
        "1": {
           "S": "9",
           "E": "19"
        },
        "2": {
           "S": "9",
           "E": "19"
        },
    }

Informações detalhadas sobre esta estrutura:

1. O formato dessa estrutura JSON é otimizado para contornar a limitação de 256 caracteres de um valor de marcação única no Azure

2. *TzId* representa o fuso horário da máquina virtual. Essa ID pode ser obtida usando a classe .NET TimeZoneInfo em uma sessão do PowerShell - **[System.TimeZoneInfo]:: GetSystemTimeZones()**.<br>

    ![GetSystemTimeZones no PowerShell](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

    - Dias da semana são representados com um valor numérico de 0 a 6. O valor 0 é igual a domingo.
    - A hora de início é representada pelo atributo **S** e seu valor está no formato de 24 horas.
    - A hora de término é representada pelo atributo **E** e seu valor está no formato de 24 horas.

    Se os atributos de S e E tiverem um valor de zero (0), a máquina virtual será deixada em seu estado atual no momento da avaliação.

3. Se você quiser ignorar a avaliação de um dia específico da semana, não adicione a seção relacionada de dia da semana. No exemplo a seguir, apenas a segunda-feira será avaliada e os outros dias da semana serão ignorados.
   
        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## Marcação de grupos de recursos ou VMs

Para VMs de desligamento, é necessário marcá-las ou aos grupos de recursos em que elas estão localizadas. Máquinas virtuais que não têm uma marcação de Agendamento não são avaliadas, portanto, não são iniciadas nem desligadas. Há duas maneiras de marcar grupos de recursos ou VMs com essa solução, diretamente do portal ou usando os runbooks **Add-ResourceSchedule**, **Update-ResourceSchedule** e **Remove-ResourceSchedule**.

### Marcando por meio do portal

Siga estas etapas para marcar uma máquina virtual ou um grupo de recursos no Portal.

1. Nivelar a cadeia de caracteres JSON e verifique se não há espaços. A cadeia de caracteres JSON deve ter esta aparência:

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
   

2. Selecione uma VM ou grupo de recursos para aplicar este agendamento selecionando o ícone de Marcação.<br>![Opção de marcação de VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)
3. As marcas são definidas por um par Chave/Valor a seguir. Digite **Agendamento** no campo **Chave** e cole a cadeia de caracteres JSON no campo **Valor** e clique em **Salvar**. A nova marcação agora deve aparecer na lista de marcações do seu recurso.<br>![Marcação de agendamento de VM](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)


### Marcação do PowerShell

Todos os runbooks importados contém informações de Ajuda no início do script para descrever como executar runbooks diretamente do PowerShell. Os runbooks Add-ScheduleResource e Update-ScheduleResource podem ser chamados do PowerShell passando parâmetros necessários, permitindo criar ou atualizar a marcação Agendamento em uma VM ou grupo de recursos fora do portal.

Para criar, adicionar e excluir marcas pelo PowerShell, primeiramente você precisa [configurar seu ambiente do PowerShell para o Azure](../powershell-install-configure.md). Depois de concluir a instalação, você pode continuar com as etapas a seguir.

### Criando uma marcação de agendamento com o PowerShell

1. Abra uma sessão do PowerShell e execute o seguinte para autenticar com sua conta Executar como e especificar uma assinatura:
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
   
2. Defina uma tabela de hash de agendamento. Aqui está um exemplo de como deve ser criado:
    
        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}

3. Defina os parâmetros necessários para o runbook. O exemplo a seguir destina-se a uma VM:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    Se você estiver marcando um grupo de recursos, bastará remover o parâmetro *VMName* da tabela de hash $params da seguinte maneira:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}

4. Execute o runbook **Add-ResourceSchedule** com os seguintes parâmetros para criar a marcação Agendamento:

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

5. Se você quiser atualizar um grupo de recursos ou marcação de máquina virtual, execute o runbook **Update-ResourceSchedule** com os seguintes parâmetros:

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### Removendo uma marcação de agendamento com o PowerShell

1. Abra uma sessão do PowerShell e execute o seguinte para autenticar com sua conta Executar como, selecionar e especificar uma assinatura:
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"

2. Defina os parâmetros necessários para o runbook. O exemplo a seguir destina-se a uma VM:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" ` 
        ;"VmName"="VM01"}

    Se você estiver removendo uma marcação de um grupo de recursos, bastará remover o parâmetro *VMName* da tabela de hash $params da seguinte maneira:

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}

3. Execute o runbook **Remove-ResourceSchedule** para remover a marcação Agendamento:

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

4. Se você quiser atualizar um grupo de recursos ou marcação de máquina virtual, execute o runbook **Remove-ResourceSchedule** com os seguintes parâmetros:

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"


>[AZURE.NOTE] É recomendável monitorar proativamente esses runbooks (e o estado da máquina virtual) para verificar se as máquinas virtuais estão sendo devidamente encerradas e iniciadas.

Você pode exibir os detalhes do trabalho do runbook **Test-ResourceSchedule** no Portal do Azure selecionando o bloco Trabalhos do runbook. O Resumo do trabalho exibirá os parâmetros de entrada e o Fluxo de Saída, além de informações gerais sobre o trabalho e todas as exceções, caso tenham ocorrido. O Histórico incluirá mensagens do Fluxo de Saída e dos Fluxos de Aviso e de Erro. Selecione o bloco de Saída para exibir os resultados detalhados da execução do runbook.<br> ![Saída do Test-ResourceSchedule](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## Próximas etapas

-  Para começar a usar os runbooks do fluxo de trabalho do PowerShell, veja [Meu primeiro runbook do fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
-  Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)
-  Para saber mais sobre o recurso de suporte a scripts do PowerShell, veja [Native PowerShell script support in Azure Automation (Suporte a scripts nativos do PowerShell na Automação do Azure)](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
-  Para saber mais sobre o registro em log de runbook e de saída, consulte [Saída e mensagens de runbook na Automação do Azure](automation-runbook-output-and-messages.md)
-  Para saber mais sobre uma Conta Executar como do Azure, e como autenticar seus runbooks usando essa conta, consulte [Autenticar Runbooks com a Conta Executar como do Azure](../automation/automation-sec-configure-azure-runas-account.md)

<!---HONumber=AcomDC_0720_2016-->