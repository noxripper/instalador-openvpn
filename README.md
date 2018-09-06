# openvpn

openvpn-install
OpenVPN instalador para Debian, Ubuntu, CentOS e Fedora.

Este script permitirá que você configure seu próprio servidor VPN em não mais que um minuto, mesmo que você não tenha usado o OpenVPN antes. Ele foi projetado para ser o mais discreto e universal possível.

Instalação
Execute o script e siga o assistente:

wget https://git.io/vpn -O openvpn-install.sh && bash openvpn-install.sh

Assim que terminar, você poderá executá-lo novamente para adicionar mais usuários, remover alguns deles ou mesmo desinstalar completamente o OpenVPN.

Doações
<ul>
  <li><a href="https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=48T8RCJUYZHW8&lc=BR&item_name=Nox%20Ripper&currency_code=BRL&bn=PP%2dDonationsBF%3abtn_donateCC_LG%2egif%3aNonHosted" target="_blank" title="Seja um Contribuidor">Se você quiser mostrar seu apreço, você pode doar via PayPal Obrigado!</a></li>
</ul>


Segue abaixo alguns parametros para ajustar seu servidor e cliente caso seja necessário:

siga as instruções do instalador.

para criar mais clientes execute novamente e escolha a opção de criar cliente alterando o nome do cliente
após instalado e gerado os clientes

<b>Para não bloquear a internet do cliente quando conectar a vpn.</b><br>
va no arquivo ovp do cliente e retire o comando 

setenv opt block-outside-dns


Entendendo parâmetros.


<b>PARÂMETROS PARA SERVIDOR E CLIENTE</b>

mode server :: introduzido a partir da versão 2.0. Nas versões anteriores, tínhamos apenas o modo P2P (point-to-point). Aqui ele é utilizado para tornar o nosso servidor um servidor multi-client, é obrigatório também, quando utilizamos alguns parâmetros, como "ifconfig-pool". 

tls-server :: habilita conexões SSL/TLS e assume ser o servidor, ajuda a bloquear ataques DDoS e Flooding na porta usada pelo OpenVPN. 

local 0.0.0.0 :: IP que o OpenVPN utilizará para conexão externa. 

port 5000 :: porta usada pelos clientes para conexão com o servidor, pode ser outra porta que esteja aberta para este tipo de comunicação. 

proto udp :: protocolo usado na conexão, pode ser UDP ou TCP. 

dev tun :: dispositivo que será criado, pode ser TAP ou TUN. O TAP permite propagação de broadcast NetBIOS (utilize este se a rede for Microsoft). O TUN, por outro lado, não permite, seria necessário possuir um servidor Windows para localizar as máquinas Windows pelo nome. 

tun-mtu 1500 :: define o tamanho máximo em bytes que podem ser enviados. 

tun-mtu-extra 32 :: este parâmetro controla o dimensionamento de buffer do OpenVPN, para que não haja sobrecarga de transmissão associado ao uso de um valor maior. 

client-cert-not-required :: não exige o certificado do cliente. 

topology net30 :: topologia utilizada pelo OpenVPN. "net30" é o padrão atual, que é apoiado pelas atuais versões. Isso informa ao servidor OpenVPN para distribuir 30 sub-redes para conectar clientes. A opção "p2p" define um ponto-a-ponto semântico. Este foi previamente conhecido na versão 2 "ifconfig-pool-linear". O problema com esta opção, é que ela não funciona com clientes Windows, por isso a sua aplicabilidade foi limitada. 

mssfix 1400 :: utilizado para melhorar aplicações remotas. 

user nobody :: usuário que o OpenVPN vai utilizar. 

group nogroup :: grupo que o OpenVPN vai utilizar. 

client-config-dir /etc/openvpn/clientes :: arquivo com as configurações dos clientes. 

secret chave.key :: define a localização do arquivo, onde é marcada a chave da autenticação da VPN. 

ca ca.crt :: define a localização do certificado de autoridade, este arquivo será utilizado pelas máquinas clientes. 

cert matriz.crt :: define a localização do certificado do servidor. Deverá ser gerado um para cada cliente. 

key matriz.key :: define a localização da chave de criptografia do servidor. Deverá ser gerada uma para cada cliente. 

dh dh1024.pem :: define os parâmetros Diffie-Hellman, criptografia para troca de chaves. 

tls-auth ta.key 0 :: define a localização do arquivo de chave compartilhada usada pelo TLS server, é uma camada a mais de segurança a ser usada, que habilita o controle de conexões. Opção 0 para server e 1 para client. 

duplicate-cn :: este parâmetro permite o uso de um mesmo certificado por duas conexões. 

cipher AES-128-CBC :: tipo de criptografia usada. 

persist-key :: garante a disponibilidade das chaves, caso o serviço da VPN seja reiniciado. 

