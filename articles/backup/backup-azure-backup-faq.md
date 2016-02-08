<properties
   pageTitle="Perguntas frequentes do Backup do Azure | Microsoft Azure"
   description="Perguntas frequentes sobre a solução de Backup do Azure – agente de backup, backup e retenção, recuperação, segurança e outras perguntas comuns sobre o serviço do Backup do Azure."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="shreeshd"
   editor=""
   keywords="solução de backup; serviço de backup"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="01/26/2016"
	 ms.author="trinadhk; giridham; arunak; markgal; jimpark;"/>

# Serviço de Backup do Azure - Perguntas frequentes
Veja a seguir uma lista de perguntas frequentes sobre o Backup do Azure. Se você tiver outras perguntas sobre a solução do Backup do Azure, vá para o [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) e poste suas perguntas. Alguém da sua comunidade o ajudará a obter respostas. Se uma pergunta for frequente, ela será adicionada a este artigo para que possa ser encontrada com rapidez e facilidade.

## Instalação e configuração
**P1. Qual é a lista de sistemas operacionais com suporte por meio dos quais posso fazer backup no Azure usando o Backup do Azure?** <br/> R1. Os sistemas operacionais da lista a seguir têm suporte no Backup do Azure


| Sistema operacional | Plataforma | SKU |
| :------------- |-------------| :-----|
| Windows 8 e SPs mais recentes | 64 bits | Enterprise, Pro |
| Windows 7 e SPs mais recentes | 64 bits | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e SPs mais recentes | 64 bits | Enterprise, Pro |
| Windows 10 | 64 bits | Enterprise, Pro, Home |
|Windows Server 2012 R2 e SPs mais recentes|	64 bits|	Standard, Datacenter, Foundation|
|Windows Server 2012 e SPs mais recentes|	64 bits|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 e SPs mais recentes |64 bits|	Standard, Workgroup|
|Windows Storage Server 2012 e SPs mais recentes |64 bits |Standard, Workgroup
|Windows Server 2012 R2 e SPs mais recentes |64 bits|	Essential|
|Windows Server 2008 R2 SP1 |64 bits|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64 bits|	Standard, Enterprise, Datacenter, Foundation|

