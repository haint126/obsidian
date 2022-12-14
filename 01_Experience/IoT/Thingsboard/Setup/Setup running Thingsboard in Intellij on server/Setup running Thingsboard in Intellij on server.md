# 1. Install necessary tools for CentOS

## 1.1. For CentOS 7:
```bash
# Install wget
sudo yum install -y nano wget
# Add latest EPEL release for CentOS 7
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest--7.noarch.rpm
```

## 1.2. For CentOS 8:
```bash
# Install wget
sudo yum install -y nano wget
# Add latest EPEL release for CentOS 8
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```

# 2. Install java 11 (OpenJDK)
- Thingsboard service is running on Java 11. Follow this instructions to install OpenJDK 11:

```bash
sudo yum install java-11-openjdk
```

- (Need verification) Configure your operating system to use OpenJDK 11 by default. You can configure which version is the default using the following command:

```bash
sudo yum install java-11-openjdk-devel
```

# 3. Config firewall to open public ports
- Open public port on server to communicate with devices and end users:

```bash
sudo firewall-cmd --zone=public --add-port=5683-5688/udp --permanent
sudo firewall-cmd --reload
```

## 3.1. Additional commands
- List all firewall rules:

```bash
firewall-cmd --list-all
```

- List all currently allowed services:

```bash
firewall-cmd --list-services
```

- List all opened ports:

```bash
firewall-cmd --list-ports
```

From <https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/security_guide/sec-viewing_current_status_and_settings_of_firewalld>

# 4. Install PostgreSQL Database
- By default ThingsBoard uses PostgreSQL database to store entities and timeseries data.

