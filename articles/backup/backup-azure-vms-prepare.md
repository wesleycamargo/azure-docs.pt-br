<properties
	pageTitle="Preparando seu ambiente para fazer backup de máquinas virtuais do Azure | Microsoft Azure"
	description="Verificar se o ambiente está preparado para fazer backup de máquinas virtuais no Azure"
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="backups; fazendo backup;"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/01/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Preparar o seu ambiente para o backup das máquinas virtuais do Azure

Antes de poder fazer backup de uma máquina virtual (VM) do Azure, há três condições que devem existir.

- Você precisa criar um cofre de backup ou identificar um cofre de backup existente *na mesma região da VM*.
- Estabeleça a conectividade de rede entre os endereços de Internet públicos do Azure e os pontos de extremidade do armazenamento do Azure.
- Instale o agente de VM na VM.

Se você souber que essas condições já existem em seu ambiente, prossiga para o [artigo Fazer backup das suas VMs](backup-azure-vms.md). Caso contrário, continue a ler este artigo. Ele guiará você pelas etapas da preparação do seu ambiente para o backup de uma VM do Azure.


## Limitações durante o backup e a restauração de uma VM

>[AZURE.NOTE] O Azure tem dois modelos de implantação para a criação e o trabalho com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). A lista a seguir fornece as limitações durante a implantação no modelo clássico.

- Não há suporte para o backup de máquinas virtuais baseadas no ARM (Azure Resource Manager, também conhecido como IaaS V2).
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

## Criar um cofre de backup para uma VM

O cofre de backup é uma entidade que armazena todos os pontos de backups e de recuperação criados ao longo do tempo. O cofre de backup também contém as políticas de backup que serão aplicadas às máquinas virtuais incluídas no backup.

