<properties 
   pageTitle="Gerenciando dados da Automação do Azure"
   description="Este artigo contém vários tópicos sobre o gerenciamento de um ambiente da Automação do Azure. Atualmente, inclui a Retenção de dados e o backup da Recuperação de desastres na Automação do Azure."
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
   ms.date="10/08/2015"
   ms.author="bwren;sngun" />

# Gerenciando dados da Automação do Azure

Este artigo contém vários tópicos sobre o gerenciamento de um ambiente da Automação do Azure.

## Retenção de dados

Quando você exclui um recurso na Automação do Azure, ele é retido por 90 dias para fins de auditoria antes de ser removido permanentemente. Você não pode ver nem usar o recurso durante esse período. Essa política também se aplica a recursos que pertencem a uma conta de automação que é excluída.

A Automação do Azure exclui de forma automática e remove de forma permanente os trabalhos com mais de 90 dias.

A tabela a seguir resume a política de retenção para diferentes recursos.

|Dados|Política|
|:---|:---|
|Contas|Removidas permanentemente 90 dias depois que a conta é excluída por um usuário.|
|Ativos|Removidos permanentemente 90 dias depois que o ativo é excluído por um usuário ou 90 dias depois que a conta que contém o ativo é excluída por um usuário.|
|Módulos|Removidos permanentemente 90 dias depois que o módulo é excluído por um usuário ou 90 dias depois que a conta que contém o módulo é excluída por um usuário.|
|Runbooks|Removidos permanentemente 90 dias depois que o recurso é excluído por um usuário ou 90 dias depois que a conta que contém o recurso é excluída por um usuário.|
|Trabalhos|Excluído e removidos permanentemente 90 dias após a última modificação. Isso pode ocorrer depois que o trabalho é concluído, interrompido ou suspenso.|

A política de retenção se aplica a todos os usuários e, atualmente não, pode ser personalizada.

## Fazendo backup da Automação do Azure

Quando você exclui uma conta de automação no Microsoft Azure, todos os objetos na conta são excluídos, incluindo runbooks, módulos, configurações, trabalhos e ativos. Os objetos não podem ser recuperados depois que a conta é excluída. Você pode usar as informações a seguir para fazer backup do conteúdo de sua conta de automação antes de excluí-la.

### Runbooks

Você pode exportar seus runbooks para arquivos de script usando o Portal de Gerenciamento do Azure ou o cmdlet [Get-AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690269.aspx) no Windows PowerShell. Esses arquivos de script podem ser importados para outra conta de automação, conforme discutido em [Criando ou importando um runbook](https://msdn.microsoft.com/library/dn643637.aspx).


### Módulos de integração

Você não pode exportar módulos de integração da Automação do Azure. Você deve garantir que eles estejam disponíveis fora da conta de automação.

### Ativos

Não é possível exportar [ativos](https://msdn.microsoft.com/library/dn939988.aspx) da Automação do Azure. Usando o Portal de Gerenciamento do Azure, você deve observar os detalhes de variáveis, credenciais, certificados, conexões e agendas. Você deve criar manualmente qualquer ativo que seja usado por runbooks importados para outra automação.

Você pode usar [cmdlets do Azure](https://msdn.microsoft.com/library/dn690262.aspx) para recuperar os detalhes de ativos não criptografados e salvá-los para referência futura ou criar ativos equivalentes em outra conta de automação.

Não é possível recuperar o valor de variáveis criptografadas nem o campo de senha de credenciais usando cmdlets. Se não souber esses valores, você poderá recuperá-los de um runbook usando as atividades [Get-AutomationVariable](https://msdn.microsoft.com/library/dn940012.aspx) e [Get-AutomationPSCredential](https://msdn.microsoft.com/library/dn940015.aspx).

Você não pode exportar certificados da Automação do Azure. Você deve garantir que todos os certificados estejam disponíveis fora do Azure.

##Replicação geográfica na Automação do Azure

A Automação do Azure dá suporte à replicação geográfica. Com a replicação geográfica, a Automação do Azure mantém seus dados duráveis em duas regiões. Ao criar uma Conta de automação no portal do Azure, você escolhe a região onde ela deve ser criada, que é a região primária. A região onde seus dados são replicados geograficamente é conhecida como a região secundária. Regiões primária e secundária conversam entre si para replicar geograficamente as atualizações feitas na Conta de automação. Como a região secundária armazena uma cópia das informações, se houver o failover de uma Conta de automação da região primária para a secundária, todas as suas informações da Conta de automação ainda estariam disponíveis na região secundária.

A replicação geográfica é interna nas Contas de automação e é oferecida sem custo adicional. Você não tem controle para escolher a região secundária, ela é determinada automaticamente com base na sua escolha de região primária.

 
###Local das réplicas geográficas

Atualmente, as Contas de automação podem ser criadas nas cinco regiões abaixo, e o suporte para mais regiões será adicionado no futuro. A tabela a seguir mostra os emparelhamentos de regiões primárias e secundárias.

|Primário |Secundário
| ---------------   |----------------
|Centro-Sul dos Estados Unidos |Centro-Norte dos EUA
|Leste dos EUA 2 |Centro dos EUA
|Europa Ocidental |Norte da Europa
|Sudeste da Ásia |Ásia Oriental
|Leste do Japão |Oeste do Japão


###Recuperação de desastres na Automação do Azure

Quando um grande desastre afeta a região primária, em primeiro lugar a equipe de Automação tenta restaurar a região primária. Em alguns casos, quando não é possível restaurar a região primária, o failover geográfico é executado e os clientes afetados são notificados por meio de sua assinatura.

<!---HONumber=Oct15_HO3-->