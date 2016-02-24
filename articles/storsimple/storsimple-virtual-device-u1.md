<properties 
   pageTitle="Dispositivo virtual StorSimple Atualização 1 | Microsoft Azure"
   description="Saiba como criar, implantar e gerenciar um dispositivo virtual StorSimple em uma rede virtual do Microsoft Azure. (Aplica-se ao StorSimple Atualização 1)."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="12/01/2015"
   ms.author="alkohli" />

# Implantar e gerenciar um dispositivo virtual StorSimple no Azure

[AZURE.INCLUDE [storsimple-version-selector-sva](../../includes/storsimple-version-selector-sva.md)]

##Visão geral
O dispositivo virtual StorSimple é um recurso adicional que acompanha a sua solução Microsoft Azure StorSimple. O dispositivo virtual StorSimple é executado em uma máquina virtual em uma rede virtual do Microsoft Azure e você pode usá-lo para fazer backup e clonar dados de seus hosts. Os tópicos a seguir o ajudarão a conhecer, a configurar e a usar o dispositivo virtual StorSimple.

- Como o dispositivo virtual difere do dispositivo físico

- Considerações de segurança para usar um dispositivo virtual

- Pré-requisitos para o dispositivo virtual

- Criar e configurar o dispositivo virtual

- Trabalhar com o dispositivo virtual

- Failover para o dispositivo virtual

- Desligar ou excluir o dispositivo virtual

Este tutorial se aplica a todos os dispositivos virtuais StorSimple executando a Atualização 1.

## Como o dispositivo virtual difere do dispositivo físico

O dispositivo virtual StorSimple é uma versão somente de software do StorSimple, que é executado em um único nó em uma Máquina Virtual do Microsoft Azure. O dispositivo virtual dá suporte a cenários de recuperação de desastres em que o seu dispositivo físico não está disponível e é adequado para uso em cenários de recuperação no nível do item de back-ups, recuperação de desastres local e desenvolvimento e de teste de nuvem.

### Diferenças do dispositivo físico

A seguir, algumas das principais diferenças entre o dispositivo virtual StorSimple e o dispositivo StorSimple físico:

- O dispositivo virtual tem apenas uma interface de rede: DADOS 0. O dispositivo físico tem seis interfaces de rede: DADOS 0 a DADOS 5.
- O dispositivo virtual é registrado durante a etapa de configuração em vez de ser registrado como uma tarefa separada.
- A chave de criptografia de dados de serviço não pode ser regenerada desde o dispositivo virtual. Durante a sobreposição da chave, você vai regenerar a chave no dispositivo físico e então atualizar o dispositivo virtual com a nova chave.
- Se for necessário aplicar atualizações ao dispositivo virtual, ele experimentará um certo tempo de inatividade enquanto o dispositivo físico não.

## Considerações de segurança para usar um dispositivo virtual

Tenha as seguintes considerações de segurança em mente ao usar o dispositivo virtual StorSimple:

- O dispositivo virtual é protegido por meio de sua assinatura do Microsoft Azure. Isso significa que, se você estiver usando o dispositivo virtual e se sua assinatura do Azure estiver comprometida, os dados armazenados em seu dispositivo virtual também estarão suscetíveis.

- A chave pública do certificado usado para criptografar os dados armazenados no Azure StorSimple foi disponibilizada com segurança para o portal clássico do Azure e a chave privada é mantida com o dispositivo StorSimple. No dispositivo virtual StorSimple, as chaves públicas e privadas são armazenadas no Azure.

- O dispositivo virtual é hospedado no datacenter do Microsoft Azure.


## Pré-requisitos para o dispositivo virtual

As seções a seguir ajudarão você a se preparar para usar o dispositivo virtual StorSimple.

### Requisitos do Azure

Antes de provisionar o dispositivo virtual, você precisará fazer as seguintes preparações no seu ambiente do Azure:

- Para o dispositivo virtual, [configure uma rede virtual no Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md). 
- É aconselhável utilizar o servidor DNS padrão fornecido pelo Azure em vez de especificar o nome do seu próprio servidor DNS. Se o nome do servidor DNS não for válido, haverá falha na criação do dispositivo virtual.
- Ponto a site e site a site são opcionais, mas não obrigatórios. Se desejar, você pode configurar essas opções para cenários mais avançados. 

