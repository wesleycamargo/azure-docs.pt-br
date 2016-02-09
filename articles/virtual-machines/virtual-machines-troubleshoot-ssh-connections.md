<properties
	pageTitle="Solucionar problemas de conexão SSH com uma VM do Azure | Microsoft Azure"
	description="Solucione problemas e corrija erros de SSH, como falha de conexão SSH ou conexão SSH recusada, para uma VM do Azure com Linux."
	keywords="conexão ssh recusada, erro de ssh, ssh do azure, falha de conexão SSH"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="dkshir"/>

# Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux

Pode haver várias causas para erros de SSH durante a tentativa de conexão com uma Máquina Virtual do Azure baseada em Linux. Este artigo vai ajudar você a localizá-los e a corrigi-los.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Este artigo aplica-se somente às máquinas virtuais do Azure que executam o Linux. Para máquinas virtuais do Azure que estejam executando o Windows, confira [Solucionar de problemas de conexão de Área de Trabalho Remota para uma VM do Azure](virtual-machines-troubleshoot-remote-desktop-connections.md).

Se você precisar de mais ajuda a qualquer momento neste artigo, poderá contatar os especialistas do Azure nos [fóruns do Azure MSDN e Stack Overflow](http://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site Suporte do Azure](http://azure.microsoft.com/support/options/) e clique em **Obter suporte**. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes do Suporte do Microsoft Azure](http://azure.microsoft.com/support/faq/).


## Corrigir erros comuns de SSH

Esta seção lista as etapas de correção rápida para problemas comuns de conexão de SSH.

### Máquinas virtuais criadas usando o modelo de implantação clássico

Tente realizar estas etapas para resolver as falhas mais comuns de conexão SSH:

1. _Redefinir Acesso Remoto_ no [portal do Azure](https://portal.azure.com).<br> Clique em **Procurar** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Linux > **Redefinir Acesso...**.

2. Reiniciar a máquina virtual.<br> No [portal do Azure](https://portal.azure.com), clique em **Procurar** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Linux > **Reiniciar**.<br> No [portal clássico do Azure](https://manage.windowsazure.com), abra as **Máquinas virtuais** > **Instâncias** > **Reiniciar**.

3. [Redimensionar a máquina virtual](https://msdn.microsoft.com/library/dn168976.aspx).

4. Siga as instruções em [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) na máquina virtual, para:

	- Redefinir a senha ou a chave SSH.
	- Criar uma nova conta de usuário _sudo_.
	- Redefinir a configuração de SSH.

5. Verifique se há problemas de plataforma na Integridade do Recurso da VM.<br> Clique em **Procurar** > **Máquinas virtuais (clássicas)** > sua máquina virtual do Linux > **Configurações** > **Verificar Integridade**.


### Máquinas virtuais criadas usando o modelo de implantação do Gerenciador de Recursos

Para resolver os problemas comuns de SSH para máquinas virtuais criadas usando o modelo de implantação do Gerenciador de Recursos, experimente as etapas a seguir.

1. _Redefina a conexão SSH_ da VM do Linux na linha de comando usando a CLI do Azure ou o Azure PowerShell. Verifique se a versão 2.0.5 ou posterior do [Agente Linux do Microsoft Azure](virtual-machines-linux-agent-user-guide.md) está instalada.

**Usando a CLI do Azure**:

a. Se você ainda não fez isso, [instale a CLI do Azure e conecte-se à sua assinatura do Azure](../xplat-cli-install.md) usando o comando `azure login`.

b. Verifique se você está no modo Gerenciador de Recursos. Versões mais recentes do CLI do Azure são padronizadas no modo Gerenciador de Recursos.

	```
	azure config mode arm
	```

c. Redefina a conexão SSH usando um dos métodos a seguir.

* Use o comando `vm reset-access` como mostrado no exemplo a seguir.

	```
	azure vm reset-access -g YourResourceGroupName -n YourVirtualMachineName -r
	```

Ele vai instalar a extensão `VMAccessForLinux` na máquina virtual.

* Como alternativa, crie um arquivo chamado PrivateConf.json com o seguinte conteúdo:

	```
	{  
		"reset_ssh":"True"
	}
	```

Em seguida, execute manualmente a extensão `VMAccessForLinux` para redefinir a conexão SSH.

	```
	azure vm extension set "YourResourceGroupName" "YourVirtualMachineName" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

**Usando o Azure PowerShell**:

a. Se você ainda não fez isso, [instale o Azure PowerShell e conecte-se à sua assinatura do Azure](../powershell-install-configure.md) usando o método do AD do Azure. Em versões do Azure PowerShell antes da 1.0. x, você precisará alternar explicitamente para o modo do Gerenciador de Recursos usando _Switch-AzureMode_.

b. Execute a extensão `VMAccessForLinux` para redefinir a conexão SSH, como mostrado no exemplo a seguir. Em versões anteriores, o comando seria _Set-AzureVMExtension_.

	```
	Set-AzureRmVMExtension -ResourceGroupName "yourRG" -VMName "yourVM" -Location "West US" -Name "VMAccessForLinux" -Publisher "Microsoft.OSTCExtensions" -ExtensionType "VMAccessForLinux" -TypeHandlerVersion "1.2" -SettingString "{}" -ProtectedSettingString '{"reset_ssh":true}'
	```

2. Reinicie a VM do Linux no [portal do Azure](https://portal.azure.com).<br> Clique em **Procurar** > **Máquinas virtuais** > sua máquina virtual do Linux > **Reiniciar**.

3. _Redefina a senha ou a chave SSH_ da VM do Linux na linha de comando, usando a CLI do Azure ou o Azure PowerShell. Você também pode criar um novo nome de usuário e senha com a autoridade _sudo_, como mostrado no exemplo a seguir.

**Usando a CLI do Azure**:

Instale e configure a CLI do Azure, conforme mencionado acima. Alterne para o modo Gerenciador de Recursos se necessário e execute a extensão usando um dos métodos a seguir.

* Execute o comando `vm reset-access` para definir qualquer uma das credenciais de SSH.

	```
	azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword
	```

Veja mais informações sobre isso digitando `azure vm reset-access -h` na linha de comando.

* Como alternativa, crie um arquivo chamado PrivateConf.json com o conteúdo a seguir.

	```
	{
		"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
	}
	```

Em seguida, execute a extensão do Linux usando o arquivo acima.

	```
	$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

Observe que você pode seguir etapas semelhantes a [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) para experimentar outras variações. Lembre-se de modificar as instruções da CLI do Azure para o modo do Gerenciador de Recursos.


**Usando o Azure PowerShell**:

Instale e configure o Azure PowerShell, conforme mencionado acima. Alterne para o modo do Gerenciador de Recursos e execute a extensão, como indicado a seguir.

	```
	$RGName = 'testRG'
	$VmName = 'testVM'
	$Location = 'West US'

	$ExtensionName = 'VMAccessForLinux'
	$Publisher = 'Microsoft.OSTCExtensions'
	$Version = '1.2'

	$PublicConf = '{}'
	$PrivateConf = '{"username":"NewUsername", "password":"NewPassword", "ssh_key":"", "reset_ssh":false, "remove_user":""}'

	Set-AzureRmVMExtension -ResourceGroupName $RGName -VMName $VmName -Location $Location -Name $ExtensionName -Publisher $Publisher -ExtensionType $ExtensionName -TypeHandlerVersion $Version -SettingString $PublicConf -ProtectedSettingString $PrivateConf
	```

Lembre-se de substituir os valores de $RGName, $VmName, $Location e as credenciais de SSH pelos valores específicos de sua instalação.



## Solução de problemas detalhada para erros de SSH

Se o cliente SSH ainda não conseguir acessar o serviço SSH na máquina virtual, isso pode ser devido a várias causas. Estes são os componentes envolvidos.

![Diagrama que mostra os componentes de serviço SSH](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

As seções a seguir ajudarão você a identificar a origem da falha e encontrar soluções ou soluções alternativas.

### Etapas preliminares

Primeiro, verifique o status da máquina virtual no portal do Azure.

No [portal clássico do Azure](https://manage.windowsazure.com), para máquinas virtuais no modelo de implantação clássico:

1. Clique em **Máquinas virtuais** > *Nome da VM*.
2. Clique no **Painel** da VM para verificar o status.
3. Clique em **Monitor** para ver a atividade recente dos recursos de computação, armazenamento e rede.
4. Clique em **Pontos de extremidade** para garantir que haja um ponto de extremidade para o tráfego SSH.

No [portal do Azure](https://portal.azure.com):

1. Para uma máquina virtual criada no modelo de implantação clássico, clique em **Procurar** > **Máquinas virtuais (clássicas)** > *Nome da VM*. Para uma máquina virtual criada com o Gerenciador de Recursos, clique em **Procurar** > **Máquinas virtuais** > *Nome da VM*. O painel de status da máquina virtual deve mostrar **Executando**. Role para baixo para mostrar a atividade recente dos recursos de computação, armazenamento e rede.
2. Clique em **Configurações** para examinar os pontos de extremidade, os endereços IP e outras configurações. Para identificar pontos de extremidade em máquinas virtuais criadas com o Gerenciador de Recursos, verifique se definiu um [Grupo de Segurança de Rede](../virtual-network/virtual-networks-nsg.md), as regras aplicadas a ele e se elas são referenciadas na sub-rede.

Para verificar a conectividade de rede, verifique os pontos de extremidade configurados e se você pode acessar a VM por meio de outro protocolo, como HTTP ou outro serviço.

Após essas etapas, tente estabelecer novamente a conexão SSH.


### Descobrir a origem do problema

O cliente SSH no computador poderá não conseguir acessar o serviço SSH na máquina virtual do Azure devido a essas possíveis fontes de problemas ou configurações incorretas:

- Computador cliente de SSH
- Dispositivo de borda da organização
- Ponto de extremidade de serviço de nuvem e ACL (lista de controle de acesso)
- Grupos de segurança de rede
- Máquina virtual baseada no Linux

#### Fonte 1: computador cliente de SSH

Para que o seu computador deixe de ser a fonte da falha, verifique se ele pode estabelecer conexões SSH com outro computador local baseado em Linux.

![Diagrama que realça o componente do computador cliente SSH](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

Se isso não for possível, verifique se há o seguinte em seu computador:

- Uma configuração de firewall local que está bloqueando o tráfego SSH de entrada ou de saída (TCP 22)
- Um software de proxy cliente instalado localmente que está impedindo conexões de SSH
- Um software de monitoramento de rede instalado localmente que está impedindo conexões SSH
- Outros tipos de software de segurança que monitoram o tráfego ou que permitem/não permitem tipos específicos de tráfego

Em todos esses casos, tente desabilitar temporariamente o software e tente estabelecer uma conexão SSH com um computador local para descobrir a causa. Em seguida, trabalhe com o administrador de rede para corrigir as configurações do software e permitir as conexões SSH.

Se você estiver usando autenticação de certificado, verifique se tem essas permissões para a pasta .ssh em seu diretório base:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (ou quaisquer outros arquivos que têm suas chaves privadas armazenadas)
- Chmod 644 ~/.ssh/known\_hosts (contém os hosts aos quais você se conectou via SSH)

#### Fonte 2: dispositivo de borda da organização

Para que o dispositivo de borda de sua organização deixe de ser a fonte da falha, verifique se um computador conectado diretamente à Internet pode estabelecer conexões SSH com sua VM do Azure. Se estiver acessando a VM em uma conexão VPN site a site ou de Rota Expressa, vá para [Fonte 4: grupos de segurança de rede](#nsg).

![Diagrama que realça o dispositivo de borda da organização](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Se não tiver um computador que esteja diretamente conectado à Internet, você poderá criar facilmente uma nova máquina virtual do Azure em seu próprio grupo de recursos ou serviço de nuvem e usá-la. Para saber mais, consulte [Criar uma máquina virtual que execute o Linux no Azure](virtual-machines-linux-tutorial.md). Exclua o grupo de recursos ou a máquina virtual e o serviço de nuvem ao concluir o teste.

Se você puder criar uma conexão SSH com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda da organização:

- Um firewall interno que está bloqueando o tráfego SSH com a Internet
- Seu servidor proxy que está impedindo conexões SSH
- Software de detecção de invasão ou de monitoramento de rede em execução em dispositivos em sua rede de borda e que está impedindo conexões SSH

Trabalhe com o administrador da rede para corrigir as configurações dos dispositivos de borda de sua organização a fim de permitir o tráfego SSH com a Internet.

#### Fonte 3: ponto de extremidade de serviço de nuvem e ACL

> [AZURE.NOTE] Esta fonte aplica-se apenas a máquinas virtuais criadas usando o modelo de implantação clássica. Para as máquinas virtuais criadas com o Gerenciador de Recursos, vá para [fonte 4: grupos de segurança de rede](#nsg).

Para que o ponto de extremidade de serviço de nuvem e uma ACL deixem de ser a fonte da falha, para as VMs criadas com o [modelo de implantação clássico](../resource-manager-deployment-model.md), verifique que outra VM do Azure na mesma rede virtual possa fazer conexões SSH com a sua VM.

![Diagrama que realça a ACL e o ponto de extremidade do serviço de nuvem](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

Se não houver outra VM na mesma rede virtual, você poderá facilmente criar uma nova. Para saber mais, consulte [Criar uma máquina virtual que execute o Linux no Azure](virtual-machines-linux-tutorial.md). Exclua a VM extra ao concluir o teste.

Se for possível criar uma conexão SSH com uma VM na mesma rede virtual, verifique:

- A configuração do ponto de extremidade para o tráfego SSH na VM de destino. A porta TCP privada do ponto de extremidade deve corresponder à porta TCP na qual o serviço SSH na VM está escutando (o padrão é 22). Para as VMs criadas no modelo de implantação do Gerenciador de Recursos usando modelos, verifique o número da porta SSH TCP no portal do Azure em **Procurar** > **Máquinas virtuais (v2)** > *Nome da VM* > **Configurações** > **Pontos de extremidade**.
- A ACL para o ponto de extremidade de tráfego de SSH na máquina virtual de destino. As ACLs permitem que você especifique tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de SSH para o ponto de extremidade. Examine suas ACLs para verificar se o tráfego de entrada dos endereços IP públicos de seu proxy ou de outro servidor de borda é permitido. Para obter mais informações, veja [Sobre ACLs (listas de controle de acesso) de rede](../virtual-network/virtual-networks-acl.md).

Para que o ponto de extremidade deixe de ser a fonte do problema, remova o ponto de extremidade atual e crie um novo, e especifique o nome do **SSH** (porta TCP 22 como o número da porta pública e privada). Para obter mais informações, consulte [Configurar pontos de extremidade em uma máquina virtual no Azure](virtual-machines-set-up-endpoints.md).

<a id="nsg"></a>
#### Fonte 4: grupos de segurança de rede

Os grupos de segurança de rede proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Examine as regras do grupo de segurança de rede para verificar se o tráfego SSH de entrada e saída da Internet é permitido. Para saber mais, confira [Sobre grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

#### Fonte 5: máquina virtual do Azure baseada no Linux

A última fonte possível de problemas é a própria máquina virtual do Azure.

![Diagrama que realça uma VM do Azure baseada em Linux](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Se você ainda não fez isso, siga as instruções em [como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) na máquina virtual.

Tente se conectar novamente do seu computador. Se você ainda não conseguir, estes são alguns dos possíveis problemas:

- O serviço SSH não está em execução na máquina virtual de destino.
- O serviço SSH não está escutando na porta TCP 22. Para testar isso, instale um cliente telnet no computador local e execute "telnet *nomedoServiçoDeNuvem*.cloudapp.net 22". Isso descobrirá se a máquina virtual permite a comunicação de entrada e de saída para o ponto de extremidade do SSH.
- O firewall local na máquina virtual de destino tem regras que estão impedindo o tráfego SSH de entrada ou de saída.
- Um software de detecção de invasão ou de monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de SSH.


## Recursos adicionais

Para máquinas virtuais no modelo de implantação clássica, [Como redefinir uma senha ou SSH para máquinas virtuais baseadas em Linux](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada em Windows](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0128_2016-->