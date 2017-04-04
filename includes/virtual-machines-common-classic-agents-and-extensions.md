

As Extensões de VM podem ajudá-lo a:

* Modificar os recursos de segurança e identidade, como redefinir os valores de conta e usar antimalware
* Iniciar, parar ou configurar o monitoramento e o diagnóstico
* Redefinir ou instalar os recursos de conectividade, como RDP e SSH
* Diagnosticar, monitorar e gerenciar suas VMs

Há muitos outros recursos. Novos recursos de extensão de VM são lançados regularmente. Este artigo descreve os agentes de VM do Azure para Windows e Linux e como dar suporte à funcionalidade de Extensão de VM. Para ver uma lista de Extensões de VM por categoria de recurso, veja [Extensões de VM e recursos do Azure](../articles/virtual-machines/virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agentes de VM do Azure para Windows e Linux
O Agente de VM (Agente de máquinas virtuais do Azure) é um processo seguro e leve que instala, configura e remove extensões de VM em instâncias de máquinas virtuais do Azure. O agente de VM atua como o serviço de controle local seguro para a sua VM do Azure. As extensões que o agente carrega fornecem recursos específicos para aumentar a sua produtividade usando a instância.

Existem dois agentes de VM do Azure, um para máquinas virtuais do Windows e outro para VMs do Linux.

Se você quiser uma instância de máquina virtual para usar uma ou mais extensões de VM, a instância deverá ter um agente de VM instalado. Uma imagem de máquina virtual criada usando o portal do Azure e uma imagem a partir de **Marketplace** instalam um agente de VM no processo de criação automaticamente. Se uma instância de máquina virtual não possui um Agente de VM, você pode instalar o Agente de VM depois que a instância de máquina virtual é criada. Você também pode instalar o agente em uma imagem VM personalizada que é carregada posteriormente.

> [!IMPORTANT]
> Esses Agentes de VM são serviços muito leves que permitem a administração segura de instâncias de máquina virtual. Pode haver casos em que você não deseja o agente de VM. Nesse caso, crie VMs que não possuem o agente instalado usando o PowerShell ou a CLI do Azure. Embora o Agente de VM possa ser removido fisicamente, o comportamento das extensões de VM na instância é indefinido. Como resultado, não há suporte para a remoção de um Agente de VM instalado.
>

O Agente de VM é habilitado nas seguintes situações:

* Quando você cria uma instância de uma VM usando o portal do Azure e selecionando uma imagem no **Marketplace**.
* Quando você cria uma instância de uma VM usando o cmdlet [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) ou [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Você pode criar uma VM sem um Agente de VM adicionando o parâmetro **– DisableGuestAgent** ao cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx).

* Quando você baixa e instala o agente de VM manualmente em uma instância VM existente e define o valor **ProvisionGuestAgent** como **true**. Você pode usar essa técnica para agentes Windows e Linux usando um comando do PowerShell ou uma chamada REST. (Se você não definir o valor **ProvisionGuestAgent** após instalar manualmente o Agente de VM, a adição do Agente de VM não será detectada corretamente.) O exemplo de código a seguir mostra como fazer isso usando o PowerShell quando os argumentos `$svc` e `$name` já foram determinados:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Quando você cria uma imagem de VM que inclui um Agente de VM instalado. Depois que a imagem com o agente de VM existe, você pode carregar a imagem no Azure. Para uma VM do Windows, baixe o [arquivo .msi do Agente de VM do Windows](http://go.microsoft.com/fwlink/?LinkID=394789) e instale o Agente de VM. Para uma VM do Linux, instale o Agente de VM do repositório GitHub localizado em <https://github.com/Azure/WALinuxAgent>. Para saber mais sobre como instalar o Agente de VM no Linux, consulte o [Guia do usuário do Agente de VM no Linux do Azure](../articles/virtual-machines/virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> No PaaS, o Agente de VM é chamado **WindowsAzureGuestAgent** e sempre está disponível na Web e nas VMs de Função de Trabalho. (Para saber mais, consulte [Arquitetura de funções do Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx).) Agora, o Agente de VM para VMs de função pode adicionar extensões às VMs de serviço de nuvem, da mesma maneira que faz para máquinas virtuais persistentes. A maior diferença entre as extensões de VM nas VMs de função e nas VMs persistentes é quando as extensões VM são adicionadas. Com as VMs de função, as extensões são adicionadas primeiro ao serviço de nuvem e, em seguida, às implantações no serviço de nuvem.
>
> Use o cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) para listar todas as extensões de VM de função disponíveis.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Localizar, adicionar, atualizar e remover extensões de VM
Para obter detalhes sobre essas tarefas, veja [Adicionar, encontrar, atualizar e remover Extensões de VM do Azure](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
