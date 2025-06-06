---
uid: Moving_MySQL_data_to_another_location
---

# Moving MySQL data to another location

If you want to move the MySQL data directory to another location, you cannot just copy the data from one directory to another. You also have to change a number of settings in both the MySQL Administrator and the my.ini file.

1. Stop DataMiner.

1. In MySQL Administrator, do the following:

   1. Go to *Service Control* and stop the MySQL service.

   1. Go to *Startup Variables* and write down the contents of both the *Base directory* and *Data directory* boxes.

1. If the data has to be moved to a folder that does not exist yet, then create that folder.

1. Copy all data in the current data directory to the folder that will become the new data directory.

1. In my.ini, located in the base directory, change the datadir parameter. If your current data directory is `C:\MySQLData`, look for the following line:

   ```txt
   datadir="C:\MySQLData\
   ```

1. In MySQL Administrator, do the following:

   1. Go to *Startup Variables* and enter the new data directory in the *Data directory* box.

   1. Go to *Service Control* and start the MySQL service.

1. Start DataMiner.

1. Check all trend data to make sure the move was successful.
