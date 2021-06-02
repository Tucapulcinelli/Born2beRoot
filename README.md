# Born2beRoot

Projeto consiste em criar um servidor ssh em maquina virtual, e você terá oportunidade de instalar o sistema operacional, configurar sudo, firewall, ssh de acordo com as regras do PDF. Além de descobrir o **Cron e o LVM**, ferramentas muito úteis.

Project consists of creating a ssh server in a virtual machine, and you will have opportunity to install the operating system, configure sudo, firewall, ssh according to PDF rules. Besides discovering **Cron and LVM**, very useful tools.

## Criando a VM / Creating the VM

Utilizando o VirtualBox, você vai separar um espaço pra configurar sua VM, pra isso você deve pesquisar entre os **CentOs e o Debian**, e escolher o que mais te agrada. Você deve saber a diferença entre eles, e explicar o motivo da sua escolha.

Using VirtualBox, you'll set aside a space to configure your VM, for that you should search between **CentOs and Debian**, and choose what you like the most. You must know the difference between them, and explain the reason for your choice.
  
## Durante a configuração / During setup:

- Separar quantidade de memoria RAM. / Separate amount of RAM memory.
- Criar tipo de disco *VDI*, com alocação dinâmica. / Create *VDI* disk type, with dynamic allocation.
- Na configuração do VirtualBox, escolher o modo Bridge Adapter. / In VirtualBox configuration, choose Bridge Adapter mode.
- Instalar o ISO do sistema que você escolher. / Install the ISO of the system you choose.
- Fazer uma instalação sem interface gráfica (requisito obrigatório). / Perform an installation without a graphical interface (mandatory requirement).
- configurar o *Hostname (login42)*. / Configure the *Hostname (login42).*
- *ROOT (login42)* e senha de acordo com a política de segurança definida no PDF. / *ROOT (login42)* and password according to the security policy defined in the PDF.
- Escolher a opção LVM criptografado, pra formatar o disco. / Choose the Encrypted LVM option to format the disk.
- Optar por partição HOME separada e usar uma senha de 20 caracteres pra criptografia. **Quando o particionamento finalizar, você vai poder excluir as partições LVM dentro da partição extendida criptografada e montar as partições da maneira que for desejada.** / Choose separate HOME partition and use a 20-character password for encryption. **When partitioning is finished, you will be able to delete the LVM partitions inside the encrypted extended partition and mount the partitions however you like.**
- Instalar apenas o servidor SSH e utilitários de sistema padrão (Não pode nada de interface gráfica). / Install only SSH server and standard system utilities (NOT ALLOWED graphical interface).
- GRUB, sim. E selecione o disco pra ter opções de inicialização. / GRUB, yes. And select the disk to have boot options.

Após esta parte você já pode checar se as partições estão de acordo com o PDF, usando o comando lsblk. Você precisa pesquisar sobre *Apt e Aptitude*, e escolher um dos dois pra usar. Eu usei o Aptitude pois não conhecia, e achei bem interessante. É importante que você saiba as diferenças entre eles, e qual o motivo da sua escolha.
Para instalar o Aptitude, usei o Apt (nativo do Debian) com o comando **apt-get install aptitude**
Muito importante também você conhecer o *SELinux* e *APParmor* , entender pra que serve e poder explicar.

After this part you can check if the partitions are PDF compliant using the lsblk command. You need to research *Apt and Aptitude*, and choose one of the two to use. I used Aptitude because I didn't know it, and I found it very interesting. It is important that you know the differences between them, and why you chose them.
To install Aptitude, I used Apt (native to Debian) with the command **apt-get install aptitude**
It is also very important that you know *SELinux* and *APParmor*, understand what it is for and be able to explain.

  
## Configurar o SUDO / Configure SUDO
  
Talvez você precise instalar o SUDO no seu sistema. Use o apt ou aptitude pra isso e depois configure as permissões do arquivo */etc/sudoers.d*. Nesta parte você deve definir algumas configurações no arquivo, referente a caminhos permitidos no sudo, tentativas de senha, mensagem de erro ao usuário, caminho do log e tty.

You may need to install SUDO on your system. Use apt or aptitude for that and then set the permissions of the */etc/sudoers.d* file. In this part you must configure some settings in the file, referring to allowed paths in sudo, password attempts, user error message, log path and tty.

- **sudo visudo**
- **passwd_tries=3**
- **bad_message="<custom-error-message>"**
- **logfile="/var/log/sudo/<filename>"**
- **requiretty**
- **secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"**

## Configurar Usuário / Configure User
  
