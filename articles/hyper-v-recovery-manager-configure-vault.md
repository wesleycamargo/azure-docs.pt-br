<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configurar-hyper-v-recuperação-compartimento" pageTitle="Configurar o Hyper-V Recovery Manager para proteger as máquinas virtuais em nuvens do VMM" metaKeywords="recuperação do hyper-v, VMM, nuvens, recuperação de desastres" description="O Gerenciador de Recuperação Hyper-V do Azure pode ajudá-lo a proteger os aplicativos e serviços coordenando a replicação e recuperação de máquinas virtuais localizado em nuvens privadas do System Center 2012 VMM." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Configurar o Gerenciador de Recuperação Hyper-V do Azure" writer="raynew" editor="jimbe" manager="cfreeman" />


# Configurar o Gerenciador de Recuperação Hyper-V do Azure



<div class="dev-callout"> 

<P>O Gerenciador de Recuperação Hyper-V do Azure coordena e gerencia a proteção de máquinas virtuais do Hyper-V localizadas em nuvens privadas em servidores do Virtual Machine Manager (VMM) no System Center 2012 Service Pack 1 (SP1) ou o System Center 2012 R2. O Gerenciador de Recuperação do Hyper-V coordena o failover dessas máquinas virtuais de um servidor do host do Hyper-V local para outro. Os cofres do Gerenciador de Recuperação do Hyper-V no Azure são usados para armazenar a configuração de proteção.</P>

<h2><a id="about"></a>Sobre este tutorial</h2>
<P>Este tutorial fornece uma breve passo a passo para implantação do Gerenciador de Recuperação do Hyper-V. Para obter informações mais detalhada, leia o seguinte:</P>
<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guia de planejamento do Gerenciador de Recuperação do Hyper-V</a>- Fornece informações detalhadas sobre as etapas de planejamento que devem ser concluídas antes de iniciar uma implantação completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guia de implantação do Gerenciador de Recuperação do Hyper-V</a>- Fornece instruções passo a passo para uma implantação completa.</LI>
<LI>Se você tiver problemas durante este tutorial, dê uma olhada em <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Gerenciador de Recuperação do Hyper-V: cenários comuns de erro e resoluções</a> ou poste suas perguntas no <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum dos Serviços de Recuperação do Azure</a></LI>
</UL>
</div>


<h2><a id="before"></a>Antes de começar</h2> 
<div class="dev-callout"> 
<P>Antes de iniciar este tutorial, verifique os pré-requisitos.</P>

<h3><a id="HVRMPrereq"></a>Pré-requisitos do Gerenciador de Recuperação de Hyper-V</h3>

<UL>
<LI>**Conta do Azure**- Você precisará de uma conta do Azure com o recurso Serviços de Recuperação do Azure ativado. Se você não tiver uma conta ou o recurso não estiver registrado, consulte a <a href="http://aka.ms/try-azure">versão de avaliação gratuita do Azure</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes de preços do Gerenciador de Recuperação do Hyper-V</a>.</LI>
<LI>**Certificado (. cer)**- Para registrar servidores do VMM em um cofre do Gerenciador de Recuperação do Hyper-V, você precisará carregar um certificado de gerenciamento (.cer) contendo a chave pública para o cofre. Observe o seguinte:<UL>
	<LI>Para fins de tutorial, você pode usar um certificado autoassinado criado usando a ferramenta Makecert.exe. Para uma implantação completa, você pode usar qualquer certificado SSL válido que está em conformidade com os pré-requisitos de certificado descritos no <a href=" http://go.microsoft.com/fwlink/?LinkId=386485">Pré-requisitos e suporte</a> no Guia de Planejamento.</LI>
	<LI>Cada cofre tem apenas um certificado .cer associado com ele a qualquer momento. Você pode carregar um novo certificado para substituir um já existente, conforme necessário.</LI>
</UL></LI>
<LI>**Certificado (arquivo. pfx)**- O certificado .cer deve ser exportado como um arquivo .pfx (contendo a chave privada). Você importa esse arquivo em cada servidor VMM que contém as máquinas virtuais que deseja proteger. Em seguida, durante a implantação, ao instalar o agente do Provedor do Gerenciador de Recuperação do Hyper-V em cada servidor do VMM, você seleciona o arquivo .pfx para registrar o servidor do VMM com o cofre.</LI>

</UL>

<h3><a id="VMMPrereq"></a>Pré-requisitos do VMM</h3>

<UL>
<LI>Pelo menos um servidor VMM executando no System Center 2012 SP1 ou o System Center 2012 R2.</LI>
<LI>Nuvens privadas do VMM. Se você estiver executando um servidor VMM, ele precisará de duas nuvens configuradas. Para dois ou mais servidores do VMM, você precisará de pelo menos uma nuvem no servidor do VMM de origem que você deseja proteger e uma nuvem no servidor VMM de destino que você usará para recuperação. A nuvem principal que você deseja proteger deve conter o seguinte:<UL>
	<LI>Um ou mais grupos de hosts do VMM</LI>
	<LI>Um ou mais servidores de host do Hyper-V em cada grupo de host.</LI>
		</UL></LI>
