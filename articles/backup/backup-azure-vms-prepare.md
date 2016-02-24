<properties
	pageTitle="Preparando seu ambiente para fazer backup de máquinas virtuais do Azure | Microsoft Azure"
	description="Verificar se o ambiente está preparado para fazer backup de máquinas virtuais no Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""
	keywords="backups; fazendo backup;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Preparar o seu ambiente para o backup das máquinas virtuais do Azure

Antes de fazer o backup de uma VM (máquina virtual) do Azure, você precisa concluir alguns pré-requisitos. Se você já tiver preparado seu ambiente para backups, comece o [backup de suas VMs](backup-azure-vms.md). Caso contrário, prossiga com as etapas abaixo para verificar se seu ambiente está preparado.

## 1\. Cofre de backup

![Cofre de backup](./media/backup-azure-vms-prepare/step1.png)

Para iniciar o backup de suas máquinas virtuais do Azure, você precisa primeiro criar um cofre de backup. O cofre é uma entidade que armazena todos os backups e pontos de recuperação que foram criados ao longo do tempo. O cofre também contém as políticas de backup que serão aplicadas às máquinas virtuais que passarão por backup.

Esta imagem mostra os relacionamentos entre as várias entidades do Backup do Azure: ![Relação e entidades do Backup do Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para criar um cofre de backup:

1. Entre no [Portal do Azure](http://manage.windowsazure.com/).

2. Clique em **Novo** -> **Serviços de Dados** -> **Serviços de Recuperação** -> **Cofre de Backup** > **Criação Rápida**. Se você tem várias assinaturas associadas à sua conta organizacional, escolha a assinatura correta a ser associada ao cofre de backup. Em cada assinatura do Azure, você pode ter vários cofres de backup para organizar as máquinas virtuais que estão sendo protegidas.

3. Em **Nome**, digite um nome amigável para identificar o cofre. Ele precisa ser exclusivo para cada assinatura.

4. Em **Região**, selecione a região geográfica para o cofre. O cofre deve estar na mesma região que as máquinas virtuais que você deseja proteger. Se você tem máquinas virtuais em diferentes regiões, crie um cofre em cada uma delas. Não é necessário especificar contas de armazenamento para armazenar os dados de backup; o cofre de backup e o serviço de Backup do Azure cuidarão disso automaticamente.

    ![Criar cofre de backup](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

5. Clique em **Criar cofre**. Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal.

    ![Criar notificação de cofre](./media/backup-azure-vms-prepare/creating-vault.png)

6. Uma mensagem confirma que o cofre foi criado com êxito. Será listada na página de **Serviços de Recuperação** como **Ativo**. Verifique se a opção apropriada de redundância de armazenamento está marcada logo depois que o cofre for criado. Leia mais sobre [como definir a opção de redundância de armazenamento no cofre de backup](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Lista de cofres de backup](./media/backup-azure-vms-prepare/backup_vaultslist.png)

7. Clique no cofre de backup para abrir a página de **Início Rápido** na qual são exibidas as instruções para fazer o backup de máquinas virtuais do Azure.

    ![Instruções de backup de máquina virtual na página Painel](./media/backup-azure-vms-prepare/vmbackup-instructions.png)



## 2\. Conectividade de rede

![Conectividade de rede](./media/backup-azure-vms-prepare/step2.png)

A extensão de backup precisa de conectividade aos endereços IP públicos do Azure para funcionar corretamente, pois ela envia comandos para um ponto de extremidade do Armazenamento do Azure (URL HTTP) para gerenciar os instantâneos da VM. Sem a conexão correta com a Internet, essas solicitações HTTP da VM atingirão o tempo limite e a operação de backup falhará.

### Restrições de rede com NSGs

Se a implantação tiver restrições de acesso (por meio de um NSG - Grupo de Segurança de Rede, por exemplo), você deverá realizar mais etapas para garantir que o tráfego de backup do cofre do Azure Backup não seja afetado.

Há duas maneiras para providenciar um caminho para o tráfego de backup:

1. Realizar a lista branca de [intervalos de IP do banco de dados do Azure](http://www.microsoft.com/pt-BR/download/details.aspx?id=41653).
2. Implantar um proxy HTTP para encaminhar o tráfego.

O compromisso será entre a capacidade de gerenciamento, o controle granular e o custo.

|Opção|Vantagens|Desvantagens|
|------|----------|-------------|
|OPÇÃO 1: Intervalos de IPs na lista branca| Sem custo adicional.<br><br>Para habilitar o acesso em NSG, use o cmdlet <i>Set-AzureNetworkSecurityRule</i> | É complexo para gerenciar, já que os intervalos de IP afetados mudam com o tempo.<br>Fornece acesso ao Azure por completo, não somente ao Armazenamento.|
|OPÇÃO 2: Proxy HTTP| É permitido o controle granular no proxy em relação às URLs de armazenamento, <br>Ponto único de acesso à Internet nas VMs, <br>Não está sujeito a alterações do endereço IP do Azure| Custos adicionais para a execução de uma VM com o software do proxy|

### Usando um proxy HTTP para backups de uma VM
Durante o backup de uma VM, os comandos de gerenciamento de instantâneos são enviados pela extensão de backup ao Armazenamento do Azure usando uma API HTTPS. Esse tráfego deve ser encaminhado da extensão por meio do proxy, já que somente o proxy estará configurado para ter acesso à Internet pública.

>[AZURE.NOTE] Não há recomendações do software de proxy a serem usadas. Escolha um proxy que seja compatível com as etapas de configuração abaixo.

No exemplo abaixo, a VM do aplicativo deve ser configurada para usar a VM do proxy para todo o tráfego HTTP destinado à Internet pública. A VM do Proxy deve ser configurada para permitir o tráfego de entrada das VMs na rede virtual. E, por fim, o NSG (chamado *NSG-lockdown*) precisa de uma nova regra de segurança que permita o tráfego de saída da Internet da VM do proxy.

![Diagrama de implantação de proxy HTTP com NSG](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

**A) Permitir conexões de rede de saída:**

1. Para computadores com o Windows, execute o seguinte comando em um prompt de comando com privilégios elevados:

	```
	netsh winhttp set proxy http://<proxy IP>:<proxy port>
	```

	Isso configurará um proxy de todo o computador e será usado para qualquer tráfego de saída HTTP/HTTPS.

2. Para computadores Linux, adicione a linha abaixo ao arquivo ```/etc/environment```:

 	```
 	http_proxy=http://<proxy IP>:<proxy port>
 	```

	Adicione as linhas abaixo ao arquivo ```/etc/waagent.conf```:

	```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

**B) Permitir conexões de entrada no servidor proxy:**

1. Abra o Firewall do Windows no servidor proxy. Clique com o botão direito do mouse em **Regras de Entrada** e clique em **Nova Regra...**.

	![Abrir o Firewall](./media/backup-azure-vms-prepare/firewall-01.png)

	![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-02.png)
2. No **Assistente para Nova Regra de Entrada**, selecione a opção **Personalizar** para **Tipo de Regra** e clique em **Avançar**. Na página para selecionar **Programa**, escolha **Todos os Programas** e clique em **Avançar**.

3. Na página **Protocolo e Portas**, use as entradas da tabela abaixo e clique em **Avançar**:

	![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-03.png)

| Campo de entrada | Valor |
| --- | --- |
| Tipo de protocolo | TCP |
| Porta local | Selecione **Portas Específicas** na lista suspensa. Na caixa de texto, insira o ```<Proxy Port>``` que foi configurado. |
| Porta remota | Selecione **Todas as Portas** na lista suspensa. |

Para o restante do assistente, clique até o fim e dê um nome a essa regra.

**C) Adicionar uma regra de exceção ao NSG:**

Em um prompt de comando do Azure PowerShell, digite o seguinte comando:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

Esse comando adiciona uma exceção ao NSG, o que permite o tráfego TCP de qualquer porta em 10.0.0.5 para qualquer endereço da Internet na porta 80 (HTTP) ou 443 (HTTPS). Se for preciso atingir determinada porta na Internet pública, adicione isso ao ```-DestinationPortRange``` também.

*Verifique se você substituiu os nomes no exemplo com os detalhes adequados para a implantação.*

## 3\. Agente de VM

![Agente de VM](./media/backup-azure-vms-prepare/step3.png)

Antes de iniciar o backup da máquina virtual do Azure, verifique se o agente de VM do Azure está instalado corretamente na máquina virtual. Como o agente de VM é um componente opcional no momento em que a máquina virtual é criada, verifique se a caixa de seleção do Agente de VM está marcada antes que a máquina virtual seja provisionada.

### Atualização e instalação manual

O agente de VM já está presente em VMs criadas na galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o agente de VM instalado. Para essas VMs, o agente de VM precisa ser instalado explicitamente. Leia mais sobre [Instalando o Agente de VM em uma VM existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalando o agente de VM | <li>Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará de privilégios de Administrador para concluir a instalação. <li>[Atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. | <li> Instale o [agente Linux](https://github.com/Azure/WALinuxAgent) mais recente do GitHub. Você precisará de privilégios de Administrador para concluir a instalação. <li> [Atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |
| Atualizando o agente de VM | A atualização do agente de VM é tão simples quanto reinstalar os [binários do agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Verifique se nenhuma operação de backup está em execução enquanto o agente de VM está sendo atualizado. | Siga as instruções em [Atualizando o agente de VM do Linux](../virtual-machines-linux-update-agent.md). <br><br>Verifique se nenhuma operação de backup está em execução enquanto o agente de VM está sendo atualizado. |
| Validando a instalação do agente de VM | <li>Navegue até a pasta *C:\\WindowsAzure\\Packages* na VM do Azure. <li>Você deve encontrar o arquivo WaAppAgent.exe presente.<li> Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do produto deve ser 2.6.1198.718 ou mais recente. | - |


Saiba mais sobre o [agente de VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como instalá-lo](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Extensão de backup

Para fazer backup da máquina virtual, o serviço do Backup do Azure instala uma extensão para o agente de VM. O serviço do Backup do Azure atualiza e corrige perfeitamente a extensão de backup sem intervenção adicional do usuário.

A extensão de backup será instalada se a VM estiver em execução. Uma VM em execução também oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo. No entanto, o serviço do Backup do Azure continuará a realizar o backup da VM mesmo quando esta estiver desativada e a extensão não puder ser instalada (também conhecida como VM Offline). Nesse caso, o ponto de recuperação será *falha consistente*, como discutido anteriormente.


## Limitações

- Não há suporte para o backup de máquinas virtuais baseadas no Gerenciador de Recursos do Azure (também conhecido como IaaS V2).
- Não há suporte para o backup de máquinas virtuais com mais de 16 discos de dados.
- Não há suporte para o backup de máquinas virtuais usando o Armazenamento Premium.
- Não há suporte para o backup de máquinas virtuais com um endereço IP reservado e nenhum ponto de extremidade definido.
- Não há suporte para a substituição de uma máquina virtual existente durante a restauração. Primeiro, exclua a máquina virtual existente e todos os discos associados e, em seguida, restaure os dados do backup.
- Não há suporte para backup e restauração entre regiões.
- O backup de máquinas virtuais usando o serviço Backup do Azure tem suporte em todas as regiões públicas do Azure (confira a [lista de verificação](https://azure.microsoft.com/regions/#services) de regiões com suporte). Se a região que você procura ainda não tem suporte, ela não aparecerá na lista suspensa durante a criação de cofre.
- O backup de máquinas virtuais usando o serviço Backup do Azure tem suporte somente para determinadas versões de sistema operacional:
  - **Linux**: confira [a lista de distribuições endossadas pelo Azure](../virtual-machines/virtual-machines-linux-endorsed-distributions.md). Outras distribuições personalizadas do Linux também devem funcionar, contanto que o agente de VM esteja disponível na máquina virtual.
  - **Windows Server**: não há suporte para versões anteriores ao Windows Server 2008 R2.
- A restauração de uma VM DC (controladora de domínio) que é parte de uma configuração multi-DC tem suporte somente usando o PowerShell. Leia mais sobre [como restaurar um controlador de domínio com vários DCs](backup-azure-restore-vms.md#restoring-domain-controller-vms)
- Apenas há suporte para a restauração de máquinas virtuais que têm as seguintes configurações de rede especial por meio do PowerShell. Máquinas virtuais que você criar usando o fluxo de trabalho de restauração na interface do usuário não terão essas configurações de rede depois que a operação de restauração for concluída. Para saber mais, confira [Restaurando VMs com configurações de rede especiais](backup-azure-restore-vms.md#restoring-vms-with-special-netwrok-configurations).
	- Máquinas virtuais sob configuração do balanceador de carga (interno e externo)
	- Máquinas virtuais com vários endereços IP reservados
	- Máquinas virtuais com vários adaptadores de rede

## Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## Próximas etapas

- [Planeje sua infraestrutura de backup da VM](backup-azure-vms-introduction.md)
- [Backup de máquinas virtuais](backup-azure-vms.md)
- [Gerenciar backups de máquinas virtuais](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0128_2016-->