Você precisa configurar um usuario, além do Root, com o seu login. Pra isso os comandos abaixo são necessários:

You need to configure a user, in addition to Root, with your login. For this the commands below are necessary:
  
- **useradd** ou/or **useradd**
- **userdel**
- **passwd**
- **sudo /etc/passwd | awk '{print $1}'**
- **sudo rebbot**
- **sudo -v**

## Configurar Grupo / Configure Group
  
Para criar o grupo *user42* e adicionar o usuário ao grupo.

Create the *user42* group and add the user to the group.

- **usermod -aG**
- **sudo groupadd**
-	**getent group** 
- **sudo groupdel**
  
## SSH

Verifique se o SSH ja esta instalado, caso não esteja, instale. (Usei openssh-server já vem no Debian). É muito importante que você saiba o que é SSH, como configurar e pra que serve.

Check if SSH is already installed, if not, install it. (I used openssh-server already on Debian). It is very important that you know what SSH is, how to configure it and what it is for.
  
-	**dpkg -l | grep ssh**
-	**sudo service ssh status**

Configurar o SSH, editando: */etc/ssh/sshd_config* / Configure the SSH, by editing: */etc/ssh/sshd_config*

- Linha 13: descomentar e mudar pra **Port 4242 / Line 13: uncomment and change to **Port 4242.**
- Linha 32: descomentar e mudar **PermitRootLogin no** / Line 32: uncomment and change **PermitRootLogin no**
  
## UFW

Instalar o UFW, instalei pelo Aptitude. Você precisa saber o que é, pra que serve e como configurar.

Install UFW, installed through Aptitude. You need to know what it is and how to configure it.
  
-	**sudo ufw status**
-	**sudo ufw enable/disable**
-	**sudo ufw default deny incoming**
-	**sudo ufw allow 4242**

Configurar o SSH, editando: */etc/network/interface*, e checar com o comando **ss -tunlp**

Configure SSH by editing: */etc/network/interface*, and check with **ss -tunlp** command

- iface enp0s3 inet **static**
- **address <seu_ip>**
- **netmask <sua_mascara>**
- **gateway <gateway_do_address>**

Após isto, testar sua conexão SSH(LAN), usando o terminal da sua maquina física.
  
After this, tests your SSH(LAN) connection, using your physical machine's terminal.
  
- **ssh username@ip_address -p port**
- **exit**

## Política de Senhas / Password Policy

Configurar senhas editando: */etc/login.defs* / Set passwords by editing: */etc/login.defs*
- modificar o caminho do **ENV_SUPATH**, acrescentando */snap/bin* no final / modify the path of **ENV_SUPATH**, adding */snap/bin* at the end
- **PASS_MAX_DAYS 	30**
- **PASS_MIN_DAYS	 2**
- **PASS_WARN_AGE	 7**

Regras só valem pra novos usuários, os antigos devem usar o comando **chage** com as variações:
  
Rules are only valid for new users, old ones must use the **chage** command with variations:
- **chage -M (max days)**
- **chage -m (min days)**
- **chage -W (warning days)**
- **chage -l**

Trocar hostname pode ser por comand line, ou editando 3 arquivos: / Changing hostname can be by command line, or by editing 3 files:
**sudo hostnamectl set-hostname novo_nome** / **sudo hostnamectl set-hostname new_name**

Via edição dos arquivos, substituir o nome do hostname: / Via editing the files, replace the hostname:
*/etc/hosts*
*/etc/hostname*
*/proc/sys/kernel/hostname*
  
Configurar uma politica de senha forte para segurança, instalar: / Set up a strong password policy for security, install:

- **libpam-pwquality**
- **libpam-cracklib**

Após isso configurar o */etc/pam.d/common-passwd* / After that configure */etc/pam.d/common-passwd*

Acrescentar as flags na linha 25: / Add the flags on line 25: 
Password	 requisite	pam_pwquality.so **retry=3 minlen=10 ucredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root**

retry=3           Tentativas 3. / Attempts 3.
minlen=10         Mínimo de 10 caracteres. / Minimum of 10 characters.
ucredit=-1        Ao menos 1 Maiúscul.a / At least 1 Uppercase.
dcredit=-1        Ao menos 1 dígit.o / At least 1 digit.
maxrepeat=3       Máximo de 3 letras idênticas em sequência. / Maximum of 3 identical letters in sequence.
difok=7           Ao menos 7 caracteres devem ser diferentes da senha anterior. / At least 7 characters must be different from the previous password.
reject_username   Não aceita o nome do usuário na senha. / Do not accept username in password.
enforce_for_root  Aplica as mesmas politicas ao Roo.t / Applies the same policies to Root.
  
