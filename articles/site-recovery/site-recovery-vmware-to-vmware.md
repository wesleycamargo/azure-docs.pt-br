<properties
	pageTitle="Replicar máquinas virtuais VMware locais ou servidores físicos em um site secundário | Microsoft Azure"
	description="Use este artigo para replicar máquinas virtuais VMware ou servidores físicos Windows/Linux para um site secundário com o Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="nsoneji"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2016"
	ms.author="nisoneji"/>


# Replicar máquinas virtuais VMware locais ou servidores físicos em um site secundário


## Visão geral

O InMage Scout no Azure Site Recovery fornece replicação em tempo real entre os sites do VMWare no local. O InMage Scout está incluído nas assinaturas para o serviço Azure Site Recovery.


## Pré-requisitos

**Conta do Azure**: você precisará de uma conta do [Microsoft Azure](https://azure.microsoft.com/). Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.


## Etapa 1: criar um cofre

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Serviços de Dados** > **Serviços de Recuperação** > **Cofre de Recuperação de Site**.
3. Clique em **Criar Novo** > **Criação Rápida**.
4. Em **Nome**, digite um nome amigável para identificar o cofre.
5. Em **Região**, selecione a região geográfica para o cofre. Para verificar as regiões com suporte, confira [Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).

Verifique a barra de status para confirmar que o cofre foi criado com sucesso. O cofre será listado como **Ativo** na página principal de **Serviços de Recuperação**.

## Etapa 2: Configurar o cofre e baixar componentes InMage Scout

1. Clique em **Criar cofre**.
2. Na página **Serviços de Recuperação**, clique no cofre para abrir a página de **Início Rápido**.
3. Na lista suspensa, selecione **Entre dois sites do VMWare no local**.
4. Baixe InMage Scout. Os arquivos de instalação para todos os componentes necessários estão no arquivo zip baixado.


## Etapa 3: Instalar atualizações de componentes

Leia sobre as últimas[atualizações](#updates). Você instalará os arquivos de atualização nos servidores na seguinte ordem:

1. Servidor RX se houver um
2. Servidores de configuração
3. Servidores de processo
3. Servidores de destino mestre
4. Servidores vContinuum
5. Servidor de origem (somente para Windows Server)

Instale as atualizações da seguinte maneira:

1. Baixe o arquivo zip [atualização](https://aka.ms/asr-scout-update3). Este arquivo zip contém os seguintes arquivos:

	-  RX_8.0.3.0_GA\_Update_3_6684045\_17Mar16.tar.gz
	-  CX\_Windows_8.0.3.0_GA\_Update_3_5048668\_16Mar16.exe
	-  UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe
	-  UA\_RHEL6-64_8.0.3.0_GA\_Update_3_7101745\_04Apr16.zip
	-  vCon\_Windows_8.0.3.0_GA\_Update_3_6873369\_16Mar16.exe

2. Extraia os arquivos .zip.
3. **Para o servidor RX**: copie **RX_8.0.3.0_GA\_Update_3_6684045\_17Mar16.tar.gz** para o servidor RX e extraia-o. Na pasta extraída, execute **/Install**.
4. **Para o servidor de configuração/servidor de processo**: copie **CX\_Windows_8.0.3.0_GA\_Update_3_5048668\_16Mar16.exe** para o servidor de configuração e o servidor de processo. Clique duas vezes para executá-lo.
5. **Para o servidor de destino mestre do Windows**: para atualizar o agente unificado, copie **UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe** para o servidor de destino mestre. Clique duas vezes nele para executá-lo. Observe que o agente unificado também se aplica ao servidor de origem. Você deve instalá-lo no servidor de origem também, conforme mencionado nesta lista.
6. **Para o servidor de destino mestre para Linux**: para atualizar o agente unificado, copie **UA\_RHEL6-64_8.0.3.0_GA\_Update_3_7101745\_04Apr16.zip** para mestre de servidor de destino e extraia-o. Na pasta extraída, execute **/Install**.
7. **Para o servidor vContinuum**: copie **vCon\_Windows_8.0.3.0_GA\_Update_3_6873369\_16Mar16.exe** para o servidor vContinuum. Verifique se que você fechou o assistente vContinuum. Clique duas vezes no arquivo para executá-lo.
8. **Para o servidor de origem para o Windows**: para atualizar o agente unificado, copie **UA\_Windows_8.0.3.0_GA\_Update_3_7101745\_04Apr16.exe** para o servidor de origem. Clique duas vezes nele para executá-lo.

## Etapa 4: Configurar a replicação
1. Configure a replicação entre os sites do VMware de origem e de destino.
2. Para obter orientação, use a documentação do InMage Scout baixada com o produto. Como alternativa, você pode acessar a documentação da seguinte maneira:

	- [Notas de versão](https://aka.ms/asr-scout-release-notes)
	- [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
	- [Guia do usuário](https://aka.ms/asr-scout-user-guide)
	- [Guia do usuário do RX](https://aka.ms/asr-scout-rx-user-guide)
	- [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)


## Atualizações

### Atualização 3 do Azure Site Recovery Scout 8.0.1
A Atualização 3 inclui as seguintes correções de bugs e aprimoramentos:

- O servidor de configuração e o RX não serão registrados no cofre de Recuperação de Site quando estiverem por trás do proxy.
- O número de horas que o RPO (objetivo de ponto de recuperação) não é atendido não é atualizado no relatório de integridade.
- O servidor de configuração não é sincronizado com RX quando os detalhes da rede ou detalhes de hardware ESX contêm caracteres UTF-8.
- Controladores de domínio do Windows Server 2008 R2 não são inicializados após a recuperação.
- A sincronização offline não está funcionando conforme o esperado.
- Após o failover da VM (máquina virtual), a exclusão de par de replicação fica bloqueada na IU de CX por um longo tempo, e os usuários podem concluir o failback nem retomar a operação.
- Operações de instantâneo gerais que são feitas pelo trabalho de consistência foram otimizadas para ajudar a reduzir desconexões de aplicativo, como clientes SQL.
- O desempenho da ferramenta de consistência (VACP.exe) foi aprimorado com a redução do uso de memória necessário para a criação de instantâneos no Windows.
- O serviço de instalação por push falha quando a senha tem mais de 16 caracteres.
- vContinuum não verifica e solicita novas credenciais vCenter quando as credenciais são alteradas.
- No Linux, o gerenciador de cache de destino mestre (cachemgr) não está baixando arquivos do servidor de processo, o que resulta na limitação do par de replicação.
- Quando a ordem de disco de MSCS (cluster de failover físico) não é a mesma em todos os nós, a replicação não é definida para alguns dos volumes de cluster. <br/>Observe que o cluster deve ser protegido para tirar proveito dessa correção.
- A Funcionalidade SMTP não está funcionando conforme o esperado depois que o RX é atualizado de Scout 7.1 para Scout 8.0.1.
- Foram adicionadas mais estatísticas no log para que a operação de reversão acompanhe o tempo gasto para concluí-la.
- Foi adicionado suporte para sistemas operacionais Linux no servidor de origem:
	- Red Hat Enterprise Linux (RHEL) 6 atualização 7
	- CentOS 6 Atualização 7
- As interfaces do usuário de RX e CX agora podem mostrar a notificação para o par que entra no modo bitmap.
- As seguintes correções de segurança foram adicionadas no RX:

**Descrição do problema**|**Procedimentos de implementação**
---|---
Autorização ignorada por meio de violação de parâmetros|Acesso restringido a usuários não aplicáveis.
Solicitação entre sites forjada|Conceito token-página implementado e sendo gerado aleatoriamente para cada página. <br/>Com isso, você verá: <li> Há apenas uma instância de logon único para o mesmo usuário.</li><li>A atualização de página não funciona; ela será redirecionada para o painel.</li>
Carregamento de arquivo mal-intencionado|Arquivos restritos a certas extensões. Extensões permitidas: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e zip.
Script persistente entre sites | Validações de entrada adicionadas.


>[AZURE.NOTE]
>
>-	Todas as atualizações de Recuperação de Site são cumulativas. A atualização 3 tem todas as correções da Atualização 1 e da Atualização 2. A Atualização 3 pode ser aplicada diretamente a 8.0.1 GA.
>-	As atualizações de servidor de configuração e RX não podem ser revertidas depois de serem aplicadas no sistema.

### Azure Site Recovery Scout 8.0.1 Atualização 2 (atualização de 3 de dezembro de 2015)

As correções na Atualização 2 incluem:

- **Servidor de configuração**: corrige um problema que impediu que o recurso de medição gratuito por 31 dias funcionasse conforme o esperado quando o servidor de configuração foi registrado na Recuperação de Site.
- **Agente unificado**: corrige um problema na atualização 1 que fazia com que a atualização que fosse instalada no servidor de destino mestre quando ele era atualizado da versão 8.0 para 8.0.1.


### Azure Site Recovery Scout 8.0.1 Atualização 1

A atualização 1 inclui as seguintes correções de bugs e novos recursos:

- 31 dias de proteção gratuita por instância de servidor. Isso lhe permite testar a funcionalidade ou configurar uma verificação de conceito.
	- Todas as operações no servidor, incluindo failover e failback, são gratuitas pelos primeiros 31 dias a partir da hora em que um servidor é protegido pela primeira vez com o Scout de Recuperação de Site.
	- A partir do dia 32, todos os servidores protegidos pagarão a taxa padrão de instância pela proteção do Azure Site Recovery de um site de propriedade do cliente.
	- A qualquer momento, todos os servidores protegidos que atualmente estão sendo cobrados estão disponíveis na página Painel do cofre do Azure Site Recovery.
- Foi adicionado suporte para vCLI (Interface de Linha de Comando de vSphere) em 5.5 Atualização 2.
- Suporte adicionado para sistemas operacionais Linux no servidor de origem:
	- RHEL 6 Update 6
	- RHEL 5 Update 11
	- CentOS 6 Update 6
	- CentOS 5 Update 11
- Correções de bugs para resolver estes problemas:
	- Falha de registro do cofre para o servidor de configuração ou RX.
	- Os volumes de cluster não aparecem conforme o esperado quando máquinas virtuais clusterizadas são protegidas ao serem retomadas.
	- O failback falha quando o servidor de destino mestre é hospedado em outro servidor ESXi das máquinas virtuais locais de produção.
	- Permissões de arquivo de configuração são alteradas quando você atualiza para 8.0.1, o que afeta a proteção e as operações.
	- O limite de ressincronização não é imposto conforme o esperado, o que resulta em um comportamento de replicação inconsistente.
	- As configurações de RPO não aparecem corretamente na interface de configuração do servidor. O valor de dados não compactados mostra incorretamente o valor compactado.
	-  A operação Remover não exclui conforme o esperado no assistente vContinuum e replicação não é excluída da interface do servidor de configuração.
	-  No assistente vContinuum, o disco é desmarcado automaticamente quando você clica em **Detalhes** na exibição do disco durante a proteção de máquinas virtuais MSCS.
	- Durante o cenário de P2V (físico para virtual), serviços HP necessários, como CIMnotify e CqMgHost, não são movidos para manual na recuperação de máquina virtual. Isso resulta em tempo de inicialização adicional.
	- A máquina virtual Linux protegida falha quando há mais de 26 discos no servidor de destino mestre.

## Próximas etapas

Publique qualquer pergunta que você desejar no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

<!---HONumber=AcomDC_0921_2016-->