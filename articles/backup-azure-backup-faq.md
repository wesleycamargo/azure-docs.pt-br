<properties
	pageTitle="Backup do Azure - perguntas Frequentes"
	description="Perguntas freqüentes sobre o serviço de Backup do Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="03/26/2015"
	 ms.author="prvijay"/>

# Backup do Azure - perguntas Frequentes
A seguir é uma lista de perguntas frequentes sobre o Backup do Azure. Se você tiver perguntas adicionais sobre o Backup do Azure, vá para o o [Fórum de discussão](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazureonlinebackup) e publique suas perguntas. Alguém da sua comunidade ajudará você a obter respostas. Se uma pergunta for frequente, adicionaremos-lo a este artigo para que ela pode ser encontrada com rapidez e facilidade.

## Instalação e configuração

**P1. O que é a lista de sistemas operacionais com suporte da qual eu pode fazer backup do Azure usando o Backup do Azure?** <br/> A1. A seguinte lista de OS'es é suportada pelo Backup do Azure


| Sistema operacional | Plataforma | SKU |
| :------------- |-------------| :-----|
| Windows 8 e o SPs mais recentes | 64 bits | Enterprise, Pro |
| O Windows 7 e o SPs mais recentes | 64 bits | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e o SPs mais recentes | 64 bits | Enterprise, Pro |
|Windows Server 2012 R2 e o SPs mais recentes|	64 bits|	Padrão, Datacenter, Foundation|
|O Windows Server 2012 e o SPs mais recentes|	64 bits|	Datacenter, Foundation, Standard|
|Windows Storage Server 2012 R2 e o SPs mais recentes |64 bits|	Standard, Workgroup|
|Windows Storage Server 2012 e o SPs mais recentes |64 bits |Standard, Workgroup
|Windows Server 2012 R2 e o SPs mais recentes |64 bits|	Essenciais|
|Windows Server 2008 R2 SP1 |64 bits|	Standard, Enterprise, Datacenter, Foundation|
|Windows Server 2008 SP2 |64 bits|	Standard, Enterprise, Datacenter, Foundation|

