1. Persisting information that was added to scada LTS browser -> MySQL.

Run the backup command: 

`docker run --rm -v scadalts_mysql-data:/volume -v $(pwd):/backup busybox tar cvf /backup/mysql-data.tar /volume`


This will create `mysql-data.tar` in your current directory.

After that, can restart the containers. 
- docker-compose up -d

2. Instructions to run Scada-LTS with MySQL and My Data:
Copy the following to the machine :
- docker-compose.yml
- mysql-data.tar (this is the backup of my MySQL volume that contains all scada LTS data)

Put them in the same folder.

3. Load the MySQL volume with my data
- first, create the volume:
- `docker volume create scadalts_mysql-data`

4. Restore the backup data into the volume:
- `docker run --rm -v scadalts_mysql-data:/volume -v $(pwd):/backup busybox tar xvf /backup/mysql-data.tar -C /`
- This step unpacks my database data into the mysql-data volume on your system.

5. Start ScadaLTS & MySQL :
   -  `docker-compose up -d`
