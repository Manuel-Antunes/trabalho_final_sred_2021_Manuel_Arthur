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
| IP do WEB | ens160 | 10.9.14.xxx | ens192 |	192.168.14.5 |
| IP do BD | ens160 | 10.9.14.xxx | ens192 |	192.168.14.6 |

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


## Considerações Finais
