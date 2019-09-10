## Odoo 12 Setup

Terminale satır satır yapıştıralım.

### Adettendir ilk önce update yapılır.
 
```
sudo apt-get update $$ sudo apt-get upgrade
```


### Python 3.7 ve yüklenir. (Birkaçı ekstra)

```
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt install python3.7 python3-venv python3.7-venv
```

### Pycharm yüklenir ve tam olarak yetkilendirilir. (Yetkilendirme ne kadar gerekli bilmiyorum.)

```
sudo snap install pycharm-community --classic
sudo chmod -R 777 /snap/pycharm-community
```

### Odoo gereksinimleri yüklenir. (Birkaç ekstra var.)

```
sudo apt install git virtualenv python3-pip libpq-dev python-dev libxml2-dev libxslt1-dev libffi-dev libcups2-dev libcairo2-dev build-essential wget libgirepository1.0-dev python3-dev python3.7-dev python3-wheel libxslt-dev libzip-dev libldap2-dev libsasl2-dev python3-setuptools node-less wkhtmltopdf
```

### Odoo yüklemek için klasör açılır ve tam yetki verilir.

```
sudo mkdir /opt/odoo12
sudo mkdir /opt/odoo12/custom-addons
sudo chmod -R 777 /opt/odoo12
```

### PostgreSQL kurulur. (İlk 3 satır gizemini korumakta.)

```
sudo apt-get install wget ca-certificates 
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
sudo apt update
sudo apt install postgresql postgresql-contrib
```

### PostgreSQL için kullanıcı yaratılır. 

```
sudo su - postgres -c "createuser -s $USER"
```

### PostgreSQL default kullanıcı olan "postgres"e ve bizim kullanıcımız "$USER"a şifre atanır. (Default olarak atanmıyor.)

```
sudo -u postgres psql
\password
Enter new password: postgres   ##"postgres" kullanıcısı için verilecek şifre, DBeaver ve PgAdmin için kullanılacak.
Enter it again: postgres
exit
```
```
sudo -u $USER psql   ## Eğer bu koddan sonra psql: FATAL:  database "$USER" does not exist derse bir aşağıdaki kodla database oluşturun.
createdb $USER
sudo -u $USER psql
\password

Enter new password: <Odoo konfigürasyon dosyasında "db_password" kısmına yazılacak şifre belirleyin.>
Enter it again: <Odoo konfigürasyon dosyasında "db_password" kısmına yazılacak şifre belirleyin.>

exit
dropdb $USER    ##Bunu yapmazsak Odoo, $USER database'ini uyumlu olmamasına rağmen açmaya çalışıp hata verebilir.
```
### Odoo 12, GitHub'tan indirilir.

```
git clone https://github.com/odoo/odoo --depth 1 --branch 12.0 /opt/odoo12/odoo
```

### Python 3.7 Virtual Environment hazırlanır.
```
cd /opt/odoo12
virtualenv -p python3.7 odoo-venv
source odoo-venv/bin/activate
pip3.7 install Babel chardet decorator docutils ebaysdk feedparser gevent greenlet html2text Jinja2 libsass lxml Mako MarkupSafe mock num2words ofxparse passlib phonenumbers Pillow psutil psycopg2 pydot pyldap pyparsing PyPDF2 pyserial python-dateutil pytz pyusb qrcode reportlab requests suds-jurko vatnumber vobject Werkzeug XlsxWriter xlwt xlrd
deactivate
```

### Odoo default konfigürasyon dosyası, gerekli yere kopyalanır ve düzenlenir.
```
sudo cp /opt/odoo12/odoo/debian/odoo.conf /etc/odoo12.conf
sudo gedit /etc/odoo12.conf
```

