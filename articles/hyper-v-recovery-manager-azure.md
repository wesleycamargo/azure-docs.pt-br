<properties linkid="configure-hyper-v-recovery-vault" urlDisplayName="configure-Azure-Site-Recovery" pageTitle="Configure Azure Site Recovery to protect virtual machines on Hyper-V server located in VMM clouds" metaKeywords="Azure Site Recovery, VMM, clouds, disaster recovery" description="Azure Site Recovery coordinates the replication, failover and recovery of Hyper-V virtual machines located in VMM clouds from one on-premises site to another. Azure Site Recovery can also replicate, failover, and recover Hyper-V virtual machine data between VMM clouds and Microsoft Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Getting Started with Azure Site Recovery: On-Premises to Azure Protection" editor="jimbe" manager="cfreeman" authors="" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Introdução à Recuperação de Site do Azure: Proteção no local para o Azure

<div class="dev-callout"> 

<p>Use a recupera&ccedil;&atilde;o de site do Azure para proteger m&aacute;quinas virtuais executadas em hosts Hyper-V localizados nas nuvens do Virtual Machine Manager (VMM) do System Center. Voc&ecirc; pode configurar:</p>

<ul>
<li><b>Prote&ccedil;&atilde;o no local para o Azure</b> - fa&ccedil;a a replica&ccedil;&atilde;o de m&aacute;quinas virtuais locais localizadas em servidores de host Hyper-V em nuvens VMM para Azure. Voc&ecirc; configura e habilita as configura&ccedil;&otilde;es de prote&ccedil;&atilde;o nos cofres da Recupera&ccedil;&atilde;o de Site do Azure. Os dados de m&aacute;quinas virtuais se replicam por meio de um servidor Hyper-V local para o armazenamento do Azure.</li>

<li><b>Prote&ccedil;&atilde;o do local para o pr&oacute;prio local</b> - fa&ccedil;a a replica&ccedil;&atilde;o de m&aacute;quinas virtuais localizadas em servidores de host Hyper-V em nuvens VMM de um local dessa instala&ccedil;&atilde;o para outro. Voc&ecirc; configura e habilita as configura&ccedil;&otilde;es de prote&ccedil;&atilde;o nos cofres da Recupera&ccedil;&atilde;o de Site do Azure. Os dados de m&aacute;quinas virtuais se replicam de um servidor Hyper-V local para outro. A Recupera&ccedil;&atilde;o de Site do Azure apenas gerencia o processo.
Aprenda sobre este cen&aacute;rio em <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Introdu&ccedil;&atilde;o &agrave; Recupera&ccedil;&atilde;o de Site do Azure: Prote&ccedil;&atilde;o do local para o pr&oacute;prio local</a>.</li>

</ul>
 
<h2><a id="about"></a>Sobre este tutorial</h2>


<P>Este tutorial &eacute; destinado a ajudar voc&ecirc; a implantar a Recupera&ccedil;&atilde;o de Site do Azure para uma prova r&aacute;pida de conceito. Ela utiliza o caminho mais r&aacute;pido e configura&ccedil;&otilde;es padr&atilde;o, onde poss&iacute;vel. Incluindo passos para:
<ul>
<li>Configurar um cofre de Recupera&ccedil;&atilde;o de Site do Azure - Obtenha um certificado carregado para o cofre, configure o servidor VMM de origem e gere uma chave de cofre. </li>
<li>Configurar o servidor VMM de origem e servidores host Hyper-V - Instale o Provedor de Recupera&ccedil;&atilde;o de Site do Azure no servidor VMM de origem e instale o Agente de Servi&ccedil;os de Recupera&ccedil;&atilde;o do Azure nos servidores host Hyper-V.</li>
<li>Configurar as nuvens VMM - Defina as configura&ccedil;&otilde;es de prote&ccedil;&atilde;o para nuvens no servidor VMM de origem.</li>
<li>Habilitar m&aacute;quinas virtuais - Habilite a prote&ccedil;&atilde;o para m&aacute;quinas virtuais</li>
<li>Execute um failover - Crie um plano de recupera&ccedil;&atilde;o e execute um teste de failover.</li>
</ul>


Para obter informa&ccedil;&otilde;es sobre uma implanta&ccedil;&atilde;o completa, consulte:</P>

<UL>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=321294">Plano para implanta&ccedil;&atilde;o da Recupera&ccedil;&atilde;o de Site do Azure</a> - Descreve as etapas de planejamento que voc&ecirc; deve concluir antes de iniciar uma implanta&ccedil;&atilde;o completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=402679">Implantar a Recupera&ccedil;&atilde;o de Site do Azure: Prote&ccedil;&atilde;o no local para o Azure</a> - Fornece instru&ccedil;&otilde;es passo a passo para uma implanta&ccedil;&atilde;o completa.</LI>
<LI><a href="http://go.microsoft.com/fwlink/?LinkId=378272">Administrar e monitorar a Recupera&ccedil;&atilde;o de Site do Azure</a> - Descreve como executar failovers e como gerenciar e monitorar sua implanta&ccedil;&atilde;o.</LI>
</UL>
<P>Se voc&ecirc; passar por problemas durante este tutorial, revise o artigo wiki <a href="http://go.microsoft.com/fwlink/?LinkId=389879">Recupera&ccedil;&atilde;o de Site do Azure: Cen&aacute;rios comuns de erro e solu&ccedil;&otilde;es</a> ou publique suas quest&otilde;es no <a href="http://go.microsoft.com/fwlink/?LinkId=313628">F&oacute;rum de Servi&ccedil;os de Recupera&ccedil;&atilde;o do Azure</a>.</P>

