---
title: Como criar uma imagem de modelo personalizado para o Azure RemoteApp | Microsoft Docs
description: Saiba como criar uma imagem de modelo personalizado para o RemoteApp do Azure. Você pode usar este modelo com uma coleção de nuvem ou híbrida.
services: remoteapp
documentationcenter: ''
author: lizap
manager: mbaldwin
editor: ''

ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: elizapo

---
# Como criar uma imagem de modelo personalizado para o Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

O RemoteApp do Azure usa uma imagem do modelo do Windows Server 2012 R2 para hospedar todos os programas que deseja compartilhar com os seus usuários. Para criar uma imagem do modelo do RemoteApp personalizada, é possível iniciar com uma imagem existente ou criar uma nova.

> [!TIP]
> Você sabia que pode criar uma imagem de uma VM do Azure? É verdade, e isso reduz o tempo necessário para importar a imagem. Confira as etapas [aqui](remoteapp-image-on-azurevm.md).
> 
> 

Os requisitos para a imagem passiva de upload para o uso com o RemoteApp do Azure são:

* O tamanho da imagem dever ser um múltiplo de MBs. Se você tentar carregar uma imagem que não é um múltiplo exato, o carregamento falhará.
* O tamanho da imagem deve ser de 127 GB ou menor.
* Ela deve estar em um arquivo VHD (não há suporte atualmente para arquivos VHDX [discos rígidos virtuais Hyper-V]).
* O VHD não deve ser uma máquina virtual de geração 2.
* O VHD podem ter tamanho fixo ou expandir dinamicamente. O VHD de expansão dinâmica é recomendado, pois demora menos para fazer o upload do Azure que o arquivo VHD de tamanho fixo.
* O disco deve ser inicializado usando o estilo de particionamento do Registro mestre de inicialização (MBR). O estilo de particionamento da tabela de partição GUID (GPT) não tem suporte.
* O VHD deve conter uma instalação única do Windows Server 2012 R2. Ele deve conter vários volumes, mas apenas um que contenha uma instalação do Windows.
* A função Host da Sessão da Área de Trabalho Remota (RDSH) e o recurso Desktop Experience devem estar instalados.
* A função do Agente de Conexão de Área de Trabalho Remota *não* deve ser instalada.
* O Encrypting File System (EFS) deve estar desabilitado.
* A imagem deve ser SYSPREPed usando os parâmetros **/oobe /generalize /shutdown** (NÃO use o parâmetro **/mode:vm**).
* Não há suporte para carregar o VHD de uma cadeia de instantâneo.

**Antes de começar**

É necessário fazer o seguinte antes de criar o serviço:

* [Inscrever-se](https://azure.microsoft.com/services/remoteapp/) no RemoteApp.
* Crie uma conta de usuário no Active Directory para usar como a conta de serviço do RemoteApp. Restrinja as permissões para esta conta para que ela possa conectar-se somente às máquinas no domínio. Consulte [Configurar o Active Directory do Azure para o RemoteApp](remoteapp-ad.md) para obter mais informações.
* Colete informações sobre a sua rede local: informações sobre endereço IP e detalhes do dispositivo VPN.
* Instale o módulo [PowerShell do Azure](../powershell-install-configure.md).
* Colete informações sobre os usuários aos quais deseja conceder acesso. Podem ser informações da conta da Microsoft ou da conta corporativa do Active Directory para usuários ou grupos.

## Criar uma imagem do modelo
Estas são as etapas de alto nível para criar uma nova imagem do modelo do zero:

1. Localize um DVD de atualização do Windows Server 2012 R2 ou a imagem ISO.
2. Crie um arquivo VHD.
3. Instale o Windows Server 2012 R2.
4. Instale a função Host da Sessão da Área de Trabalho Remota (RDSH) e o recurso Desktop Experience.
5. Instale os recursos adicionais necessários pelos seus aplicativos.
6. Instale e configure os seus aplicativos. Para facilitar o compartilhamento de aplicativos, adicione os aplicativos ou programas que você deseja compartilhar ao menu **Iniciar** da imagem, especificamente em **%systemdrive%\\ProgramData\\Microsoft\\Windows\\Menu Iniciar\\Programas.
7. Execute quaisquer configurações adicionais do Windows necessárias para os seus aplicativos.
8. Desabilite o Encrypting File System (EFS).
9. **OBRIGATÓRIO:** vá para o Windows Update e instale todas as atualizações importantes.
10. SYSPREP a imagem.

As etapas detalhadas para a criação de uma nova imagem são:

1. Localize um DVD de atualização do Windows Server 2012 R2 ou a imagem ISO.
2. Crie um arquivo VHD usando o Disk Management.
   
   1. Inicialize o Disk Management (diskmgmt.msc).
   2. Crie um VHD de expansão dinâmica com 40 GB ou com tamanho maior. (Calcule a quantia de espaço necessário para o Windows, os seus aplicativos e as suas personalizações. O Windows Server com a função RDSH e o recurso Desktop Experience instalados precisará de cerca de 10 GB de espaço).
      
      1. Clique em **Ação > Criar VHD**.
      2. Especifique a localização, o tamanho e o formato do VHD. Selecione **Expansão dinâmica** e clique em **OK**.
      
      A execução demorará vários segundos. Quando a criação do VHD estiver concluída, será possível ver um novo disco sem qualquer letra da unidade e no estado “Não inicializado” no console do Disk Management.
      
      * Clique com o botão direito do mouse no disco (não no espaço não alocado) e, em seguida, clique em **Inicializar disco**. Selecione **MBR** (Registro mestre de iniciação) como o estilo de partição e, em seguida, clique em **OK**.
      * Crie um novo volume: clique com o botão direito no espaço não alocado e, em seguida, clique em **Novo Volume Simples**. É possível aceitar os padrões no assistente, mas certifique-se de que tenha designado uma letra da unidade para evitar problemas em potencial quando for fazer o upload da imagem do modelo.
      * Clique com o botão direito do mouse no disco e clique em **Desanexar VHD**.
3. Instalar o Windows Server 2012 R2:
   
   1. Criar uma nova máquina virtual. Use o Novo assistente da máquina virtual no Hyper-V Manager ou no Client Hyper-V.
      1. Na página Especificar Geração, escolha **Geração 1**.
      2. Na página Conectar o disco virtual, selecione **Usar um disco virtual existente** e procure pelo VHD criado na etapa anterior.
      3. Na página Opções de instalação, selecione **Instalar um sistema operacional de um CD/DVD\_ROM de inicialização** e, em seguida, selecione o local da mídia de instalação do seu Windows Server 2012 R2.
      4. Escolha outras opções necessárias no assistente para instalar o Windows e os seus aplicativos. Conclua o assistente.
   2. Após a conclusão do assistente, edite as configurações da máquina virtual e faça outras alterações necessárias para instalar o Windows e os seus programas, como o número de processadores virtuais, e clique em **OK**.
   3. Conecte-se à máquina virtual e instale o Windows Server 2012 R2.
4. Instale a função Host da Sessão da Área de Trabalho Remota (RDSH) e o recurso Desktop Experience:
   1. Inicialize o Gerenciador do Servidor.
   2. Clique em **Adicionar funções e recursos** na tela Bem-vindo ou do menu **Gerenciar**.
   3. Clique em **Avançar** na página Antes de começar.
   4. Selecione **Instalação baseada em função ou recurso** e clique em **Avançar**.
   5. Selecione o computador local na lista e clique em **Avançar**.
   6. Selecione **Serviços da Área de Trabalho Remota** e clique em **Avançar**.
   7. Expanda **Interfaces e Infraestrutura do Usuário** e selecione **Desktop Experience**.
   8. Clique em **Adicionar recursos** e clique em **Avançar**.
   9. Na página Serviços da Área de Trabalho Remota e clique em **Avançar**.
   10. Clique em **Host da Sessão da Área de Trabalho Remota**.
   11. Clique em **Adicionar recursos** e clique em **Avançar**.
   12. Na página Confirmar seleções de instalação, selecione **Reiniciar o servidor de destino automaticamente, se necessário** e, em seguida, clique em **Sim** no aviso de reinício.
   13. Clique em **Instalar**. O computador será reiniciado.
5. Instale os recursos adicionais necessários pelos seus aplicativos, como .NET Framework 3.5. Para instalar os recursos, execute Adicionar funções e Assistente de recursos.
6. Instale e configure os programas e os aplicativos que deseja publicar através do RemoteApp.

> [!IMPORTANT]
> Instale a função RDSH antes da instalação dos aplicativos para garantir que quaisquer problemas de compatibilidade do aplicativo sejam descobertos antes de fazer o upload da imagem no RemoteApp.
> 
> Verifique se um atalho para seu aplicativo (arquivo **.lnk**) aparece no menu **Iniciar** para todos os usuários (%systemdrive%\\ProgramData\\Microsoft\\Windows\\Menu Iniciar\\Programas). Além disso, verifique se o ícone visto no menu **Iniciar** é o que você deseja que os usuários vejam. Caso contrário, altere-o. (Você não *precisa* adicionar o aplicativo no menu Iniciar, mas ele torna muito mais fácil publicar o aplicativo no RemoteApp. Caso contrário, você precisa fornecer o caminho de instalação para o aplicativo ao publicá-lo.)
> 
> 

1. Execute quaisquer configurações adicionais do Windows necessárias para os seus aplicativos.
2. Desabilite o Encrypting File System (EFS). Execute o comando a seguir em uma janela de comandos com privilégios elevados:
   
     Fsutil behavior set disableencryption 1
   
   Alternativamente, é possível configurar ou adicionar o valor DWORD a seguir no Registro:
   
     HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
3. Se você estiver criando sua imagem dentro de uma máquina virtual do Azure, renomeie o arquivo **\\%windir%\\Panther\\Unattend.xml**, já que ele bloqueará o script de carregamento usado posteriormente no trabalho. Modifique o nome deste arquivo para Unattend.old para que você ainda tenha este arquivo no caso de precisar reverter sua implantação.
4. Vá para o Windows Update e instale todas as atualizações importantes. Talvez seja necessário executar o Windows Update várias vezes para obter todas as atualizações. (Às vezes, você pode instalar uma atualização e essa atualização em si requerer uma atualização.)
5. SYSPREP a imagem. Em um prompt de comandos com privilégios elevados, execute o seguinte comando:
   
   **C:\\Windows\\System32\\sysprep\\sysprep.exe /generalize /oobe /shutdown**
   
   **Nota:** Não use o comutador **/mode:vm** do comando SYSPREP, mesmo que seja uma máquina virtual.

## Próximas etapas
Agora que você tem a sua imagem de modelo personalizada, você precisará atualizar aquela imagem para sua coleção do RemoteApp. Use as informações nos artigos a seguir para criar sua coleção:

* [Como criar uma coleção híbrida do RemoteApp](remoteapp-create-hybrid-deployment.md)
* [Como criar uma coleção na nuvem do RemoteApp](remoteapp-create-cloud-deployment.md)

<!---HONumber=AcomDC_0817_2016-->