>[AZURE.IMPORTANT]**Verifique se a rede virtual está na mesma região das contas de armazenamento de nuvem que você pretende usar com o dispositivo virtual.**

- É possível criar [Máquinas Virtuais do Azure](../virtual-machines/virtual-machines-about.md) (servidores de host) na rede virtual que possam usar os volumes expostos pelo dispositivo virtual. Esses servidores devem atender aos seguintes requisitos: 							
	- Ser VMs do Windows ou do Linux com software Iniciador iSCSI instalado
	- Estar em execução na mesma rede virtual que o dispositivo virtual
	- Ser capaz de se conectar ao destino iSCSI do dispositivo virtual por meio do endereço IP interno do dispositivo virtual

- Verifique se você configurou o suporte para tráfego iSCSI e de nuvem na mesma rede virtual.

### Requisitos do StorSimple

Faça as seguintes atualizações para o seu serviço do Azure StorSimple antes de criar um dispositivo virtual:


- Adicione [registros de controle de acesso](storsimple-manage-acrs.md) às máquinas virtuais que serão os servidores de host para seu dispositivo virtual.

- Certifique-se de que tem uma [conta de armazenamento](storsimple-manage-storage-accounts.md#add-a-storage-account) na mesma região do dispositivo virtual. Contas de armazenamento em regiões diferentes podem resultar em baixo desempenho.

- Certifique-se de usar uma conta de armazenamento para a criação do dispositivo virtual diferente da usada para seus dados. O uso da mesma conta de armazenamento pode resultar em baixo desempenho.

Certifique-se de ter as seguintes informações antes de começar:


- Você tem sua conta do portal clássico do Azure com credenciais de acesso.

- Você tem sua conta de armazenamento do Azure com credenciais de acesso.

- Você tem uma cópia da chave de criptografia de dados de serviço do seu dispositivo físico.

- Você tem uma cópia da chave de criptografia do armazenamento em nuvem para cada contêiner de volume.


## Criar e configurar o dispositivo virtual

Antes de executar esses procedimentos, certifique-se de que você atendeu aos [pré-requisitos para o dispositivo virtual](#prerequisites-for-the-virtual-device).

Depois de concluir estes procedimentos, você estará pronto para [Trabalhar com o dispositivo virtual](#work-with-the-StorSimple-virtual-device).

### Criar o dispositivo virtual

Depois de ter criado uma rede virtual, configurado um serviço Gerenciador do StorSimple e registrado seu dispositivo StorSimple físico no serviço, você poderá usar as seguintes etapas para criar um dispositivo virtual StorSimple.

Execute as seguintes etapas para criar o dispositivo virtual StorSimple.

1.  No portal clássico do Azure, vá até o serviço **StorSimple Manager**.

2. Vá para a página **Dispositivos**. Clique em **Criar dispositivo virtual** na parte inferior da página **Dispositivos**.

3. Na caixa de diálogo **Criar Dispositivo Virtual**, especifique os detalhes a seguir.

     ![Dispositivo virtual de criação StorSimple](./media/storsimple-virtual-device-u1/StorSimple_CreateVirtualDevice1.png)

	1. **Nome** – um nome exclusivo para seu dispositivo virtual.

	2. **Versão** - escolha a versão do dispositivo virtual. Esta opção não estará disponível se você tiver apenas dispositivos físicos com a Atualização 1 (ou acima) registrados com esse serviço. Esse campo é exibido somente se você tiver uma combinação de dispositivos físicos pré-atualização 1 e pós-atualização 1 registrados com o serviço. A versão do dispositivo virtual determinará em qual dispositivo físico você pode aplicar o failover ou clonar. É importante que você crie uma versão apropriada do dispositivo virtual. Selecione:

	   - Versão da Atualização 0.3 se você for realizar o failover ou clone de um dispositivo físico com a versão GA ou com as Atualizações 0.1 a 0.3. 
	   - Versão da Atualização 1 se você for realizar o failover ou clone de um dispositivo físico com a Atualização 1 (ou acima). Escolher a Atualização 1 na lista suspensa provisionará um dispositivo virtual de Atualização 1.1.
 
	3. **Rede virtual** – o nome da rede virtual que você deseja usar com este dispositivo virtual.

	4. **Sub-rede** – a sub-rede na rede virtual para uso com o dispositivo virtual.

	5. **Conta de Armazenamento para Criação de Dispositivo Virtual** – esta conta de armazenamento será usada para armazenar a imagem do dispositivo virtual durante o provisionamento e hospedará os discos do dispositivo virtual após o provisionamento. Esta conta de armazenamento deve estar na mesma região do dispositivo virtual e da rede virtual. Ela não deve ser usada para armazenamento de dados, o dispositivo físico ou o dispositivo virtual. Por padrão, uma nova conta de armazenamento será criada para essa finalidade. No entanto, se você souber que você já tem uma conta de armazenamento adequada para esse uso, poderá selecioná-la da lista.

    >[AZURE.NOTE]O dispositivo virtual só pode trabalhar com as contas de armazenamento do Azure. Outros provedores de serviços de nuvem, como a Amazon, HP e OpenStack (que têm suporte para o dispositivo físico) não têm suporte para o dispositivo virtual StorSimple.
	
4. Clique na marca de seleção para indicar que você entende que os dados armazenados no dispositivo virtual serão hospedados em um datacenter da Microsoft. Quando você usa apenas um dispositivo físico, sua chave de criptografia é mantida com seu dispositivo; portanto, a Microsoft não pode descriptografá-la.![Estágio de criação do dispositivo virtual StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceCreating1M.png)

    Quando você usa um dispositivo virtual, a chave de criptografia e a chave de descriptografia são armazenadas no Microsoft Azure. Para saber mais, consulte [Considerações de segurança para usar um dispositivo virtual](#security-considerations-for-using-a-virtual-device).

### Configurar e registrar o dispositivo virtual

Antes de iniciar este procedimento, verifique se você tem uma cópia da chave de criptografia de dados de serviço. A chave de criptografia de dados do serviço foi criada quando você configurou o primeiro dispositivo StorSimple e foi orientado a salvá-la em um local seguro. Se você não tiver uma cópia da chave de criptografia dos dados de serviço, deverá contatar o Suporte da Microsoft para obter assistência.

Execute as seguintes etapas para configurar e registrar o dispositivo virtual StorSimple

1. Selecione o **Dispositivo virtual StorSimple** que acabou de criar na página Dispositivos. 

2. Clique em **concluir a instalação do dispositivo**. Isso inicia o assistente Configurar dispositivo.

    ![Configuração completa do dispositivo StorSimple na página Dispositivos](./media/storsimple-virtual-device-u1/StorSimple_CompleteDeviceSetupSVA1M.png)

3. Insira a **Chave de Criptografia de Dados de Serviço** no espaço fornecido.

4. Insira as senhas do Snapshot Manager e do Administrador do Dispositivo com o comprimento e as configurações especificadas.

5. Clique na marca de seleção para concluir a configuração inicial e o registro do dispositivo virtual.

    ![Configurações do dispositivo virtual StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceSettings1.png)

Depois que a configuração e o registro forem concluídos, o dispositivo ficará online. (Pode levar vários minutos para que o dispositivo fique online.)

![Estágio online do dispositivo virtual StorSimple](./media/storsimple-virtual-device-u1/StorSimple_VirtualDeviceOnline1M.png)


### Modificar as configurações do dispositivo

A seção a seguir descreve as definições de configuração de dispositivo que você deseja configurar para o dispositivo virtual StorSimple se quiser usar CHAP, StorSimple Snapshot Manager ou alterar a senha do Administrador do Dispositivo.

#### Configurar o iniciador CHAP (opcional)

Este parâmetro contém as credenciais que o dispositivo virtual (destino) espera dos iniciadores (servidores) que estão tentando acessar os volumes. Os iniciadores fornecerão um nome de usuário CHAP e uma senha CHAP para identificá-los para o dispositivo durante essa autenticação.

#### Configurar o destino do CHAP (opcional)

Esse parâmetro contém as credenciais usadas pelo dispositivo virtual quando um iniciador compatível com CHAP solicita autenticação mútua ou bidirecional. Seu dispositivo virtual usará um nome de usuário CHAP Reverso e uma senha CHAP Reversa para identificar a si mesmo para o iniciador durante esse processo de autenticação. Observe que as configurações de destino de CHAP são configurações globais. Quando elas forem aplicadas, todos os volumes conectados ao dispositivo virtual de armazenamento usarão a autenticação CHAP. Selecione o dispositivo na página Dispositivos. Vá para a página Configurar dentro da página Dispositivos e role para baixo até encontrar a seção CHAP.

#### Configurar o StorSimple Snapshot Manager (opcional)

O software Gerenciador de Instantâneos StorSimple software reside no host Windows e permite que os administradores gerenciem backups do seu dispositivo StorSimple na forma de instantâneos locais e de nuvem.

>[AZURE.NOTE]Para o dispositivo virtual, o seu host do Windows é uma VM do Azure.

Ao configurar um dispositivo no Gerenciador de Instantâneos StorSimple, você deverá fornecer o endereço IP do dispositivo StorSimple e a senha para autenticar o dispositivo de armazenamento.

Realize as etapas a seguir para alterar a senha do StorSimple Snapshot Manager.

1. Em seu dispositivo virtual, vá para **Dispositivos > Configurar**.

2. Role para baixo até a seção **Gerenciador de Instantâneos**. Insira uma senha que tenha 14 ou 15 caracteres. Verifique se a senha contém três dentre estas quatro opções: caracteres maiúsculos, minúsculos, numéricos e especiais.

3. Confirme a senha.

4. Na parte inferior da página, clique em **Salvar**.

A senha do Gerenciador de Instantâneos StorSimple foi atualizada e poderá ser usada quando você autenticar seus hosts do Windows.

#### Alterar a senha de administrador do dispositivo

Quando você usar a interface do Windows PowerShell para acessar o dispositivo virtual, será solicitada a inserção de uma senha de administrador do dispositivo. Para a segurança de seus dados, será necessário alterar essa senha para que o dispositivo virtual possa ser usado.

Realize as etapas a seguir para alterar a senha de administrador do dispositivo para o dispositivo virtual StorSimple.

1. Em seu dispositivo virtual, vá para **Dispositivos > Configurar**.
 
2. Role para baixo até a seção **Senha do Administrador do Dispositivo**. Forneça uma senha do administrador que contenha de 8 a 15 caracteres. A senha deve conter três dentre estas quatro opções: caracteres maiúsculos, minúsculos, numéricos e especiais.

3. Confirme a senha.
 
4. Na parte inferior da página, clique em **Salvar**.

A senha do administrador do dispositivo agora deve estar atualizada. Você usará esta senha modificada para acessar a interface do Windows PowerShell em seu dispositivo virtual.

#### Configurar o gerenciamento remoto (opcional)

O acesso remoto ao seu dispositivo virtual por meio da interface do Windows PowerShell não está habilitado por padrão. Você precisa habilitar o gerenciamento remoto primeiro no dispositivo virtual e então habilitá-lo no cliente que será usado para acessar seu dispositivo virtual.

Você pode optar por conectar-se via HTTP ou HTTPS. Por motivos de segurança, recomendamos que você use HTTPS com um certificado autoassinado para se conectar ao seu dispositivo virtual.

Execute as etapas a seguir para configurar o gerenciamento remoto para seu dispositivo virtual StorSimple.


1. Em seu dispositivo virtual, vá para **Dispositivos > Configurar**.

2. Role para baixo até a seção **Gerenciamento Remoto**.

3. Definir **Habilitar o Gerenciamento Remoto** como **Sim**.

4. Agora, você pode optar por conectar-se usando HTTP. O padrão é conectar-se por HTTPS. A conexão por HTTP só será aceitável em redes confiáveis.

5. Clique em **Baixar Certificado de Gerenciamento Remoto** para baixar um certificado de gerenciamento remoto. Você especificará um local no qual o arquivo será salvo. Esse certificado precisa então ser instalado na máquina cliente ou host que você usará para se conectar ao dispositivo virtual.

6. Na parte inferior da página, clique em **Salvar**.


## Trabalhar com o dispositivo virtual StorSimple

Agora que você criou e configurado o dispositivo virtual StorSimple, está pronto para começar a trabalhar com ele. Você pode trabalhar com contêineres de volume, volumes e políticas de backup em um dispositivo virtual assim como faria em um dispositivo StorSimple físico; a única diferença é que você precisará certificar-se de selecionar o dispositivo virtual na sua lista de dispositivos. Consulte as seções a seguir para obter instruções sobre tarefas associadas:


- [Contêineres de volume](storsimple-manage-volume-containers.md)

- [Volumes](storsimple-manage-volumes.md)

- [Políticas de backup](storsimple-manage-backup-policies.md)

As seções a seguir discutem as diferenças que você encontrará ao trabalhar com o dispositivo virtual.

### Manter um dispositivo virtual StorSimple

Como é um dispositivo somente de software, a manutenção para o dispositivo virtual é mínima quando comparada à manutenção do dispositivo físico. Você tem as seguintes opções:

- **Atualizações de software** – você pode exibir a data em que o software foi atualizado pela última vez, junto com quaisquer mensagens de status de atualização. Você pode usar o botão **Verificar atualizações** na parte inferior da página para executar uma verificação manual em busca de novas atualizações. Se houver atualizações disponíveis, clique em **Instalar Atualizações** para instalá-las. Como há apenas uma única interface do dispositivo virtual, isso significa que haverá uma ligeira interrupção do serviço quando as atualizações forem aplicadas. O dispositivo virtual será automaticamente desligado e reiniciará (se for necessário) para aplicar todas as atualizações liberadas.
- **Pacote de suporte** – você pode criar e carregar um pacote de suporte para ajudar o Suporte da Microsoft a solucionar problemas com seu dispositivo virtual.

### Contas de armazenamento para um dispositivo virtual

As contas de armazenamento são criadas para uso pelo serviço Gerenciador do StorSimple, pelo dispositivo virtual e pelo dispositivo físico. Quando você cria suas contas de armazenamento, é recomendável que você use um identificador de região no nome amigável para ajudar a garantir que a região seja consistente em todos os componentes do sistema. Para um dispositivo virtual, é importante que todos os componentes estejam na mesma região para evitar problemas de desempenho.

### Desativar um dispositivo virtual StorSimple

Desativar um dispositivo virtual exclui a VM e os recursos criados quando ela foi provisionada. Depois que o dispositivo virtual for desativado, ele não poderá ser restaurado ao estado anterior. Antes de desativar o dispositivo virtual, pare ou exclua clientes e hosts que dependem dele.

Desativar um dispositivo virtual resulta nas seguintes ações:

- O dispositivo virtual é removido.

- O Disco do Sistema Operacional e os Discos de Dados Criados para o dispositivo virtual são removidos.

- O serviço hospedado e a rede virtual criados durante o provisionamento são mantidos. Se você não os estiver usando, deverá excluí-los manualmente.

- Instantâneos de nuvem criados para o dispositivo virtual são mantidos.

Assim que o dispositivo for mostrado como desativado na página do serviço Gerenciador do StorSimple, você poderá excluir o dispositivo virtual da lista de dispositivos do serviço Gerenciador do StorSimple.

### Acessar remotamente um dispositivo virtual StorSimple

Depois que você o tiver habilitado na página de configuração do dispositivo StorSimple, poderá usar o Windows PowerShell remotamente para conectar-se ao dispositivo virtual de outra máquina virtual dentro da mesma rede virtual; Por exemplo, você pode se conectar da VM host configurada e usada para conectar o iSCSI. Na maioria das implantações, você irá já terá aberto um ponto de extremidade público para acessar sua VM host que poderá ser usada para acessar o dispositivo virtual.

>[AZURE.WARNING]**Para maior segurança, é altamente recomendável que você use HTTPS durante a conexão com os pontos de extremidade e, em seguida, exclua os pontos de extremidade depois de concluir sua sessão remota do PowerShell.**

Você deve seguir os procedimentos em [Conectar-se remotamente ao seu dispositivo StorSimple](storsimple-remote-connect.md) para configurar a comunicação remota de seu dispositivo virtual.

No entanto, se você quiser se conectar diretamente ao dispositivo virtual de outro computador fora da rede virtual ou do ambiente do Microsoft Azure, precisará criar pontos de extremidade adicionais, como descrito no procedimento a seguir.

Execute as seguintes etapas para criar o dispositivo virtual StorSimple.

1. Entre no portal clássico do Azure.

- Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual que está sendo usada como seu dispositivo virtual.

- Clique em **Pontos de Extremidade**. A página de pontos de extremidade lista todos os pontos de extremidade para a máquina virtual.

- Clique em **Adicionar**. A caixa de diálogo Adicionar coadministrador é exibida. Clique na seta para continuar.

- Para o **nome**, digite o seguinte nome para o ponto de extremidade: **WinRMHttps**.

- Para o **Protocolo**, especifique **TCP**.

- Para a **Porta Pública**, digite os números de porta que você deseja usar para a conexão.

- Para a **Porta Privada**, digite **5986**.

- Clique na marca de seleção para salvar o ponto de extremidade.

Após a criação do ponto de extremidade, você poderá exibir seus detalhes para determinar o endereço IP Virtual Público (VIP). Registre esse endereço.

É recomendável que você se conecte de outra máquina virtual dentro da mesma rede virtual porque essa prática minimiza o número de pontos de extremidade públicos em sua rede virtual. Quando você usar esse método, simplesmente se conectará à máquina virtual por meio de uma sessão de Área de Trabalho Remota e configurará essa máquina virtual para uso como o faria com qualquer outro cliente do Windows em uma rede local. Você não precisa anexar o número da porta pública porque a porta já será conhecida.

### Parar e reiniciar

Ao contrário do dispositivo físico StorSimple, não há um botão liga/desliga para ser apertado em um dispositivo virtual StorSimple. No entanto, pode haver ocasiões em que você precisará parar e reiniciar o dispositivo virtual. Por exemplo, algumas atualizações podem exigir que a VM seja reiniciada para a conclusão do processo de atualização. A maneira mais fácil de iniciar, parar e reiniciar um dispositivo virtual é usar o Console de Gerenciamento de Máquinas Virtuais.

Quando você examinar o Console de Gerenciamento, o status do dispositivo virtual será **Em execução** porque ele é iniciado por padrão após sua criação. Você pode parar e reiniciar uma máquina virtual a qualquer momento.

Para parar um dispositivo virtual, clique em seu nome e, em seguida, clique em **Desligar**. Enquanto o dispositivo virtual estiver desligando, seu status será **Parando**. Depois que o dispositivo virtual parar, seu status será **Parado**.

Quando um dispositivo virtual estiver em execução e você quiser reiniciá-lo, clique em seu nome e, em seguida, clique em **Reiniciar**. Enquanto o dispositivo virtual estiver reiniciando, seu status será **Reiniciando**. Quando o dispositivo virtual estiver pronto para uso, seu status será **Em execução**.

Você também pode usar os seguintes cmdlets do Windows PowerShell para iniciar, parar e reiniciar o dispositivo virtual. Há um exemplo depois de cada cmdlet.

`Start-AzureVMC:\PS>Start-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`
    

`Stop-AzureVMC:\PS>Stop-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

`Restart-AzureVMC:\PS>Restart-AzureVM -ServiceName "MyStorSimpleservice1" -Name "MyStorSimpleDevice"`

### Redefinir para os padrões de fábrica

Se você decidir que deseja apenas recriar seu dispositivo virtual, simplesmente desative-o e exclua-o e então crie um novo. Assim como quando o dispositivo físico é redefinido, o novo dispositivo virtual não terá qualquer atualização instalada; portanto, verifique se há atualizações antes de usá-lo.


## Failover para o dispositivo virtual

A recuperação de desastres (RD) é um dos principais cenários para os quais o dispositivo virtual StorSimple foi projetado. Nesse cenário, o dispositivo físico StorSimple ou o datacenter inteiro podem não estar disponível. Felizmente, você pode usar um dispositivo virtual para restaurar operações em um local alternativo. Durante a RD, os contêineres de volume do dispositivo de origem alteram a propriedade e são transferidos para o dispositivo de destino. Os pré-requisitos para a RD são que o dispositivo virtual tenha sido criado e configurado, todos os volumes no contêiner de volume tenham sido colocados offline e o contêiner de volume tenha um de instantâneo de nuvem associado.

>[AZURE.NOTE]Você não pode realizar o failover ou clonar um dispositivo que executa a Atualização 1 para um dispositivo que executa um software anterior à Atualização 1. Se você selecionar um dispositivo de destino que esteja executando um software anterior à Atualização 1, receberá uma notificação de que precisa atualizar o dispositivo de destino antes de realizar o failover.

### Para restaurar seu dispositivo físico para o dispositivo virtual StorSimple

1. Verifique se o contêiner de volume para o qual você deseja fazer o failover associou instantâneos de nuvem.

2. Abra a página **Dispositivo** e então clique na guia **Contêineres de Volume**.

3. Selecione um contêiner de volume para o qual você gostaria de fazer failover para o dispositivo virtual. Clique no contêiner de volume para exibir a lista de volumes no contêiner. Selecione um volume e clique em **Colocar Offline** para colocar o volume offline. Repita este processo para todos os volumes no contêiner de volume.

4. Repita a etapa anterior para todos os contêineres de volume para os quais você gostaria de fazer o failover para outro dispositivo.

5. Na página **Dispositivo**, selecione o dispositivo para o qual você precisa fazer failover e, em seguida, clique em **Failover** para abrir o assistente **Failover de Dispositivo**.

6. Em **Escolher o contêiner de volume para failover**, selecione o volume para o qual você gostaria de fazer failover. Para ser exibido nesta lista, o contêiner de volume deve conter um instantâneo de nuvem e deve estar offline. Se um contêiner de volume que você esperava ver não estiver presente, cancele o assistente e verifique se ele está offline.

7. Na próxima página, em **Escolher um dispositivo de destino para os volumes**, nos contêineres selecionados, selecione o dispositivo virtual na lista suspensa de dispositivos disponíveis. Apenas os dispositivos com a capacidade disponível são exibidos na lista suspensa.

8. Examine todas as configurações de failover na página **Confirmar failover**. Se estiverem corretas, clique no ícone de seleção.

O processo de failover começará. Quando o failover estiver concluído, vá para a página Dispositivos e selecione o dispositivo virtual que foi usado como o destino para o processo de failover. Vá para a página Contêineres de Volume. Todos os contêineres de volume, juntamente com os volumes do antigo dispositivo, devem aparecer.

>[AZURE.NOTE]A quantidade de armazenamento com suporte no dispositivo virtual é de 30 TB.

## Desligar ou excluir o dispositivo virtual

Se você configurou previamente e usou um dispositivo virtual StorSimple, mas agora deseja parar de acumular encargos de computação para seu uso, poderá desligar o dispositivo virtual. Desligar o dispositivo virtual não exclui seu sistema operacional ou os discos de dados do armazenamento. Isso para o acúmulo de encargos em sua assinatura, mas os encargos de armazenamento para os discos de sistema operacional e de dados continuarão.

Se você excluir ou desligar o dispositivo virtual, ele será exibido como **Offline** na página Dispositivos do serviço Gerenciador do StorSimple. Você pode optar por desativá-lo ou por excluí-lo como um dispositivo e se também desejar excluir os backups criados pelo dispositivo virtual. Para obter mais informações, consulte [Desativar e excluir um dispositivo StorSimple](storsimple-deactivate-and-delete-device.md).

### Para desligar o dispositivo virtual StorSimple

1. Entre no portal clássico do Azure.

2. Clique em **Máquinas Virtuais** e então selecione o dispositivo virtual.

3. Clique em **Desligar**.

### Para excluir o dispositivo virtual StorSimple

1. Entre no portal clássico do Azure.

2. Clique em **Máquinas Virtuais** e então selecione o dispositivo virtual.

3. Clique em **Excluir** e opte por excluir todos os discos da máquina virtual.

## Próximas etapas

Saiba como [Restaurar um volume StorSimple a partir de um conjunto de backups](storsimple-restore-from-backup-set.md).

<!---HONumber=AcomDC_0121_2016-->