</div>

## <span id="before"></span></a>Antes de começar

<div class="dev-callout"> 
<P>Antes de iniciar este tutorial, verifique os pr&eacute;-requisitos.</P>

<h3><a id="HVRMPrereq"></a>Pr&eacute;-requisitos do Azure</h3>

<UL>
<LI><b>Conta do Azure</b> - Voc&ecirc; precisar&aacute; de uma conta do Azure. Se voc&ecirc; n&atilde;o possui uma, consulte <a href="http://aka.ms/try-azure">Avalia&ccedil;&atilde;o gratuita do Azure</a>. Obtenha informa&ccedil;&otilde;es sobre pre&ccedil;o em <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Detalhes dos Pre&ccedil;os do Gerenciador de Recupera&ccedil;&atilde;o de Site do Azure</a>.</LI>
<LI><b>Certificado</b> - Voc&ecirc; precisar&aacute; carregar um certificado de gerenciamento (.cer) com uma chave p&uacute;blica para o cofre. Voc&ecirc; exportar&aacute; o certificado como um arquivo .pfx (com chave privada) e o importar&aacute; em cada servidor VMM que voc&ecirc; deseja registrar no cofre. Para este tutorial, voc&ecirc; utilizar&aacute; um certificado autoassinado. Para uma implanta&ccedil;&atilde;o completa, voc&ecirc; pode usar um certificado SSL v&aacute;lido que esteja em conformidade com os <a href="http://go.microsoft.com/fwlink/?LinkId=321294">requisitos de certificado</a> descritos no guia de planejamento</a>. </LI>


</LI>

<LI><b>Conta de armazenamento do Azure</b> - Voc&ecirc; precisar&aacute; de uma conta de armazenamento do Azure para armazenar dados replicados para o Azure. A conta precisa estar com a georreplica&ccedil;&atilde;o habilitada. Ela deve estar localizada na mesma regi&atilde;o que o servi&ccedil;o de Recupera&ccedil;&atilde;o de Site do Azure e ser associada &agrave; mesma assinatura. Para aprender mais sobre configura&ccedil;&atilde;o do armazenamento do Azure, consulte <a href="http://go.microsoft.com/fwlink/?LinkId=398704">Introdu&ccedil;&atilde;o ao Armazenamento do Microsoft Azure</a>.</LI>
</UL>

<h3><a id="VMMPrereq"></a>Pr&eacute;-requisitos do VMM</h3>

<UL>
<LI><b>Servidor VMM</b> - Um servidor VMM executando no System Center 2012 R2.</LI>
<LI><b>Nuvens VMM</b> - Pelo menos uma nuvem no servidor VMM. A nuvem deve conter:
    <UL>
    <LI>Um ou mais grupos de hosts do VMM</LI>
    <LI>Um ou mais servidores host Hyper-V ou clusters em cada grupo de hosts.</LI>
    <li>Uma ou mais m&aacute;quinas virtuais localizadas no servidor Hyper-V de origem na nuvem. As m&aacute;quinas virtuais devem ser de gera&ccedil;&atilde;o 1.</li>
        </UL></LI>  
</UL>

<h3><a id="VMPrereq"></a>Pr&eacute;-requisitos de m&aacute;quina virtual</h3>

<UL>
<LI><b>Gera&ccedil;&atilde;o</b>- o Azure suporta somente m&aacute;quinas virtuais de gera&ccedil;&atilde;o 1.</LI>
<LI>Para obter uma lista completa de requisitos de suporte a m&aacute;quina virtual para failover para Azure, leia <a href="http://go.microsoft.com/fwlink/?LinkId=402602">Pr&eacute;-requisitos e suporte</a> no guia Planejamento. </LI>  
</UL>

<h2><a id="tutorial"></a>Etapas dos tutoriais</h2> 