<LI>Se você quiser que máquinas virtuais sejam conectadas a uma rede VM após o failover, é necessário configurar o mapeamento de rede no Gerenciador de Recuperação do Hyper-V. Antes de iniciar a implantação, verifique o seguinte:<UL>
	<LI>A máquina virtual na nuvem no servidor VMM de origem está conectada a uma rede VM. Essa rede de VM deve ser vinculada a uma rede lógica que está associada com a nuvem.</LI>
	<LI>A nuvem de destino no servidor VMM de destino possui uma rede VM correspondente. Essa rede de VM deve ser vinculada a uma rede lógica correspondente que está associada com a nuvem de destino.</LI>
	</UL></LI>
	
	<P>Para saber mais sobre o mapeamento de rede, leia <a href="http://go.microsoft.com/fwlink/?LinkId=324817">Preparar para mapeamento de rede</a> no Guia de Planejamento.</P>
</UL>


<h2><a id="tutorial"></a>Etapas do tutoriais</h2> 

Depois de verificar os pré-requisitos, faça o seguinte:
<UL>
<LI><a href="#createcert">Obter e configurar certificados</a>- Obter um certificado .cer, exportar como um arquivo .pfx e importar o arquivo .pfx para servidores do VMM.</LI>
<LI><a href="#vault">Etapa 1: Criar um cofre</a>- Criar um cofre do Gerenciador de Recuperação do Hyper-V no Azure.</LI>
<LI><a href="#upload">Etapa 2: Carregar o certificado</a>- Carregar o certificado de gerenciamento para o cofre.</LI>
<LI><a href="#download">Etapa 3: Baixar e instalar o provedor</a>- Baixe e instale o Provedor do Gerenciador de Recuperação Hyper-V em servidores do VMM que você deseja proteger. Em seguida, registre os servidores do VMM com o cofre.</LI>
<LI><a href="#clouds">Etapa 4: Configure a proteção de nuvem</a>- Configurar as definições de proteção para nuvens do VMM.</LI>
<LI><a href="#networks">Etapa 5: Configurar o mapeamento de rede</a>- Mapeia redes de VM de servidores do VMM de origem para servidores do VMM de destino.</LI>
<LI><a href="#virtualmachines">Etapa 6: Ativar a proteção para máquinas virtuais</a>- Ativa a proteção para máquinas virtuais do Hyper-V localizadas nas nuvens do VMM.</LI>
<LI><a href="#recovery plans">Etapa 7: Configurar e executar planos de recuperação</a>- Crie e personalize os planos de recuperação que agrupam máquinas virtuais para failover. Em seguida, execute o plano de recuperação.</LI>
<LI><a href="#jobs">Etapa 8: Monitorar</a>- Monitore as configurações, o status e progresso usando as guias **Trabalhos** e **Painel**.</LI>
</UL>



<a name="createcert"></a> <h2>Obter e configurar certificados</h2>

Obter e configurar certificados como a seguir:
<OL>
<LI><a href="#obtaincert">Obter um certificado para a explicação passo a passo</a>- Obtenha um certificado usando a ferramenta MakeCert ou use um certificado existente compatível com os <a href="http://go.microsoft.com/fwlink/?LinkId=321294">requisitos de certificado</a>.</LI>
<LI><a href="#exportcert">Exportar o certificado em formato .pfx</a>- No servidor no qual o certificado reside ou foi criado, exporte o arquivo .cer como um arquivo .pfx (com a chave privada). </LI>
<LI><a href="#importcert">Importar o certificado .pfx para servidores do VMM</a>- Após a conclusão da exportação do arquivo .pfx, importe-o para o repositório de certificado pessoal em cada servidor VMM que você deseja registrar com o cofre.</LI>
</OL>


<h3><a id="obtaincert"></a>Obter um certificado autoassinado (.cer)</h3>
<P>Use a ferramenta MakeCert para criar um certificado .cer x.509 que cumpre todos os requisitos de certificado, como a seguir:</P>
<ol>
<LI>
No computador no qual você deseja executar o MakeCert, baixe a versão mais recente do <a href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a>. Observe que o comando makecert.exe faz parte do núcleo do Windows Software Development Kit, portanto, não é necessário baixar e instalar o SDK inteiro.</LI>
<LI>Na página Especificar Local, selecione **Instalar o Windows Software Development Kit para Windows 8.1 neste computador**.</LI>
<LI>Na página Associar ao Programa de Aperfeiçoamento da Experiência do Cliente (CEIP), selecione configuração de sua preferência.</LI>
<LI>Na página Contrato de Licença, clique em **Aceitar** para aceitar os termos.</LI>
<LI>Na página Selecione os recursos que deseja instalar, desmarque todas as opções exceto **Windows Software Development Kit**.</LI>
<LI>Depois que a instalação for concluída, verifique se makecert.exe é exibido na pasta C:\ProgramFiles (x86) \Windows Kits\<i>WindowsVersion</i>\bin\x64.</LI>
<LI>Abra um prompt de comando (cmd.exe) com privilégios de Administrador e navegue para a pasta makecert.exe.</LI> 
<LI>>Execute o seguinte comando para criar o certificado autoassinado. Substitua CertificateName pelo nome que deseja usar para o certificado e especifique a data de vencimento real do seu certificado após -e:</LI>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<P>O certificado será criado e armazenado na mesma pasta. Você pode movê-lo para um local mais acessível, para exportar durante a próxima etapa.</P>

