<properties
	pageTitle="Replicar máquinas virtuais VMware locais ou servidores físicos em um site secundário | Microsoft Azure"
	description="Use este artigo para replicar máquinas virtuais VMware ou servidores físicos Windows/Linux para um site secundário com o Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="raynew"/>


# Replicar máquinas virtuais VMware locais ou servidores físicos em um site secundário


## Visão geral

O InMage Scout no Azure Site Recovery fornece replicação em tempo real entre os sites do VMWare no local. O InMage Scout está incluído nas assinaturas para o serviço Azure Site Recovery.


## Pré-requisitos

- **Conta do Azure**: você precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.


## Etapa 1: criar um cofre

1. Entre no [Portal de Gerenciamento](https://portal.azure.com).
2. Clique em **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Recuperação de Site**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, digite um nome amigável para identificar o cofre.
5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, consulte a Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página de Serviços de Recuperação.

## Etapa 2: Configurar o cofre e baixar componentes InMage Scout

1. Clique em **Criar cofre**.
2. Na página **Serviços de Recuperação**, clique no cofre para abrir na página de Início Rápido.
3. Na lista suspensa, selecione **Entre dois sites do VMWare no local**.
4. Baixe InMage Scout. Os arquivos de instalação para todos os componentes necessários estão no arquivo zip baixado.


## Etapa 3: Instalar atualizações de componentes

Leia sobre as últimas[atualizações](#updates). Você instalará os arquivos de atualização na seguinte ordem:

1. Servidor RX se houver um
2. Servidores de configuração
3. Servidores de processo
3. Servidores de destino mestre.
4. Servidores vContinuum.

Instale da seguinte maneira:

1. Baixe o arquivo zip [atualização](http://aka.ms/scoutupdates). Este arquivo zip contém os seguintes arquivos:

	-  RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz
	-  CX\_Windows\_8.0.2.0\_GA\_Update\_2\_4306954\_21Aug15.exe
	-  UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe
	-  UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz
	-  vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe
2. Extraia o arquivo zip.
2. **Servidor RX**: copie **RX\_8.0.1.0\_GA\_Update\_1\_3279231\_23Jun15.tar.gz**para o servidor RX e extraia. Na pasta extraída, execute **/Install**.
2. **Servidor de configuração/servidor de processo**: copie **CX\_Windows\_8.0.2.0\_GA\_Update\_2\_4306954\_21Aug15.exe** para o servidor de configuração e servidor de processo. Clique duas vezes para executá-lo.
3. **Servidor de destino mestre Windows**: para atualizar a cópia de agente unificado, copie **UA\_Windows\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.exe** para o servidor mestre de destino. Clique duas vezes nele para executá-lo. Observe que o agente unificado para Windows não é aplicável no servidor de origem. Ele deve ser instalado somente no servidor de destino mestre do Windows.
4. **Servidor de destino mestre Linux**: para atualizar o agente unificado, copie **UA\_RHEL6-64\_8.0.1.0\_GA\_Update\_1\_3259401\_23Jun15.tar.gz** para o servidor de destino mestre e extraia-o. Na pasta extraída, execute **/Install**.
5. **vContinuum server**: copie **vCon\_Windows\_8.0.1.0\_GA\_Update\_1\_3259523\_23Jun15.exe** para o servidor vContinuum. Verifique se que você fechou o assistente vContinuum. Clique duas vezes no arquivo para executá-lo.

## Etapa 4: Configurar a replicação
5. Configure a replicação entre os sites do VMware de origem e de destino.
6. Para obter orientação, use a documentação do InMage Scout baixada com o produto. Como alternativa, você pode acessar a documentação da seguinte maneira:

	- [Notas de versão](http://download.microsoft.com/download/4/5/0/45008861-4994-4708-BFCD-867736D5621A/InMage_Scout_Standard_Release_Notes.pdf)
	- [Matriz de compatibilidade](http://download.microsoft.com/download/C/D/A/CDA1221B-74E4-4CCF-8F77-F785E71423C0/InMage_Scout_Standard_Compatibility_Matrix.pdf)
	- [Guia do usuário](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf)
	- [Guia do usuário do RX](http://download.microsoft.com/download/A/7/7/A77504C5-D49F-4799-BBC4-4E92158AFBA4/InMage_ScoutCloud_RX_User_Guide_8.0.1.pdf)
	- [Guia de instalação rápida](http://download.microsoft.com/download/6/8/5/685E761C-8493-42EB-854F-FE24B5A6D74B/InMage_Scout_Standard_Quick_Install_Guide.pdf)


## Atualizações

### ASR Scout 8.0.1 Atualização de 03Dec15

As correções na atualização de 3/dez/15 incluem:

- **O servidor de configuração** — corrige um problema que impediu que o recurso de medição gratuito por 31 dias funcionasse conforme o esperado quando o servidor de configuração foi registrado na Recuperação de Site.
- **Agente unificado** — corrige um problema na Atualização 1 para o Destino Mestre, que faz com que a atualização não seja instalada no servidor de destino mestre quando ele é atualizado da versão 8.0 para a 8.0.1.

>[AZURE.NOTE]
>
>-	Todas as atualizações de ASR são cumulativas.
>-	As atualizações CS e RX não podem ser revertidas quando são aplicadas ao sistema.


### ASR Scout 8.0.1 Update 1

Essa última atualização inclui correções de bugs e novos recursos:

- 31 dias de proteção gratuita por instâncias de servidor. Isso lhe permite testar a funcionalidade ou configurar uma verificação de conceito.
	- Todas as operações no servidor, incluindo failover e failback, são gratuitas pelos primeiros 31 dias a partir da hora em que um servidor é protegido pela primeira vez usando o ASR Scout.
	- A partir do dia 32, todos os servidores protegidos pagarão a taxa padrão de instância pela proteção do Azure Site Recovery de um site de propriedade do cliente.
	- A qualquer momento, todos os servidores protegidos que atualmente estão sendo cobrados estão disponíveis na página Painel do cofre do Azure Site Recovery.
- Suporte adicionado para vCLI 5.5 Update 2.
- Suporte adicionado para sistemas operacionais Linux no servidor de origem:
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- Correções de bugs para resolver estes problemas:
	- Falha de registro do cofre para o servidor de configuração ou RX.
	- Os volumes de cluster não aparecem conforme o esperado quando máquinas virtuais clusterizadas são protegidas durante a retomada.
	- O failback falha quando o servidor de destino mestre é hospedado em outro servidor ESXi das máquinas virtuais locais de produção.
	- As permissões de arquivo de configuração são alteradas durante a atualização para 8.0.1, afetando a proteção e as operações.
	- O limite de ressincronização não é imposto conforme o esperado, resultando em um comportamento de replicação inconsistente.
	- As configurações de RPO não aparecem corretamente na interface de configuração do servidor. O valor de dados não compactados mostra incorretamente o valor compactado.
	-  A operação Remover não exclui conforme o esperado no assistente vContinuum e replicação não é excluída da interface do servidor de configuração.
	-  No assistente vContinuum, o disco é desmarcado automaticamente ao clicar em **Detalhes** na exibição do disco durante a proteção de máquinas virtuais MSCS.
	- Durante o cenário de P2V, alguns serviços necessário da HP, como CIMnotify e CqMgHost não são movidos para Manual na máquina virtual de recuperação, resultando em mais tempo de inicialização.
	- A máquina virtual Linux protegida falha quando há mais de 26 discos no servidor de destino mestre.

## Próximas etapas

Publique qualquer pergunta no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

<!---HONumber=AcomDC_0218_2016-->