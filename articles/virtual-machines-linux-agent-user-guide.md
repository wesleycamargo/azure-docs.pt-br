
<properties urlDisplayName="Linux Agent guide" pageTitle="Guia do usuário do agente Linux para o Azure" metaKeywords="" description="Saiba como instalar e configurar o agente Linux (waagent) para gerenciar sua interação de máquina virtual com os Recursos de Infraestrutura do Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure Linux Agent User Guide" authors="szarkos" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="szarkos" />





#Guia do usuário do agente Linux para o Azure

##Introdução

O Agente Linux do Azure (waagent) gerencia a interação entre uma máquina virtual e o Controlador de Malha do Azure. Ele faz o seguinte:

* **Provisionamento de imagem**
  - Criar uma conta do usuário
  - Configurar tipos de autenticação do SSH
  - Implantar chaves públicas do SSH e pares de chaves
  - Define o nome do host
  - Publica o nome do host para a plataforma de DNS
  - Relata impressão digital da chave host SSH para a plataforma
  - Gerencia o disco do recurso
  - Formata e monta o disco do recurso
  - Configura o espaço de permuta
* **Rede**
  - Gerencia as rotas para melhorar a compatibilidade com os servidores DHCP de plataforma
  - Garante a estabilidade do nome da interface de rede
* **Kernel**
  - Configura NUMA virtual
  - Consome entropia de Hyper-V para /dev/random
  - Configura os tempos limite de SCSI para o dispositivo raiz (o qual poderia ser remoto)
* **Diagnóstico**
  - Redireciona o console para a porta serial
* **Implantações SCVMM**
    - Detecta e inicializa o agente VMM para Linux quando executado em um ambiente de System Center Virtual Machine Manager 2012 R2

O fluxo de informações da plataforma para o agente ocorre por meio de dois canais:

* Um DVD anexado ao tempo de inicialização para as implementações de IaaS. Este DVD inclui um arquivo de configuração compatível com OVF que inclui todas as informações de configuração que não seja os pares de chaves SSH real.

* Um ponto de extremidade TCP expondo uma API REST usada para obter a implantação e a configuração de topologia.

###Obtenção do agente Linux
Você pode obter o agente mais recente do Linux diretamente de:

