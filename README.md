# Born2beRoot

Projeto consiste em criar um servidor ssh em maquina virtual, e você terá oportunidade de instalar o sistema operacional, configurar sudo, firewall, ssh de acordo com as regras do PDF. Além de descobrir o **Cron e o LVM**, ferramentas muito úteis.

## Criando a VM

Utilizando o VirtualBox, você vai separar um espaço pra configurar sua VM, pra isso você deve pesquisar entre os **CentOs e o Debian**, e escolher o que mais te agrada. Você deve saber a diferença entre eles, e explicar o motivo da sua escolha.

## Durante a configuração:

- Separar quantidade de memoria RAM.
- Criar tipo de disco *VDI*, com alocação dinâmica.
- Na configuração do VirtualBox, escolher o modo Bridge Adapter.
- Instalar o ISO do sistema que você escolher.
- Fazer uma instalação sem interface gráfica (requisito obrigatório).
- configurar o *Hostname (login42).*
- *ROOT (login42)* e senha de acordo com a política de segurança definida no PDF.
- Escolher a opção LVM criptografado, pra formatar o disco.
- Optar por partição HOME separada e usar uma senha de 20 caracteres pra criptografia (Quando o particionamento finalizar, você vai poder excluir as partições LVM dentro da partição extendida criptografada e montar as partições da maneira que for desejada).
- Instalar apenas o servidor SSH e utilitários de sistema padrão (Não pode nada de interface gráfica).
- GRUB, sim. E selecione o disco pra ter opções de inicialização.

Após esta parte você já pode checar se as partições estão de acordo com o PDF, usando o comando lsblk. Você precisa pesquisar sobre *Apt e Aptitude*, e escolher um dos dois pra usar. Eu usei o Aptitude pois não conhecia, e achei bem interessante. É importante que você saiba as diferenças entre eles, e qual o motivo da sua escolha.
Para instalar o Aptitude, usei o Apt (nativo do Debian) com o comando **apt-get install aptitude**
Muito importante também você conhecer o *SELinux* e *APParmor* , entender pra que serve e poder explicar.
  
## Configurar o SUDO
  
Talvez você precise instalar o SUDO no seu sistema. Use o apt ou aptitude pra isso e depois configure as permissões do arquivo */etc/sudoers.d*. Nesta parte você deve definir algumas configurações no arquivo, referente a caminhos permitidos no sudo, tentativas de senha, mensagem de erro ao usuário, caminho do log e tty.

- **sudo visudo**
- **passwd_tries=3**
- **bad_message="<custom-error-message>"**
- **logfile="/var/log/sudo/<filename>"**
- **requiretty**
- **secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"**

## Configurar Usuário
  
Você precisa configurar um usuario, além do Root, com o seu login. Pra isso os comandos abaixo são necessários:
- **useradd** ou **useradd**
- **userdel**
- **passwd**
- **sudo /etc/passwd | awk '{print $1}'**
- **sudo rebbot**
- **sudo -v**

## Configurar Grupo
  
Para criar o grupo *user42* e adicionar o usuário ao grupo.

- **usermod -aG**
- **sudo groupadd**
-	**getent group** 
- **sudo groupdel**
  
## SSH

Verifique se o SSH ja esta instalado, caso não esteja, instale. (Usei openssh-server já vem no Debian). É muito importante que você saiba o que é SSH, como configurar e pra que serve. 
  
-	**dpkg -l | grep ssh**
-	**sudo service ssh status**

Configurar o SSH, editando: */etc/ssh/sshd_config*

- Linha 13: descomentar e mudar pra **Port 4242.**
- Linha 32: descomentar e mudar **PermitRootLogin no**
  
## UFW

Instalar o UFW, instalei pelo Aptitude. Você precisa saber o que é, pra que serve e como configurar.
  
-	**sudo ufw status**
-	**sudo ufw enable/disable**
-	**sudo ufw default deny incoming**
-	**sudo ufw allow 4242**

Configurar o SSH, editando: */etc/network/interface*, e checar com o comando **ss -tunlp**

- iface enp0s3 inet **static**
- **address <seu_ip>**
- **netmask <sua_mascara>**
- **gateway <gateway_do_address>**

Após isto testar sua conexão SSH(LAN), usando o terminal da sua maquina física, 
- **ssh username@ip_address -p port**
- **exit**

## Política de Senhas

Configurar senhas editando: */etc/login.defs* 
- modificar o caminho do **ENV_SUPATH**, acrescentando */snap/bin* no final
- **PASS_MAX_DAYS 	30**
- **PASS_MIN_DAYS	 2**
- **PASS_WARN_AGE	 7**

Regras só valem pra novos usuários, os antigos devem usar o comando chage com as variações:
- ** chage -M (max days)**
- ** chage -m (min days)**
- ** chage -W (warning days)**
- ** chage -l**

Trocar hostname pode ser por comand line, ou editando 3 arquivos:
**sudo hostnamectl set-hostname novo_nome**

Via edição dos arquivos, substituir o nome do hostname:
*/etc/hosts*
*/etc/hostname*
*/proc/sys/kernel/hostname*
  