## CRON

- **crontab -e**
- **crontab -i**
- **crontab -l**
- **crontab -u username -l**
- **/etc/init.d/cron start/stop**

*Sintaxe do Cron* / *Cron Syntax*
  
A sintaxe do Cron é bem simples. / Cron's syntax is very simple:
  
-	Asterisco (*) – define todos os parâmetros de agendamento. / Defines all scheduling parameters.
-	Vírgula (,) – mantém duas ou mais horas de execução em um único comando. / Keeps two or more hours of execution in a single command.
-	Hífen (-) – determina o intervalo da hora ao definir vários tempos de execução de único comando. / Determines the time range when setting multiple single command execution times.
-	Barra inclinada (/) – cria intervalos pré-determinados de tempo dentro de um intervalo de tempo específico. / Creates pre-determined time intervals within a specific time range.
-	Last (L) – tem o propósito específico de determinar o último dia da semana do mês respectivo. Por exemplo, 3L significa a última quarta-feira. / Has the specific purpose of determining the last day of the week of the respective month. For example, 3L means last Wednesday.
-	Weekday (W) – determina o dia da semana mais próximo de um tempo dado. Por exemplo, 1W significa se o 1° for um sábado, o comando vai ser executado na segunda-feira (3°). / Determines the day of the week closest to a given time. For example, 1W means if the 1st is a Saturday, the command will be executed on Monday (3rd).
-	Hash (#) – para determinar o dia da semana, seguido por um número que varia de 1 a 5. Por exemplo, 1#2 significa a segunda segunda-feira. / To determine the day of the week, followed by a number ranging from 1 to 5. For example, 1#2 means the second Monday.
- Ponto de interrogação (?) - serve pra deixar um espaço. / Serves to leave a space.
  
-	Minute (Minuto) – é o minuto em que o comando vai rodar, variando de 0 a 59. / Is the minute the command will run, ranging from 0 to 59.
-	Hour (Hora) – é a hora em que o comando será executado, variando de 0 a 23. / Is the time the command will be executed, ranging from 0 to 23.
-	Day of the month (Dia do mês) – é o dia do mês em que o comando vai rodar, variando de 1 a 31. / Is the day of the month the command will run, ranging from 1 to 31.
-	Month (Mês) – é o mês em que o comando será executado, variando de 1 a 12. / Is the month the command will be executed, ranging from 1 to 12.
-	Day of the week (Dia da semana) – é o dia da semana que você quer que o comando rode, variando de 0 a 7. / Is the day of the week you want the controller to run, ranging from 0 to 7.

Você pode precisar configurar alguma ação com menos de um minuto, e pra isso precisa usar o **sleep segundos;** e mais de uma linha de comando pra alcançar o tempo desejado. Pesquise sobre isso.
You may need to configure some action with less than a minute, and for that you need to use **sleep seconds;** and more than one command line to reach the desired time. Research about it.
 
## SCRIPT

Fazer um script em bash, onde pegue as informações solicitadas no PDF, e coloca na tela.
Como não é obrigatório o banner, pode usar o comando wall -n (que mostra ou esconde as mensagens). Dê uma olhada no manual pra entender. Com os conhecimentos basicos do Basecamp/Piscina, você consegue fazer bem fácil. Preste atenção na filtragem dos dados, pra não pegar campos errados. Cheque seu script várias vezes, rodando como root e pelo cron pra ver se não há diferença. Lembre-se que o Cron não é um usuário, portanto ele não usa o *tty.*
Make a bash script, where it takes the requested information from the PDF, and puts it on the screen.
As the banner is not mandatory, you can use the command wall -n (which shows or hides the messages). Take a look at the manual to understand. With the basic knowledge of Basecamp/Piscina, you can do it very easy. Pay attention to data filtering, so you don't get wrong fields. Check your script several times, running as root and through cron to see if there is no difference. Remember that Cron is not a user, so it doesn't use *tty.*

## SIGNATURE

- Por último, resetar todas as senhas. / Finally, reset all passwords.
- **duplicar a VM. (Não faça clone por dentro do VirtualBox). / **Duplicate the VM. (Do not clone inside VirtualBox)
- Pegar o SHA1, usando shasum(Mac) /path_to_virtualbox/arquivo.vdi. / Get SHA1 using shasum(Mac) /path_to_virtualbox/arquivo.vdi
- colocar o numero do SHA1 no arquivo signature.txt e subir pra Vogsphere. / Put the SHA1 number in the signature.txt file and upload it to Vogsphere.

## Referências de estudo / Study References
  
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
  
  