## 4.1. Install PostgreSQL
- Please use [this link](https://wiki.postgresql.org/wiki/Detailed_installation_guides) for the PostgreSQL installation instructions.

### 4.1.1. For CentOS 7
```bash
# Install the repository RPM (for CentOS 7):
sudo yum -y install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
# Install packages
sudo yum -y install epel-release yum-utils
sudo yum-config-manager --enable pgdg12
sudo yum install postgresql12-server postgresql12
# Initialize your PostgreSQL DB
sudo /usr/pgsql-12/bin/postgresql-12-setup initdb
sudo systemctl start postgresql-12
# Optional: Configure PostgreSQL to start on boot
sudo systemctl enable --now postgresql-12
```

### 4.1.2. For CentOS 8
```bash
# Install the repository RPM (for CentOS 8):
sudo yum -y install https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
# Install packages
sudo dnf -qy module disable postgresql
sudo dnf -y install postgresql12 postgresql12-server
# Initialize your PostgreSQL DB
sudo /usr/pgsql-12/bin/postgresql-12-setup initdb
sudo systemctl start postgresql-12
# Optional: Configure PostgreSQL to start on boot
sudo systemctl enable --now postgresql-12
```

## 4.2. Change password
- Once PostgreSQL is installed you may want to
	- Create a new user
	- OR set the password for the the main user.
- This instructions below will help to set the password for main postgresql user

```bash
sudo su - postgres
psql
\password
\q
```

- Then, press “Ctrl+D” to return to main user console.

## 4.3. Enable MD5 authentication
- After configuring the password, edit the `pg_hba.conf` to use MD5 authentication with the postgres user.

```bash
sudo nano /var/lib/pgsql/12/data/pg_hba.conf
OR
sudo vi /var/lib/pgsql/12/data/pg_hba.conf
```

- Locate the following lines:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
```

- Replace **ident** with **md5**:

```
host    all             all             127.0.0.1/32            md5
```

## 4.4. Restart PostgreSQL
- Finally, you should restart the PostgreSQL service to initialize the new configuration:

```bash
sudo systemctl restart postgresql-12.service
```

- Then, press “Ctrl+D” to return to main user console and connect to the database to create thingsboard DB:

## 4.5. Create Thingsboard database
- Access Postgre terminal:

```bash
psql -U postgres -d postgres -h 127.0.0.1 -W
```

- Create database:

```sql
CREATE DATABASE thingsboard;
```

- Exit Postgres terminal:

```bash
\q
```

## 4.6. Additional command for PostgreSQL
- List database:
- List database: `\l`
- Connect to database: `\c database_name`
- List table of database:

From <https://www.geeksforgeeks.org/postgresql-psql-commands/>

| Command                                          | Description                                                         | Additional Information                                                                                    |
| ------------------------------------------------ | ------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| psql -d database -U user -W                      | Connects to a database under a specific user                        | -d: used to state the database name <br>-U:used to state the database user                                |
| psql -h host -d database -U user -W              | Connect to a database that resides on another host                  | -h: used to state the host <br>-d: used to state the database name <br>-U:used to state the database user |
| psql -U user -h host “dbname=db sslmode=require” | Use SSL mode for the connection                                     | -h: used to state the host <br>-U:used to state the database user                                         |
| \c dbname                                        | Switch connection to a new database                                 |                                                                                                           |
| \l                                               | List available databases                                            |                                                                                                           |
| \dt                                              | List available tables                                               |                                                                                                           |
| \d table_name                                    | Describe a table such as a column, type, modifiers of columns, etc. |                                                                                                           |
| \dn                                              | List all schemes of the currently connected database                |                                                                                                           |
| \df                                              | List available functions in the current database                    |                                                                                                           |
| \dv                                              | List available views in the current database                        |                                                                                                           |
| \du                                              | List all users and their assign roles                               |                                                                                                           |
| SELECT version();                                | Retrieve the current version of PostgreSQL server                   |                                                                                                           |
| \g                                               | Execute the last command again                                      |                                                                                                           |
| \s                                               | Display command history                                             |                                                                                                           |
| \s filename                                      | Save the command history to a file                                  |                                                                                                           |
| \i filename                                      | Execute psql commands from a file                                   |                                                                                                           |
| ?                                                | Know all available psql commands                                    |                                                                                                           |
| \h                                               | Get help                                                            | Eg:to get detailed information on ALTER TABLE statement use the \h ALTER TABLE                            |
| \e                                               | Edit command in your own editor                                     |                                                                                                           |
| \a                                               | Switch from aligned to non-aligned column output                    |                                                                                                           |
| \H                                               | Switch the output to HTML format                                    |                                                                                                           |
| \q                                               | Exit psql shell                                                     |                                                                                                           |

# 5. Setup environment in Intellij:

## 5.1. Clone thingsboard's repository
```bash
git clone https://github.com/thingsboard/thingsboard.git
```

## 5.2. Open Thingsboard in IntelliJ
- You can open Thingsboard Project at server over Remote Development (SSH) in IntelliJ

## 5.3. Install npm
```bash
sudo yum install npm
```

## 5.4. Setup SDK
- Select: File -> Project structure

![[01_Experience/IoT/Thingsboard/Setup/Setup running Thingsboard in Intellij on server/sdk.png]]

## 5.5. Get dependencies and build Thingsboard Project
- In terminal tab in IntelliJ, run command:

```bash
cd home/user/thingsboard
mvn clean install -DskipTests
```

## Fix `gen.MsgProtos` not found after building Maven
- Install plugins `Protobuf support`
- In file `common/message/src/main/java/org/thingsboard/server/common/msg/TbMsg.java`
	- In line `import org.thingsboard.server.common.msg.gen.MsgProtos;`
		- Press Alt+Enter
		- Choose `Add dependency ...`

## 5.10. Change Thingsboard configurations
- Any configuration (i.e. server port, username/password database) can be change in `thingsboard\application\src\java\org\thingsboard\server\thingsboard.yml`
- Change all ports:
	- Search "port:"
		- There are 11 ports to change (need verification)
	- For haint126 only: add 3 to default port
		- 8080 -> 38080
	- Read [[01_Experience/IoT/Thingsboard/Setup/Setup LWM2M/Setup LWM2M]]
- You can change Postgre database information here and skip "Environment variables" part

## 5.6. Connect Thingsboard with created PostgreSQL database 
- In directory `\thingsboard\application\src\main\java\org\thingsboard\server`

### 5.6.1. Load Thingsboard schemas to Database
- Change your database location in `SPRING_DATASOURCE_URL`
- Change `install.data_dir`

```txt
SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/YOUR_THINGSBOARD_DATABASE_NAME
SPRING_DATASOURCE_USERNAME=postgres
SPRING_DATASOURCE_PASSWORD=1
install.load_demo=true
install.data_dir=YOUR_THINGSBOARD_DIRECTORY/application/target/data      # Location of initialized data
```

![[01_Experience/IoT/Thingsboard/Setup/Setup running Thingsboard in Intellij on server/Install application.png]]

### 5.6.2. Connect database to Thingsboard server
```txt
SPRING_DATASOURCE_URL=jdbc:postgresql://localhost:5432/YOUR_THINGSBOARD_DATABASE_NAME
SPRING_DATASOURCE_USERNAME=postgres
SPRING_DATASOURCE_PASSWORD=1
```

![[01_Experience/IoT/Thingsboard/Setup/Setup running Thingsboard in Intellij on server/Server application.png]]

- Run application `ThingsboardInstallApplication`

## 5.7. Running server-side service
- There are multiple ways to start server-side container service at port 8080:

### 5.7.1. First option:
- You can run the main method of `org.thingsboard.server.ThingsboardServerApplication` class that is located in application module from your IDE.

### 5.7.2. Second option:
- You can start the server from command line as a regular Spring boot application:

```bash
cd ${THINGSBOARD_DIR}
java -jar application/target/thingsboard-${VERSION}-boot.jar
```

## 5.8. (Optional) Running UI container in hot redeploy mode
- Normally, port 8080 is the main application port.
- However, there is hot redeploy mode which can be used to change frontend code without the need to restart backend.
- To start UI in hot redeploy mode (at port 4200), run these commands:
	- This will launch a special server that will listen on 4200 port. All REST API and websocket requests will be forwarded to 8080 port.

```bash
cd ${THINGSBOARD_DIR}/ui-ngx
mvn clean install -P yarn-start
```

## 5.9. Login Thingsboard
- Navigate to <http://localhost:4200/> or <http://localhost:8080/> and login into Thingsboard using demo data credentials:
	- User: tenant@thingsboard.org
	- Password: tenant

#
---
- Status: #done
- Tags: #thingsboard
- References:
	- [Source](https://thingsboard.io/docs/user-guide/install/rhel/)
	- [Source2](https://www.jianshu.com/p/7ad9d265b953)
- Related:
