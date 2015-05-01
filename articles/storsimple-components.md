<properties 
   pageTitle = "Quais são os componentes do StorSimple?" 
   description="Descreve o dispositivo, os serviços e as tecnologias de gerenciamento do StorSimple". 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="AdinaH" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="02/17/2015"
   ms.author="v-sharos@microsoft.com"/>


# Quais são os componentes do StorSimple? 

Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrado que gerencia as tarefas de armazenamento entre os dispositivos local e o armazenamento em nuvem do Microsoft Azure.  O StorSimple foi projetado para reduzir os custos de armazenamento, simplificar o gerenciamento de armazenamento, melhorar a eficiência e a capacidade de recuperação de desastres e proporcionar mobilidade de dados.

As seções a seguir descrevem os componentes do Microsoft Azure StorSimple e explicam como a solução organiza dados, aloca armazenamento e facilita o gerenciamento de armazenamento e proteção de dados. 

> [AZURE.NOTE] As informações de implantação StorSimple publicadas no site do Microsoft Azure se aplicam apenas aos dispositivos das séries StorSimple 8000. Para obter informações sobre o dispositivo da série 7000, vá para: [Ajuda do StorSimple](http://onlinehelp.storsimple.com/).

## Dispositivo StorSimple

O dispositivo do Microsoft Azure StorSimple é uma matriz de armazenamento híbrido local que fornece armazenamento primário e acesso a iSCSI aos dados armazenados em outro lugar. Ele gerencia a comunicação com o armazenamento em nuvem e ajuda a garantir a segurança e a confidencialidade de todos os dados armazenados na solução Microsoft Azure StorSimple.

O dispositivo StorSimple inclui unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), bem como suporte para clustering e failover automático. Ele contém um processador compartilhado, armazenamento compartilhado e dois controladores espelhados. Cada controlador fornece o seguinte:

- Conexão com um computador host
- Até seis portas de rede para conexão à rede local (LAN)
- Monitoramento de hardware
- Memória de acesso aleatório não volátil (NVRAM), que mantém as informações mesmo que a alimentação seja interrompida
- Atualização com suporte a cluster para gerenciar atualizações de software em servidores em um cluster de failover para que as atualizações tenham pouco ou nenhum efeito sobre a disponibilidade do serviço
- Serviço de cluster, que funciona como um cluster de back-end, oferecendo alta disponibilidade e minimizando os efeitos adversos que podem ocorrer se um HDD ou SSD falhar ou ficar offline

Somente um controlador está ativo em qualquer momento no tempo. Se o controlador ativo falhar, o segundo controlador ficará ativo automaticamente. 

Para obter mais informações, consulte [Dispositivos StorSimple](https://msdn.microsoft.com/library/azure/dn772363.aspx).

## Dispositivo virtual StorSimple

Você pode usar o StorSimple para criar um dispositivo virtual que replique a arquitetura e os recursos do dispositivo de armazenamento híbrido real. 

O dispositivo virtual StorSimple (também conhecido como StorSimple Virtual Appliance) é executado em um único nó em uma máquina virtual do Azure. (Um dispositivo virtual só pode ser criado em uma máquina virtual do Azure. Você não pode criá-lo em um servidor local ou em um dispositivo StorSimple.) O dispositivo virtual StorSimple difere de um dispositivo StorSimple físico da seguinte maneira: 

- O dispositivo virtual tem apenas uma interface, enquanto o dispositivo físico tem seis interfaces de rede. 
- Você registra o dispositivo virtual durante a configuração do dispositivo, e não como uma tarefa separada.
- Você não pode regenerar a chave de criptografia de dados de serviço de um dispositivo virtual. Durante a substituição de chave, você regenera a chave no dispositivo físico e, em seguida, atualiza o dispositivo virtual com a nova chave.
- Se você precisar aplicar atualizações ao dispositivo virtual, terá algum tempo de inatividade. Isso não ocorre com um dispositivo StorSimple físico.

É recomendável que você use o dispositivo virtual StorSimple para cenários de recuperação de desastres em que um dispositivo físico não esteja disponível, como testes e pequenas implantações piloto.

Para obter mais informações, consulte [Dispositivo virtual StorSimple](https://msdn.microsoft.com/library/azure/dn772390.aspx).


## Tecnologias de gerenciamento de armazenamento
 
Além do dispositivo StorSimple dedicado e de um dispositivo virtual, o Microsoft Azure StorSimple usa as seguintes tecnologias de software para fornecer acesso rápido aos dados e reduzir o consumo de armazenamento:

- Camadas de armazenamento automático 
- Provisionamento dinâmico 
- Eliminação de duplicação e compactação 

### Camadas de armazenamento automático

O Microsoft Azure StorSimple organiza os dados em camadas lógicas com base no uso atual, na idade e na relação com outros dados. Dados mais ativos são armazenados localmente, enquanto dados menos ativos e inativos são migrados automaticamente para a nuvem. O diagrama a seguir ilustra essa abordagem de armazenamento.
 
![Camadas de armazenamento do StorSimple](./media/storsimple-components/hcs-data-services-storsimple-components-tiers.png)

**Camadas de armazenamento do StorSimple**

Para habilitar o acesso rápido, o StorSimple armazena dados muito ativos (dados mais acessados) em SSDs no dispositivo StorSimple. Ele armazena os dados que são usados ocasionalmente (dados menos acessados) em HDDs no dispositivo ou em servidores no data center. Ele move para a nuvem dados inativos, dados de backup e dados guardados para fins de arquivamento ou conformidade. 

O StorSimple ajusta e reorganiza as atribuições de dados e armazenamento conforme os padrões de uso mudam. Por exemplo, algumas informações poderão ficar menos ativas ao longo do tempo. Conforme ficam cada vez menos ativas, elas são migradas do SSD para o disco rígido e, em seguida, para a nuvem. Se esses mesmos dados ficarem ativos novamente, eles serão migrados para o dispositivo de armazenamento.

### Provisionamento dinâmico

Provisionamento dinâmico é uma tecnologia de virtualização em que o armazenamento disponível parece exceder os recursos físicos. Em vez de reservar armazenamento suficiente com antecedência, o StorSimple usa o provisionamento dinâmico para alocar espaço suficiente para atender às necessidades atuais. A natureza elástica de armazenamento em nuvem facilita essa abordagem porque o StorSimple pode aumentar ou diminuir o armazenamento em nuvem para atender às demandas de mudança. 

### Eliminação de duplicação e compactação

O Microsoft Azure StorSimple usa eliminação de duplicação e compactação de dados para reduzir ainda mais os requisitos de armazenamento.

A eliminação de duplicação reduz a quantidade geral de dados armazenados, eliminando a redundância no conjunto de dados armazenado. Conforme as informações mudam, o StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, o StorSimple reduz a quantidade de dados armazenados ao identificar e remover informações desnecessárias. 

## Windows PowerShell para StorSimple

O Windows PowerShell para StorSimple fornece uma interface de linha de comando que você pode usar para criar e gerenciar o serviço Microsoft Azure StorSimple e configurar e monitorar dispositivos do StorSimple. É uma interface de linha de comando com base no Windows PowerShell que inclui cmdlets dedicados para gerenciar seu dispositivo StorSimple. O Windows PowerShell para StorSimple tem recursos que permitem:

- Registrar um dispositivo.
- Configurar a interface de rede em um dispositivo.
- Instalar determinados tipos de atualizações.
- Solucionar problemas do dispositivo acessando a sessão de suporte.
- Alterar o estado do dispositivo.

Você pode acessar o Windows PowerShell para StorSimple em um console serial (em um computador host conectado diretamente ao dispositivo) ou remotamente usando o Windows PowerShell. Observe que algumas tarefas do Windows PowerShell para StorSimple, como registro de dispositivo inicial, só podem ser feitas no console serial. 

Para obter mais informações, consulte [Windows PowerShell para StorSimple](https://msdn.microsoft.com/library/azure/dn772425.aspx).

## Serviço StorSimple Manager

O Microsoft Azure StorSimple fornece uma interface do usuário baseada na web (o serviço StorSimple Manager) que permite gerenciar de modo centralizado o data center e o armazenamento em nuvem. Você pode usar o serviço StorSimple Manager para executar as seguintes tarefas:

- Definir configurações do sistema para dispositivos StorSimple.
- Configurar e gerenciar configurações de segurança para dispositivos StorSimple.
- Configurar credenciais e propriedades de nuvem.
- Configurar e gerenciar volumes em um servidor.
- Configurar grupos de volumes.
- Fazer backup e restaurar dados.
- Monitorar o desempenho.
- Revisar as configurações do sistema e identificar possíveis problemas.

Você pode usar o serviço StorSimple Manager para executar todas as tarefas de administração, exceto aquelas que exigem inatividade, como configuração inicial e instalação de atualizações do sistema.

Para obter mais informações, consulte [serviço StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx).

## Gerenciador de instantâneos do StorSimple

O Gerenciador de instantâneos do StorSimple é um snap-in do MMC (Console de Gerenciamento Microsoft) que você pode usar para criar cópias de backup consistentes de um ponto no tempo de dados locais e em nuvem. O snap-in é executado em um host baseado no Windows Server. Você pode usar o Gerenciador de instantâneos do StorSimple para:

- Configurar, fazer backup e excluir volumes.
- Configurar grupos de volumes para garantir que o backup dos dados seja consistente com o aplicativo.
- Gerenciar políticas de backup para que o backup dos dados seja feito em uma programação predeterminada e armazenado em um local designado (localmente ou na nuvem).
- Restaurar volumes e arquivos individuais.

Backups são capturados como instantâneos, que registram apenas as alterações desde o último instantâneo e exigem menos espaço de armazenamento do que os backups completos. Você pode criar agendas de backup ou fazer backups imediatos, conforme necessário. Além disso, você pode usar o Gerenciador de instantâneos do StorSimple para estabelecer políticas de retenção que controlem quantos instantâneos serão salvos. Se você precisar restaurar dados de um backup, o Gerenciador de instantâneos do StorSimple permite selecionar do catálogo de instantâneos locais ou em nuvem. 

Se ocorrer um desastre ou precisar restaurar dados por outro motivo, o Gerenciador de instantâneos do StorSimple restaurará de forma incremental conforme necessário. A Restauração de dados não requer que você desligue o sistema inteiro ao restaurar um arquivo, substituir equipamentos ou mover operações para outro site.

Para obter mais informações, consulte [Gerenciador de instantâneos do StorSimple](https://msdn.microsoft.com/library/azure/dn772365.aspx).

## Adaptador do StorSimple para SharePoint

O Microsoft Azure StorSimple inclui o adaptador do StorSimple para SharePoint, um componente opcional que estende transparentemente os recursos de proteção de dados e armazenamento do StorSimple para farms de servidores do SharePoint. O adaptador funciona com um provedor de RBS (Armazenamento de Blob Remoto) e o recurso RBS do SQL Server, permitindo que você mova os Blobs para um servidor com backup feito pelo sistema Microsoft Azure StorSimple. O Microsoft Azure StorSimple então armazena os dados BLOB localmente ou na nuvem, com base no uso.

O adaptador do StorSimple para SharePoint é gerenciado dentro do portal de Administração Central do SharePoint. Consequentemente, o gerenciamento do SharePoint permanece centralizado e todo o armazenamento parece estar no farm do SharePoint.

Para obter mais informações, consulte [Adaptador do StorSimple para SharePoint](https://msdn.microsoft.com/library/azure/dn757737.aspx). 


## Próximas etapas

Examine as [notas de versão do StorSimple](https://msdn.microsoft.com/library/azure/dn772367.aspx)






<!--HONumber=52-->