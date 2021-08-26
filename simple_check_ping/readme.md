# Checar disponibilidade de IP com notificações por E-mail com Template ICMP Ping
Esse passo a passo descreve como habilitar as notificações por e-mail no Zabbix num cenário que utiliza o _Simple Check_ do Zabbix para efetuar ping em um IP remoto,alertando para falta de disponibilidade.

# Requisitos
- Pacote [FPING instalado e ativado no Zabbix Server](https://www.zabbix.com/forum/zabbix-cookbook/10399-complete-guide-setting-up-simple-ping-non-agent).
- Configurações SMTP devidamente funcionais (ver Configuration/Media Type). Se for utilizar o GMAIL, siga os [passos para criar uma senha de aplicativo](https://support.google.com/accounts/answer/185833?hl=pt).
- Porta ICMP liberada (embora a configuração use a interface do Agent Zabbix, não é utilizada a porta padrão 10050, sim a ICMP)

# Estudo de Caso
Neste caso vamos fazer uma checagem simples para verificar se um IP alvo ( site, router, etc) está respondendo ao PING a partir do Zabbix Server, emitindo aletas por e-mail e outras mídias. 

Podemos pensar numa empresa ou instituição formada por filiais espalhadas pelo país, em que uma filial deseja monitorar o roteador de borda da outra, notificando as equipe de TI da filial local.

O desafio deve incluir:
 - Criar contas para usuários externos no Zabbix
 - Incluir permissões mais restritas no Zabbix aos grupos de usuários externos
 - Limitar acesso somente aos _hosts_ ligados a sua respectiva filial
 - Limitar acesso ao _frontend_ do Zabbix (caso não seja útil) aos usuários externos

## Habilitar a mídia EMAIL usando GMAIL
Pelo frontend do Zabbix, com permissões de administrador acesse o menu **Administration/Media Types**. Opcionalmente você pode escolher uma das mídias, abrir e clonar as configurações básicas para posterior ajustes.

Em nosso caso, optamo pelo opção **Email**, sendo que utilizamos a conta do GMAIL:

![image](https://user-images.githubusercontent.com/6537456/130980443-63086700-a4ee-4bc3-8cf8-fd1bad16c15b.png)

Atente para as mudanças no GMAIL que hoje requer a configuração de uma [senha de aplicativo](https://support.google.com/accounts/answer/185833?hl=pt) para conta que vai ser cadastrada nas configurações SMTP do Zabbix.

## Criar USER GROUP
Também no menu **Administration** acesse **User Groups** para criar um grupo de usuários. No Zabbix, é a única forma de aplicar regras de restrição de acesso aos _hosts_. Ou seja, para garantir que determinados usuários tenham ou não acesso a _hosts_ específicos, as permissões serão aplicadas ao grupo de usuários e não a uma conta individual.
![image](https://user-images.githubusercontent.com/6537456/130982416-ac0caa15-2214-415a-bc79-c7b825e2fdb8.png)

O grupo de usuários no exemplo acima tem acesso de leitura (_Read_) no grupo específico de _hosts_ chamado **filial-poa**. 
> Grupo de Usuários e Grupos de Hosts por ser um ponto de confusão!

## Criar HOST e atribuir grupo
Para criar o _hosts_ precisamos atentar para **atribuir ao grupo** que contém as devidas permssiões sobre os _hosts_ a serem monitorados, selecionamos na interface o _Agent_ apontando para o IP alvo, mas ignorando a porta padrão 10050 (no fim das contas será usado ICMP).

![image](https://user-images.githubusercontent.com/6537456/130987872-3feef9c9-2d14-4e02-87e7-382a1e15d6c3.png)

Na guia **_Templates_** é fundamental selecionar o template **ICMP Ping**. Ele trará três gatilhos (_Triggers_) que efetivamente farão as checagens usando o protocolo ICMP

![image](https://user-images.githubusercontent.com/6537456/130988502-1e88c9c6-feaa-4767-a2f3-669b9912770e.png)

> Pode ser necessário fazer liberações de _firewall_ na rota entre o Zabbix Server e o IP alvo do PING (ICMP).

## Criar USER ROLES
Em **Configuration/User Roles** são definido papéis, com respectivas permissões no sistema. Esses papéis, ao serem vinculado a um conta de usuário, atribuem a esse usuário as permissões no sistema. No cenário onde o foco é simplesmente a checagem via Ping e notificação por e-mail, foi criado um papel bem restritivo, pegando o _User Type_ Admin como arquétipo, porém retirando boa parte das permissões, deixando somente itens relacionados às notificações ( requer revisão com foco em restringir mais as permissões).
![image](https://user-images.githubusercontent.com/6537456/130984688-03500f13-9450-40be-9ed8-019d8159af72.png)

> Não foi necessário habilitar nada relacionado a API na _user role_

## Criar USER
Os pontos de cuidado para criação do usuário é preencher corretamente os dados e **atribuir o grupo** (é o que garante a permissão de acesso aos hosts da filial), cadastrar uma mídia atentando para o tipo (nesse caso E-mail) e na guia **permissões** deve-se atribuir a _user role_ definida anteriomente, que vai delimitar as permissões do usuário dentro do sistema.
![image](https://user-images.githubusercontent.com/6537456/130985753-7396f8b8-33a6-499a-9583-ae7554f823ce.png)

Imaginando que o foco aqui é somente notificar as filiais sobre indisponibilidades, convém incluir o usuário no grupo _**No access to the frontend**_, assim não terá acesso desnecessário ao console web do Zabbix, embora receberá normalmente os alertas no e-mail.

![image](https://user-images.githubusercontent.com/6537456/130985670-c08eec00-7f7c-4b20-acfc-a92f1dfad4cd.png)

## Criar HOST GROUP



## Aplicar user roles e atribuir user group aos usuários




