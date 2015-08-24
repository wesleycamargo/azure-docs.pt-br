
#### Para instalar a Atualização 1 por meio do Portal de Gerenciamento

1. Na página do serviço StorSimple, selecione seu dispositivo. Navegue até **Dispositivos** > **Manutenção**.

2. Na parte inferior da página, clique em **Verificar atualizações**. Será criado um trabalho para verificar se há atualizações disponíveis. Você será notificado quando o trabalho for concluído com êxito.

3. Na seção de **Atualizações de software** na mesma página, você verá que existem novas atualizações de software disponíveis. É recomendável que você revise as notas de versão antes de aplicar a Atualização 1.0 no seu dispositivo.

    ![Instalar as atualizações do software](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates1-include.png)

4. Na parte inferior da página, clique em **Instalar atualizações**.

5. Será solicitada a sua confirmação. Clique em **OK**.

6. Será apresentada uma caixa de diálogo **Instalar atualizações**. Certifique-se de que seu dispositivo satisfaça às verificações listadas nessa caixa de diálogo. Selecione **Eu entendi os requisitos acima e estou pronto para atualizar meu dispositivo**. Clique no ícone de verificação.

    ![Mensagem de confirmação](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates2-include.png)

7. Você será notificado de que as verificações de pré-atualização estão em andamento.
  
    ![Notificação de pré-verificação](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates3-include.png)

    Este é um exemplo em que a verificação de pré-atualização falhou. Será necessário verificar se os dois controladores do dispositivo estão em boas condições e online. Também será necessário verificar a integridade dos componentes de hardware. Neste exemplo, os componentes do Controlador 0 e do Controlador 1 precisam de atenção. Talvez seja necessário entrar em contato com o Suporte da Microsoft se você não conseguir resolver esses problemas por conta própria.

    ![Falha na pré-verificação](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

8. Depois que as verificações de pré-atualização tiverem sido concluídas com êxito, será criado um trabalho de atualização. Você será notificado quando o trabalho de atualização for criado com êxito.
 
    ![Criação do trabalho de atualização](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates4-include.png)

    Em seguida, a atualização será aplicada no seu dispositivo.
 
9. Para monitorar o andamento do trabalho de atualização, clique em **Exibir trabalho**. Na página de Trabalhos, você pode ver o progresso da atualização.

    ![Andamento do trabalho de atualização](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates5-include.png)

10. A atualização levará algumas horas para ser concluída. Você pode exibir os detalhes do trabalho a qualquer momento.

    ![Detalhes do trabalho de atualização](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates6-include.png)

11. Depois que o trabalho for concluída, acesse a página de **Manutenção** e role para baixo até **Atualizações de software**.

12. Verifique se o dispositivo está executando a **Atualização 1.0 do StorSimple Série 8000 (6.3.9600.17491)**. A **Data da última atualização** também deve ser modificada.

    ![Página de manutenção](./media/storsimple-install-update-via-portal/HCS_SoftwareUpdates7-include.png)

<!---HONumber=August15_HO7-->