Depois de verificar os pr&eacute;-requisitos, fa&ccedil;a o seguinte:
<UL>
<LI><a href="#createcert">Etapa 1: Obter e configurar certificados</a> - Obter um certificado .cer, exportar como um arquivo .pfx e importar o arquivo .pfx para servidores do VMM.</LI>
<LI><a href="#vault">Etapa 2: Criar um cofre</a> - Criar um cofre para Recupera&ccedil;&atilde;o de Site do Azure.</LI>
<LI><a href="#upload">Etapa 3: Configure o cofre</a> - carregue um certificado de gerenciamento ao cofre, ent&atilde;o, gere uma chave para esse cofre. A chave &eacute; utilizada para garantir que o Provedor, localizado no servidor VMM, somente execute comandos enviados pela Recupera&ccedil;&atilde;o de Site do Azure.</LI>
<LI><a href="#download">Etapa 4: Instalar o aplicativo Provedor</a> - Execute o aplicativo Provedor de Recupera&ccedil;&atilde;o de Site do Microsoft Azure no servidor VMM. Isso instala o Provedor e registra o servidor VMM no cofre.</LI>
<LI><a href="#agent">Etapa 5: Instalar o aplicativo do Agente</a> &mdash; Instale o Agente de Servi&ccedil;os de Recupera&ccedil;&atilde;o do Microsoft Azure em cada host Hyper-V.</LI>
<LI><a href="#clouds">Etapa 6: Configurar a prote&ccedil;&atilde;o de nuvem</a> &mdash; Configure as defini&ccedil;&otilde;es de prote&ccedil;&atilde;o para nuvens do VMM.</LI>
<LI><a href="#NetworkMapping">Etapa 7: Configurar o mapeamento de rede</a> &mdash; Voc&ecirc; pode, opcionalmente, configurar o mapeamento de rede para mapear redes de m&aacute;quinas virtuais de origem para redes Azure de destino.</LI>
<LI><a href="#virtualmachines">Etapa 8: Habilitar a prote&ccedil;&atilde;o para m&aacute;quinas virtuais</a> &mdash; Habilite a prote&ccedil;&atilde;o para m&aacute;quinas virtuais localizadas em nuvens VMM protegidas.</LI>
<LI><a href="#recovery plans">Etapa 9: Configurar e executar planos de recupera&ccedil;&atilde;o</a> &mdash; Crie um plano de recupera&ccedil;&atilde;o e execute um failover de teste para o plano.</LI>
</UL>



<a name="createcert"></a> <h2>Etapa 1: Obter e configurar certificados</h2>

Obtenha e configure certificados, conforme demonstrado a seguir:
<OL>
<LI><a href="#obtaincert">Obter um certificado autoassinado para a explica&ccedil;&atilde;o passo a passo</a> &mdash; Obtenha um certificado usando a ferramenta MakeCert.</LI>
<LI><a href="#exportcert">Exportar o certificado em formato .pfx</a> &mdash; No servidor no qual voc&ecirc; criou o certificado, exporte o arquivo .cer como um arquivo .pfx (com a chave privada). </LI>
<LI><a href="#importcert">Importar o certificado .pfx para o servidor VMM</a> &mdash; Ap&oacute;s a exporta&ccedil;&atilde;o do arquivo .pfx, importe-o para a pasta de armazenamento do Computador Local no servidor VMM que voc&ecirc; deseja registrar com o cofre.</LI>
</OL>


<h3><a id="obtaincert"></a>Obter um certificado autoassinado (.cer)</h3>
<P>Crie um certificado .cer x.509 em conformidade com todos os requisitos para certificados:</P>
<ol>
<LI>
No computador no qual voc&ecirc; deseja executar o MakeCert, baixe a vers&atilde;o mais recente do <a href="http://go.microsoft.com/fwlink/?LinkId=378269">Windows SDK</a>. Voc&ecirc; n&atilde;o precisar&aacute; instalar o SDK todo.</LI>
<ol>
<LI>Na p&aacute;gina Especificar Local, selecione <b>Instalar o Windows Software Development Kit para Windows 8.1 neste computador</b>.</LI>
<LI>Na p&aacute;gina Selecione os recursos que deseja instalar, desmarque todas as op&ccedil;&otilde;es exceto <b>Windows Software Development Kit</b>.</LI>
<LI>Depois que a instala&ccedil;&atilde;o for conclu&iacute;da, verifique se makecert.exe &eacute; exibido na pasta C:\ProgramFiles (x86)\Windows Kits\<i>WindowsVersion</i>\bin\x64.</LI>
<LI>Abra um prompt de comando (cmd.exe) com privil&eacute;gios de Administrador e navegue para a pasta makecert.exe.</LI> 
<LI>Execute o comando a seguir para criar o certificado autoassinado. Substitua CertificateName pelo nome que deseja usar para o certificado e especifique a data de vencimento real do seu certificado ap&oacute;s -e:</LI>
<code>
makecert.exe -r -pe -n CN=CertificateName -ss my -sr localmachine -eku 1.3.6.1.5.5.7.3.2 -len 2048 -e 01/01/2016 CertificateName.cer</code>
</ol>
<P>Um status bem-sucedido indica que o certificado foi criado. Ele &eacute; armazenado na mesma pasta que o makecert.exe. Voc&ecirc; pode querer mov&ecirc;-lo para um local mais acess&iacute;vel, para exporta&ccedil;&atilde;o.</P>

<h3><a id="exportcert"></a>Exporte o certificado em formato .pfx</h3>
<P>Conclua as etapas neste procedimento para exportar o arquivo .cer no formato .pfx.</P>
<ol>
<li>Um status bem-sucedido indica que o certificado foi criado. Ele &eacute; armazenado na mesma pasta que o makecert.exe. Voc&ecirc; pode querer mov&ecirc;-lo para um local mais acess&iacute;vel, para exporta&ccedil;&atilde;o.</li>
<li>No painel de detalhes, clique no certificado que voc&ecirc; deseja gerenciar.</li>
<li>No menu <b>A&ccedil;&atilde;o</b>, aponte para <b>Todas as Tarefas</b> e clique em <b>Exportar</b>. O Assistente para Exporta&ccedil;&atilde;o de Certificados &eacute; exibido. Clique em <b>Pr&oacute;ximo</b>.</li>
<li>Na p&aacute;gina <b>Exportar Chave Privada</b>, clique em <b>Sim</b> para exportar a chave privada. Clique em <b>Pr&oacute;ximo</b>. Observe que isso s&oacute; &eacute; necess&aacute;rio se voc&ecirc; deseja exportar a chave privada para outros servidores ap&oacute;s a instala&ccedil;&atilde;o.</li>
<li>Na p&aacute;gina Exportar Formato de Arquivo, selecione <b>Trocar Informa&ccedil;&atilde;o Pessoal &ndash; PKCS #12 (.PFX)</b>. Clique em <b>Pr&oacute;ximo</b>.</li>
<li>Na p&aacute;gina <b>Senha</b>, digite e confirme a senha usada para criptografar a chave privada. Clique em <b>Pr&oacute;ximo</b>.</li>
<li>Siga as p&aacute;ginas do assistente para exportar o certificado em formato .pfx.</li>
</ol>

<h3><a id="importcert"></a>Importe o certificado .pfx para servidores VMM</h3>
<p>Ap&oacute;s exportar o arquivo .pfx, copie-o para o servidor VMM e depois importe-o. Observe que se voc&ecirc; executou o MakeCert.exe no servidor VMM, voc&ecirc; n&atilde;o precisa importar o certificado nesse servidor.</p>
 
<ol>
<li>Copie os arquivos de certificado de chave privada (.pfx) em um local no servidor local.</li>
<li>No snap-in Certificados MMC, selecione <b>Conta de computador</b> e clique em <b>Avan&ccedil;ar</b>.</li>
<li>Selecione <b>Computador Local</b> e clique em <b>Concluir</b>.</li>
<li>No MMC, expanda <b>Certificados</b>, clique com o bot&atilde;o direito do mouse em <b>Pessoal</b>, aponte para <b>Todas as Tarefas</b> e clique em <b>Importar</b> para iniciar o Assistente para Importa&ccedil;&atilde;o de certificados.</li>
<li>Na p&aacute;gina Arquivo para Importar, clique em <b>Procurar</b> e localize a pasta que cont&eacute;m o arquivo de certificado .pfx que cont&eacute;m o certificado que voc&ecirc; deseja importar. Selecione o arquivo apropriado e clique em <b>Abrir</b>.</li>
<li>Na p&aacute;gina Senha, na caixa <b>Senha</b>, digite a senha para o arquivo .pfx de chave privada e clique em <b>Avan&ccedil;ar</b>.</li>
<li>Na p&aacute;gina Reposit&oacute;rio de Certificados, selecione <b>Colocar todos os certificados no reposit&oacute;rio a seguir</b>, clique em <b>Procurar</b>, selecione o reposit&oacute;rio <b>Pessoal</b>, clique em <b>OK</b> e em <b>Avan&ccedil;ar</b>. Conclua o assistente</li>
</ol>

Ap&oacute;s concluir essas etapas, quando voc&ecirc; configurar o cofre, poder&aacute; escolher o certificado .cer para carregar e selecionar o certificado .pfx ao registrar um servidor VMM durante a instala&ccedil;&atilde;o do Provedor.
</div>

<a name="vault"></a>

## Etapa 2: Criar um cofre

1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Expanda **Serviços de Dados**, expanda **Serviços de Recuperação** e clique em **Cofre de Recuperação de Site**.

3.  Clique em **Criar Novo** e depois em **Criação Rápida**.

4.  Em **Nome**, digite um nome amigável para identificar o cofre.

5.  Em **Região**, selecione a região geográfica para o cofre. As regiões geográficas disponíveis incluem Oeste da Europa, Sudeste da Ásia, Ásia Oriental, Norte da Europa, Oeste dos EUA, Leste dos EUA.
6.  Clique em **Criar cofre**.

    ![Novo cofre][Novo cofre]

Verifique a barra de status para confirmar se o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página principal de Serviços de Recuperação.

<a name="upload"></a>

## Etapa 3: Configurar o cofre

1.  Na página **Serviços de Recuperação**, clique no cofre para abrir a página Início Rápido. O Início rápido também pode ser aberto a qualquer momento pelo uso do ícone.

    ![Ícone de Inicialização Rápida][Ícone de Inicialização Rápida]

2.  Na lista suspensa **Recuperação de Configuração**, selecione **Entre um site local e o Microsoft Azure**.
3.  Para carregar o certificado (.cer) no cofre, clique em **Gerenciar Certificado**.

    ![Início rápido][Início rápido]

4.  Na caixa de diálogo **Gerenciar Certificado**, clique em **Procurar Arquivo** e selecione o arquivo .cer.

    ![Gerenciar Certificado][Gerenciar Certificado]