Esta imagem mostra os relacionamentos entre as várias entidades do Backup do Azure: ![Relação e entidades do Backup do Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para criar um cofre de backup:

1. Entre no [Portal do Azure](http://manage.windowsazure.com/).

2. No portal do Azure, clique em **Novo** > **Integração Híbrida** > **Backup**. Quando você clicar em **Backup**, alternará automaticamente para o portal clássico (mostrado após a Observação).

    ![Portal do Ibiza](./media/backup-azure-vms-prepare/Ibiza-portal-backup01.png)

    >[AZURE.NOTE] Se sua assinatura tiver sido usada pela última vez no portal clássico, ela poderá ser aberta no portal clássico. Neste caso, para criar um cofre de backup, clique em **Novo** > **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Backup** > **Criação Rápida** (veja a imagem abaixo).

    ![Criar cofre de backup](./media/backup-azure-vms-prepare/backup_vaultcreate.png)

3. Em **Nome**, insira um nome amigável para identificar o cofre. O nome precisa ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

4. Em **Região**, selecione a região geográfica para o cofre. O cofre deve estar na mesma região que as máquinas virtuais que você deseja proteger. Se você tiver máquinas virtuais em várias regiões, será necessário criar um cofre de backup em cada região. Não é necessário especificar contas de armazenamento para armazenar os dados de backup; o cofre de backup e o serviço de Backup do Azure cuidarão disso automaticamente.

5. Em **Assinatura**, selecione a assinatura que você deseja associar ao cofre de backup. Só haverá múltiplas opções se sua conta organizacional estiver associada a várias assinaturas do Azure.

6. Clique em **Criar cofre**. Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal.

    ![Criar notificação de cofre](./media/backup-azure-vms-prepare/creating-vault.png)

7. Uma mensagem confirmará que o cofre foi criado com êxito. Ele será listado na página de **serviços de recuperação** como **Ativo**. Verifique se a opção apropriada de redundância de armazenamento está marcada logo depois que o cofre for criado. Leia mais sobre [como definir a opção de redundância de armazenamento no cofre de backup](backup-configure-vault.md#azure-backup---storage-redundancy-options).

    ![Lista de cofres de backup](./media/backup-azure-vms-prepare/backup_vaultslist.png)

8. Clique no cofre de backup para abrir a página de **Início Rápido** na qual são exibidas as instruções para fazer o backup de máquinas virtuais do Azure.

    ![Instruções de backup de máquina virtual na página Painel](./media/backup-azure-vms-prepare/vmbackup-instructions.png)


## Conectividade de rede

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

1. No servidor proxy, abra o Firewall do Windows. A maneira mais fácil de acessar o firewall é procurar o Firewall do Windows com Segurança Avançada.

    ![Abrir o Firewall](./media/backup-azure-vms-prepare/firewall-01.png)

2. No diálogo Firewall do Windows, clique com botão direito do mouse em **Regras de Entrada** e clique em **Nova Regra...**.

    ![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-02.png)

3. No **Assistente para Nova Regra de Entrada**, selecione a opção **Personalizar** para **Tipo de Regra** e clique em **Avançar**.
4. Na página para selecionar **Programa**, escolha **Todos os Programas** e clique em **Avançar**.

5. Na página **Protocolo e Portas**, insira as seguintes informações e clique em **Avançar**:

    ![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-03.png)

    - para *Tipo de protocolo*, escolha *TCP*
    - para *Porta local*, escolha *Portas Específicas*, no campo abaixo especifique o ```<Proxy Port>``` que foi configurado.
    - para *Porta remota*, escolha *Todas as Portas*

    Para o restante do assistente, clique até o fim e dê um nome a essa regra.

**C) Adicionar uma regra de exceção ao NSG:**

Em um prompt de comando do Azure PowerShell, digite o seguinte comando:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

Esse comando adiciona uma exceção ao NSG, o que permite o tráfego TCP de qualquer porta em 10.0.0.5 para qualquer endereço da Internet na porta 80 (HTTP) ou 443 (HTTPS). Se for preciso atingir determinada porta na Internet pública, adicione isso ao ```-DestinationPortRange``` também.

*Verifique se você substituiu os nomes no exemplo com os detalhes adequados para a implantação.*


## Agente de VM

Antes de iniciar o backup da máquina virtual do Azure, verifique se o agente de VM do Azure está instalado corretamente na máquina virtual. Como o agente de VM é um componente opcional no momento em que a máquina virtual é criada, verifique se a caixa de seleção do Agente de VM está marcada antes que a máquina virtual seja provisionada.

### Atualização e instalação manual

O agente de VM já está presente em VMs criadas na galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o agente de VM instalado. Para essas VMs, o agente de VM precisa ser instalado explicitamente. Leia mais sobre [Instalando o Agente de VM em uma VM existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalando o agente de VM | <li>Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará de privilégios de Administrador para concluir a instalação. <li>[Atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. | <li> Instale o [agente Linux](https://github.com/Azure/WALinuxAgent) mais recente do GitHub. Você precisará de privilégios de Administrador para concluir a instalação. <li> [Atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |
| Atualizando o agente de VM | A atualização do agente de VM é tão simples quanto reinstalar os [binários do agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Verifique se nenhuma operação de backup está em execução enquanto o agente de VM está sendo atualizado. | Siga as instruções em [Atualizando o agente de VM do Linux](../virtual-machines-linux-update-agent.md). <br><br>Verifique se nenhuma operação de backup está em execução enquanto o agente de VM está sendo atualizado. |
| Validação da instalação do agente de VM | <li>Navegue até a pasta *C:\\WindowsAzure\\Packages* na VM do Azure. <li>Você deve encontrar o arquivo WaAppAgent.exe presente.<li> Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do Produto deve ser 2.6.1198.718 ou mais recente. | N/D |


Saiba mais sobre o [agente de VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como instalá-lo](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### Extensão de backup

Para fazer backup da máquina virtual, o serviço do Backup do Azure instala uma extensão para o agente de VM. O serviço do Backup do Azure atualiza e corrige perfeitamente a extensão de backup sem intervenção adicional do usuário.

A extensão de backup será instalada se a VM estiver em execução. Uma VM em execução também oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo. No entanto, o serviço do Backup do Azure continuará a realizar o backup da VM mesmo quando esta estiver desativada e a extensão não puder ser instalada (também conhecida como VM Offline). Nesse caso, o ponto de recuperação será *falha consistente*, como discutido anteriormente.


## Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## Próximas etapas
Agora que você já preparou seu ambiente para fazer backup de sua VM, a próxima etapa lógica será criar um backup. O artigo de planejamento oferece informações mais detalhadas sobre o backup de máquinas virtuais.

- [Backup de máquinas virtuais](backup-azure-vms.md)
- [Planeje sua infraestrutura de backup da VM](backup-azure-vms-introduction.md)
- [Gerenciar backups de máquinas virtuais](backup-azure-manage-vms.md)

<!---HONumber=AcomDC_0302_2016-->