Configurar uma politica de senha forte para segurança, instalar:

- **libpam-pwquality**
- **libpam-cracklib**

Após isso configurar o */etc/pam.d/common-passwd*

Acrescentar as flags na linha 25: 
Password	 requisite	pam_pwquality.so **retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root**

retry=3           Tentativas 3
minlen=10         Mínimo de 10 caracteres
ucredit=-1        Ao menos 1 Maiúscula
dcredit=-1        Ao menos 1 dígito
maxrepeat=3       Máximo de 3 letras idênticas em sequência
difok=7           Ao menos 7 caracteres devem ser diferentes da senha anterior
reject_username   Não aceita o nome do usuário na senha
enforce_for_root  Aplica as mesmas politicas ao Root.
  
## CRON

- **crontab -e**
- **crontab -i**
- **crontab -l**
- **crontab -u username -l**
- **/etc/init.d/cron start/stop**

*Sintaxe do Cron*
  
A sintaxe do Cron é bem simples:
  
-	Asterisco (*) – define todos os parâmetros de agendamento.
-	Vírgula (,) – mantém duas ou mais horas de execução em um único comando.
-	Hífen (-) – determina o intervalo da hora ao definir vários tempos de execução de único comando.
-	Barra inclinada (/) – cria intervalos pré-determinados de tempo dentro de um intervalo de tempo específico.
-	Last (L) – tem o propósito específico de determinar o último dia da semana do mês respectivo. Por exemplo, 3L significa a última quarta-feira.
-	Weekday (W) – determina o dia da semana mais próximo de um tempo dado. Por exemplo, 1Wsignifica se o 1° for um sábado, o comando vai ser executado na segunda-feira (3°).
-	Hash (#) – para determinar o dia da semana, seguido por um número que varia de 1 a 5. Por exemplo, 1#2 significa a segunda segunda-feira.
- Ponto de interrogação (?) - serve pra deixar um espaço.
  
-	Minute (Minuto) – é o minuto em que o comando vai rodar, variando de 0 a 59.
-	Hour (Hora) – é a hora em que o comando será executado, variando de 0 a 23.
-	Day of the month (Dia do mês) – é o dia do mês em que o comando vai rodar, variando de 1 a 31.
-	Month (Mês) – é o mês em que o comando será executado, variando de 1 a 12.
-	Day of the week (Dia da semana) – é o dia da semana que você quer que o comando rode, variando de 0 a 7.

Você pode precisar configurar alguma ação com menos de um minuto, e pra isso precisa usar o **sleep segundos;** e mais de uma linha de comando pra alcançar o tempo desejado. Pesquise sobre isso.
 
## SCRIPT

Fazer um script em bash, onde pegue as informações solicitadas no PDF, e coloca na tela.
Como não é obrigatório o banner, pode usar o comando wall -n (que mostra ou esconde as mensagens). Dê uma olhada no manual pra entender. Com os conhecimentos basicos do Basecamp/Piscina, você consegue fazer bem fácil. Preste atenção na filtragem dos dados, pra não pegar campos errados. Cheque seu script várias vezes, rodando como root e pelo cron pra ver se não há diferença. Lembre-se que o Cron não é um usuário, portanto ele não usa o *tty.*

## SIGNATURE

- Por último, resetar todas as senhas
- **duplicar a VM. (Não faça clone por dentro do VirtualBox)
- Pegar o SHA1, usando shasum(Mac) /path_to_virtualbox/arquivo.vdi
- colocar o numero do SHA1 no arquivo signature.txt e subir pra Vogsphere.

## Referências de estudo
  
https://debian-handbook.info/browse/pt-BR/stable/sect.apparmor.html
  
https://qastack.com.br/ubuntu/236381/what-is-apparmor
  
https://en.wikipedia.org/wiki/AppArmor
  
https://www.certificacaolinux.com.br/logical-volume-manager-lvm-no-linux/
  
https://e-tinet.com/linux/lvm/
  
https://web.mit.edu/rhel-doc/3/rhel-sag-pt_br-3/ch-lvm-intro.html
  
https://www.vivaolinux.com.br/artigo/Aptitude-uma-ferramenta-poderosa
  
https://ricardo-reis.medium.com/aptitude-da639ec01e36
  
https://qastack.com.br/unix/767/what-is-the-real-difference-between-apt-get-and-aptitude-how-about-wajig
  
https://www.todoespacoonline.com/w/2015/10/su-sudo-e-sudoers-no-linux/
  
https://www.tecmint.com/setup-ufw-firewall-on-ubuntu-and-debian/
  
https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29
  
https://servidordebian.org/pt/wheezy/security/firewall/ufw
  
https://e-tinet.com/linux/firewall-com-ufw/
  
https://www.devmedia.com.br/introducao-ao-shell-script-no-linux/25778
  
https://www.hostinger.com.br/tutoriais/cron-job-guia
  
https://www.digitalocean.com/community/tutorials/how-to-use-cron-to-automate-tasks-ubuntu-1804-pt
