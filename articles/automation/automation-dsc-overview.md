<properties 
   pageTitle="Visão Geral da DSC de Automação do Azure | Microsoft Azure" 
   description="Uma visão geral da DSC (Configuração do Estado Desejado) da Automação do Azure, seus termos e problemas conhecidos" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="09/16/2015"
   ms.author="coreyp"/>

# Visão geral da DSC da Automação do Azure #

## O que é a DSC do PowerShell? ##
A DSC (Configuração do Estado Desejado) é uma nova plataforma de gerenciamento do Windows PowerShell que habilita o gerenciamento de configuração para hosts físicos e máquinas virtuais usando uma sintaxe declarativa do PowerShell.

A DSC fornece um conjunto de extensões de linguagem do Windows PowerShell, novos cmdlets do Windows PowerShell e recursos que você pode usar para especificar declarativamente como deseja que seu ambiente de software seja configurado. Ela também fornece um meio de manter e gerenciar configurações existentes.

### Aplicações práticas ###
A seguir estão alguns cenários de exemplos em que você pode usar recursos internos da DSC para configurar e gerenciar um conjunto de computadores (também conhecidos como nós de destino) de maneira automatizada:

- Habilitando ou desabilitando recursos e funções de servidor
- Gerenciando configurações do Registro
- Gerenciando arquivos e diretórios
- Iniciando, interrompendo e gerenciando processos e serviços
- Gerenciando grupos e contas de usuário
- Implantando novo software
- Gerenciando variáveis de ambiente
- Executando scripts do Windows PowerShell
- Corrigindo uma configuração que se afastou do estado desejado
- Descobrindo o estado de configuração real em determinado nó

Além disso, você pode criar recursos personalizados para definir o estado de qualquer aplicativo ou configuração do sistema.


Para obter mais detalhes sobre a DSC do PowerShell, consulte: [Configuração em um mundo de DevOps ‒ Configuração do Estado Desejado do Windows PowerShell](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx)