persist-tun :: garante a disponibilidade da interface TUN caso a VPN seja reiniciada. 

server 10.0.0.0 255.255.255.0 :: rede que será utilizada pelo OpenVPN, caso seja utilizado e não seja definido um IP para tun, o servidor irá utilizar o primeiro IP. 

ifconfig 10.0.0.1 255.255.255.0 :: define um IP para a interface tun. 

push "route 192.168.0.0 255.255.255.0 10.0.0.1&::8243; :: faz o roteamento do IP local para o IP do túnel. Utilizar apenas em caso de redes diferentes, o parâmetro "push" força o cliente a pegar a configuração. 

push "dhcp-option DNS 192.168.0.1" :: IP do servidor DNS, o parâmetro "push" força o cliente a pegar a configuração. 

push "dhcp-option WINS 192.168.0.1" :: IP do servidor WINS, para redes que contenham servidores Windows, o parâmetro "push" força o cliente a pegar a configuração. 

route 10.0.0.0 255.255.255.0 :: roteamento do cliente. 

ifconfig-pool 10.0.0.2 10.0.0.100 255.255.255.0 :: define o range de endereços IPs que serão utilizados pelas conexões dos clientes. Se usado o valor server, não precisa deste parâmetro. 

ping 10 :: envia um pacote ping a cada 10 segundos. Este parâmetro deve ser colocado no servidor e no cliente. O ping é criptografado quanto utilizado TLS. 

ping-restart 120 :: envia um sinal SIGUSR1 restart após o tempo determinado. Necessário apenas no servidor. 

ping-timer-rem :: habilita o ping conforme programado no parâmetro keepalive. 

keepalive 10 120 :: o primeiro valor é o intervalo de ping, o segundo se refere ao tempo em que a VPN é reiniciada. Este parâmetro monitora a conexão entre servidor e cliente. 

reneg-sec 0 :: define o tempo em segundos que será refeita a conexão. Recomendável deixar em "0" para desabilitar, assim, o cliente não tem que ficar digitando a senha a todo momento. 

client-to-client :: permite que os clientes se comuniquem entre si sem precisar de um outro túnel. 

ifconfig-pool-persist ipp.txt :: define o arquivo onde serão armazenados os IPs utilizados pelos clientes. Assim, os IPs serão sempre os mesmos. 

client-config-dir ccd :: define onde ficarão as configurações individuais de cada cliente. 

max-clients 10 :: define um número máximo de clientes conectados simultaneamente. 

auth-nocache :: não efetua cache de autenticação. 

fast-io :: modo UDP sem bloqueio. 

mlock :: otimização no uso da memória. 

inactive 3600 :: define um tempo em segundos para que as tentativas de conexão sejam feitas. 

float :: utilizado em caso de IP dinâmico, o túnel continua aberto mesmo que o IP seja alterado. 

comp-lzo :: habilita compressão no link VPN via LZO. 

status /var/log/openvpn-status.log :: define o local onde será armazenado o log de status das conexões 

log /var/log/openvpn.log :: local para onde o verb irá enviar a saída. 

log-append /var/log/openvpn.log :: local onde está armazenado o log da VPN. 

verb 6 :: verbose mode, define o nível de log, os valores podem ir de 0 até 11:
0 :: silencioso, só gera log de erros fatais.
3 :: razoável, recomendável.
5 e 6 :: ajudam a resolver problemas de conexões, traz caracteres W e R.
9 para cima :: utilizado para descobrir erros quando a VPN não sobe.

mute 20 :: desabilita mensagens de log repetidas. 

PARÂMETROS EXCLUSIVOS PARA CLIENTE
client :: especifica que esta configuração é de um cliente. 

ns-cert-type server :: informa que o certificado é autoassinado. 

tls-client :: habilita conexões TLS. 

nobind :: não especifica uma porta local para o cliente se conectar. 

remote 200.200.200.200 :: IP fixo do servidor ao qual deseja se conectar. 

pull :: parâmetro utilizado para puxar as configurações do servidor. 

remote-cert-tls server :: certificado remoto com protocolo TLS utilizado para permitir acesso do cliente ao servidor. 

resolv-retry infinite :: define se o cliente continuará a tentar a conexão ou não com o servidor. Valor "0" para desabilitar.

Doações
<ul>
  <li><a href="https://www.paypal.com/cgi-bin/webscr?cmd=_donations&business=48T8RCJUYZHW8&lc=BR&item_name=Nox%20Ripper&currency_code=BRL&bn=PP%2dDonationsBF%3abtn_donateCC_LG%2egif%3aNonHosted" target="_blank" title="Seja um Contribuidor">Se você quiser mostrar seu apreço, você pode doar via PayPal Obrigado!</a></li>
</ul>



