# Born2beRoot

Projeto Consiste em criar um servidor ssh em maquina virtual, e você terá oportunidade de instalar o sistema operacional, configurar sudo, firewall, ssh de acordo com as regras do PDF. Além de descobrir o **Cron e o LVM**, ferramentas muito úteis.

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
- Optar por partição HOME separada e usar uma senha de 20 caracteres pra criptografia.
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

Verifique se o SSH ja esta instalado, caso não esteja, instale. (Usei openssh-server já vem no Debian)
-	**dpkg -l | grep ssh**
-	**sudo service ssh status**

Configurar o SSH, editando: */etc/ssh/sshd_config*

- Linha 13: descomentar e mudar pra **Port 4242.**
- Linha 32: descomentar e mudar **PermitRootLogin no**
  
## UFW

Instalar o UFW. Você precisa saber o que é, pra que serve e como configurar.
  
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

Sintaxe do Cron



