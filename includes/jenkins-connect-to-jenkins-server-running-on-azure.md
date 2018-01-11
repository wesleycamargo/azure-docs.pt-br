1. No seu navegador, navegue até sua máquina virtual Jenkins. Como o painel Jenkins é inacessível por meio de HTTP não segura, uma mensagem é exibida indicando que você precisa usar SSH para acessar a máquina virtual.

    ![Jenkins fornece informações que explicam como usar SSH para se conectar à máquina virtual Jenkins.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Abra uma janela de bash ou terminal, com acesso ao SSH.

1. Digite o seguinte `ssh` comando, substituindo o  **&lt;nome de usuário >** e  **&lt;domínio >** espaços reservados com os valores especificados ao criar a máquina virtual Jenkins:

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Siga o `ssh` prompts para fazer logon na máquina virtual Jenkins.

1. Digite o seguinte comando para recuperar a senha inicial para desbloqueio do Jenkins.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Copie a senha que é exibida na janela bash ou terminal na área de transferência.

1. No navegador, navegue até `http://localhost:8080`.

1. Cole a senha recuperada anteriormente para o campo **Senha administrativa** e selecione **Continuar**.

    ![O Jenkins armazena uma senha inicial que você deve usar para desbloquear a máquina virtual Jenkins na primeira vez que você se conectar a ela.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Selecione **Instalar plug-ins sugeridos**.

    ![O Jenkins permite instalar facilmente uma coleção de plug-ins sugeridos na entrada inicial](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. Na página **Criar primeiro usuário administrador**, crie o usuário administrador e uma senha para o painel Jenkins e selecione **Salvar e concluir**.

1. Na página **O Jenkins está pronto!** selecione **Começar a usar o Jenkins**.

    ![Depois que o Jenkins estiver instalado e configurado para acesso, você poderá começar a usá-lo para criar e compilar trabalhos.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)