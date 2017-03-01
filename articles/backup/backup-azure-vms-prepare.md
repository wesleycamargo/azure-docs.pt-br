---
title: "Preparando seu ambiente para fazer backup de máquinas virtuais do Azure | Microsoft Docs"
description: "Verificar se o ambiente está preparado para fazer backup de máquinas virtuais no Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonn
editor: 
keywords: backups; fazendo backup;
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: f517a649a6c6aa65b350767bc66cf4d60c7988b5
ms.openlocfilehash: 9a114e954d59dcecaf3310e024428770bc4a2349
ms.lasthandoff: 02/24/2017


---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Preparar o seu ambiente para o backup das máquinas virtuais do Azure
> [!div class="op_single_selector"]
> * [Modelo do Gerenciador de Recursos](backup-azure-arm-vms-prepare.md)
> * [Modelo clássico](backup-azure-vms-prepare.md)
>
>

Antes de poder fazer backup de uma máquina virtual (VM) do Azure, há três condições que devem existir.

* Você precisa criar um cofre de backup ou identificar um cofre de backup existente *na mesma região da VM*.
* Estabeleça a conectividade de rede entre os endereços de Internet públicos do Azure e os pontos de extremidade do armazenamento do Azure.
* Instale o agente de VM na VM.

Se você souber que essas condições já existem em seu ambiente, prossiga para o [artigo Fazer backup das suas VMs](backup-azure-vms.md). Caso contrário, continue a ler este artigo. Ele guiará você pelas etapas da preparação do seu ambiente para o backup de uma VM do Azure.

##<a name="supported-operating-system-for-backup"></a>Versões de sistema operacional com suporte para backup
 * **Linux**: o Backup do Azure dá suporte a [uma lista de distribuições endossadas pelo Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) exceto o principal sistema operacional Linux. _Outras distribuições personalizadas do Linux também devem funcionar, contanto que o agente de VM esteja disponível na máquina virtual e exista suporte para Python. No entanto, não endossamos essas distribuições para backup._
 * **Windows Server**: não há suporte para versões anteriores ao Windows Server 2008 R2.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitações durante o backup e a restauração de uma VM
> [!NOTE]
> O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../azure-resource-manager/resource-manager-deployment-model.md). A lista a seguir fornece as limitações durante a implantação no modelo clássico.
>
>

