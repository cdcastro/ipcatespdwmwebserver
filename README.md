# Apache

## Instalar o Apache
```
sudo apt-get update
sudo apt-get install apache2
```

## Verificar o estado do Apache na consola
```
sudo systemctl status apache2
```

## Verificar o Apache no browser
```
http://localhost ou IP local
```

## Gestão do Apache
```
sudo systemctl stop apache2
sudo systemctl start apache2
sudo systemctl restart apache2
sudo systemctl reload apache2
```

## Ativar/desativar o Apache no arranque do sistema operativo
```
sudo systemctl disable apache2
sudo systemctl enable apache2
```

## Ficheiros e pastas importantes no Apache
```
/var/www/html -> pasta por defeito onde são colocados os projetos a servir
/etc/apache2/apache2.conf -> ficheiro de configuração principal
/etc/apache2/ports.conf -> por defeito o Apache está ativo na porta 80 e na 443 (SSL). Neste ficheiro essas portas podem ser alteradas
/var/log/apache2/access.log -> Log de todos os pedidos feitos ao apache
/var/log/apache2/error.log -> Log de todos os erros do apache
```


# MySQL

## Instalar o MySQL
```
sudo apt-get install mysql-server
```


# PHP

## Instalar o PHP
Como iremos utilizar a conjugação AMP (Apache, MySQL e PHP) vamos instalar também alguns pacotes necessários
```
sudo apt-get install php libapache2-mod-php php-mcrypt php-mysql
```

## Testar o processamento PHP no servidor
Criar um ficheiro info.php na 'web root', ou seja em /var/www/html:
```
sudo nano /var/www/html/info.php
```
e colocar o seguinte conteúdo no ficheiro info.php:
```
<?php
phpinfo();
?>
```
Aceder o ficheiro através no browser:
```
http://localhost ou IP local
```


# phpMyAdmin
Um cliente web de MySQL para gestão de bases de dados.

## Instalar o phpMyAdmin
```
sudo apt-get install phpmyadmin apache2-utils
```

## Adicionar o phpMyAdmin à configuração do Apache
```
sudo nano /etc/apache2/apache2.conf
```

## Reiniciar o Apache
```
sudo service apache2 restart
```

## Aceder ao phpMyAdmin no browser
```
http://<localhost ou IP local>/phpmyadmin
```


# Apache Virtual Hosts
Num servidor apache existem normalmente vários virtual hosts. Vamos criar dois virtual hosts como exemplo: ```tespdwm.com``` e ```pw2.com```

## Criar as respetivas pastas no document root do apache
```
sudo mkdir -p /var/www/tespdwm.com/public_html
sudo mkdir -p /var/www/pw2.com/public_html
```

## Para que o nosso utilizar no sistema operativo tenha permissões de escrita nestas pastas:
```
sudo chown -R $USER:$USER /var/www/tespdwm.com/public_html
sudo chown -R $USER:$USER /var/www/pw2.com/public_html
```

## Para termos a certeza que o nosso utilizador tem acesso a todo o document root:
```
sudo chmod -R 755 /var/www
```

## Passemos à criação de uma página exemplo para cada um dos virtual hosts
```
nano /var/www/tespdwm.com/public_html/index.html
```
Colocar um conteúdo HTML nesse ficheiro.

Podemos agora copiar este ficheiro para o outro host:
```
cp /var/www/tespdwm.com/public_html/index.html /var/www/pw2.com/public_html/index.html
```
Alteremos o segundo ficheiro para que seja diferente. Estamos a simular dois websites diferentes, logo o conteúdo deve ser diferente.

## Criar novos ficheiros Virtual Hosts
Por defeito o apache possui um ficheiro ```000-default.conf```. 
Podemos utilizar esse ficheiro como referência para cada um dos novos hosts.

```
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/tespdwm.com.conf
```

Editamos o ficheiro:
```
sudo nano /etc/apache2/sites-available/tespdwm.com.conf
```

O conteúdo do ficheiro deverá ser:
```
<VirtualHost *:80>
    ServerAdmin admin@tespdwm.com
    ServerName tespdwm.com
    ServerAlias www.tespdwm.com
    DocumentRoot /var/www/tespdwm.com/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Repetimos o procedimento para o host ```pw2.com```, desta vez partindo do ficheiro já alterado para o host ```tespdwm.com```.
```
sudo cp /etc/apache2/sites-available/tespdwm.com.conf /etc/apache2/sites-available/pw2.com.conf
```

Editamos o ficheiro:
```
sudo nano /etc/apache2/sites-available/pw2.com.conf
```

O conteúdo do ficheiro deverá ser:
```
<VirtualHost *:80>
    ServerAdmin admin@pw2.com
    ServerName pw2.com
    ServerAlias www.pw2.com
    DocumentRoot /var/www/pw2.com/public_html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## Ativação dos novos Virtual Hosts
Agora que já configuramos os dois novos hosts, é necessário ativá-los.

```
sudo a2ensite tespdwm.com.conf
sudo a2ensite pw2.com.conf
``` 

O apache precisa de ser reiniciado para "reconhecer" estes novos hosts
```
sudo systemctl restart apache2
```
ou 
```
sudo service apache2 restart
```

## Atualizar o ficheiro Hosts do sistema operativo
Dado que não somos proprietários dos domínios que utilizamos nos novos hosts, temos que "informar" o sistema operativo de que esses domínios estão alojados localmente no servidor.

Editar o ficheiro de hosts do sistema operativo:
```
sudo nano /etc/hosts
```

Adicionar os novos domínios que apontam para o IP local
```
127.0.0.1 tespdwm.com
127.0.0.1 pw2.com
```

## Testar no browser
Podemos agora abrir os dois hosts no browser.
```
http://tespdwm.com

e

http://pw2.com
```

# Thats it!
by Carlos de Castro