5.  Para gerar uma chave para o cofre, clique em **Obter a chave do cofre**. A chave é gerada automaticamente. Se você gerar novamente uma chave, ela substituirá a anterior. Observe que você precisará desta chave posteriormente, quando instalar o provedor de Recuperação de Site do Azure no servidor VMM.

    ![Gerenciar Certificado][1]

<a name="download"></a>

## Etapa 4: Instale o Provedor de Recuperação de Site do Azure

1.  Na página **Inicialização Rápida**, clique em **Baixar Provedor** para obter a versão mais recente do arquivo de instalação do Provedor.

    ![Arquivo Baixar Provedor][Arquivo Baixar Provedor]

2.  Execute esse arquivo no servidor VMM de origem.

    ![Baixar Agente][Baixar Agente]

3.  Após o Provedor ter sido instalado, continue a Configuração para registrar o servidor no cofre.
    ![Configuração Concluída][Configuração Concluída]
4.  Na página Conexão de Internet, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Clique em **Avançar** para usar as configurações padrão de conexão de Internet definidas no servidor.
    ![Configurações de Internet][Configurações de Internet]
5.  Na página Registro do Cofre, faça o seguinte:

    -   Selecione a chave privada (.pfx) que você importou para o servidor VMM.
    -   Selecione o cofre no qual você deseja registrar o servidor.
    -   Especifique a chave do cofre. Essa é a chave do cofre gerada por você anteriormente. Recorte e cole o valor da chave por meio da página Início rápido.

    </p>
    ![Registro de certificado][Registro de certificado]

6.  Na página Criptografia de Dados, você especifica se deseja ou não permitir criptografia de dados durante a replicação para uma nuvem específica. Se você selecionar essa opção, um certificado SSL será gerado automaticamente. Ao executar um failover, você precisará selecionar este certificado. Após habilitar essa configuração, você pode habilitar ou desabilitar a criptografia de dados para uma nuvem no Portal de Recuperação de Site do Azure. Para esse tutorial, deixe a configuração como o padrão e clique em **Avançar**.

    ![Cobre de certificado][Cobre de certificado]

7.  Na página Servidor VMM, faça o seguinte:

    -   Especifique um nome amigável para o servidor VMM. Esse nome é usado para identificar o servidor no console de Recuperação de Site do Azure.
    -   Selecione **Sincronizar metadados de nuvem com o cofre** para sincronizar informações sobre nuvens VMM com cofre de Recuperação de Site do Azure. Esta ação só precisa acontecer uma vez em cada servidor. Se você não quiser sincronizar todas as nuvens, você pode publicar cada nuvem individualmente para sincronizar antes de definir as configurações de proteção de nuvem.

8.  Clique em **Registrar** para concluir o processo.

    ![PublishCloud][PublishCloud]

Depois que um servidor for registrado com êxito, seu nome amigável será exibido na guia **Recursos** da página Servidores, no cofre.

## <span id="storage"></span></a>Etapa 5: Instalar o Agente de Serviços de Recuperação do Azure

Instale o Agente de Serviços de Recuperação do Azure em cada servidor de host Hyper-V localizado nas nuvens VMM que você deseja proteger.

1.  Na página Início rápido, clique em **Baixar Agente de Serviços de Recuperação de Site do Azure e instalar nos hosts** para obter a versão mais recente do arquivo de instalação do agente.

    ![Instalar o Agente de Serviços de Recuperação][Instalar o Agente de Serviços de Recuperação]

2.  Execute o arquivo de instalação em cada servidor host Hyper-V localizado nas nuvens VMM que você deseja proteger.
3.  Na página Verificação de Pré-requisitos, clique em **Avançar**. Quaisquer pré-requisitos faltantes serão instalados automaticamente.

![Agente de Serviços de Recuperação de Pré-requisitos][Agente de Serviços de Recuperação de Pré-requisitos]

1.  Na página Configurações de Instalação, especifique onde você deseja instalar o Agente e selecione a localização do cache no qual os metadados de backup serão instalados. Em seguida, clique em **Instalar**.

Na página Conexão de Internet, especifique como o Provedor em execução no servidor VMM se conecta à Internet. Clique em **Avançar** para usar as configurações de Internet padrão definidas no servidor![local do Agente de Serviços de Recuperação][local do Agente de Serviços de Recuperação]

## <span id="clouds"></span></a>Etapa 6: Definir as configurações da proteção de nuvem

Depois que os servidores VMM são registrados, você pode definir as configurações de proteção de nuvem. Você habilitou a opção **Sincronizar dados de nuvem com o cofre** quando instalou o Provedor, de modo que todas as nuvens no servidor VMM aparecerão na guia **Itens Protegidos** presente no cofre.

![Publicar uma nuvem][Publicar uma nuvem]

Defina as configurações de proteção conforme descrito a seguir:

1.  Na página Início Rápido, clique em **Configurar proteção para nuvens VMM**.

![Habilitar proteção de nuvem][Habilitar proteção de nuvem]