**Q2. Onde posso baixar o agente de Backup do Azure mais recente?** <br/> A2. Você pode baixar o agente mais recente do [aqui](http://aka.ms/azurebackup_agent). Isso pode ser instalado no Windows Server, Windows cliente ou servidor SCDPM

**3º TRIMESTRE. Qual versão do servidor SCDPM é suportada?** <br/> A3. É recomendável que você instale o [mais recente](http://aka.ms/azurebackup_agent) Azure Backup agent no update rollup mais recente do SCDPM (UR5 a partir de fevereiro de 2015)

**4º TRIMESTRE. Ao configurar o agente de Backup do Azure, eu estou solicitado a inserir as credenciais"cofre". Há qualquer data de expiração anexado às credenciais do cofre?** <br/> A4. Sim, a credencial de cofre expiram após 48hours. Se o arquivo expirar, faça logon no portal do Azure e baixar os arquivos de credenciais do cofre do seu Cofre de Backup.

**P5. Há nenhum limite no número de cofres de backup que pode ser criado em cada assinatura do Azure?** <br/> A5. Sim. Desde março de 2015, você pode criar 25 cofres por assinatura. Se você precisar de mais cofres, crie uma nova assinatura.

**P6. Deve ser no cofre como uma entidade de cobrança?** <br/> A6. Embora seja possível obter uma lista detalhada de cada compartimento, é altamente recomendável que você considere uma assinatura do Azure como uma entidade de cobrança. Ela é consistente em todos os serviços e é mais fácil de gerenciar.

**P7. Há limites no número de servidores/máquinas que pode ser registrado em relação a cada cofre?** <br/> A7. Sim, você pode registrar até 50 máquinas por cofre. Se você precisar registrar mais máquinas, crie um novo cofre.

**P8. Há limites na quantidade de dados que podem fazer backup de um servidor/cliente do Windows ou um servidor SCDPM?** <br/> A8. Nº

**P9. Como registrar meu servidor para outro datacenter?**<br/> A9. Em geral, os dados de backup são enviados para o datacenter do serviço de Backup para o qual ele está registrado. A maneira mais fácil de alterar o datacenter é desinstalar o agente e reinstale o agente e registre-se para um novo data center.

**P10. O que acontece se eu renomear um servidor Windows que fazer backup de dados no Azure?** <br/> A10. Os backups atualmente configurados serão interrompidos. Você precisará registrar o servidor com o Cofre de backup e será considerado um novo servidor pelos serviços de recuperação, para que a primeira operação de backup que ocorre após o registro de um backup completo de todos os dados incluídos no backup, em vez de apenas as alterações desde o último backup. No entanto, se você precisar executar uma operação de recuperação pode recuperar os dados dos quais foi feitos usando-se recuperar de outra opção de recuperação do servidor. Para obter mais informações, consulte renomear um servidor.

**P11. Que tipos de unidades posso fazer backup de arquivos e pastas?** <br/> A11. O seguinte conjunto de unidades/volumes pode ser backup:

+ Mídia removível: A unidade deve ser reportada como corrigida para ser usado como uma fonte de item de backup.

+ Volumes somente leitura: O volume deve ser gravável para o volume shadow copy service (VSS) para funcionar.

+ Volumes offline: O volume deve estar online para VSS funcione.

+ Compartilhamento de rede: O volume deve ser local para o servidor de backup usando o backup online.

**P12. Quais tipos de arquivo e pasta posso fazer backup do meu servidor?**<br/> A12. Há suporte para os seguintes tipos:

+ Criptografado

+ Compactado

+ Esparso

+ Compactado + esparso

+ Links físicos: Não suportado, ignorado

+ Ponto de nova análise Não suportado, ignorado

+ Criptografado + compactado: Não suportado, ignorado

+ Criptografado + esparso: Não suportado, ignorado

+ Fluxo compactado: Não suportado, ignorado

+ Fluxo esparso: Não suportado, ignorado

**Q13. O que é o requisito de tamanho mínimo para a pasta de cache?** <br/> A13. O tamanho da pasta de cache é determinado pela quantidade de dados que você está fazendo backup. Em geral, você deve esperar que 10 a 15% do espaço necessário para armazenamento de dados deve ser alocado para a pasta de cache.

**Q14. Como posso isolar dados de servidor específico seja recuperado por outros servidores da minha organização? **<br/> A14. Todos os servidores registrados usando o mesmo cofre será capazes de recuperar os dados de backup por outros servidores que usam a mesma senha. Se você tiver servidores que você deseja garantir a recuperação ocorre apenas para servidores específicos na sua organização, você deve usar uma senha separada designada para esses servidores. Por exemplo, servidores de recursos humanos pode usar uma senha de criptografia, os servidores de contabilização outro armazenamento servidores e um terceiro.

**Q15. Posso "migrar" Meus dados de backup entre assinaturas?** <br/> A15: não

**Q16: Posso "migrar" Meu Cofre de backup entre assinaturas?** <br/> A16: não. O cofre é criado em um nível de assinatura e não pode ser reatribuído a outra assinatura depois que ela é criada.

## Backup e retenção
**P1. Há um limite para o tamanho de cada fonte de dados que está sendo feito backup?** <br/> A1. Desde março de 2015, cada fonte de dados deve ser menor ou igual a 1,7 TB. É uma fonte de dados

+ Volume de arquivo/pasta

+ BANCO DE DADOS SQL

+ Farm do SharePoint

+ Servidor do Exchange

+ VM do Hyper-V

**Q2. Há limite no número de vezes de backup pode ser programada por dia?**<br/> A2. Sim, Backup do Azure permite 3 cópias de backup por dia por meio de servidor/cliente Windows e duas cópias de backup por dia por meio de SCDPM.

**3º TRIMESTRE. Há uma diferença entre do DPM e diretiva de agendamento de backup Azure Backup (ou seja no Windows Server sem o DPM)?** <br/> A3. Sim. Usando o DPM, você pode especificar a programação diária, semanal, mensal, anual enquanto de um servidor do Windows (sem o DPM), você pode especificar apenas diariamente, agendamentos semanais.

**4º TRIMESTRE. Há uma diferença entre do DPM e do Azure do Backup (ou seja no Windows Server sem o DPM) política de retenção?**<br/> A4. Não, você tem os mesmos recursos. Você pode especificar diária, semanal, mensal e anual políticas de retenção.

**P5. Posso configurar meu retenção políticas – ou seja, configurar seletivamente semanalmente e diariamente, mas não anual e mensal?**<br/> A5. Você tem o conjunto completo de botões para criar diretivas que definem melhor seus requisitos de conformidade/retenção.

**P6. É possível "Agendar um backup" às 18h00 e especifique "políticas de retenção" em um momento diferente?**<br/> A6. Não. Políticas de retenção só podem ser aplicadas em pontos de backup. Na abaixo da imagem, a política de retenção está sendo especificada em backups feitos em 12: 00 e 18: 00. <br/>

![Retenção e agendamento Backup][1] <br/>

**P7. Uma cópia incremental é transferida para as políticas de retenção agendadas?** <br/> A7. Não, a cópia incremental é enviada com base na hora mencionada na página de agendamento de backup. Os pontos que podem ser mantidos são determinados com base na política de retenção.

**P8. Se o backup é mantido por muito tempo, leva um tempo significativo para recuperar os dados (digamos que o ponto mais antigo)?** <br/> A8. Não – o tempo necessário para recuperação mais antigo ou o último ponto é o mesmo. Cada ponto de recuperação se comporta como um ponto de total.

**P9. Se cada ponto de recuperação é como um ponto completo, ele afeta o armazenamento total de backup cobrável?**<br/> A9. Produtos retenção típico de longo prazo armazenam dados de backup como pontos completos. No entanto, eles são armazenamento ineficiente mas são mais fácil e rápido de restaurar. Cópias incrementais são armazenamento eficiente, mas exige que você restaure uma cadeia de dados que afeta o tempo de recuperação. Arquitetura de armazenamento exclusivo do Backup do Azure oferece o melhor dos dois mundos, armazenando dados para restaurações rápidas e incorrer em custos de armazenamento de baixo ideal. Essa abordagem garante que a largura de banda (de entrada e saída) é usada com eficiência, armazenamento é mantido no mínimo e o tempo necessário para recuperar é mantido no mínimo.

**P10. Há um limite no número de pontos de recuperação que podem ser criados?**<br/> A10. A partir de abril de 2015, você pode ter até 366 pontos de recuperação. Você pode usar qualquer permutação para chegar a um número que é menor que 366. Por exemplo – pontos a retenção de abaixo imagem adicionar 354. <br/>

![Tela de retenção][2]

**P11. Depois que a Microsoft aprimora o limite de 366, será preciso atualizar o agente ou propagar novamente o backup inicial?** <br/> A11. Não. Depois de fazermos a alteração no nosso serviço, você será notificado por meio de nossos veículos de mídia social (blogs, anúncios do Azure, portal etc.). Com base em suas necessidades, você deve, em seguida, precise apenas alterar a política de retenção.

**P12. Por que a quantidade de dados é transferida no backup não é igual à quantidade de dados de que backup feito?**<br/> A12. Todos os dados de backup são compactados e criptografados antes de serem transferidos. Você pode esperar os benefícios da compactação de 30 a 40% dependendo do tipo de dados de backup.

## Recuperações
**P1. O número de recuperações pode executar nos dados que são copiados para o Azure?**<br/> A1. Não há nenhum limite no número de recuperações do Backup do Azure.

**Q2. É necessário pagar para o tráfego de saída do data center do Azure durante recuperações?**<br/> A2. Não. Seus recuperações são gratuitas e que não seja cobrado para o tráfego de saída.

## Segurança
**P1. Os dados são enviados para o Azure criptografado?** <br/> A1. Sim. Os dados são criptografados no computador cliente/servidor/SCDPM no local usando AES256 e os dados são enviados por um link HTTPS seguro.

**Q2. São os dados de backup no Azure criptografado?**<br/> A2. Sim. Os dados que são enviados para o Azure permanecem criptografados (em repouso). Microsoft não descriptografa os dados de backup a qualquer momento.

**3º TRIMESTRE. O que é o comprimento mínimo da chave de criptografia usada para criptografar os dados de backup?** <br/> A3. A chave de criptografia deve ter pelo menos 16 caracteres.

**4º TRIMESTRE. O que acontecerá se eu esquecer onde deixou a chave de criptografia? Posso recuperar os dados (ou) pode Microsoft recuperar os dados?** <br/> A4. A chave usada para criptografar os dados de backup está presente apenas em instalações do cliente. A Microsoft não mantém uma cópia no Azure e não tem nenhum acesso à chave. Se o cliente misplaces a chave, a Microsoft não pode recuperar os dados de backup.


<!--Image references-->
[1]: ./media/backup-azure-backup-faq/Schedule.png
[2]: ./media/backup-azure-backup-faq/RetentionScreen1.png

<!---HONumber=GIT-SubDir-->