<h3><a id="exportcert"></a>Exporte o certificado em formato .pfx</h3>
<P>Conclua as etapas neste procedimento para exportar o arquivo .cer no formato .pfx.</P>
<ol>
<li>Na tela Inicial, digite **mmc.exe** para iniciar o Console de Gerenciamento da Microsoft (MMC).</li>
<li>No menu **Arquivo**, clique em **Adicionar/Remover Snap-in**. A caixa de diálogo **Adicionar ou Remover Snap-ins** aparece.</li>
<li>Em **Snap-ins Disponíveis**, clique em **Certificados** e em **Adicionar**.</li>
<li>Selecione **Conta de computador** e clique em **Avançar**.</li>
<li>Selecione **Computador local** e clique em **Concluir**.</li>
<li>No MMC, na árvore de console, expanda **Certificados** e expanda **Pessoal**.</li>
<li>No painel de detalhes, clique no certificado que você deseja gerenciar.</li>
<li>No menu **Ação**, aponte para **Todas as Tarefas** e clique em **Exportar**. O Assistente para Exportação de Certificados é exibido. Clique em **Avançar**.</li>
<li>Na página **Exportar Chave Privada**, clique em **Sim** para exportar a chave privada. Clique em **Avançar**. Observe que isso só é necessário se você deseja exportar a chave privada para outros servidores após a instalação.</li>
<li>Na página Exportar Formato de Arquivo, selecione **Trocar Informação Pessoal â €"PKCS #12 (.PFX)** Clique em **Avançar**.</li>
<li>Na página **Senha**, digite e confirme a senha usada para criptografar a chave privada. Clique em **Avançar**.</li>
<li>Siga as páginas do assistente para exportar o certificado em formato .pfx.</li>
</ol>


<h3><a id="importcert"></a>Importe o certificado .pfx para servidores VMM</h3>
<p>Depois de exportar o servidor, copie para cada servidor VMM que você deseja registrar no cofre e, em seguida, importe da seguinte maneira. Observe que se você executou o MakeCert.exe em um servidor VMM, você não precisa importar o certificado nesse servidor.</p>
 
<ol>
<li>Copie os arquivos de certificado de chave privada (.pfx) em um local no servidor local.</li>
<li>No snap-in Certificados MMC, selecione **Conta de computador** e clique em Avançar.</li>
<li>Selecione Computador Local e clique em **Concluir**. Na caixa de diálogo **Adicionar/Remover Snap-ins**, clique em **OK**. </li>
<li>No MMC, expanda **Certificados**, clique com o botão direito do mouse em **Pessoal**, aponte para Todas as Tarefas e clique em **Importar** para iniciar o Assistente para Importação de certificados.</li>
<li>Na página de boas-vindas do Assistente de Importação de Certificados, clique em **Avançar**.</li>
<li>Na página Arquivo para Importar, clique em **Procurar** e localize a pasta que contém o arquivo de certificado .pfx que contém o certificado que você deseja importar. Selecione o arquivo apropriado e clique em **Abrir**.</li>
<li>Na página Senha, na caixa **Senha**, digite a senha para o arquivo de chave privada especificado no procedimento anterior e clique em **Avançar**.</li>
<li>Na página Repositório de Certificados, selecione **Colocar todos os certificados no repositório a seguir**, clique em **Procurar**, selecione o repositório **Pessoal**, clique em **OK** e em Avançar.</li>
<li>Na página de conclusão do Assistente de Importação de Certificados, clique em **Concluir**.</li>
</ol>

Depois de concluir essas etapas, você poderá escolher o certificado .cer para carregar no cofre e para selecionar o certificado .pfx, ao registrar um servidor VMM durante a instalação do provedor.
</div>


<a name="vault"></a> <h2>Etapa 1: Criar um cofre</h2>

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).


2. Clique em **Serviços de Dados** e, em seguida, clique em **Serviços de Recuperação**.  

	![Programa de visualização](./media/hyper-v-recovery-manager-configure-vault/RS_PreviewProgram.png)

3. Clique em **Serviços de Recuperação**, clique em **Criar Novo**, aponte para **Cofre do Gerenciador de Recuperação do Hyper-V** e clique em **Criação Rápida**.
	
	![Novo cofre](./media/hyper-v-recovery-manager-configure-vault/RS_hvvault.png)

3. Em **Nome**, digite um nome amigável para identificar o cofre.

4. Em **Região**, selecione a região geográfica para o cofre. Regiões geográficas disponíveis incluem sudeste do Pacífico da Ásia, Norte da Europa e Leste dos EUA.****

5. Em **Assinatura**, digite os detalhes da sua assinatura.

5. Clique em **Criar cofre**. 

<P>Verifique o status do cofre nas notificações na parte inferior do portal. Uma mensagem confirmará que o cofre foi criado com êxito. Será listada como **Online** na página de Serviços de Recuperação.</P>

<a name="upload"></a> <h2>Etapa 2: Carregar um certificado (.cer)</h2>


2. Na página **Serviços de Recuperação**, abra o cofre necessário.
3. Clique no ícone Inicialização Rápida para abrir a página Inicialização Rápida.

	![Ícone de Inicialização Rápida](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStartIcon.png)

2. Clique em **Gerenciar Certificado**.

	![Inicialização Rápida](./media/hyper-v-recovery-manager-configure-vault/RS_QuickStart.png)

3. Na caixa de diálogo **Gerenciar Certificados**, clique em **Procurar Arquivo** para localizar o arquivo .cer que você deseja carregar no cofre.


	![Gerenciar Certificado](./media/hyper-v-recovery-manager-configure-vault/RS_ManageCert.png)


