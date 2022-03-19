# Instituto Federal de Alagoas - Campus Arapiraca
### Prof. Alaelson Jatobá
### Alunos: 
* Manuel Eduardo Nascimento Antunes
* Arthur Brito Ribeiro
* Gustavo Eloizio Cavalcante Santos
* Eduardo Roger Melo Mendonça
## Sumário

## 1. Introdução

## 2. Definições Iniciais
### CONFIGURAÇÕES DAS INTEFACES DE REDE

| Rede | IP Externo | Mascara | IP Interno | Mascara |
| --- | --- | --- | --- | --- |
| IP da Subrede: | 10.9.14.0 | /24 | 192.168.14.0 | /29 |
| IP de Broadcast: | 10.9.14.255/24 | /24 | 192.168.14.8	| /29 |

| Nome da VMs | WAN | IP | LAN | IP |
| --- | --- | --- | --- | --- |
| IP do GW: | ens160 | 10.9.14.112 | ens192 |	192.168.14.1 |
| IP do SAMBA: | ens160 | 10.9.14.106 | ens192 |	192.168.14.2 |
| IP do NS1: | ens160 | 10.9.14.132 | ens192 |	192.168.14.3 |
| IP do NS2: | ens160 | 10.9.14.118 | ens192 |	192.168.14.4 |
| IP do WEB | ens160 | 10.9.14.211 | ens192 |	192.168.14.5 |
| IP do BD | ens160 | 10.9.14.212 | ens192 |	192.168.14.6 |

### DEFINIÇÂO DE NOMES E DOMÍNIO (grupo1.turma914.ifalara.local):				

| VM | Nome | Domínio (zona): | grupo1.turma914.ifalara.local |
| --- | --- |--- | --- |
| Aluno08 | Arthur | FQDN do GW: | gw.grupo1.turma914.ifalara.local |
| Aluno03 | Eduardo |	FQDN do SAMBA: | smb.grupo1.turma914.ifalara.local |
| Aluno26	| Gustavo | FQDN do NS1: | ns1.grupo1.turma914.ifalara.local |
| Aluno09 | Manuel | FQDN do NS2: | ns2.grupo1.turma914.ifalara.local |	
| Grupo1vm1	| --- | FQDN do WEB	| www.grupo1.turma914.ifalara.local |	
| Grupo1vm2 | --- |	FQDN do BD | bd.grupo1.turma914.ifalara.local |

## 3. Implementação dos Serivços de Rede (Cada serviço uma sessão)
### Configurando o DNS

#### DNS Master