### Aşağıdaki kısım açılan dosyanın içindekiler silinerek yapıştırılır.
### Ayarlamalardan sonra save edilir.
```
[options]
; This is the password that allows database operations:
admin_passwd = <Buraya odoo için Master Password yazıyoruz. (Odoo arayüzünde database oluşturulken sorulacak.)>
db_host = localhost
db_port = 5432
db_user = <Sistem Kullanıcı Adı'nı yazın: Terminalde yeşil yazıda '@'ten önceki kısım.>
db_password = <PostgreSQL kurulumunda $USER'a verdiğiniz şifreyi yazın.>
addons_path = /opt/odoo12/odoo/addons,/opt/odoo12/custom-addons
```
### Pycharm açılır.

```
pycharm-community  ## Veya uygulamalar menüsünden de açılabilir.
```

- Open Project seçeneğinden "/opt/odoo12" seçilir.

- Projenin indexlenmesi bittiğinde sol taraftan "odoo" klasörüne sağ tıklanıp "Mark Directory as > Sources Root" seçilir.

- Sağ üst tarafta "Add Configuration" açılan pencereden sol üstte "+" seçeneğinden "Python" seçilir.

```
Name: <Keyfe kalmış Odoo 12 yazılabilir>
Script path: /opt/odoo12/odoo/odoo-bin
Parameters: -c /etc/odoo12.conf        ##Tüm parametreler: https://www.odoo.com/documentation/12.0/reference/cmdline.html
Python interpreter: (Otomatik olarak Python 3.7(odoo12) /opt/odoo12/odoo-venv....) seçilmiş olmalı, seçili değilse ayarlayın.
```

- Artık Odoo 12 sağ üst taraftan çalıştırılıp durdurulabilecek hale geldi.

- İnternet tarayıcısından "localhost:8069" adresinden Odoo açılır. 
- Database oluşturma ekranı gelecek.
- Master Password kısmına konfigürasyonda belirlediğiniz şifre yazılır.
- Geri kalan kısımlardan sadece "Mail" ve "Password" kısmı unutulmayacak şekilde yazılmalıdır.
- Mail adresinin gerçeken mail adresi olmasına gerek yok içinde "@" olmayan bir şey de yazılabilir.

## Odoo kurulumu tamam !

- Demo kullanıcısı ile giriş yapmak için = Mail:demo PW:demo


### PgAdmin 4 Kurulumu

```
sudo apt-get install pgadmin4 pgadmin4-apache2
```

- Kurulum sırasında belirteceğiniz mail adresi ve şifreyi düzgün yazın, yanlış yazılırsa çözümü şimdilik bende yok.

- Tarayıcıdan "localhost/pgadmin4" açılır.
- Add New Server
- Açılan pencerenin "General" sekmesinde "Name" kısmına keyfi isim yazılır.
- "Connection" sekmesi

```
Host name/address: localhost
Port: 5432
Maintenance database: postgres
Username: postgres
Password: postgres  ## PostgreSql kurulumunda "postgres" kullanıcısına verilen şifre.
Save Password=: True
Role: <Boş>
Service: <Boş>
```


### DBeaver Kurulumu
```
sudo add-apt-repository ppa:serge-rider/dbeaver-ce
sudo apt-get update
sudo apt-get install dbeaver-ce
```

- Ubuntunun uygulamalar menüsüne ikon olarak gelmiş olacak. Oradan açın.
- Açılan pop-up'tan PostgreSql seçin.

```
Host: localhost
Database: <Boş>
User: postgres
Password: postgres  ## PostgreSQL kurulumnda "postgres" kullanıcısına koyduğumuz şifreydi.
```

- Settings'ten Show all databases seçin. (Bunu seçtiğimizde "postgres" isimli default database focuslanacak. Yani Odoo için SQL komutları yazamayacağız. Focus değiştirmek için sol taraftaki database listesinden uygun database'e sağ tıklayıp "Set Active" diyeceksiniz.)

- Finish dediğinizde veya demeden önce de olabilir, karşınıza bir şey yüklemenizi isteyen bir ekran çıkacak.
- Download diyin.
- Artık Databaselere soldaki taraftan erişebilirsiniz.

### İŞLEM TAMAM !!

