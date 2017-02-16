## <a name="a-nameos-configaadd-ip-addresses-to-a-vm-operating-system"></a><a name="os-config"></a>Adicionar endereços IP em um sistema operacional da VM

Conecte-se e faça logon em uma VM criada com vários endereços IP privados. Você deve adicionar manualmente todos os endereços IP privados (incluindo o principal) que você adicionou à VM. Complete as etapas a seguir para seu sistema operacional VM:

### <a name="windows"></a>Windows

1. Em um prompt de comando, digite *ipconfig /all*.  Você vê apenas o endereço IP privado *Primário* (por meio do DHCP).
2. Digite *ncpa.cpl* no prompt de comando para abrir a janela **Conexões de rede**.
3. Abra as propriedades de **Rede local**.
4. Clique duas vezes em versão do Protocolo de Internet 4 (IPv4).
5. Selecione **Usar o seguinte endereço IP** e insira os seguintes valores:

    * **Endereço IP**: insira o endereço IP privado *primário*
    * **Máscara de sub-rede**: defina com base na sua sub-rede. Por exemplo, se a sub-rede for uma sub-rede /24, então, a máscara de sub-rede será 255.255.255.0.
    * **Gateway padrão**: o primeiro endereço IP na sub-rede. Se sua sub-rede for 10.0.0.0/24, o endereço IP do gateway será 10.0.0.1.
    * Clique em **Usar os seguintes endereços do servidor DNS** e insira os seguintes valores:
        * **Servidor DNS preferencial**: digite 168.63.129.16 se você não estiver usando seu próprio servidor DNS.  Se você estiver usando seu próprio servidor DNS, digite o endereço IP do seu servidor.
    * Clique no botão **Avançado** e adicione mais endereços IP. Adicione cada um dos endereços IP privados secundários listados na etapa 8 à NIC com a mesma sub-rede especificada para o endereço IP primário.
    * Clique em **OK** para fechar as configurações de TCP/IP e, em seguida, em **OK** novamente para fechar as configurações do adaptador. A conexão RDP é restabelecida.
6. Em um prompt de comando, digite *ipconfig /all*. Todos os endereços IP que você adicionou são mostrados e o DHCP está desativado.
    
### <a name="linux-ubuntu"></a>Linux (Ubuntu)

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

    ```bash
    sudo -i
    ```

3. Atualize o arquivo de configuração do adaptador de rede (supondo que 'eth0').

    * Mantenha o item de linha existente para o dhcp. O endereço IP principal permanece configurado como era anteriormente.
    * Adicione uma configuração para um endereço IP estático adicional com os seguintes comandos:

        ```bash
        cd /etc/network/interfaces.d/
        ls
        ```

    Você deve ver um arquivo. cfg.
4. Abra o arquivo: vi *filename*.

    Você verá as seguintes linhas ao final do arquivo:

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. Adicione as seguintes linhas após as linhas existentes neste arquivo:

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    ```

6. Salve o arquivo usando o seguinte comando:

    ```bash
    :wq
    ```

7. Reinicie o adaptador de rede com o seguinte comando:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > Execute ifdown e ifup na mesma linha se você estiver usando uma conexão remota.
    >

8. Verifique se que o endereço IP foi adicionado ao adaptador de rede com o seguinte comando:

    ```bash
    Ip addr list eth0
    ```

    Você verá o endereço IP adicionado como parte da lista.
    
### <a name="linux-redhat-centos-and-others"></a>Linux (Redhat, CentOS e outros)

1. Abra uma janela de terminal.
2. Verifique se você é o usuário raiz. Se não for, digite o seguinte comando:

    ```bash
    sudo -i
    ```

3. Digite sua senha e siga as instruções conforme solicitado. Quando você for o usuário raiz, navegue até a pasta de scripts de rede com o seguinte comando:

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. Liste os arquivos ifcfg relacionados usando o seguinte comando:

    ```bash
    ls ifcfg-*
    ```

    Você deve ver *ifcfg-eth0* como um dos arquivos.

5. Copie o arquivo *ifcfg-eth0* e nomeie-o *ifcfg-eth0:0* com o seguinte comando:

    ```bash
    cp ifcfg-eth0 ifcfg-eth0:0
    ```

6. Edite o arquivo *ifcfg-eth0:0* com o seguinte comando:

    ```bash
    vi ifcfg-eth0:0
    ```

7. Altere o dispositivo para o nome apropriado no arquivo; *eth0:0* , nesse caso, com o seguinte comando:

    ```bash
    DEVICE=eth0:0
    ```

8. Altere a linha *IPADDR = YourPrivateIPAddress* para refletir o endereço IP.
9. Salve o arquivo com o seguinte comando:

    ```bash
    :wq
    ```

10. Reinicie os serviços de rede e certifique-se de que as alterações foram bem-sucedidas executando os seguintes comandos:

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    Você verá o endereço IP adicionado, *eth0:0*, na lista retornada.


<!--HONumber=Dec16_HO1-->


