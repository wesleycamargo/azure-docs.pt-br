1. Copie o instalador para uma pasta local (digamos, /tmp) no servidor que você deseja proteger e execute os seguintes comandos em uma janela de terminal.
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Agora podemos instalar o serviço de Mobilidade usando a linha de comando a seguir

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de linha de comando do instalador do Serviço de Mobilidade

|Parâmetro|Tipo|Descrição|Valores possíveis|
|-|-|-|-|
|-t |Obrigatório|Tipo de Agente<br>(Será preterido na próxima versão)|*both*|
|-a |Obrigatório|Configuração do Agente<br>(Será preterido na próxima versão) |*host*|
|-R |Opcional|Função do Agente|Agente<br>MasterTarget|
|-d |Opcional|Local onde o Serviço de Mobilidade será instalado|/usr/local/ASR|
|-i |Obrigatório|Endereço IP do servidor de configuração.|Qualquer endereço IP válido|
|-p |Obrigatório|Porta na qual o servidor de configuração está escutando conexões de entrada|443|
|-s |Obrigatório|Iniciar serviço após a instalação bem-sucedida<br>(Será preterido na próxima versão)|*y*|
|-c |Obrigatório|Modo de comunicação entre o Agente e o Servidor de Processo<br>(Será preterido na próxima versão) |*https*|
|-P |Obrigatório|A frase secreta do Servidor de Configuração|Qualquer caminho de arquivo UNC ou local válido|


#### <a name="sample-usage"></a>Exemplo de uso
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```


<!--HONumber=Jan17_HO3-->