1.  Na guia **Itens Protegidos**, clique na nuvem que você deseja configurar e vá até a guia **Configuração**.
2.  Em **Destino**, selecione **Microsoft Azure**.
3.  Em **Conta de Armazenamento**, selecione o armazenamento do Azure que você deseja utilizar para armazenar máquinas virtuais do Azure.
4.  Defina **Criptografar dados armazenados** como **Desligado**. Essa configuração especifica que os dados podem ser criptografados e replicados entre o site local e o Azure.
5.  Em **Copiar frequência**, mantenha a configuração padrão. Esse valor especifica com que frequência os dados devem ser sincronizados entre os locais de origem e destino.
6.  Em **Manter pontos de recuperação para**, mantenha a configuração padrão. Com um valor padrão de zero apenas o ponto de recuperação mais recente para uma máquina virtual primária é armazenado em um servidor de host de réplica.
7.  Em **Frequência dos Instantâneos Consistentes de Aplicativo**, mantenha a configuração padrão. Esse valor especifica a frequência de criação de snapshots. Os snapshots usam o Volume Shadow Copy Service (VSS) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo é obtido. Se você definir um valor, certifique-se de que ele seja menor que o número dos pontos de recuperação adicionais que você configurar.

    ![Configuração da nuvem][Configuração da nuvem]

8.  Em **Hora de início para replicação**, especifique quando a replicação inicial dos dados para o Azure deve começar. O fuso horário do servidor de host Hyper-V será usado. Recomendamos que você agende a replicação inicial fora dos horários de pico.

    ![Configurações de replicação de nuvem][Configurações de replicação de nuvem]

Depois de salvar as configurações, um trabalho será criado e poderá ser monitorado pela guia **Trabalhos**. Todos os servidores de host Hyper-V na nuvem VMM de origem serão configurados para replicação. Após o salvamento, as configurações de nuvem podem ser modificadas a partir da guia **Configurar**. Para modificar o local de destino ou armazenamento de destino, você deve remover a configuração de nuvem e reconfigurar a nuvem. Observe que, se você mudar a conta de armazenamento, a mudança só será aplicada para máquinas virtuais habilitadas para proteção, após a conta de armazenamento ter sido modificada. Máquinas virtuais existentes não são migradas para a nova conta de armazenamento.

## <span id="networkmapping"></span></a>Etapa 7: Configurar o mapeamento de rede

Você pode, opcionalmente, habilitar o mapeamento de rede para mapear redes de máquinas virtuais de origem para redes virtuais Azure de destino. Se você não criar mapeamentos de rede, então, somente máquinas virtuais que sofram failover no mesmo plano de recuperação podem se conectar umas às outras no Azure. Se você criar um mapeamento de rede, todas as máquinas virtuais que sofrerem failover na mesma rede poderão conectar-se umas às outras, independentemente do plano de recuperação a que cada uma delas pertence. Além disso, se um gateway de rede for configurado na rede Azure de destino, as máquinas virtuais poderão conectar-se às máquinas virtuais presentes no local. Se você quiser configurar o mapeamento de rede como parte deste tutorial, consulte [Configurar o mapeamento de rede][Configurar o mapeamento de rede] no guia de implantação.

## <span id="virtualmachines"></span></a>Etapa 8: Habilitar a proteção para máquinas virtuais

Após os servidores, nuvens e redes serem configurados corretamente, você pode habilitar a proteção para máquinas virtuais na nuvem.

Antes de você habilitar a proteção para uma máquina virtual, verifique e atualize as configurações, se necessário. Por exemplo, o sistema operacional convidado na máquina virtual precisa ser Windows Server 2008 ou posterior, ou ainda Linux. A máquina virtual precisa ser de geração 1. Para obter uma lista completa de requisitos de Recuperação de Site do Azure, consulte [Pré-requisitos e suporte][Pré-requisitos e suporte] no guia Planejamento.

No console VMM, verifique e atualize as configurações. Você pode modificar as configurações do sistema operacional para a máquina virtual na página Geral das propriedades da máquina virtual. Você pode atualizar as configurações de disco do sistema operacional na página Configurações de Hardware.

![Modificar propriedades de máquina virtual][Modificar propriedades de máquina virtual]

![Modificar propriedades de máquina virtual][2]

1.  Para habilitar a proteção, na guia **Máquinas Virtuais**, na nuvem na qual a máquina virtual está localizada, clique em **Habilitar proteção** e, então, selecione **Adicionar máquinas virtuais**.
2.  por meio da lista de máquinas virtuais na nuvem, selecione aquela que você deseja proteger.

![Habilitar proteção de máquina virtual][Habilitar proteção de máquina virtual]

Após a proteção ser habilitada, dois trabalhos serão criados. O trabalho Ativar Proteção é executado. Então, após a replicação inicial ser concluída, o trabalho Finalizar Proteção é executado. A máquina virtual só fica pronta para failover após esses trabalhos serem concluídos com sucesso. Você pode monitorar o progresso na guia **Trabalhos**.

![Trabalho proteção de máquina virtual][Trabalho proteção de máquina virtual]

## <span id="recoveryplans"></span></a>Etapa 9: Configurar e executar um plano de recuperação