* Não há suporte para o backup de máquinas virtuais com mais de 16 discos de dados.
* Não há suporte para o backup de máquinas virtuais com um endereço IP reservado e nenhum ponto de extremidade definido.
* Os dados de backup não incluem unidades de rede montadas anexadas à VM.
* Não há suporte para a substituição de uma máquina virtual existente durante a restauração. Primeiro, exclua a máquina virtual existente e todos os discos associados e, em seguida, restaure os dados do backup.
* Não há suporte para backup e restauração entre regiões.
* O backup de máquinas virtuais usando o serviço Backup do Azure tem suporte em todas as regiões públicas do Azure (confira a [lista de verificação](https://azure.microsoft.com/regions/#services) de regiões com suporte). Se a região que você procura ainda não tem suporte, ela não aparecerá na lista suspensa durante a criação de cofre.
* O backup de máquinas virtuais usando o serviço Backup do Azure tem suporte somente para determinadas versões de sistema operacional:
* A restauração de uma VM DC (controladora de domínio) que é parte de uma configuração multi-DC tem suporte somente usando o PowerShell. Leia mais sobre [como restaurar um controlador de domínio com vários DCs](backup-azure-restore-vms.md#restoring-domain-controller-vms)
* Apenas há suporte para a restauração de máquinas virtuais que têm as seguintes configurações de rede especial por meio do PowerShell. Máquinas virtuais que você criar usando o fluxo de trabalho de restauração na interface do usuário não terão essas configurações de rede depois que a operação de restauração for concluída. Para saber mais, confira [Restaurando VMs com configurações de rede especiais](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Máquinas virtuais sob configuração do balanceador de carga (interno e externo)
  * Máquinas virtuais com vários endereços IP reservados
  * Máquinas virtuais com vários adaptadores de rede

## <a name="create-a-backup-vault-for-a-vm"></a>Criar um cofre de backup para uma VM
O cofre de backup é uma entidade que armazena todos os pontos de backups e recuperação que foram criados ao longo do tempo. O cofre de backup também contém as políticas de backup que serão aplicadas às máquinas virtuais que passarão pelo backup.

Esta imagem mostra os relacionamentos entre as várias entidades do Backup do Azure:     ![Entidades e relacionamentos do Backup do Azure](./media/backup-azure-vms-prepare/vault-policy-vm.png)

Para criar um cofre de backup:

1. Entre no [Portal do Azure](http://manage.windowsazure.com/).
2. No portal do Azure clique em **Novo** > **Integração Híbrida** > **Backup**. Ao clicar em **Backup**, você alternará automaticamente para o portal clássico (mostrado após a Observação).

    ![Portal do Ibiza](./media/backup-azure-vms-prepare/Ibiza-portal-backup01.png)

   > [!NOTE]
   > Se sua assinatura foi usada pela última vez no portal clássico, ela poderá ser aberta no portal clássico. Neste caso, para criar um cofre de backup, clique em **Novo** > **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Backup** > **Criação Rápida** (veja a imagem abaixo).
   >
   >

    ![Criar cofre de backup](./media/backup-azure-vms-prepare/backup_vaultcreate.png)
3. Para **Nome**, insira um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Digite um nome que contenha de 2 a 50 caracteres. Ele deve começar com uma letra e pode conter apenas letras, números e hifens.
4. Em **Região**, selecione a região geográfica para o cofre. O cofre deve estar na mesma região que as máquinas virtuais que você deseja proteger. Se tiver máquinas virtuais em várias regiões, será necessário criar um cofre de backup em cada região. Não é necessário especificar contas de armazenamento para armazenar os dados de backup; o cofre de backup e o serviço de Backup do Azure cuidarão disso automaticamente.
5. Em **Assinatura** , selecione a assinatura que deseja associar ao cofre de backup. Haverá várias opções somente se sua conta organizacional estiver associada a várias assinaturas do Azure.
6. Clique em **Criar cofre**. Pode levar algum tempo para que o cofre de backup seja criado. Monitore as notificações de status na parte inferior do portal.

    ![Criar notificação de cofre](./media/backup-azure-vms-prepare/creating-vault.png)
7. Uma mensagem confirmará que o cofre foi criado com êxito. Ele será listado na página de **serviços de recuperação** como **Ativo**. Verifique se a opção apropriada de redundância de armazenamento está marcada logo depois que o cofre for criado. Leia mais sobre [como definir a opção de redundância de armazenamento no cofre de backup](backup-configure-vault.md#create-a-recovery-services-vault).

    ![Lista de cofres de backup](./media/backup-azure-vms-prepare/backup_vaultslist.png)
8. Clique no cofre de backup para abrir a página de **Início Rápido** na qual são exibidas as instruções para fazer o backup de máquinas virtuais do Azure.

    ![Instruções de backup de máquina virtual na página Painel](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## <a name="network-connectivity"></a>Conectividade de rede
Para gerenciar os instantâneos de VM, a extensão de backup precisa de conectividade com os endereços IP públicos do Azure. Sem a conexão correta com a Internet, as solicitações HTTP da máquina virtual atingirão o tempo limite e a operação de backup falhará. Se sua implantação possui restrições de acesso em vigor (por meio de um NSG, Grupo de Segurança de Rede, por exemplo), escolha uma destas opções para fornecer um caminho livre para o tráfego de backup:

* [Lista de autorizados de intervalos de IP de datacenter do Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) : consulte o artigo para obter instruções sobre como colocar os endereços IP na lista de autorizados.
* Implante um servidor de proxy HTTP para rotear o tráfego.

Ao decidir qual opção usar, desvantagens entre a capacidade de gerenciamento, controle granular e custo.

| Opção | Vantagens | Desvantagens |
| --- | --- | --- |
| Intervalos de IPs na lista de autorizados |Sem custo adicional.<br><br>Para habilitar o acesso em NSG, use o cmdlet <i>Set-AzureNetworkSecurityRule</i>. |É complexo para gerenciar, já que os intervalos de IP afetados mudam com o tempo.<br><br>Fornece acesso ao Azure por completo, não somente ao Armazenamento. |
| Proxy HTTP |É permitido o controle granular no proxy em relação às URLs de armazenamento.<br>Único ponto de acesso à Internet para VMs.<br>Não está sujeito a alterações do endereço IP do Azure |Custos adicionais para a execução de uma VM com o software do proxy |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>lista de autorizados de intervalos de IP de datacenter do Azure
Para colocar os intervalos IP do datacenter do Azure na lista de autorizados, consulte o [site do Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) para obter detalhes sobre os intervalos de IP e as instruções.

### <a name="using-an-http-proxy-for-vm-backups"></a>Usando um proxy HTTP para backups de uma VM
Ao fazer backup de uma VM, a extensão de backup na VM envia os comandos de gerenciamento de instantâneo para o Armazenamento do Azure usando a API de HTTPS. Roteie o tráfego da extensão de backup por meio do proxy HTTP, pois ele é o único componente configurado para dar acesso à Internet pública.

> [!NOTE]
> Não há recomendações do software de proxy a serem usadas. Escolha um proxy que seja compatível com as etapas de configuração abaixo.
>
>

A imagem de exemplo abaixo mostra as três etapas de configuração necessárias para usar um proxy HTTP:

* A VM de aplicativo roteia todo o tráfego HTTP voltado para a Internet pública por meio da VM do Proxy.
* A VM do Proxy permite a passagem do tráfego de entrada de VMs na rede virtual.
* O NSG (Grupo de Segurança de Rede) chamado bloqueio de NSF precisa de uma regra de segurança para permitir a passagem de tráfego de Internet da VM do Proxy.

![Diagrama de implantação de proxy HTTP com NSG](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Para usar um proxy HTTP para se comunicar com a Internet pública, siga estas etapas:

#### <a name="step-1-configure-outgoing-network-connections"></a>Etapa 1. Configurar conexões de rede de saída
###### <a name="for-windows-machines"></a>Para computadores Windows
Isso definirá a configuração do servidor de proxy para a Conta do Sistema Local.

1. Baixe o [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Execute o seguinte comando no prompt com privilégios elevados,

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Isso abrirá a janela do Internet Explorer.
3. Acesse Ferramentas -> Opções da Internet -> Conexões -> Configurações de LAN.
4. Verifique as configurações de proxy para a conta do Sistema. Defina o IP e a porta do proxy.
5. Feche o Internet Explorer.

Isso configurará um proxy de todo o computador e será usado para qualquer tráfego de saída HTTP/HTTPS.

Se você configurou um servidor de proxy em uma conta de usuário atual (não uma Conta do Sistema Local), use o script a seguir para aplicá-la ao SYSTEMACCOUNT:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Se você vir "(407) Autenticação de Proxy Necessária" no log do servidor proxy, verifique se a configuração da sua autenticação está correta.
>
>

###### <a name="for-linux-machines"></a>Para computadores Linux
Adicione a seguinte linha ao arquivo ```/etc/environment``` :

```
http_proxy=http://<proxy IP>:<proxy port>
```

Adicione as linhas abaixo ao arquivo ```/etc/waagent.conf``` :

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Etapa 2. Permitir conexões de entrada no servidor de proxy:
1. No servidor proxy, abra o Firewall do Windows. A maneira mais fácil de acessar o firewall é procurar o Firewall do Windows com Segurança Avançada.

    ![Abrir o Firewall](./media/backup-azure-vms-prepare/firewall-01.png)
2. No diálogo Firewall do Windows, clique com o botão direito do mouse em **Regras de Entrada** e clique em **Nova Regra...**.

    ![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-02.png)
3. No **Assistente para Nova Regra de Entrada**, selecione a opção **Personalizar** para **Tipo de Regra** e clique em **Avançar**.
4. Na página para selecionar **Programa**, escolha **Todos os Programas** e clique em **Avançar**.
5. Na página **Protocolo e Portas**, insira as seguintes informações e clique em **Avançar**:

    ![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-03.png)

   * para *Tipo de protocolo*, escolha *TCP*
   * para *Porta local*, escolha *Portas Específicas*, no campo abaixo especifique o ```<Proxy Port>``` que foi configurado.
   * para *Porta remota*, escolha *Todas as Portas*

     Para o restante do assistente, clique até o fim e dê um nome a essa regra.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Etapa 3. Adicionar uma regra de exceção ao NSG:
Em um prompt de comando do Azure PowerShell, digite o seguinte comando:

O comando a seguir adiciona uma exceção ao NSG. Essa exceção permite a passagem de tráfego TCP de qualquer porta em 10.0.0.5 para qualquer endereço da Internet na porta 80 (HTTP) ou 443 (HTTPS). Se você exigir uma porta específica na Internet pública, adicione-a também ao ```-DestinationPortRange``` .

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*Verifique se você substituiu os nomes no exemplo com os detalhes adequados para a implantação.*

## <a name="vm-agent"></a>Agente de VM
Antes de iniciar o backup da máquina virtual do Azure, verifique se o agente de VM do Azure está instalado corretamente na máquina virtual. Como o agente de VM é um componente opcional no momento em que a máquina virtual é criada, verifique se a caixa de seleção do Agente de VM está marcada antes que a máquina virtual seja provisionada.

### <a name="manual-installation-and-update"></a>Atualização e instalação manual
O agente de VM já está presente em VMs criadas na galeria do Azure. No entanto, as máquinas virtuais que são migradas de datacenters locais não teriam o agente de VM instalado. Para essas VMs, o agente de VM precisa ser instalado explicitamente. Leia mais sobre [Instalando o Agente de VM em uma VM existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalando o agente de VM |<li>Baixe e instale o [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Você precisará de privilégios de Administrador para concluir a instalação. <li>[Atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |<li> Instale o [agente Linux](https://github.com/Azure/WALinuxAgent) mais recente do GitHub. Você precisará de privilégios de Administrador para concluir a instalação. <li> [Atualize a propriedade de VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |
| Atualizando o agente de VM |A atualização do agente de VM é tão simples quanto reinstalar os [binários do agente de VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Verifique se nenhuma operação de backup está em execução enquanto o agente de VM está sendo atualizado. |Siga as instruções em [Atualizando o agente de VM do Linux ](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). <br><br>Verifique se nenhuma operação de backup está em execução enquanto o agente de VM está sendo atualizado. |
| Validação da instalação do agente de VM |<li>Navegue até a pasta *C:\WindowsAzure\Packages* na VM do Azure. <li>Você deve encontrar o arquivo WaAppAgent.exe presente.<li> Clique com o botão direito do mouse no arquivo, vá para **Propriedades** e selecione a guia **Detalhes**. O campo Versão do Produto deve ser 2.6.1198.718 ou mais recente. |N/D |

Saiba mais sobre o [Agente de VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como instalá-lo](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### <a name="backup-extension"></a>Extensão de backup
Para fazer backup da máquina virtual, o serviço do Backup do Azure instala uma extensão para o agente de VM. O serviço do Backup do Azure atualiza e corrige perfeitamente a extensão de backup sem intervenção adicional do usuário.

A extensão de backup será instalada se a VM estiver em execução. Uma VM em execução também oferece uma maior chance de obter um ponto de recuperação consistente com o aplicativo. No entanto, o serviço do Backup do Azure continuará a realizar o backup da VM mesmo quando esta estiver desativada e a extensão não puder ser instalada (também conhecida como VM Offline). Nesse caso, o ponto de recuperação será *falha consistente* , como discutido anteriormente.

## <a name="questions"></a>Perguntas?
Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Próximas etapas
Agora que você já preparou seu ambiente para fazer backup de sua VM, a próxima etapa lógica será criar um backup. O artigo de planejamento oferece informações mais detalhadas sobre o backup de máquinas virtuais.

* [Backup de máquinas virtuais](backup-azure-vms.md)
* [Planeje sua infraestrutura de backup da VM](backup-azure-vms-introduction.md)
* [Gerenciar backups de máquinas virtuais](backup-azure-manage-vms.md)