<a name="download"></a> <h2>Etapa 3: Baixar e instalar o provedor</h2>
Instale o Provedor do Gerenciador de Recuperação do Hyper-V em cada servidor VMM que você deseja registrar no cofre. A versão mais recente do arquivo de instalação do Provedor é armazenada no Centro de Download do Azure. Ao executar o arquivo em um servidor VMM, o Provedor está instalado e o servidor VMM é registrado com o cofre.

1. Na página **Inicialização Rápida**, clique em **Baixar Provedor** para obter o arquivo .exe de instalação do Provedor. Execute esse arquivo no servidor VMM para iniciar a Instalação do Provedor.

	![Baixar Agente](./media/hyper-v-recovery-manager-configure-vault/RS_installwiz.png)

2. Siga as etapas para concluir a instalação do Provedor.

	![Instalação concluída](./media/hyper-v-recovery-manager-configure-vault/RS_SetupComplete.png)

3. Depois da instalação do Provedor estar concluída, siga as etapas do assistente para registrar o servidor VMM com o cofre.
4. Na página Conexão com a Internet, especifique como o Provedor em execução no servidor VMM se conecta à Internet. O Provedor pode usar as configurações de conexão com a Internet padrão no servidor ou clicar em **Usar um servidor de proxy para solicitações da Internet** para usar as configurações personalizadas.
	
	![Configurações da Internet](./media/hyper-v-recovery-manager-configure-vault/RS_ProviderProxy.png)

	Se você desejar usar as configurações personalizadas durante esta explicação passo a passo, leia as informações na <a href="http://go.microsoft.com/fwlink/?LinkId=378266">Etapa 2: Instalar o provedor e registrar os servidores VMM</a> do Guia de implantação.


5. Na página Registro de Certificado, selecione o arquivo .pfx que corresponde ao .cer carregado para o cofre. 

	![Registro de certificado](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg1.png)

	![Cobre de certificado](./media/hyper-v-recovery-manager-configure-vault/RS_CertReg2.png)

1. Na página Servidor VMM, especifique um nome amigável para o servidor VMM. Esse nome é usado para identificar o servidor no console do Gerenciador de Recuperação do Hyper-V.
2. Selecione **Sincronizar dados de nuvem com o cofre** para sincronizar os dados em todas as nuvens privadas localizadas no servidor VMM com o cofre do Gerenciador de Recuperação do Hyper-V. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode publicar cada nuvem individualmente para sincronizar antes de definir as configurações de proteção de nuvem.  
3. Clique em **Registrar** para concluir o processo. 

	![Configurações da Internet](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloudSetup.png)

<P>Nesse estágio, os metadados do servidor VMM é recuperado pelo Gerenciador de Recuperação do Hyper-V para orquestrar o failover e a recuperação. Depois que um servidor registrar com êxito seu nome amigável será exibido na guia **Recursos** da página Servidores no cofre.</P>


<h2><a id="clouds"></a>Etapa 4: Definir as configurações da proteção de nuvem</h2>

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Se você não ativar a opção **Sincronizar dados de nuvem com o cofre** ao instalar o Provedor no servidor VMM, será necessário publicar a nuvem para o Gerenciador de Recuperação do Hyper-V no console do VMM. Depois das nuvens serem detectadas pelo Gerenciador de Recuperação do Hyper-V, você pode definir as configurações de proteção. 

<h3><a id="publishclouds"></a>Publicar uma nuvem</h3>