- [Diferentes provedores de distribuição endossando o Linux no Azure](http://support.microsoft.com/kb/2805216)
- ou do [Repositório de software livre do Github para o agente Linux Azure](https://github.com/WindowsAzure/WALinuxAgent)


###Distribuições Linux com suporte
* CoreOS
* CentOS 6.2+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

Outros sistemas com suporte:

* FreeBSD 9+ (WALinuxAgent v2.0.0+)


###Requisitos

Waagent depende de alguns pacotes de sistema para funcionar corretamente:

* Python 2.5+
* OpenSSL 1.0 +
* Openssh 5.3+
* Utilitários de sistema de arquivos: sfdisk, fdisk, mkfs
* Ferramentas de senha: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: roteamento ip

##Instalação

Instalação usando um RPM ou um pacote DEB do repositório de pacotes da distribuição é o método preferencial para instalar e atualizar o Azure do Microsoft Azure Linux.

Se instalar manualmente, waagent deve ser copiado para /usr/sbin/waagent e instalada executando: 

	# sudo chmod 755 /usr/sbin/waagent
	# /usr/sbin/waagent -install -verbose

Arquivo de log do agente é mantido em /var/log/waagent.log.


##Opções de linha de comando

###Sinalizadores

- detalhado: Aumentar o detalhamento do comando especificado
- força: Ignorar a confirmação interativa para alguns comandos

###Comandos

- help: Lista os comandos e sinalizadores com suporte.

- install: Instalação manual do agente
 * Verifica o sistema para dependências necessárias

 * Cria o script SysV init (/etc/init.d/waagent)para o arquivo de configuração logrotate (/etc/logrotate.d/waagent e configura a imagem para executar o script de inicialização na inicialização

 * Arquivo de configuração função Web de gravações para /etc/waagent.conf

 * Qualquer arquivo de configuração existente é movido para /etc/waagent.conf.old

 * Detectar a versão do kernel e aplica a solução VNUMA, se necessário

 * Move as regras udev que podem interferir na rede (/ lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) para/var/lib/waagent /  

- desinstalar: Remover waagent e arquivos associados
 * Cancela o registro do script de inicialização do sistema e o exclui

 * Exclui a configuração do logrotate e o arquivo de configuração waagent em /etc/waagent.conf

 * Restaura quaisquer regras udev movidas que foram movidas durante a instalação

 * Não há suporte para a reversão automática da solução alternativa VNUMA; edite os arquivos de configuração do GRUB manualmente para reabilitar NUMA, se necessário.

- desprovisionar: Tentativa de limpar o sistema e torná-lo adequado para reprovisionamento. Esta operação excluiu o seguinte:
 * Todas as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)

 * Configuração de servidor de nomes em /etc/resolv.conf

 * Senha raiz do /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)

 * Concessões de cliente DHCP em cache

 * Reinicia o nome de host para localdomain.localdomain

 **Aviso:**O desprovisionamento não garante que a imagem esteja limpa de todas as informações confidenciais e adequada para redistribuição.

- deprovision+user: Executa tudo em - deprovision (acima) e também exclui a última conta de usuário provisionado (obtida de /var/lib/waagent) e dados associados. Este parâmetro é quando a desconfiguração de uma imagem que foi anteriormente provisionamento no Azure para podem ser capturada e usada novamente.

- version: Exibe a versão do waagent

- serialconsole: Configura GRUB para marcar ttyS0 (a primeira porta serial) como
   o console de inicialização. Isso garante que o logs de inicialização do kernel sejam enviados à
   porta serial e disponibilizados para depuração.

- daemon: Executar waagent como um daemon para gerenciar a interação com a plataforma.
   Esse argumento é especificado para waagent no script de inicialização de waagent.

##Configuração

Um arquivo de configuração (/etc/waagent.conf) controla as ações do waagent. 
Uma amostra do arquivo de configuração é mostrada abaixo:
	
	#
	# Azure Linux Agent Configuration	
	#
	Role.StateConsumer=None 
	Role.ConfigurationConsumer=None 
	Role.TopologyConsumer=None
	Provisioning.Enabled=y
	Provisioning.DeleteRootPassword=n
	Provisioning.RegenerateSshHostKeyPair=y
	Provisioning.SshHostKeyPairType=rsa
	Provisioning.MonitorHostName=y
	ResourceDisk.Format=y
	ResourceDisk.Filesystem=ext4
	ResourceDisk.MountPoint=/mnt/resource 
	ResourceDisk.EnableSwap=n 
	ResourceDisk.SwapSizeMB=0
	LBProbeResponder=y
	Logs.Verbose=n
	OS.RootDeviceScsiTimeout=300
	OS.OpensslPath=None

Várias opções de configuração são descritas em detalhes abaixo. 
As opções de configuração são de três tipos: Booliana, cadeia de caracteres ou número inteiro. 
As opções de configuração Boolean podem ser especificadas como "y" ou "n". 
A palavra-chave especial "Nenhum" pode ser usado para entradas de configuração de tipo algum sequência conforme detalhado abaixo.

**Role.StateConsumer:**

Tipo: Cadeia de caracteres  
Padrão: Nenhum

Se for especificado um caminho para um programa executável, ele é chamado quando waagent forneceu a imagem e o estado "Pronto" está prestes a ser relatado à malha. O argumento especificado para o programa serão "Pronto". O agente não aguardará para que o programa retorne antes de continuar.

**Role.ConfigurationConsumer:**

Tipo: Cadeia de caracteres  
Padrão: Nenhum

Se um caminho para um programa executável for especificado, o programa é invocado quando a malha indica que um arquivo de configuração está disponível para a máquina virtual. O caminho para o arquivo de configuração XML é fornecido como um argumento para o executável. Isso pode ser chamado várias vezes sempre que for alterado o arquivo de configuração. Um arquivo de exemplo é fornecido no Apêndice a. O caminho atual desse arquivo é /var/lib/waagent/HostingEnvironmentConfig.xml.

**Role.TopologyConsumer:**

Tipo: Cadeia de caracteres  
Padrão: Nenhum

Se um caminho para um programa executável for especificado, o programa é invocado quando a malha indica que um novo layout de topologia de rede está disponível para a máquina virtual. O caminho para o arquivo de configuração XML é fornecido como um argumento para o executável. Isso pode ser chamado várias vezes sempre que a topologia de rede é alterado (devido ao serviço reparo por exemplo). Um arquivo de exemplo é fornecido no Apêndice a. O caminho atual desse arquivo é /var/lib/waagent/SharedConfig.xml.

**Provisioning.Enabled:**

Tipo: Booliano  
Padrão: y

Isso permite que o usuário habilite ou desabilite a funcionalidade de provisionamento no agente. Os valores válidos são "y" ou "n". Se o provisionamento for desabilitado, as chaves SSH de host e usuário da imagem serão preservadas e qualquer configuração especificada na API de provisionamento do Azure será ignorada.

**Provisioning.DeleteRootPassword:**

Tipo: Booliano  
Padrão: n

Se definido, a senha raiz no arquivo sombra é apagado durante o processo de provisionamento.

**Provisioning.RegenerateSshHostKeyPair:**

Tipo: Booliano  
Padrão: y

Se o conjunto de todos os SSH host pares de chaves (ecdsa, dsa e rsa) será excluído durante o processo de provisionamento de /etc/ssh /. E um único par de chave novo é gerado.

O tipo de criptografia para o novo par de chaves é configurável pela entrada do Provisioning.SshHostKeyPairType. Observe que algumas distribuições novamente criará pares de chaves SSH para todos os tipos de criptografia está faltando quando é reiniciado o daemon do SSH (por exemplo, após uma reinicialização).

**Provisioning.SshHostKeyPairType:**

Tipo: Cadeia de caracteres  
Padrão: rsa

Isso pode ser definido como um tipo de algoritmo de criptografia com suporte pelo daemon SSH na máquina virtual. Os valores geralmente aceitos são "rsa", "dsa" e "ecdsa". Observe que "putty.exe" no Windows não dá suporte a "ecdsa". Portanto, se você pretende usar putty.exe no Windows para conectar-se a uma implantação do Linux, use "rsa" ou "dsa".

**Provisioning.MonitorHostName:**

Tipo: Booliano  
Padrão: y

Se definido, waagent monitorará máquina virtual Linux para alterações de nome do host (conforme retornado pelo comando "hostname") e atualizar automaticamente a configuração de rede da imagem para refletir a alteração. Para enviar por push a alteração do nome para os servidores DNS, a rede será reiniciado na máquina virtual. Isso resultará em resumo perda de conectividade com a Internet.

**ResourceDisk.Format:**

Tipo: Booliano  
Padrão: y

Se definido, o disco de recursos fornecido pela plataforma será formatado e montado por waagent se o tipo de sistema de arquivos solicitado pelo usuário em "ResourceDisk.Filesystem" for algo diferente de "ntfs". Uma única partição do tipo Linux (83) será disponibilizada no disco. Observe que essa partição não será formatada se ele pode ser montado com êxito.

**ResourceDisk.Filesystem:**

Tipo: Cadeia de caracteres  
Padrão: ext4

Especifica o tipo de sistema de arquivos para o disco do recurso. Valores aceitos variam de acordo com a distribuição do Linux. Se a sequência for X, em seguida, mkfs.X deve estar presente na imagem do Linux. Imagens de 11 SLES geralmente devem utilizar 'ext3'. FreeBSD imagens devem usar 'ufs2' aqui.

**ResourceDisk.MountPoint:**

Tipo: Cadeia de caracteres  
Padrão: /mnt/resource 

Especifica o caminho em que o disco do recurso é montado. Observe que o disco de recurso é um disco *temporário* e pode ser esvaziado quando a VM é desprovisionada.

**ResourceDisk.EnableSwap:**

Tipo: Booliano  
Padrão: n 

Se definido, um arquivo de permuta (/swapfile) é criado no disco recursos e adicionado ao espaço de troca de sistema.

**ResourceDisk.SwapSizeMB:**

Tipo: Número inteiro  
Padrão: 0

Especifica o tamanho máximo do arquivo de permuta em megabytes.

**LBProbeResponder:**

Tipo: Booliano  
Padrão: y

Se definido, waagent responderá ao balanceador de carga de testes da plataforma (se presente).

**Logs.Verbose:**

Tipo: Booliano  
Padrão: n

Se definido, a verbosidade do log é aumentado. Waagent faz /var/log/waagent.log e aproveita a funcionalidade de logrotate do sistema para girar os logs.

**OS.RootDeviceScsiTimeout:**

Tipo: Número inteiro  
Padrão: 300

Isso configura o tempo limite de SCSI em segundos nos drives de disco e os dados de SO. Se não for definido, o sistema de padrões são usados.

**OS.OpensslPath:**

Tipo: Cadeia de caracteres  
Padrão: Nenhum

Isso pode ser usado para especificar um caminho alternativo para o openssl binário a ser usado para operações de criptografia.

##Apêndice

###Exemplo do arquivo de configuração de função

	<?xml version="1.0" encoding="utf-8"?>
	<HostingEnvironmentConfig version="1.0.0.0" goalStateIncarnation="1">
	  <StoredCertificates>
	    <StoredCertificate name="Stored0Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" certificateId="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" storeName="My" configurationLevel="System" />
	  </StoredCertificates>
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_2" guid="{5c87ab8b-2f6a-4758-9f74-37e68c3e957b}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" hostingEnvironmentVersion="1" software="" softwareType="ApplicationPackage" entryPoint="" parameters="" settleTimeSeconds="10" />
	  <HostingEnvironmentSettings name="full" Runtime="rd_fabric_stable.111026-1712.RuntimePackage_1.0.0.9.zip">
	    <CAS mode="full" />
	    <PrivilegeLevel mode="max" />
	    <AdditionalProperties><CgiHandlers></CgiHandlers></AdditionalProperties></HostingEnvironmentSettings>
	    <ApplicationSettings>
	      <Setting name="__ModelData" value="&lt;m role=&quot;LinuxVM&quot; xmlns=&quot;urn:azure:m:v1&quot;>&lt;r name=&quot;LinuxVM&quot;>&lt;e name=&quot;HTTP&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp&quot; />&lt;e name=&quot;Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput&quot; />&lt;e name=&quot;SSH&quot; />&lt;/r>&lt;/m>" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="..." />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2015-11-06T23:59:59.0000000-08:00" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="rdos" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" />
	      <Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" />
	      <Setting name="startpage" value="Hello World!" />
	      <Setting name="Certificate|Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" value="sha1:C093FA5CD3AAE057CB7C4E04532B2E16E07C26CA" />
	    </ApplicationSettings>
	    <ResourceReferences>
	      <Resource name="DiagnosticStore" type="directory" request="Microsoft.Cis.Fabric.Controller.Descriptions.ServiceDescription.Data.Policy" sticky="true" size="1" path="a99549a92e38498f98cf2989330cd2f1.LinuxVM.DiagnosticStore\" disableQuota="false" />
	    </ResourceReferences>
	  </HostingEnvironmentConfig>

###Exemplo do arquivo de topologia de função

	<?xml version="1.0" encoding="utf-8"?>
	<SharedConfig version="1.0.0.0" goalStateIncarnation="2">
	  <Deployment name="a99549a92e38498f98cf2989330cd2f1" guid="{374ef9a2-de81-4412-ac87-e586fc869923}" incarnation="14">
	    <Service name="LinuxDemo1" guid="{00000000-0000-0000-0000-000000000000}" />
	    <ServiceInstance name="a99549a92e38498f98cf2989330cd2f1.4" guid="{250ac9df-e14c-4c5b-9cbc-f8a826ced0e7}" />
	  </Deployment>
	  <Incarnation number="1" instance="LinuxVM_IN_1" guid="{a7b94774-db5c-4007-8707-0b9e91fd808d}" />
	  <Role guid="{47a04da2-d0b7-26e2-f039-b1f1ab11337a}" name="LinuxVM" settleTimeSeconds="10" />
	  <LoadBalancerSettings timeoutSeconds="32" waitLoadBalancerProbeCount="8">
	    <Probes>
	      <Probe name="LinuxVM" />
	      <Probe name="03F7F19398C4358108B7ED059966EEBD" />
	      <Probe name="47194D0E3AB3FCAD621CAAF698EC82D8" />
	    </Probes>
	  </LoadBalancerSettings>
	  <OutputEndpoints>
	    <Endpoint name="LinuxVM:Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" type="SFS">
	      <Target instance="LinuxVM_IN_0" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_1" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	      <Target instance="LinuxVM_IN_2" endpoint="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" />
	    </Endpoint>
	  </OutputEndpoints>
	  <Instances>
	    <Instance id="LinuxVM_IN_1" address="10.115.38.202">
	      <FaultDomains randomId="1" updateId="1" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="HTTP" address="10.115.38.202:80" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:80" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="80" to="80" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.38.202:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	          <RemoteInstances>
	            <RemoteInstance instance="LinuxVM_IN_0" />
	            <RemoteInstance instance="LinuxVM_IN_2" />
	          </RemoteInstances>
	        </Endpoint>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.RdpInput" address="10.115.38.202:20000" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:3389" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="20000" to="20000" />
	          </LocalPorts>
	        </Endpoint>
	        <Endpoint name="SSH" address="10.115.38.202:22" protocol="tcp" isPublic="true" loadBalancedPublicAddress="70.37.56.176:22" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="22" to="22" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_0" address="10.115.58.82">
	      <FaultDomains randomId="0" updateId="0" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.82:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	    <Instance id="LinuxVM_IN_2" address="10.115.58.148">
	      <FaultDomains randomId="0" updateId="2" updateCount="2" />
	      <InputEndpoints>
	        <Endpoint name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Rdp" address="10.115.58.148:3389" protocol="tcp" isPublic="false" enableDirectServerReturn="false" isDirectAddress="false" disableStealthMode="false">
	          <LocalPorts>
	            <LocalPortRange from="3389" to="3389" />
	          </LocalPorts>
	        </Endpoint>
	      </InputEndpoints>
	    </Instance>
	  </Instances>
	</SharedConfig>

<!--HONumber=35.1-->
