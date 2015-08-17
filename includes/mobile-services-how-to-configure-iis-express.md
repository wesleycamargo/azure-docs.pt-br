
1. Certifique-se de interromper o serviço móvel se ele estiver sendo executado no IIS Express. Clique com o botão direito do mouse no ícone de bandeja e clique em **parar** para o serviço móvel.

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)


2. Em uma janela de prompt de comando, execute o comando **ipconfig** para procurar um endereço IP local válido para sua estação de trabalho.

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)


3. No Visual Studio, abra o arquivo applicationhost.config para o IIS Express. Esse arquivo está localizado no seguinte subdiretório de seu diretório de perfil de usuário.

        C:\Users<your profile name>\Documents\IISExpress\config\applicationhost.config

4. Configure o IIS Express para permitir solicitações de conexão remota ao serviço. Para fazer isso, no arquivo applicationhost.config, localize o elemento site do serviço móvel e adicione um novo elemento `binding` para a porta usando o endereço IP que você anotou anteriormente. Em seguida, salve o arquivo applicationhost.config.

    Seu elemento site atualizado deverá ser semelhante ao seguinte:

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. Abra o console do Firewall do Windows e crie uma nova regra de porta para permitir conexões à porta. Para obter mais informações sobre como criar uma nova regra de porta do Firewall do Windows, consulte [Como adicionar uma nova regra de porta do Firewall do Windows].

    >[AZURE.NOTE]Se seu computador de teste tiver ingressado em um domínio, exceções do firewall poderão ser controladas por uma política do domínio. Nesse caso, será necessário entrar em contato com o administrador do domínio para obter uma isenção para a porta em seu computador.

    Você agora deve estar configurado para testar com o IIS Express hospedando o serviço móvel.

    >[AZURE.NOTE]Depois de concluir o teste do serviço localmente, você deve excluir a regra do Firewall do Windows que criou.


<!-- URLs. -->
[Como adicionar uma nova regra de porta do Firewall do Windows]: http://go.microsoft.com/fwlink/?LinkId=392240

<!---HONumber=August15_HO6-->