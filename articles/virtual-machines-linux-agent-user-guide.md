<properties linkid="manage-linux-howto-linux-agent" urlDisplayName="Guia do agente Linux" pageTitle="Guia do usuário do agente Linux para o Azure" metaKeywords="" description="Aprenda a instalar e configurar o Agente Linux (waagent) para gerenciar sua interação de máquina virtual com o Azure Fabric Controller." metaCanonical="" services="virtual-machines" documentationCenter="" title="Guia do usuário do agente Linux para o Azure" authors="" solutions="" manager="" editor="" />





#Guia do usuário do agente Linux para o Azure

##Introdução

O agente do Linux Azure (waagent) gerencia a interação de máquina virtual com o Azure Fabric Controller. Ele fornece a seguinte funcionalidade para Linux IaaS implantações:

* **Provisionamento de imagem**
  - Criação de uma conta de usuário
  - Configurando tipos de autenticação do SSH
  - Implantação de chaves públicas do SSH e pares de chaves
  - Configuração do nome de host
  - Publicar o nome do host para a plataforma de DNS
  - Relatório de impressão digital da chave host SSH para a plataforma
  - Gerenciamento de recursos de disco
  - Formatação e montar o disco do recurso
  - Configurando o espaço de permuta
* **Rede**
  - Gerencia rotas para melhorar a compatibilidade com servidores DHCP de plataforma
  - Garante a estabilidade do nome da interface de rede
* **Kernel**
  - Configurando NUMA virtual
  - Consumir entropia de Hyper-V para /dev/random
  - Configurando o tempo limite de SCSI para dispositivo raiz (o que poderia ser remoto)
* **Diagnostics**
  - Redirecionamento de console de porta serial
* **Implantações SCVMM:**
    - Detectar e inicializar o agente VMM para Linux quando executado em um sistema
      Ambiente do Virtual Machine Manager 2012 R2 Center


O fluxo de informações da plataforma para o agente ocorre por meio de dois canais:

* Um tempo de inicialização anexado DVD para implantações de IaaS. Este DVD inclui um arquivo de configuração compatível com OVF que inclui todas as informações de configuração que não seja os pares de chaves SSH real.

* Um ponto de extremidade TCP expor uma API REST usada para obter a implantação e a configuração de topologia.

###Como obter o agente Linux
Você pode obter o agente mais recente do Linux diretamente do:

