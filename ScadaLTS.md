To check that there is data in the scada LTS container, that the schema contains all the tables and data.

All the `.frm` and `.ibd` files correspond to tables in the ScadaLTS database, so everything you added in the browser is persisted in the `scadalts_mysql-data` Docker volume.

```
sreedevirajavelu@srees-MacBook-Air ~ % docker run --rm -v scadalts_mysql-data:/var/lib/mysql busybox ls -l /var/lib/mysql/scadalts

total 16080
-rw-r-----    1 27       27            8624 Aug 13 05:22 category_views_hierarchy.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 category_views_hierarchy.ibd
-rw-r-----    1 27       27            8792 Aug 13 05:22 compoundEventDetectors.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 compoundEventDetectors.ibd
-rw-r-----    1 27       27            8650 Aug 13 05:22 dataPointUsers.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 dataPointUsers.ibd
-rw-r-----    1 27       27            8664 Aug 13 05:22 dataPointUsersProfiles.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 dataPointUsersProfiles.ibd
-rw-r-----    1 27       27            8793 Aug 13 05:22 dataPoints.frm
-rw-r-----    1 27       27          475136 Aug 25 08:16 dataPoints.ibd
-rw-r-----    1 27       27            8610 Aug 13 05:22 dataSourceUsers.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 dataSourceUsers.ibd
-rw-r-----    1 27       27            8624 Aug 13 05:22 dataSourceUsersProfiles.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 dataSourceUsersProfiles.ibd
-rw-r-----    1 27       27            8694 Aug 13 05:22 dataSources.frm
-rw-r-----    1 27       27          114688 Aug 22 08:58 dataSources.ibd
-rw-r-----    1 27       27              61 Aug 13 05:22 db.opt
-rw-r-----    1 27       27            8586 Aug 13 05:22 eventDetectorTemplates.frm
-rw-r-----    1 27       27           98304 Aug 13 05:22 eventDetectorTemplates.ibd
-rw-r-----    1 27       27            8786 Aug 13 05:22 eventHandlers.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 eventHandlers.ibd
-rw-r-----    1 27       27            9076 Aug 13 05:22 events.frm
-rw-r-----    1 27       27         9437184 Sep 15 04:12 events.ibd
-rw-r-----    1 27       27           57822 Aug 13 05:22 flexProjects.frm
-rw-r-----    1 27       27           98304 Aug 13 05:22 flexProjects.ibd
-rw-r-----    1 27       27            1574 Aug 13 05:22 historyAlarms.frm
-rw-r-----    1 27       27            2208 Aug 13 05:22 liveAlarms.frm
-rw-r-----    1 27       27            8632 Aug 13 05:22 mailingListInactive.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 mailingListInactive.ibd
-rw-r-----    1 27       27            8682 Aug 13 05:22 mailingListMembers.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 mailingListMembers.ibd
-rw-r-----    1 27       27            8970 Aug 13 05:22 mailingLists.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 mailingLists.ibd
-rw-r-----    1 27       27            9468 Aug 13 05:22 maintenanceEvents.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 maintenanceEvents.ibd
-rw-r-----    1 27       27            8650 Aug 13 05:22 mangoViewUsers.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 mangoViewUsers.ibd
-rw-r-----    1 27       27            8894 Aug 13 05:22 mangoViews.frm
-rw-r-----    1 27       27          131072 Sep 15 03:41 mangoViews.ibd
-rw-r-----    1 27       27               0 Aug 13 05:22 multi_changes_history.MYD
-rw-r-----    1 27       27            1024 Aug 13 05:22 multi_changes_history.MYI
-rw-r-----    1 27       27            8790 Aug 13 05:22 multi_changes_history.frm
-rw-r-----    1 27       27            8914 Aug 13 05:22 plcAlarms.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 plcAlarms.ibd
-rw-r-----    1 27       27            9104 Aug 13 05:22 pointEventDetectors.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 pointEventDetectors.ibd
-rw-r-----    1 27       27            8652 Aug 13 05:22 pointHierarchy.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 pointHierarchy.ibd
-rw-r-----    1 27       27            8792 Aug 13 05:22 pointLinks.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 pointLinks.ibd
-rw-r-----    1 27       27            8774 Aug 13 05:22 pointValueAnnotations.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 pointValueAnnotations.ibd
-rw-r-----    1 27       27             349 Aug 13 05:22 pointValues.TRG
-rw-r-----    1 27       27            8706 Aug 13 05:22 pointValues.frm
-rw-r-----    1 27       27          147456 Aug 25 07:50 pointValues.ibd
-rw-r-----    1 27       27            8614 Aug 13 05:22 publishers.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 publishers.ibd
-rw-r-----    1 27       27            8708 Aug 13 05:22 reportInstanceData.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 reportInstanceData.ibd
-rw-r-----    1 27       27            8802 Aug 13 05:22 reportInstanceDataAnnotations.frm
-rw-r-----    1 27       27           98304 Aug 13 05:22 reportInstanceDataAnnotations.ibd
-rw-r-----    1 27       27            9098 Aug 13 05:22 reportInstanceEvents.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 reportInstanceEvents.ibd
-rw-r-----    1 27       27           21204 Aug 13 05:22 reportInstancePoints.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 reportInstancePoints.ibd
-rw-r-----    1 27       27            8772 Aug 13 05:22 reportInstanceUserComments.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 reportInstanceUserComments.ibd
-rw-r-----    1 27       27            9006 Aug 13 05:22 reportInstances.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 reportInstances.ibd
-rw-r-----    1 27       27            8678 Aug 13 05:22 reports.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 reports.ibd
-rw-r-----    1 27       27            9472 Aug 13 05:22 scheduledEvents.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 scheduledEvents.ibd
-rw-r-----    1 27       27            8676 Aug 13 05:22 scheduledExecuteInactiveEvent.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 scheduledExecuteInactiveEvent.ibd
-rw-r-----    1 27       27            8940 Aug 13 05:22 schema_version.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 schema_version.ibd
-rw-r-----    1 27       27           57864 Aug 13 05:22 scripts.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 scripts.ibd
-rw-r-----    1 27       27            8706 Aug 13 05:22 synopticPanels.frm
-rw-r-----    1 27       27           98304 Aug 13 05:22 synopticPanels.ibd
-rw-r-----    1 27       27            8620 Aug 13 05:22 systemSettings.frm
-rw-r-----    1 27       27           98304 Sep 15 03:41 systemSettings.ibd
-rw-r-----    1 27       27            9168 Aug 13 05:22 templatesDetectors.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 templatesDetectors.ibd
-rw-r-----    1 27       27              43 Aug 13 05:22 tri_notify_faults_or_alarms.TRN
-rw-r-----    1 27       27            8714 Aug 13 05:22 userComments.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 userComments.ibd
-rw-r-----    1 27       27            8638 Aug 13 05:22 userEvents.frm
-rw-r-----    1 27       27          196608 Sep 15 04:12 userEvents.ibd
-rw-r-----    1 27       27           13428 Aug 13 05:22 users.frm
-rw-r-----    1 27       27           98304 Sep 15 03:44 users.ibd
-rw-r-----    1 27       27            8614 Aug 13 05:22 usersProfiles.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 usersProfiles.ibd
-rw-r-----    1 27       27            8612 Aug 13 05:22 usersUsersProfiles.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 usersUsersProfiles.ibd
-rw-r-----    1 27       27               0 Aug 13 05:22 values_multi_changes_history.MYD
-rw-r-----    1 27       27            1024 Aug 13 05:22 values_multi_changes_history.MYI
-rw-r-----    1 27       27            8758 Aug 13 05:22 values_multi_changes_history.frm
-rw-r-----    1 27       27            8654 Aug 13 05:22 viewUsersProfiles.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 viewUsersProfiles.ibd
-rw-r-----    1 27       27            8638 Aug 13 05:22 views_category_views_hierarchy.frm
-rw-r-----    1 27       27           98304 Aug 13 05:22 views_category_views_hierarchy.ibd
-rw-r-----    1 27       27            8658 Aug 13 05:22 watchListPoints.frm
-rw-r-----    1 27       27          131072 Aug 20 09:51 watchListPoints.ibd
-rw-r-----    1 27       27            8650 Aug 13 05:22 watchListUsers.frm
-rw-r-----    1 27       27          114688 Aug 13 05:22 watchListUsers.ibd
-rw-r-----    1 27       27            8664 Aug 13 05:22 watchListUsersProfiles.frm
-rw-r-----    1 27       27          131072 Aug 13 05:22 watchListUsersProfiles.ibd
-rw-r-----    1 27       27            8648 Aug 13 05:22 watchLists.frm
-rw-r-----    1 27       27          131072 Aug 20 09:51 watchLists.ibd
sreedevirajavelu@srees-MacBook-Air ~ % 
```


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
