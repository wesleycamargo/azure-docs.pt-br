---
title: "Alta disponibilidade e recuperação de desastre do SAP HANA no Azure (instâncias grandes) | Microsoft Docs"
description: "Estabeleça a alta disponibilidade e planeje a recuperação de desastre do SAP HANA no Azure (instâncias grandes)."
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 4356e35609fa7a9727d8ad4f20fd18df01a05e84
ms.contentlocale: pt-br
ms.lasthandoff: 08/30/2017

---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Alta disponibilidade e recuperação de desastre do SAP HANA (instâncias grandes) no Azure 

A alta disponibilidade e a recuperação de desastre são aspectos importantes da execução crucial do SAP HANA em servidores do Azure (instâncias grandes). É importante trabalhar junto com a SAP, seu integrador de sistemas ou a Microsoft para arquitetar e implementar adequadamente a estratégia de alta disponibilidade e recuperação de desastre adequada para você. Também é importante considerar o objetivo do ponto de recuperação e o objetivo do tempo de recuperação, que são específicos para seu ambiente.

A Microsoft dá suporte aos métodos de alta disponibilidade do SAP HANA “prontos” com Instâncias Grandes do HANA. Estão incluídos:

- **Replicação de armazenamento:** a capacidade do sistema de armazenamento de replicar todos os dados para outro Stamp de Instância Grande HANA em outra Região do Azure. O SAP HANA funciona independentemente desse método.
- **Replicação do sistema HANA:** a replicação de todos os dados no SAP HANA para um sistema separado. O objetivo de tempo de recuperação é minimizado por meio da replicação de dados em intervalos regulares. O SAP HANA dá suporte os modos assíncronos, síncronos in-memory e síncronos (recomendados para sistemas SAP HANA somente dentro do mesmo data center ou a menos de 100 km de distância). No design atual dos carimbos de instância grande HANA, a replicação de sistema HANA pode ser usada apenas para alta disponibilidade. Com base no design de rede atual, a Replicação de Sistema HANA não pode ser usada sem o componente de proxy reverso de terceiros para configurações de recuperação de desastres em outra Região do Azure. 
- **Failover automático de host:** uma solução de recuperação de falhas local para SAP HANA para ser usada como uma alternativa à Replicação do Sistema HANA. Quando o nó mestre fica indisponível, um ou mais nós SAP HANA em espera são configurados no modo de escala horizontal e o SAP HANA faz automaticamente o failover para um nó em espera.

O SAP HANA no Azure (instâncias grandes) é oferecido em duas regiões do Azure em três regiões geopolíticas diferentes (EUA, Austrália e Europa). Duas regiões diferentes que hospedam stamps de Instância Grande HANA estão conectados a circuitos de rede dedicados que são usados para a replicação de instantâneos de armazenamento para fornecer métodos de recuperação de desastres. A replicação não é estabelecida por padrão. Ela é configurada para os clientes que solicitaram a funcionalidade de recuperação de desastres. A replicação de armazenamento depende do uso de instantâneos de armazenamento para Instâncias Grandes HANA. Também não é possível escolher uma Região do Azure como a região de DR, que está em uma área geopolítica diferente. 

Combinações e métodos de Alta Disponibilidade e Recuperação de Desastres com suporte no momento podem ser vistos nesta tabela:

| Cenário com suporte em Instâncias Grandes HANA | Opção de Alta Disponibilidade | Opção de Recuperação de Desastre | Comentários |
| --- | --- | --- | --- |
| Nó único | Não disponível | Configuração de recuperação de desastres dedicada<br /> Configuração de recuperação de desastres multipropósito | |
| Failover automático de host: N + m<br /> incluindo 1 + 1 | Possível com Em espera usando a função ativa<br /> HANA controla a opção de função | Configuração de recuperação de desastres dedicada<br /> Configuração de recuperação de desastres multipropósito<br /> Sincronização de recuperação de desastres usando replicação de armazenamento | Conjuntos de volumes do HANA anexados a todos os nós (n + m)<br /> O site de recuperação de desastres deve ter o mesmo número de nós |
| Replicação de sistema do HANA | Possível com a instalação primária/secundária<br /> A secundária move para a função primária em um caso de failover<br /> Replicação de sistema HANA e controle de failover do SO | Configuração de recuperação de desastres dedicada<br /> Configuração de recuperação de desastres multipropósito<br /> Sincronização de recuperação de desastres usando replicação de armazenamento<br /> A recuperação de desastres usando replicação de sistema do HANA ainda não é possível sem componentes de terceiros | Conjunto separado de volumes de disco anexados a cada nó<br /> Somente os volumes de disco de réplica secundária no site de produção são replicados para o local de recuperação de desastres<br /> Um conjunto de volumes necessários no local de recuperação de desastres | 

Como configuração de recuperação de desastres dedicada, caracterizamos uma instalação em que a unidade de Instância Grande HANA no local de recuperação de desastres não é usada para executar qualquer outra carga de trabalho ou sistema de não produção. A unidade é passiva e é implantada apenas para o caso de pular para a ação se um failover de desastre for executado. Até agora não temos um único cliente com essa configuração.

Como uma configuração de recuperação de desastres multipropósito, caracterizamos uma configuração no local de recuperação de desastres, em que a unidade de Instância Grande HANA executa uma carga de trabalho de não produção. Em caso de desastre em que o sistema de não produção é desligado, os conjuntos de volume de armazenamento replicado (adicional) montados e a instância HANA de produção são iniciados. Até agora todos os clientes usando a funcionalidade de recuperação de desastres de Instância Grande HANA estão usando essa alternativa de configuração. 


Encontre mais informações sobre a Alta Disponibilidade do SAP HANA nos seguintes artigos do SAP: 

