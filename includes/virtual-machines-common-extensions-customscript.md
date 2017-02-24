

Desde seu lançamento, a extensão do Script Personalizado é amplamente usada para configurar cargas de trabalho de VMs do Windows e do Linux. Com a introdução dos modelos do Gerenciador de Recursos do Azure, os usuários agora podem criar um modelo único que não apenas provisiona a VM, mas também configura as cargas de trabalho nela.

## <a name="about-azure-resource-manager-templates"></a>Sobre os modelos do Azure Resource Manager
O modelo do Azure Resource Manager permite especificar de forma declarativa a infraestrutura IaaS do Azure na linguagem JSON definindo as dependências entre recursos. Para obter uma visão geral detalhada dos modelos do Gerenciador de Recursos do Azure, consulte os seguintes artigos:

* [Visão geral do Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)
* [Implantação de modelos com Azure PowerShell](../articles/virtual-machines/virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="prerequisites"></a>Pré-requisitos
1. Baixe [aqui](https://azure.microsoft.com/downloads/)as ferramentas de linha de comando do Azure para seu sistema operacional.
2. Se os scripts serão executados em uma VM existente, verifique se o agente de VM está habilitado na VM. Caso contrário, siga as orientações do [Linux](../articles/virtual-machines/virtual-machines-linux-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) ou do [Windows](../articles/virtual-machines/virtual-machines-windows-classic-manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para instalar uma.
3. Carregue os scripts que você deseja executar na máquina virtual para o armazenamento do Azure. Os scripts podem vir de um único ou vários contêineres de armazenamento.
4. Como alternativa, os scripts também podem ser carregados em uma conta do GitHub.
5. O script deve ser criado de forma que o script de entrada que por sua vez é iniciado pela extensão inicia outros scripts.

## <a name="using-the-custom-script-extension"></a>Usando a extensão de Script Personalizado
Para implantar com modelos, usamos a mesma versão da Extensão de Script Personalizado disponível para APIs do Azure Service Management. A extensão dá suporte os mesmos parâmetros e cenários de como carregar arquivos para a conta de Armazenamento do Azure ou local do GitHub. A principal diferença ao usar modelos é que a versão exata da extensão deve ser especificada, em vez de especificar a versão do formato de majorversion.*.



<!--HONumber=Nov16_HO3-->


