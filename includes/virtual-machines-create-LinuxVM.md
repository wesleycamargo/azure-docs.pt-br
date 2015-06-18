1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com) do Azure. Na barra de comandos, clique em **Nova**.

2. Clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**.

3. Em **Escolher uma imagem**, selecione uma imagem de uma das listas. (As imagens disponíveis podem ser diferentes dependendo da assinatura que você estiver usando). Clique na seta para continuar.

4. Se várias versões da imagem estão disponíveis, na **Data de lançamento da versão**, selecione a versão que deseja usar.

5. Em **Nome da Máquina Virtual**, digite o nome que você deseja usar. Para esta máquina virtual, digite **MyTestVM1**.

6. Em **Tamanho**, selecione o tamanho que você deseja usar para a máquina virtual. O tamanho que você escolhe depende do número de núcleos que são necessários para o seu aplicativo. Para esta máquina virtual, escolha o menor tamanho disponível.

7. Em **Novo nome de usuário**, digite o nome da conta que você usará para administrar a máquina virtual. Você não pode usar a raiz para o nome de usuário. Para esta máquina virtual, digite **NewUser1**.

8. Em autenticação, verifique **Fornecer uma Senha**. Em seguida, forneça as informações necessárias e clique na seta para continuar.

9. Você pode colocar máquinas virtuais juntas no serviço de nuvem, mas para este tutorial, você está criando uma única máquina virtual. Para fazer isso, selecione **Criar um novo serviço de nuvem**.

10. Em **Nome DNS de serviço de nuvem**, digite um nome que usa entre 3 e 24 letras minúsculas e números. Você precisará sugerir o próprio nome do serviço de nuvem porque ele deve ser exclusivo no Azure. O nome do serviço de nuvem se torna parte do URI que é usado para entrar em contato com a máquina virtual por meio do serviço de nuvem.

11. Em **Região/Grupo de Afinidade/Rede Virtual**, selecione a região onde você deseja localizar a máquina virtual.

12. Você pode selecionar uma conta de armazenamento onde o arquivo VHD está armazenado. Para este tutorial, aceite a configuração padrão de **Usar uma conta de armazenamento gerada automaticamente**.

13. Em **Conjunto de disponibilidade**, para os fins deste tutorial usam-se a configuração padrão de **Nenhum**.

14.	Em **Pontos de extremidade**, examine o ponto de extremidade que é criado automaticamente para permitir conexões Secure Shell (SSH) para a máquina virtual. (Pontos de extremidade permitem que os recursos na Internet ou outras redes virtuais se comuniquem com uma máquina virtual.) Você pode adicionar mais pontos de extremidade agora ou criá-los mais tarde. Para obter instruções sobre como criá-las mais tarde, consulte [Como configurar pontos de extremidade para uma máquina virtual](../articles/virtual-machines/virtual-machines-set-up-endpoints.md).

15.  Em **Agente de VM**, veja as extensões disponíveis. Essas extensões oferecem diversos recursos que facilitam o uso e o gerenciamento de uma máquina virtual. Para obter detalhes, consulte [Extensões de VM do Azure](http://go.microsoft.com/FWLink/p/?LinkID=390493).


Após a criação da máquina virtual e do serviço de nuvem, o Portal de Gerenciamento lista a nova máquina virtual em **Máquinas Virtuais** e lista o serviço de nuvem em **Serviços de Nuvem**. A máquina virtual e o serviço de nuvem são iniciados automaticamente. <!--HONumber=52-->