- [Os diferentes provedores de distribuição endossando o Linux no Azure](http://support.microsoft.com/kb/2805216)
- ou o [Github abrir repositório de origem para o Azure agentes do Linux](https://github.com/WindowsAzure/WALinuxAgent)


###Distribuições Linux suportadas
* CentOS 6.2+
* 7.0 + Debian
* Ubuntu 12.04 +
* openSUSE 12.3+
* SLES 11 SP2+
* Oracle Linux 6.4+

Outros sistemas suportados:

* FreeBSD 9 + (WALinuxAgent v2.0.0 +)


###Requisitos

Waagent depende de alguns pacotes de sistema para funcionar corretamente:

* Python 2.5+
* OpenSSL 1.0 +
* Openssh 5.3 +
* Utilitários de sistema de arquivos: sfdisk, fdisk, mkfs
* Ferramentas de senha: chpasswd, sudo
* Ferramentas de processamento de texto: sed, grep
* Ferramentas de rede: roteamento ip

##Instalação

Instalação usando um RPM ou um pacote DEB do repositório de pacotes da distribuição é o método preferencial para instalar e atualizar o Azure do Windows Azure Linux.

Se instalar manualmente, waagent deve ser copiado para /usr/sbin/waagent e instalada executando: 

	# sudo chmod 755 /usr/sbin/waagent
	# /usr/sbin/waagent -install -verbose

Arquivo de log do agente é mantido em /var/log/waagent.log.


##Opções de linha de comando

###Sinalizadores

- verbose: aumentar o nível de detalhes do comando especificado
- forçar: Ignorar confirmação interativa para alguns comandos

###Comandos

- Ajuda: lista os comandos com suporte e sinalizadores.

- instalar: instalação Manual do agente
 * Verifica o sistema para dependências necessárias

 * Cria o script de inicialização de SysV (/ etc/init.d/waagent), o arquivo de configuração do logrotate (/etc/logrotate.d/waagent e configura a imagem para executar o script de inicialização na inicialização

 * Arquivo de configuração de exemplo de gravações para /etc/waagent.conf

 * Arquivos de configuração existentes é movido para /etc/waagent.conf.old

 * Detectar a versão do kernel e aplica a solução VNUMA, se necessário

 * Move udev regras que podem interferir na rede (/ lib/udev/rules.d/75-persistent-net-generator.rules, /etc/udev/rules.d/70-persistent-net.rules) para/var/lib/waagent /  

- desinstalar: remover waagent e arquivos associados
 * Cancela o registro o script de inicialização do sistema e exclui-lo

 * Exclui a configuração do logrotate e o arquivo de configuração waagent em /etc/waagent.conf

 * Restaura quaisquer regras movido udev que foram movidas durante a instalação

 * Não há suporte para a reversão automática da solução alternativa VNUMA, edite os arquivos de configuração do GRUB manualmente para reativar NUMA se necessário.

- deprovision: tentativa de limpar o sistema e torná-lo adequado para reprovisionamento. Esta operação excluiu o seguinte:
 * Todas as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)

 * Configuração de servidor de nomes no /etc/resolv.conf

 * Senha raiz do /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)

 * Concessões de cliente DHCP em cache

 * Redefine o nome de host para localdomain

 **Aviso:** Deprovision não garante que a imagem está limpo de todas as informações confidenciais e adequada para redistribuição.

- deprovision + user: executa tudo em - deprovision (acima) e também exclui a última conta de usuário provisionado (obtida em /var/lib/waagent) e dados associados. Este parâmetro é quando a desconfiguração de uma imagem que foi anteriormente provisionamento no Azure para podem ser capturada e usada novamente.

- versão: exibe a versão do waagent

- serialconsole: configura GRUB para marcar ttyS0 (a primeira porta serial) como o console de inicialização. Isso garante que os logs de inicialização do kernel são enviadas para a porta serial e disponibilizados para depuração.

- daemon: executar waagent como um daemon para gerenciar a interação com a plataforma.
   Esse argumento é especificado para waagent no script de inicialização de waagent.

##Configuração

Um arquivo de configuração (/ etc/waagent.conf) controla as ações de waagent. 
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
Opções de configuração são de três tipos: booliano, String ou Integer. 
As opções de configuração Boolean podem ser especificadas como "y" ou "n". 
A palavra-chave especial "Nenhum" pode ser usado para entradas de configuração de tipo algum sequência conforme detalhado abaixo.

**Role.StateConsumer:**

Tipo: String  
Padrão: nenhum

Se for especificado um caminho para um programa executável, ele é chamado quando waagent forneceu a imagem e o estado "Pronto" está prestes a ser relatado à malha. O argumento especificado para o programa serão "Pronto". O agente não aguardará para que o programa retorne antes de continuar.

**Role.ConfigurationConsumer:**

Tipo: String  
Padrão: nenhum

Se um caminho para um programa executável for especificado, o programa é invocado quando a malha indica que um arquivo de configuração está disponível para a máquina virtual. O caminho para o arquivo de configuração XML é fornecido como um argumento para o executável. Isso pode ser chamado várias vezes sempre que for alterado o arquivo de configuração. Um arquivo de exemplo é fornecido no Apêndice a. O caminho atual desse arquivo é /var/lib/waagent/HostingEnvironmentConfig.xml.

**Role.TopologyConsumer:**

Tipo: String  
Padrão: nenhum

Se um caminho para um programa executável for especificado, o programa é invocado quando a malha indica que um novo layout de topologia de rede está disponível para a máquina virtual.O caminho para o arquivo de configuração XML é fornecido como um argumento para o executável. Isso pode ser chamado várias vezes sempre que a topologia de rede é alterado (devido ao serviço reparo por exemplo). Um arquivo de exemplo é fornecido no Apêndice a. O caminho atual desse arquivo é /var/lib/waagent/SharedConfig.xml.

**Provisioning.Enabled:**

Tipo: booliano  
Padrão: y

Isso permite que o usuário habilite ou desabilite a funcionalidade de provisionamento no agente. Os valores válidos são "y" ou "n". Se a configuração for desabilitada, as chaves SSH de host e usuário da imagem são preservadas e qualquer configuração especificada no Azure provisionamento API será ignorada.

**Provisioning.DeleteRootPassword:**

Tipo: booliano  
Padrão: n

Se definido, a senha raiz no arquivo sombra é apagado durante o processo de provisionamento.

**Provisioning.RegenerateSshHostKeyPair:**

Tipo: booliano  
Padrão: y

Se o conjunto de todos os SSH host pares de chaves (ecdsa, dsa e rsa) será excluído durante o processo de provisionamento de /etc/ssh /. E um único par de chave novo é gerado.

O tipo de criptografia para o novo par de chaves é configurável pela entrada do Provisioning.SshHostKeyPairType. Observe que algumas distribuições novamente irá criar pares de chaves SSH para todos os tipos de criptografia está faltando quando é reiniciado o daemon do SSH (por exemplo, após uma reinicialização).

**Provisioning.SshHostKeyPairType:**

Tipo: String  
Padrão: rsa

Isso pode ser definido como um tipo de algoritmo de criptografia que é suportado pelo daemon SSH na máquina virtual. Os valores geralmente aceitos são "rsa", "dsa" e "ecdsa". Observe que "putty.exe" no Windows não oferece suporte a "ecdsa". Portanto, se você pretende usar putty.exe no Windows para conectar-se a uma implantação do Linux, use "rsa" ou "dsa".

**Provisioning.MonitorHostName:**

Tipo: booliano  
Padrão: y

Se definido, waagent monitorará máquina virtual Linux para alterações de nome do host (conforme retornado pelo comando "hostname") e atualizar automaticamente a configuração de rede da imagem para refletir a alteração. Para enviar a alteração do nome para os servidores DNS, a rede será reiniciado na máquina virtual. Isso resultará em resumo perda de conectividade com a Internet.

**ResourceDisk.Format:**

Tipo: booliano  
Padrão: y

Se definido, o disco de recursos fornecido pela plataforma será formatado e montado por waagent se o tipo de sistema de arquivos solicitado pelo usuário em "ResourceDisk.Filesystem" for algo diferente de "ntfs". Uma única partição do tipo Linux (83) será disponibilizada no disco. Observe que essa partição não será formatada se ele pode ser montado com êxito.

**ResourceDisk.Filesystem:**

Tipo: String  
Padrão: ext4

Especifica o tipo de sistema de arquivos para o disco do recurso. Valores aceitos variam de acordo com a distribuição do Linux. Se a sequência for X, em seguida, mkfs.X deve estar presente na imagem do Linux. Imagens de 11 SLES geralmente devem utilizar 'ext3'. FreeBSD imagens devem usar 'ufs2' aqui.

**ResourceDisk.MountPoint:**

Tipo: String  
Padrão: recurso /dev/emcpowera1 

Especifica o caminho em que o disco do recurso é montado.

**ResourceDisk.EnableSwap:**

Tipo: booliano  
Padrão: n 

Se definir um arquivo de permuta (/ arquivo de permuta) é criado no disco recursos e adicionado ao espaço de troca de sistema.

**ResourceDisk.SwapSizeMB:**

Tipo: inteiro  
Padrão: 0

Especifica o tamanho máximo do arquivo de permuta em megabytes.

**LBProbeResponder:**

Tipo: booliano  
Padrão: y

Se definido, waagent responderá ao balanceador de carga de testes da plataforma (se presente).

**Logs.Verbose:**

Tipo: booliano  
Padrão: n

Se definido, a verbosidade do log é aumentado. Waagent faz /var/log/waagent.log e aproveita a funcionalidade de logrotate do sistema para girar os logs.

**SISTEMA OPERACIONAL.RootDeviceScsiTimeout:**

Tipo: inteiro  
Padrão: 300

Isso configura o tempo limite de SCSI em segundos nos drives de disco e os dados de SO. Se não for definido, o sistema de padrões são usados.

**SISTEMA OPERACIONAL.OpensslPath:**

Tipo: String  
Padrão: nenhum

Isso pode ser usado para especificar um caminho alternativo para o openssl binário a ser usado para operações de criptografia.

##Apêndice

###Amostra do Arquivo de configuração de função

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

###Amostra do Arquivo de topologia de função

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