1. No console VMM, abra o espaço de trabalho **VMs e Serviços**.
2. no painel **VMs e Serviços**, abra a nuvem que você deseja publicar.
3. Na página **Geral** das propriedades da nuvem, para publicar a nuvem, selecione **Enviar dados de configuração sobre esta nuvem no Gerenciador de Recuperação Hyper-V do Azure**. Depois que a nuvem é publicada, ela será exibida no cofre.

	![Nuvens](./media/hyper-v-recovery-manager-configure-vault/RS_PublishCloud.png)

	![Publicar uma nuvem](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

<h3><a id="configureclouds"></a>Configurar nuvens</h3>

Para configurar nuvens para proteção, faça o seguinte: 

1. Na página de Inicialização Rápida, clique em **Definir Configurações de Proteção**.
2. Na guia **Itens Protegidos**, selecione a nuvem que você deseja configurar e vá para a guia **Configuração**.

	![Configuração da nuvem](./media/hyper-v-recovery-manager-configure-vault/RS_CloudConfig.png)


3. Em **Local de Destino**, especifique o servidor VMM que gerencia a nuvem que você deseja usar para a recuperação.
4. Em **Nuvem de Destino**, especifique a nuvem de destino que você deseja usar para failover de máquinas virtuais na nuvem de origem.
5. Em **Copiar Frequência**, deixe a configuração padrão. Esse valor especifica a frequência com que dados devem ser sincronizados entre os locais de origem e de destino. Só é relevante quando o host do Hyper-V estiver executando o Windows Server 2012 R2. Para outros servidores, é usada uma configuração padrão de cinco minutos.
6. Em **Pontos de Recuperação Adicionais**, deixe a configuração padrão. Esse valor especifica se você deseja criar pontos de recuperação de adição. Com um valor padrão de zero apenas o ponto de recuperação mais recente para uma máquina virtual primária é armazenado em um servidor de host de réplica. 
7. Em **Frequência do Snapshot Consistente de Aplicativo**, mantenha a configuração padrão. Esse valor especifica a frequência de criação de snapshots. Os snapshots usam o Volume Shadow Copy Service (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é obtido.  Se você deseja definir esse valor para o tutorial passo a passo, certifique-se de que ele está definido para menos do que o número de pontos de recuperação adicional que você configurar.
8. Em **Compactação de Transferência de Dados**, especifique se os dados replicados transferidos devem ser compactados. 
9. Em **Autenticação**, especifique como o tráfego é autenticado entre os servidores de host do Hyper-V primário e de recuperação. A menos que tenha um ambiente Kerberos configurado em funcionamento, recomendamos que você selecione HTTPS para esta explicação passo a passo. Com HTTPS selecionado, os servidores de host irão autenticar entre si usando um certificado de servidor e o tráfego será criptografado. O Gerenciador de Recuperação do Hyper-V configura certificados para serem usados para autenticação HTTPS automaticamente. Nenhuma configuração manual é necessária. Observe que esta configuração só é relevante para servidores de host Hyper-V no Windows Server 2012 R2.
10. Em **Porta**, deixe a configuração padrão. Esse valor define o número da porta em que os computadores de origem e destino do host Hyper-V ouvem o tráfego de duplicação. 
11. Em **Configurações de Replicação Inicial**, especifique como a replicação inicial de dados de locais de origem para destino será tratada, antes de iniciar a replicação normal. 
	- **Pela rede**- Copiando dados pela rede pode ser demorado e consumir muitos recursos. Recomendamos usar essa opção se a nuvem contém máquinas virtuais com discos rígidos virtuais relativamente pequenos e se o servidor VMM primário está conectado ao servidor VMM secundário através de uma conexão com banda larga ampla. Você pode especificar que a cópia deve iniciar imediatamente ou selecionar uma hora. Se você usar a replicação de rede, recomendamos que você agende fora dos horários de pico.
	- **Offline**- Esse método especifica que a replicação inicial será executada usando mídia externa. É útil se você deseja evitar a degradação no desempenho da rede ou para locais geograficamente remotos. Para usar esse método, você especifica o local de exportação na nuvem de origem e o local de importação na nuvem de destino. Ao ativar a proteção de uma máquina virtual, o disco rígido virtual é copiado para o local de exportação especificado. Você envia para o local de destino e copia para o local de importação. O sistema copia as informações importadas para as máquinas virtuais de réplica. Para obter uma lista completa dos pré-requisitos de replicação offline, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=323469">Etapa 3: Definir configurações de proteção para nuvens VMM</a> no Guia de implantação.

<h4><a id="cloudsettingd"></a>Definições após a configuração de proteção</h4>
Depois de configurar uma nuvem, todos os clusters e servidores host configurados nas nuvens de origem e de destino estão definidos para replicação. Especificamente, configure o seguinte:

- Regras de firewall usadas pela Réplica do Hyper-V para que as portas para o tráfego de replicação sejam abertas. 
- Certificados necessários para a replicação são instalados.
- As configurações da Réplica Hyper-V são definidas.

As configurações de nuvem podem ser modificadas na guia **Configurar**. Observe que:

- É recomendável selecionar uma nuvem de destino que atende aos requisitos de recuperação para máquinas virtuais que você protegerá. 
- Uma nuvem só pode pertencer a um par único de nuvem - como uma nuvem primária ou de destino.
- Depois de salvar a configuração de nuvem, um trabalho será criado e pode ser monitorado na guia **Trabalhos**. Depois de salvar a configuração, para modificar a nuvem de destino local ou destino você deve remover a configuração de nuvem e reconfigure a nuvem.




<h2><a id="networks"></a>Etapa 5: Configurar o mapeamento de rede</h2>

Na guia **Redes**, você configura o mapeamento entre redes VM nos servidores VMM de origem e destino. O mapeamento de rede garante que depois da réplica de failover, as máquinas virtuais são conectadas às redes adequadas e para garantir que as máquinas virtuais de réplica sejam colocadas nos hosts que podem acessar as redes VM. Verificar os requisitos de rede VMM em <a href=" http://go.microsoft.com/fwlink/?LinkId=386485">Pré-requisitos e suporte</a> no Guia de Planejamento. Recomendamos que você mapeie as redes da seguinte maneira: 

- Mapeie redes usadas por cada nuvem configurada para proteção.
- Realize o mapeamento de rede antes de ativar máquinas virtuais para proteção, pois o mapeamento é usado durante o posicionamento das máquinas virtuais de réplica. O mapeamento irá funcionar corretamente se você configurar depois de ativar a proteção, mas talvez seja necessário migrar algumas máquinas virtuais de réplica.  

Para mapear redes, faça o seguinte:

1. Na página de Inicialização Rápida, clique em **Definir Mapeamento de Rede**.
4. Em **Local de Origem**, selecione o servidor VMM de origem. 
5. Em **Local de Destino**, selecione o servidor VMM de destino. Se você estiver implantando o Gerenciador de Recuperação do Hyper-V com um único servidor VMM, a origem e o destino serão os mesmos.
A lista de redes VM de origem e suas redes VM de destino associadas é exibida. Um valor vazio é mostrado para redes que não são mapeadas.
6. Selecione uma entrada não mapeada na lista de origem e de destino e clique em **Mapear**. O serviço detecta as redes VM no servidor de destino e as exibe.  

	![Gerenciar certificado](./media/hyper-v-recovery-manager-configure-vault/RS_networks.png)

7. Na página Selecionar uma Rede de Destino, selecione rede VM de destino que você deseja usar no servidor VMM de destino.
	![Rede de destino](./media/hyper-v-recovery-manager-configure-vault/RS_TargetNetwork.png) 

8. Clique nos ícones de informações ao lado dos nomes de rede de origem e de destino para exibir as subredes e tipo para cada rede.

	Ao selecionar uma rede de destino, as nuvens protegidas que utilizam a rede de origem são exibidas e a disponibilidade das redes VM de destino associadas com as nuvens também é exibida. Recomendamos que você selecione uma rede de destino disponível para todas as nuvens usadas para proteção.

8. Clique na marca de seleção para concluir o processo de mapeamento. Um trabalho inicia para rastrear o progresso do mapeamento. Isso pode ser visto na guia **Trabalhos**.

	Isso se conecta a qualquer réplica de máquinas virtuais existente correspondente às máquinas virtuais conectadas a rede VM de origem para a rede VM de destino. Isso também se conecta a qualquer nova réplica de máquinas virtuais criadas depois de ativar a réplica nas máquinas virtuais conectadas a rede VM de origem para a rede VM de destino.

<h3><a id="modifynetworks"></a>Modificar mapeamentos de rede</h3>
Os mapeamentos de rede podem ser modificados ou removidos na guia **Redes**. Observe o seguinte:
<UL>
<LI>Se você cancelar o mapeamento selecionado, o mapeamento é removido e as máquinas virtuais de réplica são desconectadas da rede que estão conectadas no momento do mapeamento.</LI>
<LI>Ao modificar um mapeamento selecionado, as alterações são atualizadas e as máquinas virtuais de réplica são conectadas para as novas configurações de rede fornecidas.</LI>
</UL>

<h2><a id="virtualmachines"></a>Etapa 6: Ativar a proteção para máquinas virtuais</h2>

<p>Depois de redes, servidores e nuvens estarem configurados corretamente, você pode ativar a proteção para máquinas virtuais na nuvem. Habilite a proteção no console VMM, clicando com o botão direito do mouse em cada máquina virtual que você deseja proteger e selecionando **Ativar Recuperação**.</p>

<p>Depois que a proteção é ativada, você pode ver a máquina virtual na lista de máquinas virtuais da nuvem. Você pode exibir o andamento da ação ativar proteção na guia **Trabalhos**.</p>

![Máquinas Virtuais](./media/hyper-v-recovery-manager-configure-vault/RS_Clouds.png)

<P>Três trabalhos são criados ao ativar a proteção de máquinas virtuais. O trabalho Ativar Proteção é executado. Após a replicação inicial concluir, mais dois trabalhos Finalizar Proteção são executados. A máquina virtual está pronta para failover somente depois que esses três trabalhos forem concluídos com êxito.</P>

<h2><a id="recoveryplans"></a>Etapa 7: Configurar e executar planos de recuperação</h2>
Um plano de recuperação reúne máquinas virtuais juntos em grupos, para que possam ser processadas como uma única unidade e especifica a ordem em que os grupos devem apresentar failover. Além de definir grupos de máquinas virtuais, é possível personalizar os planos de recuperação para executar scripts automáticos ou aguardar a confirmação de ações manuais. Para criar um plano de recuperação, faça o seguinte: 

1. Na página de Inicialização Rápida, clique em **Criar Plano de Recuperação**.
2. Na página Especificar a Recuperação, Nome e Destino, digite um nome e os servidores VMM de destino e de origem. Se você estiver implantando o Gerenciador de Recuperação do Hyper-V em um único servidor VMM, o servidor de origem e destino serão os mesmos. O servidor VMM de origem deve conter máquinas virtuais com proteção ativada.

	![Criar Plano de Recuperação](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan1.png)
3. Na página Selecionar Máquinas Virtuais, selecione as máquinas virtuais para adicionar ao plano de recuperação. Elas são adicionadas ao grupo padrão do plano de recuperação (Grupo 1).
4. Clique na marca de seleção para criar o plano de recuperação. Os planos de recuperação criados podem ser excluídos na guia **Planos de Recuperação**.

	![VMs do Plano de Recuperação](./media/hyper-v-recovery-manager-configure-vault/RS_RecoveryPlan2.png)


Depois de criar um plano de recuperação, é possível executar as seguintes ações:

- Personalizar o plano. Você pode adicionar novos grupos a um plano de recuperação ou adicionar máquinas virtuais a um grupo. Você pode definir ações personalizadas na forma de scripts e ações manuais que são concluídas antes e depois de grupos especificados. Os nomes de grupo são incrementais. Depois do Grupo 1 do plano de recuperação padrão, crie o Grupo 2, o Grupo 3 e assim por diante. Failover de máquinas virtuais de acordo com a ordem do grupo. Para obter mais informações sobre como personalizar os planos de recuperação, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=378271">Etapa 6: Criar e personalizar os planos de recuperação</a> no Guia de Implantação.
- Execute um teste de failover.


<h3><a id="run"></a>Executar planos de recuperação</h3>
Os planos de recuperação podem ser executados como parte de um teste proativo ou failover planejado ou durante um failover não planejado. Esta explicação passo a passo descreve como executar um teste de failover. Para obter informações sobre outros tipos de failover, leia o <a href="hhttp://go.microsoft.com/fwlink/?LinkId=378272">Guia de monitoramento e operações</a>.

<h3><a id="protect"></a>Execute o plano.</h3>

Execute um failover de teste para verificar seu plano de recuperação em um ambiente de teste. Um failover de teste é útil para verificar se seu plano de recuperação e estratégia de failover de máquinas virtuais estão funcionando conforme o esperado.  O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. 

<h5><a id="networkrecommendations"></a>Antes de começar</h5>
Quando um failover de teste é acionado, você será solicitado a especificar como máquinas virtuais de teste devem ser conectadas a redes após o failover. 
<UL>
<LI>Se você quiser usar uma rede existente, é recomendável criar uma rede lógica separada que não é usada na produção para essa finalidade.</LI>
<LI>Se você selecionar a opção para criar automaticamente uma rede VM de teste, as redes temporárias e máquinas virtuais de teste são limpas automaticamente após concluir o failover de teste.</LI>
<LI>Se você estiver usando uma rede lógica baseada em LAN virtual (VLAN), certifique-se de que os sites de rede adicionados à rede lógica são isolados. </LI>
<LI>Se você estiver usando uma rede lógica baseada em Virtualização de Rede do Windows, o Gerenciador de Recuperação do Hyper-V criará automaticamente redes VM isoladas.</LI>
</UL>


<h5><a id="runtest"></a>Execute um teste de failover</h5>
Execute um teste de failover para um plano de recuperação da seguinte maneira:

<OL>
<LI>Na guia **Planos de Recuperação**, selecione o plano de recuperação necessário.</LI>
<LI>Para iniciar o failover, clique no botão **Failover de Teste**.</LI>
<LI>Na página Confirmar Failover de Teste, especifique como máquinas virtuais devem ser conectadas a redes após o failover de teste, como a seguir:</LI>
<UL>
<LI>**Nenhum**- Selecione esta configuração para especificar que as redes VM não devem ser usadas no teste de failover. Use esta opção se você deseja testar máquinas virtuais individuais em vez de sua configuração de rede. Também fornece uma breve explicação sobre como testar se a funcionalidade de failover funciona. As máquinas virtuais de teste não serão conectadas a redes após um failover.</LI>
<LI>**Usar existente**- Use esta opção se você já criou e isolou uma rede VM a ser usada para teste de failover. Após fazer o teste de failover de todas as máquinas virtuais usadas de teste, o failover será conectado a rede especificada em **Rede VM**.</LI>
<LI>**Criar automaticamente**- Selecione esta configuração para especificar que o Gerenciador de Recuperação do Hyper-V deve criar automaticamente uma rede VM com base na configuração especificada na Rede Lógica e seus sites de rede relacionados. Use esta opção se o plano de recuperação usa mais de uma rede VM. No caso de redes de Virtualização de Rede do Windows, essa opção pode ser usada para criar automaticamente redes VM com as mesmas configurações (subredes e pools de endereços IP) na rede da máquina virtual de réplica. Essas redes VM são limpas automaticamente após concluir o teste de failover.</LI>
</UL>
</OL>


<P>Após o teste de failover estar concluído, faça o seguinte:</P>
<OL>
<LI>Verificar se as máquinas virtuais foram iniciadas com êxito.</LI>
<LI>Depois de verificar que máquinas virtuais foram iniciadas com êxito, conclua o teste de failover para limpar o ambiente isolado. Se você optar por criar automaticamente redes VM, limpar exclui todas as máquinas de virtuais de teste e testa as redes.</LI>
<LI>Clique em **Observações** para gravar e salvar observações associadas com o teste de failover.</LI>
<LI>Quando um teste failover é iniciado para um plano de recuperação, o processo de failover é exibido na página de detalhes do plano de recuperação. Você pode ver as etapas de failover de teste individuais e seus status e exibir ou criar observações associadas com o teste de failover. Além disso, os detalhes dos trabalhos de failover podem ser exibidos observando o trabalho de failover na guia **Trabalhos**. Os detalhes exibem cada trabalho associado com o failover, seu status, quando ele foi iniciado e sua duração. Você pode exportar um trabalho na lista de failover em uma planilha do Excel.</LI>
</OL>


Observe o seguinte:

- Na página Confirmar Failover de Teste, os detalhes do servidor VMM em que as máquinas virtuais de teste serão criadas são exibidos.  
- Você pode acompanhar o andamento dos trabalhos de failover de teste na guia **Trabalhos**. 






<h2><a id="jobsdashboard"></a>Etapa 8: Monitoramento</h2>
<h3><a id="jobsdashboard"></a>Usando a guia Trabalhos e o painel de controle</h3>

<h4><a id="jobs"></a>Trabalhos</h4>
A guia **Trabalhos** exibe as principais tarefas executadas pelo cofre do Gerenciador de Recuperação do Hyper-V, incluindo a configuração da proteção para uma nuvem; ativa e desativa a proteção para uma máquina virtual; executa um failover (planejado, não planejado ou teste); confirma um failover não planejado; e configura uma replicação inversa. 

Na guia **Trabalhos**, é possível executar as seguintes tarefas:



- **Executar uma consulta de trabalho**- Você pode executar uma consulta para recuperar trabalhos que correspondam a critérios especificados. Você pode filtrar para trabalhos usando os seguintes parâmetros:

- 	Encontrar os trabalhos que foram executados em um servidor VMM específico.
- 	Encontrar os trabalhos que foram executados em uma nuvem, máquina virtual, rede, plano de recuperação em uma nuvem ou em todos eles.
- 	Identificar trabalhos que ocorreram durante uma data e intervalo de tempo específico.

Observe que uma consulta pode retornar um máximo de 200 trabalhos, portanto, é recomendável restringir os parâmetros de consulta para retornar menos do que o máximo.

- **Obter detalhes do trabalho**- Você pode clicar em um trabalho na lista **Trabalhos** para obter mais detalhes. Os detalhes incluem o nome do trabalho, seu status, quando foi iniciado e quanto tempo durou. Na guia Detalhes do trabalho, você pode exportar os detalhes de trabalho para uma planilha do Excel ou tentar reiniciar um trabalho cancelado, ignorado ou com falha. 
- **Exportar Trabalhos**- Você pode exportar os resultados de uma consulta de trabalho para uma planilha do Excel.
- **Reiniciar**- Você pode reiniciar trabalhos que falharam ao tentar executá-los novamente.
- **Detalhes do Erro**- Para trabalhos com falha, você pode clicar em **Detalhes do Erro** para obter uma lista de erros do trabalho. As recomendações e possíveis causas são exibidas para cada erro. A descrição do erro pode ser copiada para a área de transferência para solução de problemas.


>A guia **Trabalhos** fornece a seguinte informação:
<UL>
<LI>**Nome**- O nome do trabalho</LI>
<LI>**Item**- O nome da nuvem, o plano de recuperação, o servidor VMM ou a máquina virtual na qual o trabalho foi executado.</LI>
<LI>**Status**- Status do trabalho - concluído, cancelado, com falha ou outro</LI>
<LI>**Tipo**- Tipo de trabalho</LI>
<LI>**Iniciado em**- Quando o trabalho foi iniciado</LI>
<LI>**Tempo da tarefa**- A duração da tarefa</LI>
</UL>

![Guia Trabalhos](./media/hyper-v-recovery-manager-configure-vault/RS_Jobs.png)



<h4><a id="protect"></a>Executar uma consulta de trabalho</h4>

Você pode executar consultas para recuperar trabalhos que correspondam a critérios especificados, incluindo:
<UL>
	<LI>Trabalhos que foram executados em um servidor VMM específico.</LI>
	<LI>Trabalhos que foram executados em uma nuvem, máquina virtual, rede, plano de recuperação específicos ou em todos eles.</LI>
	<LI>Trabalhos que ocorreram durante um intervalo de tempo específico.</LI>
</UL>
	

Para executar uma consulta de trabalho, faça o seguinte:
<OL>
<LI>Abra a guia **Trabalhos**</LI>
<LI>Em **Servidor**, especifique o servidor VMM no qual o trabalho foi executado</LI>
<LI>Em **Tipo**, especifique se o trabalho foi executado em uma nuvem, máquina virtual, rede ou plano de recuperação em uma nuvem.</LI>
<LI>Em **Status**, especifique o status do trabalho.</LI>
<LI>Em **Duração**, especifique o intervalo de tempo no qual ocorreu o trabalho.</LI>
</OL>


<h4><a id="dashboard"></a>Painel</h4>
<P>A guia **Painel** fornece uma visão geral rápida do uso do Gerenciador de Recuperação do Hyper-V em sua organização.  Ele fornece um gateway centralizado para exibir máquinas virtuais protegidas pelos cofres do Gerenciador de Recuperação do Hyper-V. O Painel fornece a seguinte funcionalidade:</P>

Na guia **Painel**, é possível executar as seguintes tarefas:



- **Baixar Provedor**- Baixe o provedor de Gerenciador de Recuperação do Hyper-V para instalação em um servidor VMM.
- **Gerenciar Certificados**- Modificar as configurações para o certificado associado ao cofre.
- **Excluir-** Exclui um cofre.
- **Ressincronizar máquinas virtuais**- Se o Gerenciador de Recuperação do Hyper-V detecta que qualquer máquina virtual primária e de recuperação não são sincronizadas conforme o esperado, é possível exibir uma lista das máquinas virtuais relevantes e selecionar as que você deseja tentar sincronizar novamente do Painel. 


A guia **Painel** fornece a seguinte informação:
<UL>
<LI**>Visão Geral de Uso**- Mostra o número de máquinas virtuais que tenha proteção gerenciada pelo Gerenciador de Recuperação do Hyper-V.</LI>
<LI>**Resumo**- Exibe informações de configuração cruciais para os serviços de recuperação e o cofre do Gerenciador de Recuperação do Hyper-V. Informa se o cofre está online, qual o certificado é atribuído a ele, quando o certificado expira e os detalhes da assinatura do serviço.</LI>
<LI>**Trabalhos Recentes**- Mostra trabalhos que tiveram êxito ou falha nas últimas 24 horas ou trabalhos que estão em andamento ou aguardando ação.</LI>
<LI>**Problemas**- O painel mostra informações sobre problemas com conexões de servidor VMM e problemas com configurações de nuvem ou sincronização de replicação da máquina virtual. Você pode obter mais detalhes de uma problema, exibir trabalhos associados com o problema ou tentar ressincronizar máquinas virtuais.</LI>
</UL>

![Painel](./media/hyper-v-recovery-manager-configure-vault/RS_Dashboard.png)

<h2><a id="next"></a>Próximas etapas</h2>
<UL>
<LI>Para planejar e implantar o Gerenciador de Recuperação do Hyper-V em um ambiente de produção completo, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=321294">Guia de planejamento para o Gerenciador de Recuperação do Hyper-V</a> e <a href="http://go.microsoft.com/fwlink/?LinkId=321295">Guia de implantação do Gerenciador de Recuperação do Hyper-V</a>.</LI>
<LI>Para dúvidas, visite o <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Fórum de serviços de recuperação do Azure</a>.</LI> 
</UL>