Um plano de recuperação reúne máquinas virtuais em grupos, para que possam sofrer failover como uma única unidade. Para criar um plano de recuperação, faça o seguinte:

1.  Na guia **Planos de Recuperação**, clique em **Criar**.
2.  Na página Especificar o Nome da Página de Recuperação e de Destino, selecione o servidor VMM como origem e o Azure como o destino.

    ![Criar Plano de Recuperação][Criar Plano de Recuperação]

3.  Na página Selecionar Máquinas Virtuais, selecione as máquinas virtuais para adicionar ao plano de recuperação. Somente as máquinas virtuais com proteção habilitada são exibidas. As máquinas virtuais são adicionadas ao plano de recuperação no grupo padrão (Grupo 1).
4.  Clique na marca de seleção para criar o plano de recuperação.

    ![VMs do Plano de Recuperação][VMs do Plano de Recuperação]

### <span id="run"></span></a>Testar um failover

Os planos de recuperação podem ser executados como parte de um teste proativo ou failover planejado ou durante um failover não planejado. Esta explicação passo a passo descreve como executar um teste de failover por meio do VMM para Azure para verificar se a sua estratégia de failover está funcionando conforme esperado. O failover de teste simula o mecanismo de failover e recuperação em uma rede isolada. Observe o seguinte:

-   Se você quiser se conectar à máquina virtual no Azure usando a Área de trabalho remota após o failover, habilite a Conexão de Área de Trabalho Remota na máquina virtual antes de executar o teste de failover.
-   Após o failover, você usará um endereço IP público para conectar-se à máquina virtual no Azure usando a Área de trabalho remota. Se você quiser fazer isso, certifique-se de não ter nenhuma política de domínio que impeça que você se conecte a uma máquina virtual usando um endereço público.

#### <span id="runtest"></span></a>Executar o failover

Execute um teste de failover para um plano de recuperação da seguinte maneira:

1.  Antes de executar o plano de recuperação, você pode validar as configurações das máquinas virtuais no plano. Para fazer isso, na página propriedades para a nuvem, clique na máquina virtual. Na página Propriedades de Origem e Destino para Failover, verifique as configurações. Em particular, verifique se o tamanho sugerido para a máquina virtual de destino no Azure está correto e se as configurações de rede estão definidas com precisão. Para obter uma lista completa de pré-requisitos de máquina virtual, consulte [Pré-requisitos e suporte][3].

![Propriedades de Máquina Virtual][Propriedades de Máquina Virtual]

1.  Na guia **Planos de Recuperação**, selecione o plano de recuperação requerido.
2.  Para iniciar o failover, clique no botão **Failover de Teste**.
3.  Na página Confirmar Failover de Teste, selecione a rede do Azure à qual suas máquinas virtuais serão conectadas após o failover. Opcionalmente, você pode selecionar **Sem Rede**. Com essa configuração selecionada, as máquinas virtuais não ficarão conectadas a uma rede após o failover.

![Failover de Teste][Failover de Teste]

Você pode acompanhar o andamento dos trabalhos de failover de teste na guia **Trabalhos**. Após o teste de failover estar concluído, faça o seguinte:

1.  Verificar se as máquinas virtuais foram iniciadas com êxito no Azure.
2.  Clique em **Observações** para gravar e salvar observações associadas ao failover de teste.
3.  Além dos detalhes na guia **Trabalhos**, quando você executa um failover de teste para um plano de recuperação, o processo é exibido na página Detalhes do plano de recuperação. Você pode ver as etapas de failover e seus status, além de exibir ou criar observações associadas ao failover de teste.

#### <span id="runtest"></span></a>Monitorar atividade

Você pode usar a guia **Trabalhos** e o **Painel** para exibir e monitorar os principais trabalhos realizados pelo cofre de Recuperação de Site do Azure, incluindo proteção de configuração para uma nuvem, habilitando e desabilitando a proteção para uma máquina virtual, executando um failover (planejado, não planejado ou de teste) e confirmando failover não planejado.

Na guia **Trabalhos**, você visualiza trabalhos, navega pelos detalhes e os erros do trabalho, executa consultas em trabalhos para recuperar trabalhos que correspondam a critérios específicos, exporta trabalhos para o Excel e reinicia trabalhos com falha.

No **Painel**, você pode baixar as versões mais recentes dos arquivos de instalação para Provedor e Agente, obter informações de configuração sobre o cofre, ver o número de máquinas virtuais que têm proteção gerenciada pelo cofre, ver trabalhos recentes, gerenciar o certificado do cofre e sincronizar novamente as máquinas virtuais.

Para obter mais informações sobre a interação com trabalhos e sobre o Painel, consulte o [Guia de Operações e Monitoramento][Guia de Operações e Monitoramento].

## <span id="next"></span></a>Próximas etapas

