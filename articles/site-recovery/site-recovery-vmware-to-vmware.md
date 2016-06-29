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
	ms.date="06/14/2016"
	ms.author="nisoneji"/>


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
5. Servidor de origem (somente para Windows Server)

Instale da seguinte maneira:

1. Baixe o arquivo zip [atualização](https://aka.ms/asr-scout-update3). Este arquivo zip contém os seguintes arquivos:

	-  RX\_8.0.3.0\_GA\_Update\_3\_6684045\_17Mar16.tar.gz
	-  CX\_Windows\_8.0.3.0\_GA\_Update\_3\_5048668\_16Mar16.exe
	-  UA\_Windows\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.exe
	-  UA\_RHEL6-64\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.zip
	-  vCon\_Windows\_8.0.3.0\_GA\_Update\_3\_6873369\_16Mar16.exe

2. Extraia o arquivo zip.
3. **Servidor RX**: copie **RX\_8.0.3.0\_GA\_Update\_3\_6684045\_17Mar16.tar.gz** para o servidor RX e extraia. Na pasta extraída, execute **/Install**.
4. **Servidor de configuração/servidor de processo**: copie **CX\_Windows\_8.0.3.0\_GA\_Update\_3\_5048668\_16Mar16.exe** para o servidor de configuração e servidor de processo. Clique duas vezes para executá-lo.
5. **Servidor de destino mestre Windows**: para atualizar a cópia de agente unificado, copie **UA\_Windows\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.exe** para o servidor mestre de destino. Clique duas vezes nele para executá-lo. Observe que o agente unificado também se aplica ao servidor de origem. Ele deve ser instalado no servidor de origem e onde for mencionado a seguir.
6. **Servidor de destino mestre Linux**: para atualizar o agente unificado, copie **UA\_RHEL6-64\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.zip** para o servidor de destino mestre e extraia-o. Na pasta extraída, execute **/Install**.
7. **vContinuum server**: copie **vCon\_Windows\_8.0.3.0\_GA\_Update\_3\_6873369\_16Mar16.exe** para o servidor vContinuum. Verifique se que você fechou o assistente vContinuum. Clique duas vezes no arquivo para executá-lo.
8. **Servidor de origem Windows**: para atualizar a cópia de agente unificado **UA\_Windows\_8.0.3.0\_GA\_Update\_3\_7101745\_04Apr16.exe** para o servidor de origem. Clique duas vezes nele para executá-lo. 

## Etapa 4: Configurar a replicação
1. Configure a replicação entre os sites do VMware de origem e de destino.
2. Para obter orientação, use a documentação do InMage Scout baixada com o produto. Como alternativa, você pode acessar a documentação da seguinte maneira:

	- [Notas de versão](https://aka.ms/asr-scout-release-notes)
	- [Matriz de compatibilidade](https://aka.ms/asr-scout-cm)
	- [Guia do usuário](https://aka.ms/asr-scout-user-guide)
	- [Guia do usuário do RX](https://aka.ms/asr-scout-rx-user-guide)
	- [Guia de instalação rápida](https://aka.ms/asr-scout-quick-install-guide)


## Atualizações

### ASR Scout 8.0.1 Atualização 3
A Atualização 3 inclui as seguintes correções de bugs e aprimoramentos:

1. O servidor de configuração e o RX não conseguiram registrar no cofre ASR quando estavam atrás do proxy.
2. O número de horas de RPO não alcançado não é atualizado no relatório de integridade.
3. O servidor de configuração não é sincronizado com o RX quando os detalhes de hardware ESX ou de rede contêm caracteres UTF-8.
4. Os computadores de controlador de domínio do Windows 2008 Server R2 não inicializam após a recuperação.
5. A sincronização offline não está funcionando conforme o esperado. 
6. Após o failover da VM, a exclusão de par de replicação fica presa na UI CX por um longo tempo e o usuário não pode fazer a operação de retomada de failback.
7. Operações de instantâneo geral otimizadas feitas por trabalho de consistência para ajudar a reduzir a desconexão de aplicativos, como clientes SQL.
8. Melhor desempenho do VACP, reduzindo o uso de memória necessário para criar instantâneos no Windows.
9. Serviço de instalação por push falha quando a senha tem mais de 16 caracteres
10. vContinuum não está verificando e solicitando novas credenciais vCenter quando as credenciais são alteradas.
11. No cache de destino mestre do Linux (cachemgr), o gerenciador não está baixando arquivos do servidor de processo, resultando em limitação de pares de replicação.
12. Quando a ordem dos discos de cluster do MSCS físico não é igual em todos os nós, a replicação não é definida para alguns dos volumes de cluster. <br/>Observação: para se beneficiar dessa correção, o cluster precisará ser protegido novamente.  
13. A Funcionalidade SMTP não está funcionando conforme o esperado depois que o RX é atualizado de Scout 7.1 para Scout 8.0.1.
14. Adicionadas mais estatísticas no log para que a operação de reversão possa controlar o tempo gasto para concluí-la.
15. Suporte adicionado para sistemas operacionais Linux no servidor de origem 
	- RHEL 6 Atualização 7
	- CentOS 6 Atualização 7 
16. As interfaces do usuário de RX e CX agora podem mostrar a notificação para o par que entra no modo bitmap.
17. As correções de segurança a seguir foram adicionadas em RX.

**#**|**Descrição do problema**|**Procedimentos de implementação**
---|---|---
1\. |Autorização ignorada por meio de violação de parâmetros|Acesso restringido a usuários não aplicáveis
2\. |Solicitação entre sites forjada|Conceito token-página implementado e gerando aleatoriamente para cada página. <br/>Com isso, você verá <br/>1) somente instância de logon único para o mesmo usuário.,br/>2) a atualização da página não funcionará e redirecionará para o painel. <br/>
3\. |Carregamento de arquivo mal-intencionado|Arquivos restritos a certas extensões. São permitidos: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml, zip
4\. | Script persistente entre sites | Validações de entrada adicionadas


>[AZURE.NOTE]
>
>-	Todas as atualizações de ASR são cumulativas. A Atualização 3 possui todas as correções das Atualizações 1 e 2. A Atualização 3 pode ser aplicada diretamente a 8.0.1 GA.
>-	As atualizações CS e RX não podem ser revertidas quando são aplicadas ao sistema.

### ASR Scout 8.0.1 Atualização de 3/12/15 (Atualização 2)

As correções na Atualização 2 incluem:

- **O servidor de configuração** — corrige um problema que impediu que o recurso de medição gratuito por 31 dias funcionasse conforme o esperado quando o servidor de configuração foi registrado na Recuperação de Site.
- **Agente unificado** — corrige um problema na Atualização 1 para o Destino Mestre, que faz com que a atualização não seja instalada no servidor de destino mestre quando ele é atualizado da versão 8.0 para a 8.0.1.


### ASR Scout 8.0.1 Update 1

A Atualização 1 inclui correções de bugs e novos recursos:

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

<!---HONumber=AcomDC_0615_2016-->