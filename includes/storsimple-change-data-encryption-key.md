<!--author=SharS last changed: 9/17/15-->

### Etapa 1: Autorizar um dispositivo para alterar a chave de criptografia de dados de serviço no Portal de Gerenciamento

Normalmente, o administrador do dispositivo solicita que o administrador do serviço autorize um dispositivo a alterar as chaves de criptografia de dados de serviço. O administrador do serviço então autoriza o dispositivo a alterar a chave.

Essa etapa é executada no Portal de Gerenciamento. O administrador do serviço pode selecionar um dispositivo em uma lista exibida de dispositivos que estão qualificados para serem autorizados. O dispositivo é então autorizado a iniciar o processo de alteração da chave de criptografia de dados de serviço.

#### Quais dispositivos podem ser autorizados a alterar as chaves de criptografia de dados de serviço?

Um dispositivo deve atender aos seguintes critérios para que possa ser autorizado a iniciar as alterações da chave de criptografia de dados de serviço:

- O dispositivo deve estar online para ser qualificado para autorização da alteração da chave de criptografia de dados de serviço.

- Você pode autorizar o mesmo dispositivo novamente após 30 minutos, caso a alteração de chave não tenha sido iniciada.

- Você pode autorizar um dispositivo diferente, desde que a alteração de chave não tenha sido iniciada pelo dispositivo autorizado anteriormente. Depois que o novo dispositivo tiver sido autorizado, o dispositivo antigo não poderá iniciar a alteração.

- Não é possível autorizar um dispositivo enquanto a substituição da chave de criptografia de dados de serviço estiver em andamento.

- Você pode autorizar um dispositivo quando alguns dos dispositivos registrados no serviço tiverem substituído a criptografia, enquanto outros não. Nesses casos, os dispositivos qualificados são aqueles que concluíram a alteração da chave de criptografia de dados de serviço.

> [AZURE.NOTE]No Portal de Gerenciamento, os dispositivos virtuais StorSimple não são mostrados na lista de dispositivos que podem ser autorizados a iniciar a alteração da chave.

Execute as etapas a seguir para selecionar e autorizar um dispositivo a iniciar a alteração da chave de criptografia de dados de serviço.

#### Para autorizar um dispositivo a alterar a chave

1. Na página do painel de serviço, clique em **Alterar chave de criptografia de dados de serviço**.

    ![Alterar chave de criptografia de serviço](./media/storsimple-change-data-encryption-key/HCS_ChangeServiceDataEncryptionKey-include.png)

2. Na caixa de diálogo **Alterar chave de criptografia de dados de serviço**, selecione e autorize um dispositivo a iniciar a alteração da chave de criptografia de dados de serviço. A lista suspensa apresenta todos os dispositivos qualificados que podem ser autorizados.

3. Clique no ícone de verificação ![ícone de verificação](./media/storsimple-change-data-encryption-key/HCS_CheckIcon-include.png).

### Etapa 2: Usar o Windows PowerShell para StorSimple para iniciar a alteração da chave de criptografia de dados de serviço

Essa etapa é executada na interface do Windows PowerShell para StorSimple no dispositivo StorSimple autorizado.

> [AZURE.NOTE]Nenhuma operação poderá ser executada no Portal de Gerenciamento do serviço StorSimple Manager até que a substituição da chave seja concluída.

Se você estiver usando o console serial do dispositivo para se conectar à interface do Windows PowerShell, execute as etapas a seguir.

#### Para iniciar a alteração da chave de criptografia de dados de serviço

1. Selecione a Opção 1 para fazer logon com acesso completo.

2. No prompt de comando, digite:

     `Invoke-HcsmServiceDataEncryptionKeyChange`

3. Depois que o cmdlet tiver sido concluído com êxito, você receberá uma nova chave de criptografia de dados de serviço. Copie e salve essa chave para uso na etapa 3 deste processo. Essa chave será usada para atualizar todos os dispositivos restantes registrados no serviço StorSimple Manager.

    > [AZURE.NOTE]Esse processo deve ser iniciado em quatro horas, a contar da autorização de um dispositivo StorSimple.

   Essa nova chave é então enviada ao serviço para ser enviada por push a todos os dispositivos que são registrados no serviço. Um alerta aparecerá no painel de serviço. O serviço desabilitará todas as operações nos dispositivos registrados e o administrador do dispositivo precisará atualizar a chave de criptografia de dados de serviço nos outros dispositivos. No entanto, as E/Ss (hosts que enviam dados à nuvem) não serão interrompidas.

   Se você tiver um único dispositivo registrado no serviço, o processo de substituição agora está concluído e a próxima etapa poderá ser ignorada. Se você tiver vários dispositivos registrados em seu serviço, passe para a etapa 3.

### Etapa 3: Atualizar a chave de criptografia de dados de serviço em outros dispositivos StorSimple

Essas etapas devem ser executadas na interface do Windows PowerShell de seu dispositivo StorSimple, caso você tenha vários dispositivos registrados no serviço StorSimple Manager. A chave que você obteve na Etapa 2: Usar o Windows PowerShell para StorSimple para iniciar a alteração da chave de criptografia de dados serviço deve ser usada para atualizar todos os demais dispositivos StorSimple registrados no serviço StorSimple Manager.

Execute as etapas a seguir para atualizar a criptografia de dados de serviço no seu dispositivo.

#### Para atualizar a chave de criptografia de dados de serviço

1. Use o Windows PowerShell para StorSimple para se conectar ao console. Selecione a Opção 1 para fazer logon com acesso completo.

2. No prompt de comando, digite:

    `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`

3. Forneça a chave de criptografia de dados de serviço que você obteve na [Etapa 2: Usar o Windows PowerShell para StorSimple para iniciar a alteração da chave de criptografia de dados de serviço](#to-initiate-the-service-data-encryption-key-change).

<!---HONumber=Sept15_HO3-->