-   Para planejar e implantar a Recuperação de Site do Azure em um ambiente de produção completo, consulte [Guia de planejamento para Recuperação de Site do Azure][Plano para implantação da Recuperação de Site do Azure] e [Guia de Implantação para Recuperação de Site do Azure][Guia de Implantação para Recuperação de Site do Azure].
-   Caso tenha perguntas, visite o [Fórum de Serviços de Recuperação do Azure][Fórum de Serviços de Recuperação do Azure].

  [Introdução à Recuperação de Site do Azure: Proteção do local para o próprio local]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Plano para implantação da Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=321294
  [Implantar a Recuperação de Site do Azure: Proteção no local para o Azure]: http://go.microsoft.com/fwlink/?LinkId=402679
  [Administrar e monitorar a Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=378272
  [Recuperação de Site do Azure: Cenários comuns de erro e soluções]: http://go.microsoft.com/fwlink/?LinkId=389879
  [Fórum de Serviços de Recuperação do Azure]: http://go.microsoft.com/fwlink/?LinkId=313628
  [Avaliação gratuita do Azure]: http://aka.ms/try-azure
  [Detalhes dos Preços do Gerenciador de Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=378268
  [Introdução ao Armazenamento do Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=398704
  [Pré-requisitos e suporte]: http://go.microsoft.com/fwlink/?LinkId=402602
  [Etapa 1: Obter e configurar certificados]: #createcert
  [Etapa 2: Criar um cofre]: #vault
  [Etapa 3: Configure o cofre]: #upload
  [Etapa 4: Instalar o aplicativo Provedor]: #download
  [Etapa 5: Instalar o aplicativo do Agente]: #agent
  [Etapa 6: Configurar a proteção de nuvem]: #clouds
  [Etapa 7: Configurar o mapeamento de rede]: #NetworkMapping
  [Etapa 8: Habilitar a proteção para máquinas virtuais]: #virtualmachines
  [Etapa 9: Configurar e executar planos de recuperação]: #recovery%20plans
  [Obter um certificado autoassinado para a explicação passo a passo]: #obtaincert
  [Exportar o certificado em formato .pfx]: #exportcert
  [Importar o certificado .pfx para o servidor VMM]: #importcert
  [Windows SDK]: http://go.microsoft.com/fwlink/?LinkId=378269
  [Portal de Gerenciamento]: https://manage.windowsazure.com
  [Novo cofre]: ./media/hyper-v-recovery-manager-configure-vault/SR_HvVault.png
  [Ícone de Inicialização Rápida]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStartIcon.png
  [Início rápido]: ./media/hyper-v-recovery-manager-configure-vault/SR_QuickStart.png
  [Gerenciar Certificado]: ./media/hyper-v-recovery-manager-configure-vault/SR_ManageCert.png
  [1]: ./media/hyper-v-recovery-manager-configure-vault/SR_VaultKey.png
  [Arquivo Baixar Provedor]: ./media/hyper-v-recovery-manager-configure-vault/SR_DownloadProviderFile.png
  [Baixar Agente]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderInstall1.png
  [Configuração Concluída]: ./media/hyper-v-recovery-manager-configure-vault/SR_InstallWiz.png
  [Configurações de Internet]: ./media/hyper-v-recovery-manager-configure-vault/SR_ProviderProxy.png
  [Registro de certificado]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg1.png
  [Cobre de certificado]: ./media/hyper-v-recovery-manager-configure-vault/SR_CertReg2.png
  [PublishCloud]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMServerName.png
  [Instalar o Agente de Serviços de Recuperação]: ./media/hyper-v-recovery-manager-configure-vault/SR_HyperVAgent.png
  [Agente de Serviços de Recuperação de Pré-requisitos]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentPrereqs.png
  [local do Agente de Serviços de Recuperação]: ./media/hyper-v-recovery-manager-configure-vault/SR_AgentInstallSettings.png
  [Publicar uma nuvem]: ./media/hyper-v-recovery-manager-configure-vault/SR_Clouds.png
  [Habilitar proteção de nuvem]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableCloudProtection.png
  [Configuração da nuvem]: ./media/hyper-v-recovery-manager-configure-vault/SR_CloudConfigureE2A1.png
  [Configurações de replicação de nuvem]: ./media/hyper-v-recovery-manager-configure-vault/SR_PublishCloudSetup2.png
  [Configurar o mapeamento de rede]: http://go.microsoft.com/fwlink/?LinkId=402533
  [Modificar propriedades de máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsGeneral.png
  [2]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMMVMPropsHW.png
  [Habilitar proteção de máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_EnableProtectionVM.png
  [Trabalho proteção de máquina virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMJobs.png
  [Criar Plano de Recuperação]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan1.png
  [VMs do Plano de Recuperação]: ./media/hyper-v-recovery-manager-configure-vault/SR_RecoveryPlan2.png
  [3]: http://go.microsoft.com/fwlink/?LinkId=402676
  [Propriedades de Máquina Virtual]: ./media/hyper-v-recovery-manager-configure-vault/SR_VMPropertiesE2A.png
  [Failover de Teste]: ./media/hyper-v-recovery-manager-configure-vault/SR_TestFailover.png
  [Guia de Operações e Monitoramento]: http://go.microsoft.com/fwlink/?LinkId=398534
  [Guia de Implantação para Recuperação de Site do Azure]: http://go.microsoft.com/fwlink/?LinkId=321295