1. Use o comando

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
> Para corrigir a interface 192, adicinar ips das maquinas dos integrantes da equipe da interface 160 e 192 e correção do domínio
![image](https://user-images.githubusercontent.com/62352928/157165409-1fed1de8-b136-4725-a0d1-ae2796ff93a2.png)


2. Use

```bash
sudo cp /etc/bind/db.empty /etc/bind/zones/db.grupo1.turma914.ifalara.local
```
> Para copiar o arquivo db.empty para pasta zones com o dominio correto
![image](https://user-images.githubusercontent.com/62352928/157165476-e822bc0c-d1f0-437d-a4b0-11c4d4af7d04.png)


3. Use

```bash
sudo nano /etc/bind/zones/db.grupo1.turma914.ifalara.local 
```
> Para modificar o arquivo adicionando os domínios corretos e o IP
![image](https://user-images.githubusercontent.com/62352928/157165536-4ca43a80-d361-4239-8761-93619d9e7dce.png)

4. Utilize

```bash
sudo nano /etc/bind/zones/db.10.9.14.rev
```
> Para corigir os domínios e adicionar os IPs dos integrantes
![image](https://user-images.githubusercontent.com/62352928/157165661-e3ec0a9c-30c6-4ddb-9603-0f2474208033.png)

5. Utilize

```bash
sudo nano /etc/bind/named.conf.local
```
> Para atualizar o domínio e adicionar o ip de transferência
![image](https://user-images.githubusercontent.com/62352928/157165783-76ba3471-ffef-4989-b1ea-41f657f74e9e.png)

6. Utilize sudo named-checkconf para checar sintaxe

```bash
sudo named-checkconf
```

7. Utilizar

```bash
sudo named-checkzone grupo1.turma914.ifalara.local db.grupo1.turma914.ifalara.local
```
```bash
sudo named-checkzone 14.9.10.in-addr.arpa db.10.9.14.rev 
```
> Para checar as zonas
![image](https://user-images.githubusercontent.com/62352928/157166256-39cb86c8-d4f1-4992-886e-6680df3b4bed.png)

8. Usar

```bash
sudo systemctl restart bind9
```
> Para atualizar as configurações
![image](https://user-images.githubusercontent.com/62352928/157166396-89f0e1b2-c62a-42d1-81c7-5de83fbc82f2.png)


```bash
ifconfig
```
> Para confirmar que o arquivo 00-installer-config.yaml está correto
![image](https://user-images.githubusercontent.com/62352928/157166555-34232e4c-f220-429d-bb6b-fea2681859ec.png)

9. Utilizar

```bash
systemd-resolve --status
```
> Para testar se o DNS Master está funcionando da máquina ns1
![image](https://user-images.githubusercontent.com/62352928/157166735-8ba04791-cd15-407d-a927-05faf4966842.png)


10. Teste de ping com o google.com
![image](https://user-images.githubusercontent.com/62352928/157166752-ee1d2359-cdfc-4445-8627-37a0f3bc373c.png)

#### DNS Slave

1. Para instalar o bind9
```bash
sudo apt-get install bind9 dnsutils bind9-doc -y
```

2. Para testar o bind9
```bash
sudo systemctl status bind9
```

3. Para configurar o arquivo para conectar com o Master
```bash
sudo nano /etc/bind/named.conf.local 
```
![image](https://user-images.githubusercontent.com/62352928/157167275-fa3c7ac5-faa5-42a7-b664-589d06b0551c.png)

4. Para checar a sintaxe
```bash
sudo named-checkconf
```

5. Corrigir o dominio e adição dos ips dos integrantes na maquina ns2 através do comando 
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
![image](https://user-images.githubusercontent.com/62352928/157167590-3bdddaef-9319-4ec0-b1d4-b28670f34426.png)

6. Para conferir configurações do slave
```bash
systemd-resolve --status
```

7. Testar o DNS do Slave 
```bash
ping google.com
```
![image](https://user-images.githubusercontent.com/62352928/157167834-1ae7b75f-e7c2-4051-871d-9f0e2c89fc24.png)

8. Mudar o nome da máquina para o que está definido na tabela:
```bash
sudo hostnamectl set-hostname ns2.grupo1.turma914.ifalara.local
```

9. Correção no arquivo 
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
![image](https://user-images.githubusercontent.com/62352928/158070971-b27a9de8-09c0-4e35-90f5-7d412b0c85fb.png)

10. Teste de dig no gateway com:
```bash
dig @10.9.14.132 gw.grupo1.turma914.ifalara.local
```
![image](https://user-images.githubusercontent.com/62352928/158070997-1ea88df6-df2d-490e-8240-422cdbe3be95.png)

11. Para verificar se a interface 160 esta funcionando e ping google.com para o teste de ping
```bash
systemd-resolve --status ens160
```
![image](https://user-images.githubusercontent.com/62352928/158071059-8e66665f-91c1-4265-afba-79fbf8aaa7ac.png)

### Instalação do PHP 7.4

```bash
sudo apt install php7.4 libapache2-mod-php7.4 php7.4-mysql php-common php7.4-cli php7.4-common php7.4-common php7.4-json php7.4-opcache php7.4-readline para instalar o php 7.4
```

1. Carregar o php7.4 no apache2:
```bash
sudo a2enmod php7.4
```

2. Reiniciar o apache:
```bash
sudo systemctl restart apache2
```

3. Criar o arquivo php na pasta
```bash
sudo touch /var/www/html/info.php
```

4. Modificar o arquivo 
```bash
sudo nano /var/www/html/info.php
```
![image](https://user-images.githubusercontent.com/62352928/158397210-619fcfdf-d8f4-4935-bc01-e0d9af36b7c8.png)

5. Utiliza http://10.9.14.211/info.php no browser e esta página deve aprecer se tudo foi feito corretamente
![image](https://user-images.githubusercontent.com/62352928/158397346-6e06d236-252d-4148-a35a-f3fe695aa760.png)

## Instalação do Apache

1. Para fazer update na maquina via apt:
```bash
$ sudo apt update 
```

2. Para instalar o apache:
```bash
$ sudo apt install apache2 
```

3. Para ver o status do apache:
```bash
sudo systemctl status apache2
```
![image](https://user-images.githubusercontent.com/62352928/158071203-1e592108-1445-498b-8a96-14c1bbbfb5a2.png)

## Criação do Site

1. Para criar um diretorio com nosso dominio
```bash
sudo mkdir /var/www/grupo1.turma914.ifalara.local
```

2. Para atribui a propriedade do diretorio com a variavel do ambiente $USER:
```bash
sudo chown -R $USER:$USER /var/www/grupo1.turma914.ifalara.local 
```

3. Para criar permissões do web host:
```bash
sudo chmod -R 755 /var/www/grupo1.turma914.ifalara.local 
```

4. Para criar pagina web em html:
```bash
sudo nano /var/www/grupo1.turma914.ifalara.local/index.html
```
![image](https://user-images.githubusercontent.com/62352928/158071373-174e463f-a252-4e34-89ec-7330f34d5093.png)

5. Digita ip da maquina www no navegador para confirmar que esta funcinando:
![image](https://user-images.githubusercontent.com/62352928/158071411-4b67df2b-8b71-4044-88cd-28f812167ac2.png)

6. Para criar arquivo de configuração novo:
```bash
sudo nano /etc/apache2/sites-available/grupo1.turma914.ifalara.local.conf
```
![image](https://user-images.githubusercontent.com/62352928/158071490-46741202-a07a-40a9-b659-2f66a1df2e9d.png)

7. Para habilitar nova configuração:
```bash
sudo a2ensite grupo1.turma914.ifalara.local.conf
```

8. Para desabilitar site padrão:

```bash
sudo a2dissite 000-default.conf
```

9. Para garantir que toda sintaxe está correta:
```bash
sudo apache2ctl configtest
```

11. Para reiniciar o apache e habilitar as configurações:
```bash
sudo systemctl restart apache2
```

12. Acessa novamente o IP da máquina www no navegador para garantir que o site foi atualizado para o html definido:
![image](https://user-images.githubusercontent.com/62352928/158071606-755069eb-2485-4854-b387-5cd14601ea4b.png)

### Inicio do processo na máquina bd

1. Para mudar o nome da maquina para o atribuido na planilha:
```bash
sudo hostnamectl set-hostname bd.grupo1.turma914.ifalara.local
```

2. Para configurar o arquivo com o dominio correto, e dns existente:
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
![image](https://user-images.githubusercontent.com/62352928/158071698-3f30ca1e-5a71-46a6-af67-ccc7b9bd0457.png)

3. Adicionar as maquinas www e bd na zona direta do ns1
```bash
sudo nano /etc/bind/zones/db.grupo1.turma914.ifalara.local
```
![image](https://user-images.githubusercontent.com/62352928/158395916-42341f58-42b8-493f-9712-106da1c54ab3.png)

4. Adicionar as maquinas www e bd na zona reversa do ns1
```bash
sudo nano /etc/bind/zones/db.10.9.14.rev 
```
![image](https://user-images.githubusercontent.com/62352928/158396110-57cb5f3c-3cfb-4482-b19a-2469faa1112f.png)

5. Utilizar na maquina www para conectar com o dns do ns1 e ns2
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
![image](https://user-images.githubusercontent.com/62352928/158396317-95d7528e-a99e-454b-8453-63ee824b156f.png)

6. Teste de dig com a maquina bd, note que na linha de answer section aparece o dominio correto e o ip 

![image](https://user-images.githubusercontent.com/62352928/158942783-ca8ea659-30f5-41b8-b68d-bfe66fd4637d.png)

7. Para saber se a interface 160 está funcionando:
```bash
systemd-resolve --status ens160
```
Teste de ping com a maquina bd:
```bash
ping google.com
```
![image](https://user-images.githubusercontent.com/62352928/158943006-50194b3d-01e1-449c-b8f9-ecdf815c1200.png)

### Instalar MySQL

1.
```bash
sudo apt install mysql-server
```

2. Para garantir que o mysql já está funcionando:
```bash
sudo systemctl status mysql
```
![image](https://user-images.githubusercontent.com/62352928/158943275-ec65ad4b-4d2c-46b0-a4fe-9a99013d1b37.png)

3. Para mudar as linhas de mysqlx-bind-addresse bind-address de 127.0.0.1 para 0.0.0.0 a fim de liberar as interfaces de rede com o ip coringa, utilize:
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```
![image](https://user-images.githubusercontent.com/62352928/158943431-5c7b9a21-ab66-4124-97d3-4a0fd59e923e.png)

4. Reinicie o MySQL:
```bash
sudo systemctl restart mysql
```

5. Defina uma senha forte:
```bash
sudo mysql_secure_installation
```
![image](https://user-images.githubusercontent.com/62352928/158943576-e47119eb-ff7d-4cc3-8972-4e7ab20832ff.png)

6. Inicie o MySQL:
```bash
sudo mysql
```
![image](https://user-images.githubusercontent.com/62352928/158943684-5172262b-adff-4726-ae66-55959327074f.png)
Nesse momento faz-se a criação do banco de dados

7. Banco de Dados
![image](https://user-images.githubusercontent.com/62352928/158943743-028fb5aa-a9cb-4030-9a8c-c3edece5d898.png)

8. Agora mudamos para a máquina www e vamos baixar o script de teste, primeiramente baixando o zip unzip:
```bash
sudo apt install zip unzip -y
```

9. Para baixar o script de teste damos unzip script_teste_db.zip para desempacotar
```bash
curl https://github.com/alaelson/labredes2021/blob/main/network/lamp/testedb/script_teste_db.zip -o script_teste_db.zip
```

10. Agora vamos acessar o diretorio do arquivo desempacotado:
```bash
cd script_teste_db/
```

11. Modifica o script de deletar:
```bash
sudo nano del.php
```
![image](https://user-images.githubusercontent.com/62352928/158944163-398628e1-c0a7-4c94-9194-ff431e147150.png)

12. Modifica o arquivo de insert do script:
```bash
sudo nano insert.php
```
![image](https://user-images.githubusercontent.com/62352928/158944262-51b6306f-81cf-4472-affa-5fb652ab07ef.png)

13. Modifica o arquivo de select do script:
```bash
sudo nano select.php
```
![image](https://user-images.githubusercontent.com/62352928/158944371-1450964e-28ee-44ad-8617-17e6c757f31a.png)

14. Modifica o arquivo de update do script:
```bash
sudo nano update.php
```
![image](https://user-images.githubusercontent.com/62352928/158944477-12bb2136-3591-42dd-a994-dc82143c1fcf.png)

### Criação do site com integração com o banco de dados

1. iremos em https://github.com/alaelson/labredes2021/blob/551391a34728b53de28b2251ae206b17bedc277d/network/lamp/siteDemo/www.grupox.turma914.ifalara.local.zip para fazer o download do script do site demo e enviaremos para nossa maquina web via SCP onde o deixaremos no diretorio /var/www/grupo1.turma914.ifalara.local

2. Vamos para o diretorio referido com:
```bash
cd /var/www/www.grupo1.turma914.ifalara.local/
```

3. E descompactamos esse script
```bash
 sudo unzip www.grupox.turma914.ifalara.local.zip
```

4. Damos f5 em nossa pagina no browser e o script ja deve estar funcionando
![image](https://user-images.githubusercontent.com/62352928/159005212-db20197f-eb2a-4696-bdd5-f39e39e66353.png)

5. E teremos que dar sudo nano em todos os arquivos .php do arquivo descompactado para colocarmos nossas credenciais referentes a nossa vm bd e ao nosso banco (ip e dominio)

### Configuração da VM Samba

1. Mudar o nome
```bash
sudo hostnamectl set-hostname samba-srv
```

2. Reiniciar a máquina
```bash
sudo reboot 
```

3. Adicionar os ips do ns1, ns2 e o domínio
```bash
sudo nano /etc/netplan/00-installer-config.yaml 
```
![image](https://user-images.githubusercontent.com/62352928/159005670-e90de6e6-f583-49c0-97fd-1c6f462a6c4a.png)

4.  Para garantir que esta tudo certo:
```bash
sudo netplan apply e ifconfig -a
```
![image](https://user-images.githubusercontent.com/62352928/159005801-742f91f2-e78f-4c4d-9ca5-449a28229bae.png)

5. Teste de ping para vm gw com maquina smb
![image](https://user-images.githubusercontent.com/62352928/159005892-3d7cfda1-0577-4fd1-90f7-05182e03d7f0.png)

### Continuação da configuração da máquina Samba

1. Copiar o arquivo de backup
```bash
sudo cp /etc/samba/smb.conf{,.backup}
```

2. Remover os comentários do arquivo
```bash
sudo bash -c 'grep -v -E "^#|^;" /etc/samba/smb.conf.backup | grep . > /etc/samba/smb.conf'
```

3. Configurar o arquivo smba e aplicar as interfaces existentes
```bash
sudo nano smb.conf
```
![image](https://user-images.githubusercontent.com/62352928/159007550-033902d2-4557-40ec-a65d-5d2536716a8b.png)

4. Reiniciar servidor e aplicar alterações
```bash
systemctl restart smbd
```

5. Após criação do usuário do samba, iremos criar o diretorio primeiramente com:
```bash
mkdir sambashare
```
```bash
sudo mkdir -p /samba/public
```

6. Mudar permissão do grupo
```bash
sudo chown -R nobody:nogroup /samba/public
```

7. Mudar permissão de escrita do grupo
```bash
sudo chmod -R 0775 /samba/public
```

8. Atribuir ao grupo sambashare
```bash
sudo chgrp sambashare /samba/public
```

9. Conecta ao serviço samba com as credenciais e ip do seu servidor samba através do explorador de arquivo
![image](https://user-images.githubusercontent.com/62352928/159008547-ab67cb9e-42cb-4465-95b1-23bc418c4b48.png)

10. Editar a pasta public para permitir apenas usuarios ligados ao sambashare
```bash
sudo nano /etc/samba/smb.conf
```

11. Reiniciar o serviço e aplicar alterações
```bash
systemctl restart smbd
```

12. Serviço samba em funcionamento: https://drive.google.com/file/d/1z-mU56gICKlH_aMJNzXO65f2AkcpRmG_/view?usp=sharing


### Configuração da máquina GW

1. Mudar o nome da máquina
```bash
sudo hostnamectl set-hostname gw.grupo1.turma914.ifalara.local
```
```bash
sudo reboot
```

2. Adicionar os dominios e ips corretos e atualizados e aplicar:
```bash
sudo nano /etc/netplan/00-installer-config.yaml 
```
```bash
sudo netplan apply
```

3. Habilitar e liberar acesso ssh do firewall
```bash
sudo ufw enable
```
```bash
sudo ufw allow ssh
```

4. Remover marca de comentária onde net/ipv4/ip_forward=1
```bash
sudo nano /etc/ufw/sysctl.conf
```
![image](https://user-images.githubusercontent.com/62352928/159006697-79ffdbfd-1f16-45b4-9673-24a63616dd52.png)

5. Checar se está tudo correto
```bash
ifconfig -a
```
![image](https://user-images.githubusercontent.com/62352928/159006829-96581577-29c0-47e0-bc05-3e215b212c05.png)

6. Video do servidor WEB LAMP em funcionamento: https://drive.google.com/file/d/14xYc_dvHQEkp6SFXqEvGQn_h1qiTqFkz/view?usp=sharing

### Máquina gw de configuração do gateway server/NAT

1. Criar arquivo lembrando de colocar seu LAN e WAN corretamente
```bash
sudo nano /etc/rc.local
```
![image](https://user-images.githubusercontent.com/62352928/159009255-59808347-4aa8-4308-8da3-67b992c48337.png)

2. Tornar o arquivo inicializável no boot
```bash
sudo chmod 755 /etc/rc.local
```

3. Rodar o arquivo
```bash
sudo /etc/rc.local
```

4. Ir na maquina samba para modificar o arquivo adicionando o gateway configurado e comentar o outro
```bash
sudo nano /etc/netplan/00-installer-config.yaml
```
![image](https://user-images.githubusercontent.com/62352928/159009729-823f0dca-ef4b-4d6b-964b-13594c645e77.png)

5. Aplicar alteração
```bash
sudo netplan apply
```

6. A partir desse momento nossa maquina samba so fica acesśel a partir da nossa maquina gw pois mudamos a rota

7. Por conta disso precisaremos modificar no gw o arquivo /etc/rc.local adicionando as rotas com o ip do samba
![image](https://user-images.githubusercontent.com/62352928/159010006-0f30cb19-2f06-4c50-98ba-ab6b09505c68.png)

8. Vamos agora no ns1 em /etc/bind/zones/db.grupo1.turma914.ifalara.local  para comentar o samba existente e atribuir ao samba o ip do gw ou simplesmente deixar samba cname gw
![image](https://user-images.githubusercontent.com/62352928/159010144-84b98980-bcc4-4852-b589-657264a8a21e.png)

9. Reiniciar e aplicar as alterações
```bash
sudo systemctl restart bind9
```

10. Alterar o ip do samba pela subrede
```bash
sudo vi /etc/rc.local
```
![image](https://user-images.githubusercontent.com/62352928/159010411-3795dec8-070a-42fc-ae62-849854d991eb.png)

11. Executar o arquivo
```bash
sudo /etc/rc.local
```

12. No arquivo /etc/rc.local adicionar o encaminhamento para o servidor web a porta 80
![image](https://user-images.githubusercontent.com/62352928/159010607-0905c005-d496-41e8-94f0-c795f42864b9.png)

13. Ir na maquina www em /etc/netplan/00-installer-config.yaml para fazer a dependência ao gateway pela subrede
![image](https://user-images.githubusercontent.com/62352928/159010707-10e768a6-22ef-4d8f-b80d-5355d4b06ea2.png)

14. Agora só teremos acesso ao nosso site atraves do gw (olhar URL)
![image](https://user-images.githubusercontent.com/62352928/159010791-684385e4-c47f-44e2-b1dc-eed44982be47.png)

15. Por fim, adicionar o bd á dependência do gateway modificando o arquivo /etc/netplan/00-installer-config.yaml da maquina bd
![image](https://user-images.githubusercontent.com/62352928/159010924-860e5652-5f11-418d-ab4c-476afbe81872.png)

## Considerações Finais