- [Whitepaper de Alta Disponibilidade do SAP HANA](http://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [Guia de administração do SAP HANA](http://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [Vídeo do SAP Academy sobre Replicação do Sistema SAP HANA](http://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP Note de Suporte nº 1999880 – perguntas frequentes sobre replicação do sistema SAP HANA](https://bcs.wdf.sap.corp/sap/support/notes/1999880)
- [SAP Note de Suporte SAP nº 2165547 – backup e restauração do SAP HANA no ambiente de replicação do sistema SAP HANA](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP Note de Suporte SAP nº 1984882 – uso da replicação do sistema SAP HANA para troca de hardware com tempo de inatividade mínimo/zero](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>Considerações de rede para recuperação de desastres com Instâncias Grandes HANA

Para aproveitar a funcionalidade da recuperação de desastre de Instâncias Grandes HANA, você precisa começar a projetar a conectividade de rede para as duas regiões diferentes do Azure. Para tal, você precisaria de uma conexão de circuito do Azure ExpressRoute conectado aos locais em sua região principal do Azure e outro circuito conectado aos locais em sua região de recuperação de desastre. Essa medida abrange uma situação em que uma região do Azure completa, incluindo local do MSEE (Microsoft Enterprise Edge Router), tem um problema.

Como uma segunda medida, é possível se conectar a todas as VNets do Azure que se conectam ao SAP HANA no Azure (instâncias grandes) em uma das regiões para os circuitos do ExpressRoute que conectam as Instâncias Grandes HANA a outra região. Com essa 'conexão cruzada', serviços em execução em uma Vnet do Azure na região 1, é possível se conectar a unidades de Instância Grande HANA na região nº2 e vice-versa. Essa medida resolve casos em que apenas um dos locais de MSEE que se conecta ao seu local com o Azure está fora de serviço.

O gráfico a seguir ilustra uma configuração flexível para recuperação de desastres:

![Configuração ideal para a recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)



## <a name="other-requirements-for-using-hana-large-instances-storage-replication-for-disaster-recovery"></a>Outros requisitos para usar a Replicação de Armazenamento de Instâncias Grandes HANA para recuperação de desastres

Os requisitos adicionais para uma configuração de recuperação de desastres com Instâncias Grandes HANA são:

- Você precisa solicitar SKUs do SAP HANA no Azure (Instâncias Grandes) do mesmo tamanho das suas SKUs de produção ordenadas e implantadas na região de recuperação de desastre. Em implantações de clientes até agora, essas instâncias são usadas para executar instâncias HANA de não produção. Nós nos referimos a elas como configurações de recuperação de desastres multipropósito.   
- Você precisa solicitar armazenamento adicional no site da recuperação de desastre para cada uma das SKUs do SAP HANA no Azure (instâncias grandes) que você deseja recuperar no local de recuperação de desastre, se necessário. Essa ação leva à alocação de volumes de armazenamento, que são o destino de replicação de armazenamento de sua região de produção do Azure para a região de recuperação de desastre do Azure.

Mais detalhes sobre a Recuperação de Desastres são encontrados após os últimos capítulos do documento.
 

## <a name="backup-and-restore"></a>Backup e restauração

Um dos aspectos mais importantes para bancos de dados operacionais é verificar se o banco de dados pode ser protegido de diversos eventos catastróficos. Esses eventos podem ser causados por qualquer coisa, desde desastres naturais até erros de usuário simples.

O backup de um banco de dados, com a capacidade de restaurar para qualquer ponto no tempo (como antes de alguém excluir dados críticos), permite a restauração para um estado o mais próximo possível à forma como ele estava antes da interrupção.

Dois tipos de backup devem ser executados para obter melhores resultados:

- Backups de banco de dados – backups completos, incrementais ou diferenciais
- Backups do log de transações

Além de backups completos do banco de dados executados no nível de aplicativo, você pode ser ainda mais cuidadoso ao executar backups com instantâneos de armazenamento. Embora os instantâneos de armazenamento não estejam substituindo backups de log de transações. A execução de backups de log de transações permanece importante para restaurar o banco de dados a um determinado ponto no tempo ou limpar os logs das transações já confirmadas. Mas os instantâneos de armazenamento podem acelerar a recuperação fornecendo uma imagem de roll forward do banco de dados de uma maneira rápida. 

O SAP HANA no Azure (grandes instâncias) oferece duas opções de backup e de restauração:

- DIY (Faça Você Mesmo). Depois de calcular para garantir espaço em disco suficiente, execute o banco de dados completo e os backups de log usando os métodos de backup de disco diretamente para os volumes anexados às unidades de Instância Grande HANA para a configuração de compartilhamentos NFS em uma VM do Azure. No último caso, os clientes configuram uma VM do Linux no Azure, anexam o Armazenamento do Azure à máquina virtual e compartilham o armazenamento por meio de um servidor NFS configurado na VM. Se executar o backup em volumes conectados diretamente a unidades de Instância Grande HANA, os backups precisarão ser copiados em uma conta de armazenamento do Azure (depois de configurar uma VM do Azure que exporta os compartilhamentos NFS com base no Armazenamento do Azure) ou use um Cofre de Backup do Azure ou armazenamento frio do Azure. Outra opção é usar uma ferramenta de proteção de dados de terceiros para armazenar os backups depois que eles são copiados em uma conta de armazenamento do Azure. A opção de backup DIY (Faça você mesmo) também pode ser necessária para dados que precisam ser armazenados por períodos mais longos devido à conformidade e para fins de auditoria. Em todos os casos, os backups são copiados nos compartilhamentos NFS representados por meio de uma VM e armazenamento do Azure.
- Use o backup e restaurar a funcionalidade que fornece a infraestrutura subjacente do SAP HANA no Azure (Instâncias Grandes). Essa opção atende a necessidade de backups e restaurações rápidas. O restante desta seção aborda essa funcionalidade de backup e restauração oferecida com Instâncias Grandes HANA. E sua relação com a funcionalidade de recuperação de desastres oferecida pelas Instâncias Grandes HANA.

> [!NOTE]
> A tecnologia de instantâneo que é usada pela infraestrutura subjacente das Instâncias Grandes HANA tem uma dependência de instantâneos do SAP HANA. Instantâneos do SAP HANA não funcionam junto com os múltiplos locatários dos Contêineres de Banco de Dados do SAP HANA até o momento. Como resultado, esse método de backup não pode ser usado para implantar múltiplos locatários nos Contêineres de Banco de Dados do SAP HANA. Embora ele funcione apenas se um locatário for implantado.

### <a name="using-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Usando instantâneos de armazenamento do SAP HANA no Azure (Instâncias Grandes)

A infraestrutura de armazenamento subjacente SAP HANA no Azure (Instâncias Grandes) dá suporte a noção de um instantâneo de volumes de armazenamento. O backup e a restauração do volume têm suporte com as seguintes considerações:

- Em vez de backups de banco de dados completos, instantâneos de volume de armazenamento são executados com frequência.
- Ao acionar um instantâneo nos volumes /hana/data, 'hana/log e /hana/shared (inclui /usr/sap), o instantâneo de armazenamento inicia um instantâneo SAP HANA antes de executar o instantâneo de armazenamento. Esse instantâneo SAP HANA é o ponto de instalação para restaurações de log eventual após a recuperação do instantâneo de armazenamento.
- Após o ponto em que o instantâneo de armazenamento é executado com êxito, o instantâneo SAP HANA será excluído.
- Backups de log de transações são usados frequentemente e armazenados no volume /hana/logbackups ou no Azure. O volume de /hana/logbackups que contém os backups de log de transações pode ser acionado para instantâneo separadamente. Nesse caso, nenhum instantâneo HANA deve ser executado.
- Se o banco de dados tiver de ser restaurado para um determinado ponto no tempo, será feita uma solicitação para o Suporte do Microsoft Azure (interrupção de produção) ou do SAP HANA no Gerenciamento de Serviços do Azure para restaurar para um determinado instantâneo de armazenamento (por exemplo, uma restauração planejada de um sistema de área restrita para seu estado original).
- O instantâneo SAP HANA incluído no instantâneo de armazenamento é um ponto de deslocamento para a aplicação de backups de log de transações que foram executados e armazenados após o instantâneo de armazenamento ter sido tirado.
- Esses backups de log de transações são aplicados para restaurar o banco de dados para um determinado ponto no tempo.

Você, como um cliente, tem a possibilidade de executar instantâneos de armazenamento direcionando três classes diferentes de volumes:

- instantâneo combinado em /hana/data, /hana/log e /hana/shared (inclui /usr/sap), juntos. Esse instantâneo requer a execução de um instantâneo SAP HANA)
- Instantâneo separado em /hana/logbackups
- Partição do sistema operacional (somente para o tipo I das Instâncias Grandes HANA)


### <a name="storage-snapshot-considerations"></a>Considerações sobre o instantâneo de armazenamento

>[!NOTE]
>Instantâneos de armazenamento estão consumindo o espaço de armazenamento que foi alocado para as unidades de Instância Grande HANA. Portanto, você precisa considerar os aspectos abaixo em termos de programação de instantâneos de armazenamento e manter um número de instantâneos de armazenamento. 

A mecânica específica de instantâneos de armazenamento para SAP HANA no Azure (Instâncias Grandes) inclui:

- Um instantâneo de armazenamento específico (no ponto no tempo em que é obtido) consome pouco de armazenamento.
- Como as alterações de conteúdo de dados e o conteúdo na alteração dos arquivos no volume de armazenamento de dados do SAP HANA, o instantâneo precisa armazenar o conteúdo do bloco original.
- O instantâneo de armazenamento aumenta de tamanho. Quanto mais tempo o instantâneo existe, quanto maior o instantâneo de armazenamento ficar.
- Mais alterações feitas no volume de banco de dados do SAP HANA durante o tempo de vida de um instantâneo do armazenamento, maior o consumo de espaço do instantâneo do armazenamento.

SAP HANA no Azure (instâncias grande) vem com tamanhos de volume fixo para o volume de dados e de log do SAP HANA. A execução de instantâneos desses volumes consome seu espaço de volume. Portanto, é sua responsabilidade agendar instantâneos de armazenamento, monitorar o consumo de volumes de armazenamento e gerenciar o número de instantâneos armazenados. Somente você pode optar por desabilitar, eventualmente, instantâneos de armazenamento por importar muitos dados ou executar outras alterações significativas no banco de dados do HANA. 


As seções a seguir fornecem informações para executar esses instantâneos incluindo recomendações gerais:

- Embora o hardware possa sustentar 255 snapshots por volume, é altamente recomendável ficar bem abaixo desse limite.
- Antes de executar instantâneos de armazenamento, monitore e mantenha o controle do espaço livre.
- Reduza o número de instantâneos de armazenamento com base em espaço livre. Pode ser necessário reduzir o número de instantâneos que você mantém ou talvez você precise estender os volumes. (Você pode solicitar armazenamento adicional em unidades de 1 TeraByte.)
- Durante as atividades, como a movimentação de dados em SAP HANA com ferramentas de migração da plataforma SAP (com R3load) ou restaurando bancos de dados do SAP HANA de backups, é desabilitar instantâneos de armazenamento no volume /hana/data. 
- Durante a maior reorganizações de tabelas do SAP HANA, instantâneos de armazenamento devem ser evitados se possível.
- Os instantâneos de armazenamento são um pré-requisito para usar os recursos de recuperação de desastre do SAP HANA no Azure (Instâncias Grandes).

### <a name="setting-up-storage-snapshots"></a>Configurar instantâneos de armazenamento

As etapas aproximadas para configurar instantâneos de armazenamento com Instâncias Grandes HANA se parecem com:
1. Verifique se o Perl está instalado no sistema operacional Linux no servidor HANA (grandes instâncias).
2. Modifique /etc/ssh/ssh\_config para adicionar alinha _MACs hmac-sha1_.
3. Crie uma conta de usuário de backup do SAP HANA no nó principal para cada instância do SAP HANA que estão em execução (se aplicável).
4. O cliente SAP HANA HDB deve ser instalado em todos os servidores HANA (grandes instâncias).
5. No primeiro servidor SAP HANA (grandes instâncias) de cada região, deve ser criada uma chave pública para acessar a infraestrutura básica de armazenamento que controla a criação do instantâneo.
6. Copie os scripts e o arquivo de configuração [deste local do github](https://github.com/Azure/hana-large-instances-self-service-scripts) até o local do **hdbsql** da instalação do SAP HANA.
7. Modifique o arquivo HANABackupDetails.txt conforme necessário para as especificações do cliente apropriado.

### <a name="step-1-install-sap-hana-hdbclient"></a>Etapa 1: Instalar o HDBClient do SAP HANA

O sistema operacional Linux instalado em SAP HANA no Azure (instâncias grandes) inclui as pastas e os scripts necessários para executar instantâneos de armazenamento SAP HANA para fins de backup e recuperação de desastre. Procure versões mais recentes em [github](https://github.com/Azure/hana-large-instances-self-service-scripts). A versão mais recente dos scripts é a 2.0.
No entanto, é sua responsabilidade instalar o SAP HANA HDBclient nas unidades de Instância Grande HANA durante a instalação do SAP HANA. (A Microsoft não instala o HDBclient nem o SAP HANA.)

### <a name="step-2-change-etcsshsshconfig"></a>Etapa 2: Alterar/etc/ssh/ssh\_config

Altere /etc/ssh/ssh\_config adicionando a linha _MACs hmac-sha1_ conforme mostrado aqui:
```
#   RhostsRSAAuthentication no
#   RSAAuthentication yes
#   PasswordAuthentication yes
#   HostbasedAuthentication no
#   GSSAPIAuthentication no
#   GSSAPIDelegateCredentials no
#   GSSAPIKeyExchange no
#   GSSAPITrustDNS no
#   BatchMode no
#   CheckHostIP yes
#   AddressFamily any
#   ConnectTimeout 0
#   StrictHostKeyChecking ask
#   IdentityFile ~/.ssh/identity
#   IdentityFile ~/.ssh/id_rsa
#   IdentityFile ~/.ssh/id_dsa
#   Port 22
Protocol 2
#   Cipher 3des
#   Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-cbc
#   MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160
MACs hmac-sha1
#   EscapeChar ~
#   Tunnel no
#   TunnelDevice any:any
#   PermitLocalCommand no
#   VisualHostKey no
#   ProxyCommand ssh -q -W %h:%p gateway.example.com
```

### <a name="step-3-create-a-public-key"></a>Etapa 3: Criar uma chave pública

Para habilitar o acesso às interfaces de instantâneo de armazenamento do seu locatário de Instância Grande HANA, você precisa estabelecer um logon por meio de chave pública. No primeiro servidor SAP HANA no Azure (instâncias grandes) no seu locatário, crie uma chave pública para ser usada para acessar a infraestrutura de armazenamento, permitindo a criação de instantâneos. A chave pública garante que uma senha não será necessária para entrar nas interfaces do instantâneo de armazenamento e que as credenciais de senha não serão mantidas. No Linux no servidor SAP HANA (grandes instâncias), execute o seguinte comando para gerar a chave pública:
```
  ssh-keygen –t dsa –b 1024
```
O novo local é _/root/.ssh/id\_dsa.pub. Não insira uma senha real, caso contrário, você precisará inserir a senha todas as vezes que entrar. Em vez disso, pressione **Enter** duas vezes para remover o requisito de senha para entrar.

Verifique se a chave pública foi corrigida conforme o esperado alterando as pastas para /root/.ssh/ e executando o comando **ls**. Se a chave estiver presente, você poderá copiá-la executando o seguinte comando:

![Chave pública é copiada executando este comando](./media/hana-overview-high-availability-disaster-recovery/image2-public-key.png)

Neste ponto, entre em contato com o SAP HANA no Gerenciamento de Serviços do Azure e forneça a chave pública. O representante de serviço usa uma chave pública para registrá-la na infraestrutura de armazenamento subjacente que está gravada para seu locatário de Instância Grande HANA.

### <a name="step-4-create-an-sap-hana-user-account"></a>Etapa 4: Criar uma conta de usuário do SAP HANA

Para poder iniciar a criação de instantâneos do SAP HANA, você precisa criar um usuário no SAP HANA, que pode ser usado pelos scripts de instantâneo de armazenamento. Crie uma conta de usuário do SAP HANA no SAP HANA Studio para essa finalidade. Essa conta deve ter os seguintes privilégios: _Administrador de Backup_ e _Leitura de Catálogo_. Neste exemplo, o nome de usuário SCADMIN é criado. O nome da conta de usuário criado no HANA Studio diferencia maiúsculas de minúsculas.  Certifique-se de clicar em 'Não' na opção de requer que o usuário altere a senha no próximo logon.

![Criando um usuário no Studio HANA](./media/hana-overview-high-availability-disaster-recovery/image3-creating-user.png)

### <a name="step-5-authorize-the-sap-hana-user-account"></a>Etapa 5: Autorizar a conta de usuário do SAP HANA

Nesta etapa, é necessário autorizar a conta de usuário do SAP HANA que você criou para que os scripts não precisem enviar senhas em tempo de execução. O comando `hdbuserstore` do SAP HANA permite criar uma chave de usuário do SAP HANA, que é armazenada em um ou mais nós do SAP HANA. A chave de usuário permite que o usuário acesse o SAP HANA sem a necessidade de gerenciar suas senhas no processo de script que será discutido posteriormente.

>[!IMPORTANT]
>Execute o comando a seguir como `root`. Caso contrário, o script não funcionará corretamente.

Insira o comando `hdbuserstore` da seguinte maneira:

![Digite comando hdbuserstore](./media/hana-overview-high-availability-disaster-recovery/image4-hdbuserstore-command.png)

No exemplo a seguir, no qual o usuário é SCADMIN01 e o nome do host é lhanad01 e o número de instância é 01, o comando é:
```
hdbuserstore set SCADMIN01 lhanad01:30115 <backup username> <password>
```
Se tiver uma configuração de expansão do SAP HANA, você deverá gerenciar todos os scripts de um único servidor. Neste exemplo, a chave SCADMIN01 do SAP HANA deve ser alterada para cada host de forma a refletir o host que está relacionado à chave. Ou seja, a conta de backup do SAP HANA é redefinida com o número da instância do HANA DB. A chave deve ter privilégios administrativos no host ao qual está atribuída e o usuário de backup para escala horizontal deve ter direitos de acesso a todas as instâncias do SAP HANA. Supondo os três nós de expansão com os nomes lhanad01, lhanad02 e lhanad03, a sequência de comandos pareceria com:

```
hdbuserstore set SCADMIN01 lhanad01:30115 SCADMIN <password>
hdbuserstore set SCADMIN02 lhanad02:30215 SCADMIN <password>
hdbuserstore set SCADMIN03 lhanad03:30315 SCADMIN <password>
```

### <a name="step-6-get-snapshot-scripts-configure-snapshots-and-test-configuration-and-connectivity"></a>Etapa 6: Obter scripts de instantâneo, configurar instantâneos e testar configuração e conectividade

Baixe a versão mais recente dos scripts [aqui](https://github.com/Azure/hana-large-instances-self-service-scripts). Copie os scripts baixados e o arquivo de texto no diretório de trabalho para **hdbsql**. Para instalações atuais do HANA, esse diretório é /hana/shared/D01/exe/linuxx86\_64/hdb.
```
azure\_hana\_backup.pl
azure\_hana\_replication\_status.pl
azure\_hana\_snapshot\_details.pl
azure\_hana\_snapshot\_delelte.pl
testHANAConnection.pl
testStorageSnapshotConnection.pl
removeTestStorageSnapshot.pl
HANABackupCustomerDetails.txt
```

A finalidade dos arquivos e scripts diferentes é semelhante a:

- **azure\_hana\_backup.pl**: este script precisa ser agendado com cron para que os instantâneos de armazenamento sejam executados nos volumes data/log/shared HANA, volume /hana/logbackups ou no sistema operacional (para SKUs de tipo I das Instâncias Grandes HANA)
- **azure\_hana\_replication\_status.pl**: este script foi desenvolvido para fornecer detalhes básicos sobre o status da replicação do site de produção para o local de recuperação de desastres.  O script é usado para monitorar se a replicação está ocorrendo e mostrar os tamanhos de itens que estão sendo replicados.  Ele também fornece orientação se uma replicação estiver demorando muito ou se o link for potencialmente baixo.
- **azure\_hana\_snapshot\_details.pl**: o objetivo desse script é fornecer ao cliente uma lista de detalhes básicos sobre todos os instantâneos por volume que existem em seu ambiente. Esse script pode ser executado no servidor primário ou em uma unidade de servidor no local da recuperação de desastres. O script fornece as informações a seguir divididas por cada volume que contém os instantâneos: o tamanho dos instantâneos totais em um volume e, em seguida, para cada instantâneo desse volume os detalhes de: nome do instantâneo, horário de criação, tamanho do instantâneo, frequência do instantâneo e a ID de Backup do HANA associada a esse instantâneo (se relevante).
- **azure\_hana\_snapshot\_delelte.pl**: esse script foi desenvolvido para excluir um instantâneo de armazenamento ou um conjunto de instantâneos, usando a ID de backup do SAP HANA como encontrada no HANA Studio ou pelo nome do instantâneo de armazenamento.  Atualmente, a ID de backup só está vinculada aos instantâneos criados para os volumes data/log/shared HANA.  Caso contrário, se a ID de instantâneo for inserida, serão procurados todos os instantâneos que correspondam à ID de instantâneo inserida.  
- **testHANAConnection.pl**: esse script testa a conexão à instância do SAP HANA e é necessário para configurar os instantâneos de armazenamento.
- **testStorageSnapshotConnection.pl**: esse script tem dois propósitos. Primeiro, garantir que a unidade de Instância Grande HANA que executa os scripts tenha acesso à máquina virtual de armazenamento atribuído e com a interface de instantâneo de armazenamento de suas Instâncias Grandes HANA. A segunda finalidade é criar um instantâneo temporário para a instância do HANA que você está testando. Esse script deve ser executado para cada instância do HANA em um servidor para garantir que os scripts de backup funcionem conforme o esperado.
- **removeTestStorageSnapshot.pl**: esse script é usado para excluir o instantâneo de teste criado com o script testStorageSnapshotConnection.pl. 
- **HANABackupCustomerDetails.txt**: é um arquivo de configuração modificável que você precisa modificar para adaptar-se à sua configuração do SAP HANA

 
O arquivo HANABackupCustomerDetails.txt é o arquivo de configuração e controle para o script que executará os instantâneos de armazenamento. Você precisa ajustar o arquivo às suas finalidades e configuração. Você deve ter recebido o _Nome do Backup de Armazenamento_ e o _Endereço IP do Armazenamento_ do SAP HANA no Gerenciamento de Serviços do Azure quando suas instâncias foram implantadas. Não é possível modificar a sequência, ordenação nem o espaçamento de qualquer uma das variáveis nesse arquivo. Caso contrário, os scripts não serão executados corretamente. Além disso, você recebeu o endereço IP do nó escalável ou o nó mestre (se expandido) do SAP HANA no Gerenciamento de Serviços do Azure. Depois de instalar o SAP HANA, você também saberá o número de instância do HANA e, em seguida, precisará adicionar um nome de backup ao arquivo de configuração

Para uma implantação de expansão ou escalável, o arquivo de configuração teria a aparência abaixo depois de preencher o nome do backup de armazenamento e o endereço IP de armazenamento. Dados adicionais que devem ser preenchidos no arquivo de configuração são o endereço IP do nó mestre ou nó único, o número da instância HANA e nome do backup que você pode escolher:
```
#Provided by Microsoft Service Management
Storage Backup Name: client1hm3backup
Storage IP Address: 10.240.20.31
#Node IP addresses, instance numbers, and HANA backup name
#provided by customer.  HANA backup name created using
#hdbuserstore utility.
Node 1 IP Address: 
Node 1 HANA instance number:
Node 1 HANA Backup Name:
```

>[!NOTE]
>Atualmente, apenas os detalhes do Nó 1 são usados no script de instantâneo do armazenamento HANA real. É recomendável que você teste o acesso de ou para todos os nós do HANA para que, se o nó de backup mestre for alterado, já esteja garantido que qualquer outro nó poderá assumir seu lugar modificando os detalhes no Nó 1.

Depois de colocar todos os dados de configuração no arquivo HANABackupCustomerDetails.txt, você precisará verificar se as configurações estão corretas em relação aos dados de instância do HANA usando o script testHANAConnection.pl. Esse script é independente de uma configuração de expansão ou escalável do SAP HANA

```
testHANAConnection.pl
```

Se você tiver uma configuração de expansão do SAP HANA, certifique-se de que a instância mestre do HANA tenha acesso a todos os servidores instâncias necessários do HANA. Não existem parâmetros para o script de teste, porém é necessário concluir seus dados no arquivo de configuração HANABackupCustomerDetails.txt para que o script seja executado corretamente. Como somente os códigos de erro de comando shell são retornados, não é possível para o script verificar os erros de cada instância. Ainda assim, o script ainda oferece alguns comentários úteis para você verificar novamente.

Para executar o script:
```
 ./testHANAConnection.pl
```
O script exibirá a mensagem de que a conexão do HANA foi bem-sucedida se for bem-sucedido ao obter o status da instância do HANA.


A próxima etapa de teste é verificar a conectividade com o armazenamento com base nos dados que você inseriu no arquivo de configuração HANABackupCustomerDetails.txt e executar um instantâneo de teste.  Antes de executar o script azure\_hana\_backup.pl, você deverá executar esse teste. Se um volume não contiver nenhum instantâneo, será impossível determinar se o volume está simplesmente vazio ou se há uma falha de ssh para obter os detalhes do instantâneo. Por esse motivo, script é executado em duas etapas:

- Ele verifica se a máquina virtual de armazenamento e interfaces dos locatários está acessível para os scripts para executar instantâneos.
- Ele cria um instantâneo de teste ou fictício para cada volume por instância do HANA.

Por esse motivo, a instância do HANA é incluída como um argumento. Se a execução falhar, não será possível fornecer a verificação de erros para a conexão de armazenamento. Mas o script fornecerá dicas úteis nesses casos.

O script é executado como:
```
 ./testStorageSnapshotConnection.pl <HANA SID>
```
Como próxima etapa, o script tentará fazer logon no armazenamento usando a chave pública fornecida antes das etapas de configuração e os dados configurados em HANABackupCustomerDetails.txt. Se for bem-sucedido, será retornado o seguinte conteúdo:

```
**********************Checking access to Storage**********************
Storage Access successful!!!!!!!!!!!!!!
```

Se ocorrerem problemas ao se conectar ao console do armazenamento, a saída será semelhante a:

```
**********************Checking access to Storage**********************
WARNING: Storage check status command 'volume show -type RW -fields volume' failed: 65280
WARNING: Please check the following:
WARNING: Was publickey sent to Microsoft Service Team?
WARNING: If passphrase entered while using tool, publickey must be re-created and passphrase must be left blank for both entries
WARNING: Ensure correct IP address was entered in HANABackupCustomerDetails.txt
WARNING: Ensure correct Storage backup name was entered in HANABackupCustomerDetails.txt
WARNING: Ensure that no modification in format HANABackupCustomerDetails.txt like additional lines, line numbers or spacing
WARNING: ******************Exiting Script*******************************
```

Após um logon bem-sucedido nas interfaces de máquina virtual de armazenamento, o script deverá continuar com a fase 2 e criar um instantâneo de teste, conforme mostrado aqui, para uma configuração de expansão de três nós do SAP HANA:

```
**********************Creating Storage snapshot**********************
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_data_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_dp ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_backups_hm3_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00001_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00002_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_log_hm3_mnt00003_t020_vol ...
Snapshot created successfully.
Taking snapshot testStorage.recent for hana_shared_hm3_t020_vol ...
Snapshot created successfully.
```

Se o instantâneo de teste foi executado com êxito com o script, você poderá continuar com a configuração de instantâneos de armazenamento reais. Se não houver êxito, investigue os problemas antes de prosseguir. O instantâneo de teste criado deve permanecer até que os primeiros instantâneos sejam concluídos.



### <a name="step-7-perform-snapshots"></a>Etapa 7: Executar instantâneos

Como todas as etapas de preparação foram concluídas, você pode começar a configurar a configuração de instantâneo de armazenamento real. O script a ser agendado funciona com as configurações de expansão e escalável do SAP HANA. A ideia é que você agende a execução de scripts por meio de cron. 

Há três tipos de backups de instantâneos que podem ser feitos:
- HANA: backup de instantâneo combinado no qual os volumes que contêm /hana/data, /hana/log, /hana/shared (que contém também /usr/sap) são cobertos pelo instantâneo coordenado. Uma restauração de arquivo único é possível desse instantâneo.
- Logs: backup de instantâneo do volume hana/logbackups. Nenhum instantâneo HANA é acionado para executar esse instantâneo de armazenamento. Esse volume de armazenamento é o volume que deve conter os backups de log de transações SAP HANA, que são executados com mais frequência para restringir o crescimento do log e evitar a perda potencial de dados. Uma restauração de arquivo único é possível desse instantâneo. Você não deve diminuir a frequência para menos de cinco minutos.
- Inicialização: o instantâneo do volume que contém o LUN de inicialização da Instância Grande HANA. Esse backup de instantâneo só é possível com os SKUs tipo I das Instâncias Grandes HANA. Você não pode executar restaurações de arquivo único do instantâneo do volume que contém o LUN de inicialização.  


A sintaxe de chamada para esses três tipos diferentes de instantâneos seria semelhante a:
```
HANA backup covering /hana/data, /hana/log, /hana/shared (includes/usr/sap)
./azure_hana_backup.pl hana <HANA SID> manual 30

For /hana/logbackups snapshot
./azure_hana_backup.pl logs <HANA SID> manual 30

For snapshot of the volume storing the boot LUN
./azure_hana_backup.pl boot manual 30

```

Os parâmetros a seguir precisam ser especificados:

- O primeiro parâmetro a ser especificado caracteriza o tipo de backup de instantâneo. Como valores permitidos, temos 'hana', 'logs' e 'boot'
- O segundo valor é o SID do HANA (como HM3). Esse parâmetro não é necessário para executar um backup do volume de inicialização.
- O terceiro parâmetro é um instantâneo que é o rótulo de backup para o tipo de instantâneo. Espera-se que ele permaneça o mesmo para instantâneos agendados de um tipo específico ou frequência.
- O quarto parâmetro define a retenção dos instantâneos indiretamente definindo o número de instantâneos com o mesmo prefixo de instantâneo (rótulo) que será mantido. Esse parâmetro é importante para a execução agendada por meio de cron. 

No caso de expansão, o script realiza verificações adicionais para garantir que todos os servidores HANA sejam acessados e todas as instâncias HANA retornem o status apropriado das instâncias antes de prosseguir com a criação de instantâneo do SAP HANA que é seguido por um instantâneo de armazenamento.

A execução do script azure\_hana\_backup.pl cria o instantâneo de armazenamento nessas três fases distintas:

- Execute um instantâneo do SAP HANA.
- Execute um instantâneo do armazenamento.
- Remova o instantâneo do SAP HANA que foi criado antes da execução do instantâneo de armazenamento.

Execute o script chamando-o na pasta executável HDB para a qual ele foi copiado. 

O período de retenção é administrado, com o número de instantâneos enviado como um parâmetro ao executar o script (como 30, mostrado acima). Assim, a quantidade de tempo coberta pelos instantâneos de armazenamento é uma função do período de execução e o número de instantâneos enviado como parâmetro ao executar o script. Se o número de instantâneos que é mantido exceder o número identificado como parâmetro na chamada do script, o instantâneo de armazenamento mais antigo do mesmo rótulo (no caso anterior, _manual_) será excluído antes de executar um novo instantâneo. Isso significa que o número que você conceder como o último parâmetro da chamada é o número que você pode usar para controlar o número de instantâneos que é mantido. Com isso, você também pode controlar indiretamente o espaço em disco usado para instantâneos. 

>Como alterar o rótulo, a contagem começa novamente. Significa que você precisa ser estrito nos rótulos.

### <a name="snapshot-strategies"></a>Estratégias de instantâneo
A frequência de instantâneos para os diferentes tipos depende de se você usa a funcionalidade de recuperação de desastres de Instância Grande HANA ou não. Uma vez que a funcionalidade de recuperação de desastre de Instâncias Grandes HANA depende do instantâneo de armazenamento, algumas recomendações especiais podem ser aplicadas em termos de períodos de frequência e execução de instantâneos de armazenamento. Nas considerações e recomendações a seguir, vamos supor que você NÃO usa a funcionalidade de recuperação de desastres que as Instâncias Grandes HANA estão oferecendo. Em vez disso, você usa os instantâneos de armazenamento para ter backups e ser capaz de fornecer recuperação pontual nos últimos 30 dias. Devido às limitações do número de instantâneos e espaço, os clientes implantados consideraram os seguintes tipos de requisitos:

- Requisitos de objetivo de tempo de recuperação para recuperação pontual.
- Uso de espaço.
- Requisitos do objetivo de ponto de recuperação e objetivo de tempo de recuperação de recuperação de desastre em potencial
- Execução eventual de backups completos de banco de dados do HANA em discos. Sempre que um backup completo do banco de dados em discos ou da interface _backint_, for realizado, a execução de instantâneos de armazenamento falhará. Se você planeja executar backups de banco de dados completos sobre instantâneos de armazenamento, verifique se a execução de instantâneos de armazenamento está desabilitada durante esse momento.
- O número de instantâneos por volume é limitado a 255.


Para clientes que não usam a funcionalidade de recuperação de desastres de Instâncias Grandes HANA, o período de instantâneo é menos frequente. Nesses casos, podemos ver clientes executando os instantâneos combinados em /hana/data, /hana/log, /hana/shared (inclui /usr/sap) em período de 12 horas ou 24 horas e manter esses instantâneos para abranger um mês inteiro. O mesmo acontece com os instantâneos do volume de backup de log. Enquanto isso, a execução de backups de log de transações do SAP HANA no volume de backup de log é executada em 5 minutos para o período de 15 minutos.

Recomendamos que você realize os instantâneos de armazenamento usando cron, além de usar o mesmo script para todas as necessidades de backup e recuperação de desastres (modificando as entradas do script para corresponder aos diferentes tempos de backup solicitados). Esses instantâneos são todos agendados de forma diferente no cron dependendo de seu tempo de execução: por hora, 12 horas, diária ou semanal. 

Um exemplo de um agendamento de cron no /etc/crontab seria semelhante ao seguinte:
```
00 1-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 46
10 00 * * *  ./azure_hana_backup.pl hana HM3 dailyhana 28
00,05,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
22 12 * * *  ./azure_hana_backup.pl log HM3 dailylogback 28
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```
No exemplo acima, há um instantâneo combinado por hora que aborda os volumes que contêm os locais /hana/data, /hana/log, /hana/shared (inclui /usr/sap). Esse tipo de instantâneo será usado para recuperação pontual mais rápida dentro de dois dias. Além disso, há um instantâneo diário nesses volumes. Assim, você tem dois dias de cobertura por instantâneos por hora além de quatro semanas de cobertura por instantâneos diários. Além disso, o volume de backup de log de transações tem seu backup feito uma vez por dia. Esses backups também são mantidos por quatro semanas. Como você pode ver na terceira linha de crontab, o backup do log de transações HANA está agendado para execução a cada cinco minutos. Você também pode observar que os minutos de início dos trabalhos de cron diferentes que executam os instantâneos de armazenamento são alternados, assim, os instantâneos não são executados simultaneamente em um determinado ponto no tempo. 

No exemplo abaixo, você executaria um instantâneo combinado que aborda os volumes que contêm os locais /hana/data, /hana/log, /hana/shared (inclui /usr/sap) por hora. Você manteria esses instantâneos por cerca de dois dias. Os instantâneos dos volumes de backup de log de transações são executados em uma base de cinco minutos e são mantidos por quatro horas. Como antes, o backup do arquivo de log de transação do HANA está agendado para ser executado a cada cinco minutos. O instantâneo do volume de backup de log de transações é executado com um atraso de dois minutos após o backup de log de transações ser iniciado. Dentro desses dois minutos, o backup de log de transações do SAP HANA deverá ser concluído em circunstâncias normais. Como antes, o volume que contém o LUN de inicialização tem seu backup feito uma vez por dia por um instantâneo de armazenamento e é mantido por quatro semanas.

```
10 0-23 * * * ./azure_hana_backup.pl hana HM3 hourlyhana 48
0,5,10,15,20,25,30,35,40,45,50,55 * * * *  Perform SAP HANA transaction log backup
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup.pl log HM3 logback 48
30 00 * * *  ./azure_hana_backup.pl boot dailyboot 28
```

Ilustradas em um gráfico, as sequências do exemplo acima, exceto o LUN de inicialização, seriam semelhantes a:

![Relação entre os backups e os instantâneos](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot.PNG)

O SAP HANA está executando gravações regulares no volume /hana/log para documentar as alterações confirmadas no banco de dados. Regularmente, o SAP HANA grava um ponto de salvamento para o volume /hana/data. Conforme especificado em crontab, um backup de log de transações do SAP HANA é executado a cada cinco minutos. Você também verá um instantâneo do SAP HANA sendo executado a cada hora, como resultado do acionamento de um instantâneo de armazenamento combinado do volume /hana/data, /hana/log e /hana/shared. Depois do êxito do instantâneo do HANA, o instantâneo de armazenamento combinado é executado. Conforme instruído no crontab a cada cinco minutos, aproximadamente dois minutos após o backup de log de transações do HANA, o instantâneo de armazenamento no volume /hana/logbackup é executado.


>[!IMPORTANT]
> O uso de instantâneos de armazenamento para backups do SAP HANA só é válido quando os instantâneos são executados junto com backups de log de transações do SAP HANA. Esses backups de log de transações precisam ser capazes de abordar os períodos de tempo entre os instantâneos de armazenamento. 

Se você firmou um compromisso com os usuários de uma recuperação pontual de 30 dias, será necessário o seguinte:

- Capacidade de acessar um instantâneo de armazenamento combinado em over/hana/data, /hana/log e /hana/shared que tem, no caso extremo, 30 dias.
- Backups de log de transações contíguos que abrangem o tempo entre todos os instantâneos de armazenamento combinado. Então o instantâneo mais antigo do volume de backup de log de transações deve ser de 30 dias. A menos que você tenha copiado os backups de log de transações em outro compartilhamento NFS que está localizado no armazenamento do Azure. Nesse caso, você pode efetuar pull de backups de log de transações antigos desse compartilhamento NFS.

Para poder se beneficiar de instantâneos de armazenamento e replicação de armazenamento eventual dos backups de log de transações, você precisa alterar o local no qual o SAP HANA grava os backups de log de transações. Essa alteração pode ser feita no HANA Studio conforme mostrado abaixo. Embora o SAP HANA esteja fazendo backup de segmentos de log completos automaticamente, você deve especificar um intervalo de backup de log para ser determinístico. Especialmente ao usar a opção de recuperação de desastres, você geralmente desejará executar backups de log com um período determinístico. No caso abaixo, usamos 15 minutos como o intervalo de backup de log.

![Agendar logs de backup SAP HANA no Studio de SAP HANA](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Você pode escolher os backups com frequência maior que 15 minutos. Principalmente em conjunto com a recuperação de desastres. Alguns clientes executam backups de log de transações a cada cinco minutos.  

Se o banco de dados nunca teve backup feito, a etapa final será executar um backup de banco de dados com base no arquivo para criar uma entrada de backup única que deve existir no catálogo de backup. Caso contrário, SAP HANA não pode iniciar os backups de log indicados.

![Faça um backup baseado em arquivo para criar uma única entrada de backup](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Após seus primeiros instantâneos de armazenamento serem executados com êxito, você também poderá excluir o instantâneo de teste que foi executado na etapa 6. Para fazer isso, você precisa executar o script removeTestStorageSnapshot.pl, como mostrado abaixo:
```
./removeTestStorageSnapshot.pl <hana instance>
```

### <a name="monitoring-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Monitoramento do número e tamanho dos instantâneos no volume de disco

Em um volume de armazenamento específico, você pode monitorar o número de instantâneos e o consumo de armazenamento desses instantâneos. O comando `ls` não mostra o diretório de instantâneo ou arquivos. No entanto, o comando de sistema operacional Linux `du` mostra detalhes sobre esses instantâneos de armazenamento, pois eles são armazenados nos mesmos volumes. O comando pode ser usado com as seguintes opções:

- `du –sh .snapshot` apresenta o total de todos os instantâneos dentro do diretório de instantâneo.
- `du –sh --max-depth=1` lista todos os instantâneos que são salvos na pasta '.snapshot' e o tamanho de cada um deles.
- `du –hc` fornece o tamanho total usado por todos os instantâneos.

Use estes comandos para verificar se os instantâneos foram obtidos e armazenado não estão consumindo todo o armazenamento nos volumes.

>[!NOTE]
>Os instantâneos do LUN de inicialização não são visíveis com os comandos acima.

### <a name="getting-details-of-snapshots"></a>Obter detalhes de instantâneos
Para obter mais detalhes dos instantâneos, você também pode usar o script azure\_hana\_snapshot\_details.pl. Esse script poderá ser executado em qualquer local se houver um servidor ativo no local de recuperação de desastres. O script fornece as informações a seguir divididas por cada volume que contém os instantâneos: o tamanho dos instantâneos totais em um volume e, em seguida, para cada instantâneo desse volume os detalhes de: nome do instantâneo, horário de criação, tamanho do instantâneo, frequência do instantâneo e a ID de Backup do HANA associada a esse instantâneo (se relevante). A sintaxe de execução do script é semelhante a:

```
./azure_hana_snapshot_details.pl 
```

Como o script tenta recuperar a ID de backup do HANA, ele precisa se conectar à instância do SAP HANA e, portanto, requer que o arquivo de configuração HANABackupCustomerDetails.txt seja definido corretamente. Uma saída de dois instantâneos de um volume pode ser semelhante a:

```
**********************************************************
****Volume: hana_shared_SAPTSTHDB100_t020_vol       ***********
**********************************************************
Total Snapshot Size:  411.8MB
----------------------------------------------------------
Snapshot:   customer.2016-09-20_1404.0
Create Time:   "Tue Sep 20 18:08:35 2016"
Size:   2.10MB
Frequency:   customer 
HANA Backup ID:   
----------------------------------------------------------
Snapshot:   customer2.2016-09-20_1532.0
Create Time:   "Tue Sep 20 19:36:21 2016"
Size:   2.37MB
Frequency:   customer2
HANA Backup ID:   
```


### <a name="file-level-restore-from-storage-snapshot"></a>Restauração no nível do arquivo do instantâneo de armazenamento
Para os tipos de instantâneo 'hana' e 'logs', é possível acessar os instantâneos diretamente nos volumes no diretório '.snapshot'. Você pode encontrar um subdiretório para cada um dos instantâneos. Você deve ser capaz de copiar cada arquivo que foi coberto pelo instantâneo no estado que ele tinha no ponto do instantâneo do subdiretório na estrutura de diretório real.

>[!NOTE]
>A restauração de arquivo único não funciona para instantâneos do LUN de inicialização. O diretório '.snapshot' não é exposto no LUN de inicialização. 


### <a name="reducing-the-number-of-snapshots-on-a-server"></a>Reduzindo o número de instantâneos em um servidor

Conforme explicado anteriormente, você pode reduzir o número armazenado de certos rótulos de instantâneos. Os dois últimos parâmetros do comando para iniciar um instantâneo são um rótulo e o número de instantâneos que você deseja manter.

```
./azure_hana_backup.pl hana HM3 hanadaily 30
```

No exemplo anterior, o rótulo de instantâneo é _cliente_ e o número de instantâneos com este rótulo a ser retido é _30_. Ao responder ao consumo de espaço em disco, pode ser útil reduzir o número de instantâneos armazenados. A maneira fácil de reduzir o número de instantâneos para, por exemplo, 15, é executar o script com o último parâmetro definido como 15:

```
./azure_hana_backup.pl hana HM3 hanadaily 15
```

Como resultado da execução do script com essa configuração, o número de instantâneos, incluindo o novo instantâneo de armazenamento, é _15_. Os 15 instantâneos mais recentes são mantidos enquanto os 15 instantâneos mais antigos são excluídos.

 >[!NOTE]
 > Esse script reduzirá o número de instantâneos somente se houver instantâneos com mais de uma hora. O script não excluir instantâneos que com menos de uma hora de idade. Essas restrições estão relacionadas à funcionalidade de recuperação de desastre opcional oferecida.

Se você não deseja manter um conjunto de instantâneos com um rótulo de backup específico (hanadaily nos exemplos de sintaxe), você pode executar o script com _0_ como o número de retenção para remover todos os instantâneos que correspondem ao rótulo. Entretanto, remover todos os instantâneos pode afetar os recursos de recuperação de desastre.

Uma segunda possibilidade para excluir instantâneos específicos é usar o script azure\_hana\_snapshot\_delelte.pl. Esse script foi desenvolvido para excluir um instantâneo ou um conjunto de instantâneos usando a ID de backup do estúdio HANA como encontrada no HANA Studio ou pelo nome do instantâneo.  Atualmente, a ID de backup só está vinculada aos instantâneos criados para o tipo de instantâneo 'hana'. Backups de instantâneo do tipo 'logs' e 'boot' não executam um instantâneo do SAP HANA. Portanto, não há nenhuma ID de backup a ser localizada para esses instantâneos. Se o nome do instantâneo for inserido, ele procurará todos os instantâneos em volumes diferentes que correspondam ao nome do instantâneo inserido. A sintaxe de chamada do script é:

```
./azure_hana_snapshot_delete.pl 

```

Você precisa executar o script como usuário _raiz_

Se selecionar um instantâneo, você terá a capacidade de excluir cada instantâneo individualmente. É solicitado, primeiramente, o volume que contém o instantâneo e, em seguida, o nome do instantâneo atual. Se o instantâneo existir no volume e tiver mais de uma hora, ele será excluído. Você pode encontrar os nomes de volume e nomes de instantâneo executando o script azure_hana_snapshot_details. 

>[!IMPORTANT]
>Se houver dados que existirem somente no instantâneo que você está excluindo, a execução da exclusão significará que os dados serão perdidos para sempre.

   

### <a name="recovering-to-the-most-recent-hana-snapshot"></a>Recuperando para o instantâneo de HANA mais recente

No caso de ocorrer um cenário de inatividade na produção, o processo de recuperação de um instantâneo de armazenamento poderá ser iniciado como um incidente do cliente com suporte do Microsoft Azure. Um cenário inesperado é um caso de alta urgência se os dados foram excluídos em um sistema de produção e a única maneira de recuperá-los é restaurar o banco de dados de produção.

Em um caso diferente, uma recuperação pontual pode ter baixa urgência e ser planejada com dias de antecedência. Você pode planejar essa recuperação com o SAP HANA no Gerenciamento de Serviços do Azure em vez de gerar um problema de alta prioridade. Por exemplo, você pode tentar realizar uma atualização do software SAP aplicando um novo pacote de melhorias e, em seguida, precisa reverter para um instantâneo que representa o estado antes da atualização de EHP.

Antes de emitir a solicitação, você precisa fazer algumas preparações. O SAP HANA na equipe de Gerenciamento de Serviços do Azure poderá manipular a solicitação e fornecer os volumes restaurados. Depois, caberá a você restaurar o banco de dados do HANA com base em instantâneos. Veja aqui como se preparar para a solicitação:

>[!NOTE]
>A interface do usuário pode variar de capturas de tela seguintes, dependendo da versão do SAP HANA usada.

1. Decida qual instantâneo para restaurar. Somente o volume hana/data será restaurado, salvo instrução em contrário. 

2. Desligar a instância do HANA.

 ![Desligar a instância do HANA](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

3. Desmonte os volumes de dados em cada nó do banco de dados do HANA. Se os volumes de dados ainda estiverem montados para o sistema operacional, a restauração do instantâneo falhará.
 ![Desmontar os volumes de dados em cada nó de banco de dados do HANA](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

4. Abra uma solicitação de suporte do Azure para instruir a restauração de um instantâneo específico.

 - Durante a restauração: o SAP HANA no Gerenciamento de Serviços do Azure pode solicitar que você participe de uma chamada em conferência para garantir a coordenação, a verificação e a confirmação para certificar-se de que o instantâneo de armazenamento correto será restaurado. 

 - Depois da restauração: o SAP HANA no Gerenciamento de Serviços do Azure será notificado quando o instantâneo de armazenamento tiver sido restaurado.

5. Depois que o processo de restauração for concluído, monte novamente todos os volumes de dados.

 ![Montar novamente todos os volumes de dados](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)

6. Selecione opções de recuperação no SAP HANA Studio, caso elas não apareçam automaticamente ao reconectar ao HANA DB por meio do SAP HANA Studio. O exemplo a seguir mostra uma restauração para o último instantâneo HANA. Um instantâneo do armazenamento incorpora um instantâneo do HANA e, se você estiver restaurando para o instantâneo de armazenamento mais recente, ele deverá ser o instantâneo mais recente do HANA. (Se você está restaurando os instantâneos de armazenamento mais antigos, você precisa localizar o instantâneo do HANA baseado na hora em que o armazenamento de instantâneo foi tirado).

 ![Selecionar as opções de recuperação no SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image10-recover-options-a.png)

7. Selecione **Recuperar o banco de dados para um instantâneo de backup ou armazenamento de dados específico**.

 ![A janela “Especificar o tipo de recuperação”](./media/hana-overview-high-availability-disaster-recovery/image11-recover-options-b.png)

8. Selecione **Especificar backup sem catálogo**.

 ![A janela “Especificar o local de backup”](./media/hana-overview-high-availability-disaster-recovery/image12-recover-options-c.png)

9. Na lista **Tipo de Destino**, selecione **Instantâneo**.

 ![A janela “Especificar o Backup a ser Recuperado”](./media/hana-overview-high-availability-disaster-recovery/image13-recover-options-d.png)

10. Clique em **Concluir** para iniciar o processo de recuperação.

 ![Clique em “Concluir” para iniciar o processo de recuperação](./media/hana-overview-high-availability-disaster-recovery/image14-recover-options-e.png)

11. O banco de dados do HANA é restaurado e recuperado para o instantâneo HANA incluído pelo instantâneo de armazenamento.

 ![O banco de dados do HANA é restaurado e recuperado para o instantâneo do HANA](./media/hana-overview-high-availability-disaster-recovery/image15-recover-options-f.png)

### <a name="recovering-to-the-most-recent-state"></a>Recuperando para o estado mais recente

O processo a seguir restaura um instantâneo HANA que foi incluído no instantâneo de armazenamento. Ele restaura os backups de log de transação para o estado mais recente do banco de dados antes de restaurar o instantâneo de armazenamento.

>[!IMPORTANT]
>Antes de continuar, verifique se você tem uma cadeia completa e contígua de backups de log de transações. Sem esses backups, você não poderá restaurar o estado atual do banco de dados.

1. Conclua as etapas 1 a 6 do procedimento anterior em “Recuperando o instantâneo mais recente do HANA”.

2. Selecione **Recuperar o banco de dados para seu estado mais recente**.

 ![Selecione “Recuperar o banco de dados para seu estado mais recente”](./media/hana-overview-high-availability-disaster-recovery/image16-recover-database-a.png)

3. Especifique o local dos backups de log mais recentes do HANA. O local especificado deve conter todos os backups de log de transações do instantâneo HANA para o estado mais recente.

 ![Especifique o local dos backups de log mais recentes do HANA](./media/hana-overview-high-availability-disaster-recovery/image17-recover-database-b.png)

4. Selecione um backup como uma base recuperar o banco de dados. Em nosso exemplo, o instantâneo HANA na captura de tela, é o instantâneo HANA que foi incluído no instantâneo de armazenamento. 

 ![Selecione um backup como uma base recuperar o banco de dados](./media/hana-overview-high-availability-disaster-recovery/image18-recover-database-c.png)

5. Desmarque a caixa de seleção **Usar Backups Delta** se não existirem deltas entre a hora do instantâneo do HANA e o estado mais recente.

 ![Desmarque a caixa de seleção “Usar Backups de Delta” se nenhum delta existir](./media/hana-overview-high-availability-disaster-recovery/image19-recover-database-d.png)

6. Na tela de resumo, clique em **Concluir** para iniciar o procedimento de restauração.

 ![Clique em “Concluir” na tela de resumo](./media/hana-overview-high-availability-disaster-recovery/image20-recover-database-e.png)

### <a name="recovering-to-another-point-in-time"></a>Recuperar para outro ponto no tempo
Para recuperar para um ponto de tempo entre o instantâneo HANA (incluído no instantâneo de armazenamento) e um posterior àquele da recuperação pontual, faça o seguinte:

1. Certifique-se de que você tenha todas as dos backups do log de transações do instantâneo do HANA até o momento em que você deseja recuperar.
2. Inicie o procedimento em “Recuperando para o estado mais recente”.
3. Na etapa 2 do procedimento, na janela **Especificar o Tipo de Recuperação**, selecione **Recuperar o banco de dados para o seguinte ponto**, especifique o ponto em questão e conclua as etapas 3 a 6.

### <a name="monitoring-the-execution-of-snapshots"></a>Monitorando a execução de instantâneos

À medida que você usa instantâneos de armazenamento de Instâncias Grandes HANA, você também precisará monitorar a execução desses instantâneos de armazenamento. O script que executa um instantâneo de armazenamento grava a saída em um arquivo e salva no mesmo local que os scripts Perl. Um arquivo separado é criado para cada instantâneo de armazenamento. A saída de cada arquivo mostra claramente as várias fases de execução do script de instantâneo:

- Localizando os volumes necessários para criar um instantâneo
- Localizando os instantâneos tirados desses volumes
- Excluindo instantâneos existentes eventuais para corresponder ao número de instantâneos que você especificou
- Criação de um instantâneo SAP HANA
- Criação do instantâneo de armazenamento sobre os volumes
- Exclusão de um instantâneo SAP HANA
- Renomear o instantâneo mais recente para **'.0'**

A parte mais importante do cab de script é identificada como essa parte:
```
**********************Creating HANA snapshot**********************
Creating the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data create snapshot"" ...
HANA snapshot created successfully.
**********************Creating Storage snapshot**********************
Taking snapshot hourly.recent for hana_data_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_backup_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_log_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for hana_shared_lhanad01_t020_vol ...
Snapshot created successfully.
Taking snapshot hourly.recent for sapmnt_lhanad01_t020_vol ...
Snapshot created successfully.
**********************Deleting HANA snapshot**********************
Deleting the HANA snapshot with command: "./hdbsql -n localhost -i 01 -U SCADMIN01 "backup data drop snapshot"" ...
HANA snapshot deletion successfully.
```
Você pode ver a este exemplo como o script registros a criação do instantâneo HANA. No caso de escala horizontal, esse processo é iniciado no nó principal. O nó mestre iniciará a criação síncrona de instantâneos SAP HANA em cada um de nós de trabalho. Em seguida, o instantâneo de armazenamento é criado. Após a execução bem-sucedida de instantâneos de armazenamento, o instantâneo do HANA será excluído. Iniciada novamente no nó mestre.


## <a name="disaster-recovery-principles"></a>Princípios da recuperação de desastres
Com Instâncias Grandes HANA, nós oferecemos uma funcionalidade de recuperação de desastres entre stamps de Instância Grande HANA em diferentes regiões do Azure. Isso significa que se você implantar unidades de Instância Grande HANA na região do Azure do Oeste dos EUA, você poderá usar unidades de Instância Grande HANA na região Leste dos EUA como unidades de recuperação de desastres. Como mencionado anteriormente, a recuperação de desastres não está configurada automaticamente porque requer que você também pague por outra unidade de Instância Grande HANA na região de recuperação de desastres. A configuração de recuperação de desastres funciona para configurações de expansão, bem como escaláveis. 

Nos cenários de implantação até agora, os clientes usaram a unidade na região de recuperação de desastres para executar os sistemas de não produção que usam uma instância instalada do HANA. A unidade de Instância Grande HANA deve ser da mesma SKU que a SKU usada para fins de produção. Você precisa solicitar mais volumes de disco para ter esses volumes de disco usados como destinos para a replicação de armazenamento do sistema de produção no local de recuperação de desastres. Os volumes, que estão sendo replicados para o local de recuperação de desastres, abrangem:

- /hana/data
- /hana/log
- /hana/shared
- /hana/log/backup (inclui /usr/sap)


A base da funcionalidade de recuperação de desastres oferecida é a funcionalidade de replicação de armazenamento oferecida pela infraestrutura de Instância Grande HANA. A funcionalidade usada no lado do armazenamento não é um fluxo constante de alterações que são replicadas de forma assíncrona conforme as alterações ocorrem no armazenamento. Em vez disso, é um mecanismo que depende do fato de que os instantâneos são criados com regularidade desses volumes. O delta entre um instantâneo já replicado e um novo instantâneo ainda não replicado é transferido para o local de recuperação de desastres em volumes de disco de destino (mesmo tamanho que os volumes de produção). A primeira vez em que os dados completos de um volume precisam ser transferidos antes da quantidade de dados está se tornando menor com deltas entre instantâneos. Como resultado, os volumes no local de recuperação de desastres contêm todos os instantâneos de volume executados no local de produção. Esse fato permite que você use eventualmente esse sistema de recuperação de desastres para obter um status anterior para recuperar dados perdidos sem reverter o sistema de produção.

Em casos em que você usa Replicação de Sistema HANA como funcionalidade de Alta Disponibilidade no seu site de produção, apenas os volumes da instância da Camada 2 (ou réplica) são replicados. Essa configuração pode resultar em um efeito, que ser um atraso na replicação de armazenamento para o local de recuperação de desastres se você mantiver ou levar a unidade do servidor de réplica secundária (camada 2) ou a instância do SAP HANA nesta unidade. 

>[!IMPORTANT]
>Assim como acontece com Replicação de Sistema do HANA de múltiplas camadas, um desligamento da unidade de instância ou servidor de camada 2 do HANA bloqueará a replicação para o local de recuperação de desastres ao usar a funcionalidade de recuperação de desastres de Instância Grande HANA.


>[!NOTE]
>A funcionalidade de replicação de armazenamento de Instância Grande HANA está espelhando/replicando os instantâneos de armazenamento. Portanto, se você não executar instantâneos de armazenamento, conforme apresentado na seção de backup deste documento, não poderá haver qualquer replicação para o local de recuperação de desastre. A execução do instantâneo de armazenamento é um pré-requisito para a replicação de armazenamento para o local recuperação de desastres.

Para minimizar o Objetivo de Ponto de Recuperação, os intervalos de replicação a seguir são definidos no serviço de Instância Grande HANA:
- Os volumes cobertos pelo instantâneo combinado (tipo de instantâneo = 'hana') são replicados a cada 15 minutos para os destinos de volume de armazenamento equivalentes no local de recuperação de desastres.
- O volume de backup de log de transações (tipo de instantâneo = 'logs') é replicado a cada 3 minutos para os destinos de volume de armazenamento equivalentes no local de recuperação de desastres.

Para minimizar o Objetivo de Ponto de Recuperação, as seguintes condições devem ser configuradas por você
- Execute um instantâneo de armazenamento do tipo 'hana' (consulte a etapa 7 – Executar instantâneos) a cada 30 minutos a uma hora.
- Execute backups de log de transações do SAP HANA a cada cinco minutos.
- Execute um tipo 'logs' de instantâneo de armazenamento a cada 5-15 minutos. Com esse período, você deve ser capaz de alcançar um RPO de aproximadamente 15 a 25 minutos.

Com essa configuração, a sequência de backups de log de transações, instantâneos de armazenamento e replicação do volume do backup de log de transações do HANA e /hana/data, /hana/log, /hana/shared (inclui usr/sap) poderiam ser semelhantes ao mostrado neste gráfico.

 ![Relação entre o instantâneo de backup de log de transações e o espelho de ajuste no eixo de tempo](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Para alcançar um melhor objetivo de ponto de recuperação, no caso de recuperação de desastre, você pode copiar os backups de log de transações do HANA do SAP HANA no Azure (instâncias grandes) e para outra região do Azure. Para obter essa redução adicional de objetivo de ponto de recuperação, execute as seguintes etapas estimadas:

1. Faça backup do log de transações do HANA com a maior frequência possível para /hana/logbackups.
2. Copie os backups de log de transações, usando rsync, quando eles tiverem concluído o compartilhamento de NFS de VMs (Máquinas Virtuais) do Azure hospedadas, que estão nas redes virtuais do Azure na região de produção do Azure e nas regiões de recuperação de desastres. Você precisa se conectar a ambas as VNets do Azure para o circuito ExpressRoute conectando as Instâncias Grandes HANA de produção ao Azure (consulte os gráficos na seção: 'Requisitos de recuperação de desastres' deste documento). 
3. Mantenha os backups de log de transações nessa região da VM anexada ao armazenamento exportado de NFS.
4. No caso de failover de desastre, complemente os backups de log de transações que encontrar no volume /hana/logbackups com backups de log de transações mais recentes no compartilhamento de NFS no local de recuperação de desastres. 
5. Agora você pode iniciar uma restauração de backup de log de transações para o backup mais recente que foi salvo na região de recuperação de desastres.

## <a name="disaster-recovery-fail-over-procedure"></a>Procedimento de failover de recuperação de desastres
Caso queira ou precise fazer failover para o local de recuperação de desastres, você examinará um processo em que você precisa interagir com o SAP HANA nas Operações do Azure. Nas etapas estimadas, o processo até agora se parece com:

- Como você está executando uma instância de não produção do HANA na unidade de recuperação de desastres de Instâncias Grandes HANA, será necessário desligar esta instância. Vamos supor que há uma instância de produção do HANA inativa pré-instalada.
- Você precisa garantir que não há mais nenhum processo do SAP HANA em execução. Você pode fazer isso com o comando: /usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> – função GetProcessList. A saída agora deve mostrar a você o processo hdbdaemon em um estado interrompido e nenhum outro processo do HANA em estado de execução ou iniciado.
- Agora você precisa verificar a qual nome de instantâneo ou ID de backup do SAP HANA você deseja ter a recuperação de desastres restaurada. Em casos de recuperação de desastres reais, esse instantâneo é geralmente o instantâneo mais recente. No entanto, você pode estar em uma situação em que você precisa recuperar dados perdidos. Portanto, você precisa escolher um instantâneo anterior.
- Você precisa entrar em contato com o SAP HANA no Gerenciamento de Serviços do Azure por meio de uma solicitação de suporte de alta prioridade e solicitar a restauração desse instantâneo/ID de backup HANA no local de recuperação de desastres. No lado das operações, as etapas a seguir vão acontecer:
    - A replicação de instantâneos do volume de produção para volumes de recuperação de desastres será interrompida. ainda não aconteceu causado pelo motivo da interrupção do local de produção.
    - O instantâneo de armazenamento/ID de backup que você escolheu está sendo restaurado nos volumes de recuperação de desastres.
    - Após a restauração, os volumes de recuperação de desastres ficam disponíveis para serem montados nas unidades de Instância Grande HANA na região de recuperação de desastres.
- A próxima etapa no lado do cliente é montar os volumes de recuperação de desastres para a unidade de Instância Grande HANA no local de recuperação de desastres. 
- Em seguida, você pode iniciar a instância de produção SAP HANA inativa até o momento.
- Se optar por copiar logs de backup de log de transações para reduzir o tempo RPO, você precisará mesclar esses backups de log de transações no diretório /hana/logbackups da recuperação de desastres montado recentemente. Não substitua um backup existente, apenas copie os mais novos que não tenham sido replicados pelo armazenamento.

A seguinte sequência de etapas envolve a recuperação da instância de produção SAP HANA com base no instantâneo de armazenamento restaurado e nos backups de log de transações que estão disponíveis. as etapas se parecem com:

Altere o local de backup para /hana/logbackups usando o SAP HANA Studio conforme mostrado abaixo ![Alterar o local de backup para recuperação de desastres](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

O SAP HANA verifica os locais do arquivo de backup e sugere o backup de log de transações mais recente para ser restaurado. A verificação pode levar alguns minutos até que uma tela, como mostrado abaixo, seja mostrada: ![Lista de backups de log de transações para a recuperação de desastre](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

Na próxima etapa, você precisa ajustar algumas configurações padrão ao:

- Desmarcar **Usar Backups Delta**
- Selecionar **Inicializar Área de Log**, conforme mostrado abaixo:

 ![Definir Inicializar Área de Log](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

Na próxima tela, pressione **Concluir** conforme mostrado abaixo:

 ![Concluir a restauração de recuperação de desastres](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Uma janela de progresso, como mostrado aqui, deve aparecer. Tenha em mente que o exemplo é de uma restauração de recuperação de desastre de uma configuração do SAP HANA de expansão de 3 nós.

 ![Progresso da restauração](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Em casos em que a restauração parece parar de responder na tela 'Concluir' e não é ativada com a tela de progresso, verifique se todas as instâncias do SAP HANA em nós de trabalho estão em execução. Se necessário, inicie manualmente as instâncias SAP HANA.


### <a name="fail-back-from-dr-to-production-site"></a>Failback de recuperação de desastres para o local de produção
Vamos supor que o failover para o local de recuperação de desastre foi causado por problemas na região de produção do Azure e não por você exigir o retorno de dados perdidos. Isso significa que estava em produção ou no local de recuperação de desastres. Conforme são resolvidos os problemas no local de produção, você deseja realizar failback no local de produção. Como você não pode perder dados, a etapa no local de produção envolve várias etapas e cooperação com o SAP HANA em Operações do Azure. É você como um cliente que deve disparar operações para iniciar a sincronização no local de produção depois que os problemas forem resolvidos.

A sequência de etapas é semelhante a:

- O SAP HANA nas Operações do Azure obtém o gatilho para sincronizar os volumes de armazenamento de produção dos volumes de armazenamento de recuperação de desastres, que estão representando o estado de produção agora. Nesse estado, a unidade de Instância Grande HANA no local de produção é desligada.
- O SAP HANA nas Operações do Azure monitora a replicação e assegura que uma atualização é obtida antes de informar você como um cliente
- Você precisa fechar os aplicativos, que usam a instância de produção do HANA no local de recuperação de desastres. Em seguida, execute um backup de log de transações do HANA e, em seguida, interrompa a instância HANA em execução nas unidades de Instância Grande HANA no local de recuperação de desastres
- Depois que a instância do HANA em execução na unidade de Instância Grande HANA no local de recuperação de desastres é desligada, operação precisa sincronizar manualmente os volumes de disco novamente.
- O SAP HANA nas Operações do Azure iniciará a unidade de Instância Grande HANA no site de produção novamente e passar para você. Certifique-se de que a instância do SAP HANA está em um estado de desligamento no tempo de inicialização da unidade de Instância Grande HANA.
- Agora você precisa executar o mesmo etapas de restauração de banco de dados como fez no failover para o local de recuperação de desastres anteriormente.

### <a name="monitoring-disaster-recovery-replication"></a>Monitoramento de replicação de recuperação de desastres

É possível monitorar o status de seu progresso de replicação de armazenamento por meio da execução do script azure\_hana\_replication\_status.pl. Esse script deve ser executado de uma unidade em execução no local de recuperação de desastres, caso contrário ele funcionará conforme o esperado. O script funciona independentemente de a replicação estar ativa. O script pode ser executado para cada unidade de Instância Grande HANA do seu locatário no local de recuperação de desastres. Ele não pode ser usado para obter detalhes sobre o volume de inicialização.

Chame o script assim:
```
./replication_status.pl <HANA SID>
```

A saída é dividida por volume nas seguintes seções:  

- Status do Link
- Atividade de Replicação Atual
- Instantâneo Mais Recente Replicado 
- Tamanho do Instantâneo Mais Recente
- Tempo de retardo atual entre a última a replicação de instantâneo concluída e agora.  

O status do link é mostrado como ‘Ativo’ a menos que o link entre os locais esteja inativo ou se um evento de failover estiver em andamento. A atividade de replicação endereça se dados estão sendo replicados, se estão ociosos ou se outras atividades estão ocorrendo no momento para o link. O último instantâneo replicado deve ser exibido apenas como 'snapmirror...'. O tamanho do último instantâneo é exibido. Por fim, o tempo de retardo é mostrado. O tempo de retardo representa o tempo desde o tempo da replicação agendada até o momento em que replicação é concluída. Um tempo de retardo pode mostrar maior do que uma hora para a replicação de dados, especialmente para a primeira replicação inicial, mesmo que a replicação já tenha sido iniciada. O tempo de retardo continuará aumentando até que a replicação em andamento seja concluída.

Um exemplo de uma saída pode parecer com:

```
hana_data_hm3_mnt00002_t020_dp
-------------------------------------------------
Link Status: Broken-Off
Current Replication Activity: Idle
Latest Snapshot Replicated: snapmirror.c169b434-75c0-11e6-9903-00a098a13ceb_2154095454.2017-04-21_051515
Size of Latest Snapshot Replicated: 244KB
Current Lag Time between snapshots: -   ***Less than 90 minutes is acceptable***
```