##O que é a DSC da Automação do Azure?##
A DSC da Automação do Azure baseia-se nos conceitos básicos introduzidos na DSC do PowerShell para fornecer uma experiência de gerenciamento de configuração ainda mais fácil. A DSC da Automação do Azure oferece a mesma camada de gerenciamento para a [Configuração do Estado Desejado do PowerShell](https://technet.microsoft.com/library/dn249912.aspx) <link> que a Automação do Azure oferece para scripts do PowerShell atualmente.

A DSC da Automação do Azure permite que você [crie e gerencie Configurações do Estado Desejado do PowerShell](https://technet.microsoft.com/library/dn249918.aspx), importe [Recursos da DSC](https://technet.microsoft.com/library/dn282125.aspx) e gere Configurações de Nós da DSC (documentos MOF), tudo na nuvem. Esses itens da DSC serão colocados no [servidor de pull da DSC](https://technet.microsoft.com/library/dn249913.aspx) da Automação do Azure para que nós de destino (como máquinas físicas e virtuais) na nuvem ou no local possam coletá-los, automaticamente manter a conformidade com o estado desejado que especificam e relatar sua conformidade com o estado desejado para a Automação do Azure.

Prefere assistir do que ler? Examine o vídeo abaixo, de 2015 de maio, quando a DSC de Automação do Azure foi anunciada pela primeira vez. **Observação:** embora os conceitos e ciclo de vida abordados neste vídeo estejam corretos, a DSC de Automação do Azure avançou muito desde que este vídeo foi gravado. Ele agora está em visualização pública, tem uma interface do usuário mais ampla no portal do Azure e dá suporte a recursos adicionais.

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]

## Termos da DSC da Automação do Azure ##
### Configuração ###
A DSC do PowerShell introduziu um novo conceito, chamado de configurações. As configurações permitem que você defina, por meio da sintaxe do PowerShell, o estado desejado para seu ambiente. Para usar a DSC para configurar seu ambiente, primeiro defina um bloco de script do Windows PowerShell usando a palavra-chave de configuração, inclua um identificador depois dela e, em seguida, chaves ({}) para delimitar o bloco.

![texto alternativo](./media/automation-dsc-overview/AADSC_1.png)

Dentro do bloco de configuração, você pode definir blocos de configuração de nó que especificam a configuração desejada para um conjunto de nós (computadores) em seu ambiente que devem ser configurados exatamente da mesma maneira. Assim, uma configuração de nó representa uma "função" que um ou mais nós devem assumir. Um bloco de configuração de nó começa com a palavra-chave do nó. Inclua depois dessa palavra-chave o nome da função, que pode ser uma variável ou expressão. Após o nome da função, use chaves {} para delimitar o bloco de configuração de nó.

![texto alt](./media/automation-dsc-overview/AADSC_2.png)
 
Dentro do bloco de configuração de nó, você pode definir blocos de recursos para configurar recursos específicos da DSC. Um bloco de recursos começa com o nome do recurso, seguido do identificador que você deseja especificar para o bloco e chaves {} para delimitar o bloco.

![texto alternativo](./media/automation-dsc-overview/AADSC_3.png)

Para obter informações mais detalhadas sobre a palavra-chave de configuração, consulte: [Noções básicas sobre a palavra-chave de configuração na Configuração do Estado Desejado](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Noções básicas sobre a palavra-chave de configuração na Configuração do Estado Desejado")

A execução (compilação) de uma configuração da DSC produzirá uma ou mais configurações de nós da DSC (documentos MOF), que são o que os nós da DSC aplicam para manter a conformidade com o estado desejado.

A DSC da Automação do Azure permite que você importe, crie e compile configurações da DSC na Automação do Azure, da mesma forma como runbooks podem ser importados, criados e iniciados na Automação do Azure.

>[AZURE.IMPORTANT]Uma configuração deve conter apenas um bloco de configuração com o mesmo nome que a configuração, na DSC da Automação do Azure.


###Configuração de nó###

Quando uma Configuração da DSC é compilada, uma ou mais configurações de nó são produzidas, dependendo dos blocos de Nó na configuração. Uma configuração de nó é o mesmo que um "MOF" ou "documento de configuração" (se você estiver familiarizado com esses termos da DSC do PS) e representa uma "função", como servidor Web ou trabalhador, o estado desejado que um ou mais nós devem assumir. Nomes de configurações de nó na DSC de Automação do Azure assumem a forma de "<Configuration-name>.<Node configuration-block-name>".

Os nós da DSC do PS ficam cientes de configurações de nós que eles devem aplicar via push da DSC ou métodos de pull. A DSC da Automação do Azure utiliza o método de pull da DSC, em que nós solicitam configurações de nó que devem aplicar do servidor de pull da DSC da Automação do Azure. Como os nós fazem a solicitação à DSC da Automação do Azure, eles podem estar atrás de firewalls, ter todas as portas de entrada fechadas etc. Eles só precisam de acesso de saída à Internet.


###Nó###

Um nó DSC é qualquer computador que tenha sua configuração gerenciada pelo DSC. Pode se tratar de uma VM do Azure ou de uma VM local/host físico. Os nós aplicam configurações de nó para obter e manter a conformidade com o estado desejado que definem e também podem relatar a um servidor de relatórios seu status de configuração e sua conformidade.

A DSC da Automação do Azure facilita a integração de nós para gerenciamento pela DSC da Automação do Azure e permite a alteração da configuração de nó atribuída a cada nó do servidor, Assim, na próxima vez que um nó verificar o servidor para obter instruções, ele assumirá uma função diferente e alterará sua configuração de forma correspondente. Os nós também relatam seu status e sua conformidade de configuração à DSC da Automação do Azure.


###Recurso###
Os recursos da DSC são blocos de construção que você pode usar para definir uma DSC (Configuração do Estado Desejado) do Windows PowerShell. A DSC vem com um conjunto de funcionalidade interna para configurar recursos, como arquivos e pastas, recursos de servidor e funções, configurações do Registro, variáveis de ambiente, serviços e processos. Para saber mais sobre a lista completa de recursos internos da DSC e como usá-los, consulte [Recursos internos da Configuração do Estado Desejado do Windows PowerShell](https://technet.microsoft.com/library/dn249921.aspx).

Os recursos da DSC também podem ser importados como parte dos módulos do PowerShell para estender o conjunto de recursos internos da DSC. Os recursos não padrão serão obtidos por nós da DSC do servidor de pull da DSC, se uma configuração de nó que o nó precisar aplicar contiver referências a esses recursos. Para saber como criar recursos personalizados, consulte [Criar recursos personalizados da Configuração do Estado Desejado do Windows PowerShell](https://technet.microsoft.com/library/dn249927.aspx).

A DSC da Automação do Azure é fornecida com todos os mesmos recursos internos da DSC que a DSC do PS. Recursos adicionais podem ser adicionados à DSC da Automação do Azure importando-se módulos do PowerShell que contêm os recursos para a Automação do Azure.


###Trabalho de compilação###
Um trabalho de compilação na DSC da Automação do Azure é uma instância da compilação de uma configuração para criar uma ou mais configurações de nó. Eles são semelhantes aos trabalhos de runbook da Automação do Azure, com a exceção de que não executam realmente tarefa alguma, exceto a criação de configurações de nó. As configurações do nó criadas por um trabalho de compilação são colocadas automaticamente no servidor de pull da DSC de Automação do Azure e substituem versões anteriores das configurações de nó, se elas existirem para essa configuração. O nome de uma configuração de nó produzida por um trabalho de compilação tem o formato "<Configuration-name>.<Node configuration-block-name>". Por exemplo, a compilação da configuração a seguir geraria uma única configuração de nó chamada "MyConfiguration.webserver"


![texto alternativo](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE]Assim como os runbooks, as configurações podem ser publicadas. Isso não está relacionado a colocar itens da DSC no servidor de pull da DSC da Automação do Azure. Os trabalhos de compilação fazem com que itens da DSC sejam colocados no servidor de pull da DSC da Automação do Azure. Para obter mais informações sobre como "publicar" na Automação do Azure, consulte [Publicando um Runbook](https://msdn.microsoft.com/library/dn903765.aspx).

No momento, a DSC da Automação do Azure fornece os seguintes cmdlets no [módulo do PowerShell do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/mt244122.aspx) para gerenciamento de trabalhos de compilação:

-	`Get-AzureAutomationDscCompilationJob`
-	`Get-AzureAutomationDscCompilationJobOutput`
-	`Start-AzureAutomationDscCompilationJob`

##Ciclo de vida de DSC de Automação do Azure##
Passar de uma conta de automação vazia para um conjunto gerenciado de nós configurados corretamente envolve um conjunto de processos para definir as configurações, transformá-las em configurações de nó e integrar os nós à DSC de Automação do Azure e a essas configurações de nó. O diagrama a seguir ilustra o ciclo de vida de DSC de Automação do Azure:

![texto alt](./media/automation-dsc-overview/DSCLifecycle.png)


##Problemas gerais conhecidos:##

- No momento, a DSC da Automação do Azure não dá suporte a configurações da DSC parciais ou compostas.

- A última versão do WMF 5 deve ser instalada para que o agente da DSC do PowerShell para Windows possa se comunicar com a Automação do Azure. No momento, o agente da DSC do PowerShell para Linux não dá suporte à comunicação com a automação do Azure. Isso deve ser atualizado em breve.

- A Automação do Azure não dá suporte ao uso de módulos do PowerShell lado a lado. Isso significa que todas as configurações em uma conta da Automação devem trabalhar com a última versão de um módulo de PowerShell importado para essa conta da Automação de automação e com quaisquer recursos da DSC do PowerShell que o módulo contenha e que a configuração use.

- O servidor de pull da DSC do PowerShell tradicional espera que zips de módulos sejam colocados no servidor de pull no formato **ModuleName\_Version.zip "**. A Automação do Azure espera que módulos do PowerShell sejam importados com nomes no formato **ModuleName.zip**. Consulte [esta postagem de blog](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/) para obter mais informações sobre o formato do Módulo de Integração necessário para importar o módulo para a Automação do Azure.

- Os módulos do PowerShell que especificam recursos da DSC lado a lado dentro do módulo, usando o formato "versão por pasta", não funcionarão na Automação do Azure no momento.

- Os módulos do PowerShell importados para a Automação do Azure não podem conter arquivos .doc ou .docx. Alguns módulos do PowerShell contendo recursos da DSC contêm esses arquivos, para fins de ajuda. Esses arquivos devem ser removidos dos módulos antes da importação para a Automação do Azure.

- Quando um nó é registrado pela primeira vez com uma conta da Automação do Azure ou o nó é alterado para ser mapeado para uma configuração de nó diferente no servidor, seu status será ‘Compatível’, mesmo que o status do nó não seja realmente compatível com a configuração de nó para a qual ele está mapeado agora. Depois que o nó executa seu primeiro pull e envia seu primeiro relatório após o registro ou uma alteração de mapeamento de configuração de nó, o status do nó é confiável.

- Ao se integrar uma VM do Azure para gerenciamento com a DSC da Automação do Azure usando `Register-AzureAutomationDscNode`, `Set-AzureVMExtension` ou a extensão de VM da DSC da Automação do Azure no portal de visualização do Azure, se o registro falhar com **O nome do computador não foi especificado, e o diretório de configuração não tem nenhum arquivo de configuração**, isso é um alarme falso, e o registro da VM foi bem-sucedido. O registro bem-sucedido pode ser verificado usando-se o cmdlet `Get-AzureAutomationDscNode`.

- Ao integrar uma VM do Azure para gerenciamento com a DSC de Automação do Azure usando `Register-AzureAutomationDscNode`, `Set-AzureVMExtension` ou a extensão de VM da DSC de Automação do Azure no portal de visualização do Azure, pode levar até uma hora para que a VM seja mostrada como um nó da DSC na Automação do Azure. Isso se deve à instalação do Windows Management Framework 5.0 na VM pela extensão DSC da VM do Azure, que precisa integrar a VM à DSC da Automação do Azure.

- Registrar nós envolve a negociação, por parte dos nós, de um certificado a ser usado para a autenticação desse nó específico para a DSC de Automação do Azure, pós-registro. Este certificado tem uma validade de um ano desde sua criação e, atualmente, o protocolo pull da DSC do PowerShell não tem nenhum método para emitir um novo certificado quando esse certificado está prestes a expirar. Por esse motivo, os nós precisarão ser registrados com a DSC de Automação do Azure após o período de um ano, até que esse protocolo seja implementado em uma versão futura do WMF (espera-se que seja em menos de um ano da data atual).

##Artigos relacionados##

- [cmdlets da DSC de Automação do Azure](https://msdn.microsoft.com/library/mt244122.aspx)
- [preço da DSC de Automação do Azure](http://azure.microsoft.com/pricing/details/automation/)

<!---HONumber=Sept15_HO4-->