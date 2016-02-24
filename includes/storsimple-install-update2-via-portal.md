<!--author=alkohli last changed: 01/26/16-->

#### Para instalar a Atualização 2 por meio do Portal do Azure

1. Na página do serviço StorSimple, selecione seu dispositivo. Navegue até **Dispositivos** > **Manutenção**.

2. Na parte inferior da página, clique em **Verificar atualizações**. Será criado um trabalho para verificar se há atualizações disponíveis. Você será notificado quando o trabalho for concluído com êxito.

3. Na seção de **Atualizações de software** na mesma página, você verá que existem novas atualizações de software disponíveis. É recomendável examinar as notas de versão antes de aplicar a Atualização 2 no seu dispositivo.

    ![Instalar as atualizações do software](./media/storsimple-install-update2-via-portal/scanupdate1.png)

4. Na parte inferior da página, clique em **Instalar atualizações**.

5. Será solicitada a sua confirmação. Clique em **OK**.

6. Será apresentada uma caixa de diálogo **Instalar atualizações**. Seu dispositivo deve atender às verificações listadas nessa caixa de diálogo. Estas etapas foram concluídas antes da atualização. Selecione **Eu entendi os requisitos acima e estou pronto para atualizar meu dispositivo**. Clique no ícone de verificação.

    ![Mensagem de confirmação](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)

7. Um conjunto de pré-verificações automáticas será iniciado agora. Estão incluídos:

	- **Verificações de integridade do controlador** para verificar se ambos os controladores do dispositivo estão em boas condições e online.
	
	- **Verificações de integridade do componente de hardware** para verificar se todos os componentes de hardware no dispositivo StorSimple estão em boas condições.
	
	- **Verificações de DATA 0** para verificar se DATA 0 está habilitada no dispositivo. Se essa interface não estiver habilitada, você precisará habilitá-la e repetir a ação.
	
	- **Verificações de DATA 2 e DATA 3** para verificar se as interfaces de rede DATA 2 e DATA 3 não estão habilitadas. Se essas interfaces estiverem habilitadas, você precisará desabilitá-las e tentar atualizar o dispositivo. Essa verificação será realizada somente se você estiver atualizando de um dispositivo que está executando o software de disponibilidade geral. Os dispositivos que executam versões 0.1, 0.2 ou 0.3 não precisarão dessa verificação.
	
	- **Verificação de gateway** em qualquer dispositivo executando uma versão anterior à Atualização 1. Essa verificação é realizada em todos os dispositivos que executam o software de pré-atualização 1, mas falha em dispositivos com um gateway configurado para uma interface de rede diferente de DATA 0.
 
	A Atualização 2 será aplicada somente se todas as verificações da pré-atualização acima tiverem sido concluídas com êxito. Você será notificado de que as verificações de pré-atualização estão em andamento.
  
    ![Notificação de pré-verificação](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)

    Este é um exemplo em que a verificação de pré-atualização falhou. Será necessário verificar se os dois controladores do dispositivo estão em boas condições e online. Também será necessário verificar a integridade dos componentes de hardware. Neste exemplo, os componentes do Controlador 0 e do Controlador 1 precisam de atenção. Talvez seja necessário entrar em contato com o Suporte da Microsoft se você não conseguir resolver esses problemas por conta própria.

   	 ![Falha na pré-verificação](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)

	
	> [AZURE.NOTE] Se você está atualizando de um software pré-Atualização 1, depois de você ter aplicado a Atualização 2 no seu dispositivo StorSimple, as verificações DATA 2 e DATA 3 e a verificação de gateway não serão mais necessárias para as atualizações futuras. As outras pré-verificações ainda serão obrigatórias. Se você atualizou da Atualização 1 ou posterior, as pré-verificações de gateway DATA 2, DATA 3 e de gateway não são executadas.


8. Depois que as verificações de pré-atualização tiverem sido concluídas com êxito, será criado um trabalho de atualização. Você será notificado quando o trabalho de atualização for criado com êxito.
 
    ![Criação do trabalho de atualização](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)

    Em seguida, a atualização será aplicada no seu dispositivo.
 
9. Para monitorar o andamento do trabalho de atualização, clique em **Exibir trabalho**. Na página **Trabalhos**, você pode ver o andamento da atualização.
    
10. A atualização levará algumas horas para ser concluída. Selecione o trabalho de atualização e clique em **Detalhes** para exibir os detalhes do trabalho a qualquer momento.
  
11. Depois que o trabalho for concluída, acesse a página de **Manutenção** e role para baixo até **Atualizações de software**.

12. Verifique se o dispositivo está executando a **Atualização 2 do StorSimple Série 8000 (6.3.9600.17673)**. A **Data da última atualização** também deve ser modificada.


13. Agora, você verá que as atualizações do modo de manutenção estão disponíveis. Em alguns casos, quando você está executando a Atualização 1.2, o firmware de disco pode já estar atualizado. Nessas circunstâncias, o portal determinará isso automaticamente e não solicitará as atualizações do modo de manutenção.

	As atualizações do modo de manutenção são atualizações interrompidas que resultam no tempo de inatividade do dispositivo e podem ser aplicadas apenas por meio da interface do Windows PowerShell do seu dispositivo. Siga as etapas relacionadas em [instalar e verificar o hotfix do modo de manutenção](#to-install-and-verify-maintenance-mode-hotfix) para instalar essas atualizações do modo de Manutenção.

> [AZURE.NOTE] Em determinadas instâncias, a mensagem que indica que as atualizações do modo de manutenção estão disponíveis pode ser exibida até 24 horas depois que as atualizações do modo de manutenção forem aplicadas com êxito no dispositivo.

<!---HONumber=AcomDC_0128_2016-->