<properties 
	pageTitle="Informações de privacidade para Azure Site Recovery" 
	description="Descreve informações adicionais de privacidade para Azure Site Recovery" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="raynew"/>

# Informações de privacidade para Azure Site Recovery

Esta seção fornece informações adicionais de privacidade para o serviço Microsoft Azure Site Recovery ("Serviço"). Para exibir a política de privacidade para serviços do Microsoft Azure, consulte a
[Política de Privacidade do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

<table>
<thead>
<tr>
	<th>Recurso</th>
	<th>O que faz</th>
	<th>Informações coletadas</th>
	<th>Uso</th>
	<th>Opção</th>
</tr>
</thead>
<tbody>
<tr>
	<td><p><b>Registro</b></p></td>
	<td><p>Registra o servidor no serviço para que as máquinas virtuais possam ser protegidas</p></td>
	<td><p>Depois de registrar um serviço, o Serviço coleta, processa e transmite essas informações:</p>
		<ul>
			<li>Informações de certificado de gerenciamento do servidor VMM que se destinam a fornecer recuperação de desastres usando o nome do serviço do servidor VMM</li>
			<li>O nome de nuvens de máquina virtual no servidor VMM</li>
		</ul>
</td>
	<td><p>O Serviço usa as informações acima da seguinte forma:</p>
		<ul>
			<li>Certificado de gerenciamento: usado para ajudar a identificar e autenticar o servidor VMM registrado para acessar o Serviço. O Serviço usa a parte da chave pública do certificado para proteger um token que somente o servidor VMM registrado pode acessar. O servidor precisa usar esse token para obter acesso aos recursos do Serviço.</li>
			<li>Nome do servidor VMM: o nome do servidor VMM é necessário para identificar e se comunicar com o servidor VMM apropriado no qual as nuvens estão localizadas. 
			</li>
			<li>Nomes de nuvem do servidor VMM: o nome de nuvem é necessário ao se usar o recurso de emparelhamento/desemparelhamento da nuvem do Serviço descrito abaixo. Quando você decide emparelhar sua nuvem de um data center primário com outra nuvem no data center de recuperação, os nomes de todas as nuvens do data center de recuperação são apresentados.</li>
		</ul>
</td>
	<td><p>Essa informação é uma parte essencial do processo de registro do Serviço porque ajuda você e o Serviço a identificar o servidor VMM para o qual você deseja fornecer a proteção do Azure Site Recovery, além de identificar o servidor VMM registrado correto. Se você não quiser enviar essas informações ao Serviço, não use esse Serviço. Se registrar seu servidor e, mais tarde, quiser cancelar o registro, você poderá fazer isso excluindo as informações do servidor VMM do portal do Serviço (que é o portal do Azure).</p></td>
</tr>

<tr>
	<td><p><b>Habilitar a proteção do Azure Site Recovery</b></p></td>
	<td><p>O Provedor do Azure Site Recovery instalado no servidor VMM é o canal de comunicação com o serviço. O Provedor é uma DLL (biblioteca de vínculo dinâmico) hospedada no processo do VMM. Depois que o Provedor é instalado, o recurso "Recuperação do Data Center" é habilitado no console do administrador do VMM. Máquinas virtuais novas ou existentes em uma nuvem podem habilitar uma propriedade chamada "Recuperação do Data Center" para ajudar a proteger a máquina virtual. Quando essa propriedade é definida, o Provedor envia o nome e a ID da máquina virtual ao Serviço. A proteção virtual é habilitada pela tecnologia de replicação do Hyper-V do Windows Server 2012 ou Windows Server 2012 R2. Os dados da máquina virtual são replicados de um host Hyper-V para outro (normalmente localizado em um data center de "recuperação" diferente).</p></td>
	<td><p>O serviço coleta, processa e transmite metadados para a máquina virtual, que incluem o nome, a ID, a rede virtual e o nome da nuvem à qual pertencem.</p>
	</td>
	<td><p>O serviço usa as informações acima para popular as informações da máquina virtual no portal do Serviço.</p>
	</td>
	<td><p>Essa é uma parte essencial do serviço e não pode ser desativada. Se não quiser que essas informações sejam enviadas ao serviço, não habilite a proteção do Azure Site Recovery para máquinas virtuais. Observe que todos os dados enviados pelo Provedor ao Serviço são enviados via HTTPS.</p></td>
</tr>
<tr>
	<td><p><b>Plano de recuperação</b></p></td>
	<td><p>Esse recurso o ajuda a criar um plano de orquestração para o data center de "recuperação". Você pode definir a ordem na qual as máquinas virtuais ou um grupo de máquinas virtuais devem ser iniciados no local de recuperação. Você também pode especificar scripts automatizados para execução ou qualquer ação manual a ser executada no momento da recuperação para cada máquina virtual. O failover (abordado na próxima seção) costuma ser disparado no nível do Plano de Recuperação para a recuperação coordenada.</p></td>
	<td><p>O serviço coleta e transmite essas informações como parte do recurso de plano de recuperação:</p>
		<ul>
			<li>Plano de recuperação, incluindo os metadados da máquina virtual</li>
			<li>Metadados do script de automação ou a nota da ação manual.</li>
		</ul>
	</td>
	<td><p>Os metadados descritos acima são usados para criar o plano de recuperação no portal do Serviço.</p>
	</td>
	<td><p>Essa é uma parte essencial do serviço e não pode ser desativada. Se não quiser que essas informações sejam enviadas ao serviço, não crie Planos de Recuperação nesse Serviço.</p></td>
</tr>
<tr>
	<td><p><b>Mapeamento de rede</b></p></td>
	<td><p>Esse recurso permite que você mapeie as informações de rede do data center principal para o data center de recuperação. Quando as máquinas virtuais são recuperadas no site de recuperação, esse mapeamento ajuda a estabelecer a conectividade de rede para elas.</p></td>
	<td><p>Como parte do recurso de mapeamento de rede, o Serviço coleta, processa e transmite os metadados das redes lógicas para cada site (primário e data center).</p>
	</td>
	<td><p>O serviço usa os metadados para popular o portal do Serviço, em que você pode mapear as informações de rede.</p>
	</td>
	<td><p>Essa é uma parte essencial do Serviço e não pode ser desativada. Se não quiser que essas informações sejam enviadas ao Serviço, não use o recurso de mapeamento de rede.</p></td>
</tr>
<tr>
	<td><p><b>Falha - planejada, não planejada, texto</b></p></td>
	<td><p>Esse recurso ajuda o failover de uma máquina virtual de um data center gerenciado do VMM para outro data center gerenciado do VMM. A ação de failover é disparada pelo usuário em seu portal do Serviço. As possíveis razões para um failover incluem um evento não planejado (por exemplo, no caso de desastre natural; um evento planejado (por exemplo, o balanceamento de carga do data center); um failover de teste (por exemplo, um ensaio de plano de recuperação).</p>
	<p>O Provedor no servidor VMM é notificado do evento pelo Serviço e executa uma ação de failover no host Hyper-V por meio das interfaces do VMM. O failover real da máquina virtual de um host Hyper-V para outro (normalmente em execução em um data center de "recuperação" diferente) é tratado pela tecnologia de replicação do Hyper-V do Windows Server 2012 ou Windows Server 2012 R2. Depois que o failover for concluído, o Provedor instalado no servidor VMM do data center de "recuperação" enviará as informações de êxito ao serviço.</p>
	</td>
	<td><p>O Serviço usa as informações acima para popular o status das informações de ação de failover no portal do Serviço.</p>
	</td>
	<td><p>O Serviço usa as informações acima da seguinte forma:</p>
		<ul>
			<li>Certificado de gerenciamento: usado para ajudar a identificar e autenticar o servidor VMM registrado para acessar o Serviço. O Serviço usa a parte da chave pública do certificado para proteger um token que somente o servidor VMM registrado pode acessar. O servidor precisa usar esse token para obter acesso aos recursos do Serviço.</li>
			<li>Nome do servidor VMM: o nome do servidor VMM é necessário para identificar e se comunicar com o servidor VMM apropriado no qual as nuvens estão localizadas. 
			</li>
			<li>Nomes de nuvem do servidor VMM: o nome de nuvem é necessário ao se usar o recurso de emparelhamento/desemparelhamento da nuvem do Serviço descrito abaixo. Quando você decide emparelhar sua nuvem de um data center primário com outra nuvem no data center de recuperação, os nomes de todas as nuvens do data center de recuperação são apresentados.</li>
		</ul>
	</td>
	<td><p>Essa é uma parte essencial do serviço e não pode ser desativada. Se não quiser que essas informações sejam enviadas ao Serviço, não use esse Serviço.</p></td>
</tr>
</table>



<!--HONumber=49-->