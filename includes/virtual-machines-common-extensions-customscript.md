

Desde seu lançamento, a extensão do Script Personalizado é amplamente usada para configurar cargas de trabalho de VMs do Windows e do Linux. Com a introdução dos modelos do Gerenciador de Recursos do Azure, os usuários agora podem criar um modelo único que não apenas provisiona a VM, mas também configura as cargas de trabalho nela.

## Sobre os modelos do Gerenciador de Recursos do Azure

O modelo do Gerenciador de Recursos do Azure permite que você especifique de forma declarativa a infraestrutura IaaS do Azure na linguagem JSON definindo as dependências entre recursos. Para obter uma visão geral detalhada dos modelos do Gerenciador de Recursos do Azure, consulte os seguintes artigos:

- [Visão geral do Grupo de Recursos](../articles/resource-group-overview.md)
- [Implantação de modelos com CLI do Azure](../articles/virtual-machines/virtual-machines-linux-cli-manage.md)
- [Implantação de modelos com Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-ps-manage.md)

### Pré-requisitos

1. Baixe [aqui](https://azure.microsoft.com/downloads/) as ferramentas de linha de comando do Azure para seu sistema operacional.
2. Se os scripts terão de ser executados em uma VM existente, verifique se o Agente de VM está habilitado na VM; em caso negativo, siga as diretrizes em [Linux](../articles/virtual-machines/virtual-machines-linux-classic-manage extensions.md) ou [Windows](../articles/virtual-machines/virtual-machines-windows-classic-manage extensions.md) para instalar uma.
3. Carregue os scripts que você deseja executar na máquina virtual para o armazenamento do Azure. Os scripts podem vir de um único ou vários contêineres de armazenamento.
4. Como alternativa, os scripts também podem ser carregados em uma conta do GitHub.
5. O script deve ser criado de forma que o script de entrada que por sua vez é iniciado pela extensão inicia outros scripts.

## Usando a extensão de script personalizado

Para implantar com modelos, usamos a mesma versão da Extensão de Script Personalizado disponível para APIs de Gerenciamento de Serviços do Azure. A extensão dá suporte os mesmos parâmetros e cenários de como carregar arquivos para a conta de Armazenamento do Azure ou local do Github. A principal diferença ao usar modelos é que a versão exata da extensão deve ser especificada, em vez de especificar a versão do formato de majorversion.*.

<!---HONumber=AcomDC_0330_2016-->