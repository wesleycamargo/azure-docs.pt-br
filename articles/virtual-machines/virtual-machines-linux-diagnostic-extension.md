
<properties
		pageTitle="Usar a Extensão de Diagnóstico do Linux para monitorar os dados de desempenho e diagnóstico da VM Linux | Microsoft Azure"
	description="Saiba como usar a Extensão de Diagnóstico do Linux para monitorar os dados de desempenho e diagnóstico da VM Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="NingKuang"
	manager="timlt"
	editor=""
	tags=""/>

<tags
		ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2015"
	ms.author="Ning"/>


# Usar a Extensão de Diagnóstico do Linux para monitorar os dados de desempenho e diagnóstico da VM Linux

## Introdução

A Extensão de Diagnóstico do Linux ajuda um usuário a monitorar as VMs Linux em execução no Microsoft Azure, com as seguintes funcionalidades:

- Coleta e carrega dados de desempenho, diagnóstico e syslog do sistema da VM Linux na tabela de armazenamento do usuário.
- Permite que o usuário personalize as métricas de dados que serão coletadas e carregadas.
- Permite que o usuário carregue arquivos de log especificados na tabela de armazenamento designada.

Para a versão 2.0, os dados incluem:

- Todos os logs Rsyslog do Linux, incluindo logs de sistema, segurança e aplicativos.
- Todos os dados de sistema especificados neste [documento](https://scx.codeplex.com/wikipage?title=xplatproviders").
- Arquivos de log especificados pelo usuário.

## Como habilitar a extensão
A extensão pode ser habilitada por meio do [portal do Azure](https://ms.portal.azure.com/#), Azure PowerShell ou de scripts da CLI do Azure.

Para exibir e configurar os dados de desempenho e sistema diretamente do portal do Azure, siga estas [etapas](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "URL para o blog do Windows").


Este artigo se concentra na habilitação e configuração de extensão por meio de comandos da CLI do Azure. Isso permite ler e exibir os dados diretamente da tabela de armazenamento.


## Pré-requisitos
- Agente Linux do Microsoft Azure versão 2.0.6 ou posterior. Observe que a maioria das imagens de galeria da VM Linux do Azure inclui a versão 2.0.6 ou posterior. Você pode executar **WAAgent-version** para confirmar a versão instalada na VM. Se a VM estiver executando uma versão anterior ao 2.0.6, execute estas [instruções](https://github.com/Azure/WALinuxAgent "instruções") para atualizá-la.
- [CLI do Azure](./xplat-cli.md). Siga [esta diretriz](./xplat-cli-install.md) para configurar o ambiente da CLI do Azure em seu computador. Quando a CLI do Azure estiver instalada, você poderá usar o comando **azure** na sua interface de linha de comando (Bash, Terminal, prompt de comando) para acessar os comandos da CLI do Azure. Por exemplo, execute **azure vm extension set --help** para ver o uso detalhado, execute **azure login** para fazer logon no Azure, execute **azure vm list** para listar todas as máquinas virtuais que possui no Azure.
- Uma conta de armazenamento para armazenar os dados. Você precisará de um nome de conta de armazenamento e de uma tecla de acesso criados anteriormente para carregar os dados no armazenamento.


## Usar o comando CLI do Azure para habilitar a Extensão de Diagnóstico do Linux

###  Cenário 1. Habilitar a extensão com o conjunto de dados padrão
Para a versão 2.0 ou posterior, os dados padrão que serão coletados incluem:

- Todas as informações de Rsyslog (incluindo os logs de sistema, segurança e aplicativo).  
- Um conjunto principal de dados do sistema base. Observe que o conjunto de dados completo foi descrito neste [documento](https://scx.codeplex.com/wikipage?title=xplatproviders). Se quiser habilitar dados extras, continue com as etapas do cenário 2 e 3.

Etapa 1. Crie um arquivo denominado PrivateConfig.json com o conteúdo a seguir.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account"
	}

Etapa 2. Execute **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Cenário 2: Personalizar a métrica do monitor de desempenho  
Esta seção descreve como personalizar a tabela de dados de desempenho e diagnóstico.

Etapa 1. Crie um arquivo denominado PrivateConfig.json com o conteúdo que aparece no próximo exemplo. Especifique os dados específicos que deseja coletar.

Para todos os provedores e variáveis que recebem suporte, consulte este [documento](https://scx.codeplex.com/wikipage?title=xplatproviders). Você pode ter várias consultas e armazená-las em várias tabelas, acrescentando mais consultas ao script.

Por padrão, os dados de Rsyslog sempre são coletados.

	{
     	"storageAccountName":"storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"perfCfg":[
           	{"query":"SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation","table":"LinuxMemory"
           	}
          ]
	}


Etapa 2. Execute **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Cenário 3: Carregar os próprios arquivos de log
Esta seção descreve como coletar e carregar arquivos de log específicos em sua conta de armazenamento. Você precisa especificar o caminho até o arquivo de log e o nome da tabela para armazenar seu log. Você pode ter vários arquivos de log adicionando várias entradas de arquivo/tabela ao script.

Etapa 1. Crie um arquivo denominado PrivateConfig.json com o conteúdo a seguir.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"key of the account",
      	"fileCfg":[
           	{"file":"/var/log/mysql.err",
             "table":"mysqlerr"
           	}
          ]
	}


Etapa 2. Execute **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


###   Cenário 4: Desabilitar a extensão do monitor Linux
Etapa 1. Crie um arquivo denominado PrivateConfig.json com o conteúdo a seguir.

	{
     	"storageAccountName":"the storage account to receive data",
     	"storageAccountKey":"the key of the account",
     	“perfCfg”:[],
     	“enableSyslog”:”False”
	}


Etapa 2. Execute **azure vm extension set vm\_name LinuxDiagnostic Microsoft.OSTCExtensions 2.* --private-config-path PrivateConfig.json**.


## Examinar os dados
Os dados de desempenho e diagnóstico são armazenados em uma tabela de Armazenamento do Azure. Leia [este artigo](storage-ruby-how-to-use-table-storage.md) para saber como acessar os dados na tabela de armazenamento usando scripts da CLI do Azure.

Além disso, você pode usar as ferramentas de interface do usuário a seguir para acessar os dados:

1.	Use o Gerenciador de Servidores do Visual Studio. Navegue até sua conta de armazenamento. Depois que a VM for executada por aproximadamente 5 minutos, você deverá ver as quatro tabelas padrão: "LinuxCpu", "LinuxDisk", "LinuxMemory" e "Linuxsyslog". Clique duas vezes no nome da tabela para exibir os dados.
2.	Use o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/ "Gerenciador de Armazenamento do Azure") para acessar os dados.

![imagem](./media/virtual-machines-linux-diagnostic-extension/no1.png)

Se tiver habilitado fileCfg ou perfCfg especificado nos Cenários 2 e 3, você poderá usar as ferramentas anteriores para exibir dados não padrão.



## Problemas conhecidos
- Para a versão 2.0, as informações do Rsyslog e o arquivo de log especificado pelo cliente só podem ser acessados por meio de scripts.
- Para a versão 2.0, se você tiver habilitado a extensão de Diagnóstico do Linux por meio de script primeiro, não será possível exibir os dados no portal do Azure. Se você tiver habilitado a extensão no portal primeiro, os scripts ainda funcionarão.

<!---HONumber=August15_HO9-->