**P2. Onde posso baixar o agente mais recente do Backup do Azure?** <br/> R2. Você pode baixar o agente mais recente [aqui](http://aka.ms/azurebackup_agent). Ele pode ser instalado no Windows Server, no servidor SCDPM ou no cliente Windows.

**P3. Qual versão do servidor SCDPM tem suporte?** <br/> R3. Recomendamos que você instale o agente de Backup do Azure [mais recente](http://aka.ms/azurebackup_agent) no pacote cumulativo de atualizações mais recente do SCDPM (UR6 de julho de 2015)

**P4. Ao configurar o agente do Backup do Azure, sou solicitado a inserir as “credenciais do cofre”. Há uma data de expiração associada às credenciais do cofre?** <br/> R4. Sim, a credencial do cofre expira após 48 horas. Se o arquivo expirar, faça logon no Portal do Azure e baixe os arquivos de credenciais de cofre no seu cofre de backup.

**P5. Há algum limite para o número de cofres de backup que podem ser criados em cada assinatura do Azure?** <br/> R5. Sim. Desde julho de 2015, você pode criar 25 cofres por assinatura. Se você precisar de mais cofres, crie uma nova assinatura.

**P6. Devo considerar o cofre como uma entidade de cobrança?** <br/> R6. Embora seja possível obter uma fatura detalhada de cada cofre, é altamente recomendável que você considere uma assinatura do Azure como uma entidade de cobrança. Ela é consistente entre todos os serviços e é mais fácil de gerenciar.

**P7. Há um limite para o número de servidores/computadores que podem ser registrados em cada cofre?** <br/> R7. Sim, você pode registrar até 50 computadores por cofre. Para máquinas virtuais IaaS do Azure, o limite é 200 VMs por cofre. Se você precisar registrar mais computadores, crie um novo cofre.

**P8. Há um limite em relação à quantidade de dados que pode ser incluída no backup de um servidor/cliente Windows ou em um servidor SCDPM?** <br/> R8. Nº

**P9. Como registro meu servidor em outro datacenter?**<br/> R9. Em geral, os dados de backup são enviados para o datacenter do serviço de Backup no qual estão registrados. A maneira mais fácil de alterar o datacenter é desinstalar e reinstalar o agente e registrar-se em um novo datacenter.

**P10. O que acontece se eu renomear um servidor Windows que está fazendo backup de dados no Azure?** <br/> R10. Os backups configurados atualmente serão interrompidos. Você precisará registrar novamente o servidor no cofre de backup e ele será considerado um novo servidor pelos Serviços de Recuperação, portanto, a primeira operação de backup que ocorrer após o registro será um backup completo de todos os dados incluídos no backup e não apenas as alterações desde o último backup. No entanto, se você precisar executar uma operação de recuperação, poderá recuperar os dados que passaram por backup usando Recuperar em outra opção de recuperação de servidor.

**P11. Em que tipos de unidades posso fazer backup de arquivos e pastas?** <br/> R11. Não é possível fazer backup do seguinte conjunto de unidades/volumes:

- Mídia removível: a unidade deve ser indicada como fixa para ser usada como uma origem de itens de backup.
- Volumes somente leitura: o volume deve ser gravável para que o VSS (Serviço de Cópias de Sombra de Volume) funcione.
- Volumes offline: o volume deve estar online para que o VSS funcione.
- Compartilhamento de rede: O volume deve ser local para o backup do servidor usando o backup online.
- Volumes protegidos pelo BitLocker: o volume deve ser desbloqueado antes de ser possível realizar o backup.
- Identificação do sistema de arquivos: NTFS é o único sistema de arquivos com suporte para esta versão do serviço de backup online.

**P12. De quais tipos de arquivo e pasta no servidor posso fazer backup?**<br/> R12. Os seguintes tipos têm suporte:

- Criptografado
- Compactado
- Esparso
- Compactado + esparso
- Links físicos: sem suporte, ignorado
- Ponto de nova análise: sem suporte, ignorado
- Criptografado + compactado: sem suporte, ignorado
- Criptografado + esparso: sem suporte, ignorado
- Fluxo compactado: sem suporte, ignorado
- Fluxo esparso: sem suporte, ignorado

**P13. Qual é o requisito de tamanho mínimo para a pasta de cache?** <br/> R13. O tamanho da pasta de cache é determinado pela quantidade de dados submetida a backup. Em geral, deve esperar que 5% do espaço necessário para o armazenamento de dados deve ser alocado para a pasta de cache.

**P14. Como faço para isolar os dados de servidor específicos para que eles não sejam recuperados por outros servidores em minha organização?**<br/> R14. Qualquer servidor registrado no mesmo cofre será capaz de recuperar os dados do backup feito por outros servidores que usam a mesma senha. Se você tiver servidores que deseja garantir que a recuperação ocorra apenas em servidores específicos na sua organização, deverá usar uma senha separada designada para esses servidores. Por exemplo, os servidores de recursos humanos podem usar uma senha de criptografia, os servidores de contabilidade podem usar outra senha e os outros servidores de armazenamento podem usar uma terceira senha.

**P15. Posso "migrar" meus dados de backup entre assinaturas?** <br/> R15: Não

**P16. Posso "migrar" meu cofre de backup entre assinaturas?** <br/> R16: Não. O cofre é criado no nível da assinatura e não pode ser reatribuído a outra assinatura depois de criado.

**P17. O Agente de Backup do Azure funciona em um servidor que usa a eliminação de duplicação do Windows Server 2012?** <br/> R17: Sim. O serviço do agente converte os dados com eliminação de duplicação para dados normais quando prepara a operação de backup. Ele então otimiza os dados para backup, criptografa os dados e envia os dados criptografados para o serviço de backup online.

**P18. Os dados de backup serão excluídos se eu cancelar um backup depois de ter começado?** <br/> R18: Não. O cofre de backup armazena o backup dos dados que foram transferidos até o ponto do cancelamento. O Backup do Azure usa um mecanismo de ponto de verificação para que os dados de backup sejam verificados ocasionalmente durante o backup e o próximo processo de backup possa validar a integridade dos arquivos. O próximo backup acionado seria incremental em relação os dados cujo backup foi realizado anteriormente. Isso fornece uma melhor utilização da largura de banda, para que você não precise transferir os mesmos dados repetidamente.

**P19. Por que vejo o aviso "Não foram configurados Backups do Azure para esse servidor" embora tenha agendado backups regulares anteriormente?** <br/> R19: Isso poderá ocorrer quando as configurações de agendamento de backup armazenadas no servidor local não forem iguais às configurações armazenadas no cofre de backup. Quando o servidor ou as configurações tiverem sido recuperadas para um bom estado conhecido, os agendamentos de backup podem perder a sincronização. Se isso tiver acontecido, você deverá reconfigurar a política de backup e **Executar o Backup Agora** para sincronizar novamente o servidor local com o Azure.

**P20. Quais regras de firewall devem ser configuradas para o backup do Backup do Azure?** <br/> R20. Certifique-se de que as regras de firewall permitem a comunicação com as URLs abaixo para o backup contínuo do local para a proteção do Azure e da carga de trabalho no Azure:

- www.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- *.windows.net


## Backup e retenção
**P1. Há um limite para o tamanho de cada fonte de dados submetida a backup?** <br/> R1. A partir de agosto de 2015, o tamanho máximo da fonte de dados será mencionado abaixo para vários sistemas operacionais

|S.Não |	Sistema operacional |	Tamanho máximo da fonte de dados |
| :-------------: |:-------------| :-----|
|1| Windows Server 2012 ou superior| 54400 GB|
|2| Windows 8 ou superior| 54400 GB|
|3| Windows Server 2008, Windows Server 2008 R2 | 1700 GB|
|4| Windows 7 | 1700 GB|

O tamanho da fonte de dados é medido como mencionado abaixo

|	Fonte de dados |	Detalhes |
| :-------------: |:-------------|
|Volume |A quantidade de dados incluídos no backup do volume único de uma máquina. Isso é aplicável para os volumes protegidos no servidor e em computadores clientes.|
|Máquina virtual do Hyper-V|Soma dos dados de todos os VHDs da máquina virtual da qual está sendo feito o backup|
|Banco de dados do Microsoft SQL Server|O tamanho de um banco de dados SQL único do qual está sendo feito o backup |
|Microsoft SharePoint|Soma dos bancos de dados de conteúdo e de configuração em um farm do SharePoint do qual está sendo feito o backup|
|Microsoft Exchange|Soma de todos os bancos de dados do Exchange em um servidor Exchange do qual está sendo feito o backup|
|Estado do Sistema/BMR|Cada cópia individual do BMR ou do estado do sistema da máquina da qual está sendo feito o backup|

**P2. Há um limite para o número de vezes que um backup pode ser agendado por dia?**<br/> R2. Sim, o Backup do Azure permite três cópias de backup por dia com o Windows Server/Client, duas cópias de backup por dia com o SCDPM e um backup por dia para VMs IaaS.

**P3. Há uma diferença entre a política de agendamento de backup do DPM e do Backup do Azure (ou seja, no Windows Server sem o DPM)?** <br/> R3. Sim. Usando o DPM, você pode especificar as programações diária, semanal, mensal ou anual. Já em um Windows Server (sem o DPM), você pode especificar apenas as programações diária e semanal.

**P4. Há uma diferença entre a política de retenção de backup do DPM e do Backup do Azure (ou seja, no Windows Server sem o DPM)?**<br/> R4. Não, você tem os mesmos recursos. Você pode especificar as políticas de retenção diária, semanal, mensal e anual.

**P5. Posso configurar minhas políticas de retenção de forma seletiva – ou seja, configurar semanal e diária, mas não anual e mensal?**<br/> R5. Sim, a estrutura de retenção de Backup do Azure permite que você tenha total flexibilidade na definição da política de retenção de acordo com suas necessidades.

**P6. Posso “agendar um backup” às 18h e especificar “políticas de retenção” em um momento diferente?**<br/> R6. Não. As políticas de retenção só podem ser aplicadas em pontos de backup. Na imagem abaixo, a política de retenção está sendo especificada em backups realizados à 00h e às 18h. <br/>

![Retenção e agendamento de Backup](./media/backup-azure-backup-faq/Schedule.png) <br/>

**P7. Uma cópia incremental é transferida de acordo com as políticas de retenção agendadas?** <br/> R7. Não. A cópia incremental é enviada com base na hora mencionada na página de agendamento de backup. Os pontos que podem ser retidos são determinados com base na política de retenção.

**P8. Se o backup for retido por um período longo, a recuperação dos dados demora muito (por exemplo, o ponto mais antigo)?** <br/> R8. Não. O tempo necessário para recuperar tanto o ponto mais recente quanto o mais antigo é o mesmo. Cada ponto de recuperação se comporta como um ponto completo.

**P9. Se cada ponto de recuperação é como um ponto completo, isso afeta o armazenamento de backup total cobrável?**<br/> R9. Os produtos típicos de ponto de retenção de longo prazo armazenam dados de backup como pontos completos. No entanto, eles não oferecem economia de armazenamento, mas são mais fáceis e rápidos de restaurar. As cópias incrementais oferecem economia de armazenamento, mas exigem que você restaure uma cadeia de dados, o que afeta o tempo de recuperação. A arquitetura de armazenamento exclusiva do Backup do Azure oferece o melhor dos dois recursos, armazenando dados de forma otimizada para restaurações rápidas e incorrendo em baixos custos de armazenamento. Essa abordagem garante que a largura de banda (de entrada e saída) seja usada com eficiência e que o armazenamento e o tempo de recuperação sejam mínimos.

**P10. Há um limite para o número de pontos de recuperação que podem ser criados?**<br/> R10. Não. Eliminamos os limites nos pontos de recuperação. Você pode criar quantos pontos de recuperação desejar.

**P11. Por que a quantidade de dados transferida no backup não é igual à quantidade de dados da qual fiz backup?**<br/> A11. Todos os dados que passam por backup são compactados e criptografados antes de serem transferidos. Você pode esperar benefícios de compactação de 30 a 40% dependendo do tipo de dados de backup.

## Recuperação
**P1. Quantas recuperações posso executar nos dados incluídos no backup no Azure?**<br/> R1. Não há limite para o número de recuperações do Backup do Azure.

**P2. É necessário pagar pelo tráfego de saída do datacenter do Azure durante as recuperações?**<br/> R2. Não. As suas recuperações são gratuitas e você não é cobrado pelo tráfego de saída.

## Segurança
**P1. Os dados são enviados para o Azure criptografados?** <br/> R1. Sim. Os dados são criptografados localmente no computador cliente/servidor/SCDPM usando AES256 e são enviados por um link HTTPS seguro.

**P2. Os dados de backup também são criptografados no Azure?**<br/> R2. Sim. Os dados enviados para o Azure permanecem criptografados (em repouso). A Microsoft não descriptografa os dados de backup em nenhum momento.

**P3. Qual é o comprimento mínimo da chave de criptografia usada para criptografar os dados de backup?** <br/> R3. A chave de criptografia deve ter pelo menos 16 caracteres.

**P4. O que acontecerá se eu inserir a chave de criptografia incorretamente? Posso recuperar os dados ou a Microsoft pode recuperar os dados?** <br/> R4. A chave usada para criptografar os dados de backup está presente apenas nas instalações do cliente. A Microsoft não mantém uma cópia no Azure e não tem qualquer acesso à chave. Se o cliente inserir a chave incorretamente, a Microsoft não poderá recuperar os dados de backup.

## Cache de backup

**P1. Como posso alterar o local de cache especificado para o agente de Backup do Azure?**

+ Parar o OBEngine executando o comando abaixo em um prompt de comando elevado:

  ```PS C:\> Net stop obengine```

+ Copie a pasta de espaço de cache para outra unidade com espaço suficiente. Recomendamos que você copie os arquivos da pasta de espaço de cache em vez de movê-los; o espaço de cache original poderá ser removido após a confirmação de que os backups estão funcionando com o novo espaço de cache.

+ Atualize as entradas do registro a seguir com o caminho para a nova pasta de espaço de cache:


	| Caminho do registro | Chave do Registro | Valor |
	| ------ | ------- | ------ |
	| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` | ScratchLocation | <i>Novo local da pasta de cache</i> |
	| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` | ScratchLocation | <i>Novo local da pasta de cache</i> |


+ Inicie o OBEngine executando o comando abaixo em um prompt de comando elevado:

  ```PS C:\> Net start obengine```

Assim que os backups começarem a ser executados com êxito com o novo local de cache, você poderá remover a pasta de cache original.

<!---HONumber=AcomDC_0128_2016-->