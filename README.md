### Insert data

```
CREATE TABLE my_table (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    backup_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);


DELIMITER //
CREATE PROCEDURE InsertTenThousandRecordsIntoMyTable()
BEGIN
    DECLARE counter INT DEFAULT 0;
    DECLARE random_name VARCHAR(255);

    WHILE counter < 10000 DO
        SET random_name = CONCAT('Name', RAND());
        INSERT INTO my_table (name) VALUES (random_name);
        SET counter = counter + 1;
    END WHILE;
END //
DELIMITER ;

CALL InsertTenThousandRecordsIntoMyTable();

Select * from my_table;
```

## For full backup

`mysqldump -u root -p dbdb > backup/backup.sql`

`mysql -u root -p dbdb < /backup/backup.sql`

#### File size 544 kb

## For differential. Done with timestamp

`mysqldump -u root -p dbdb > backup/backup.sql`

`mysqldump -u root -p --no-create-info dbdb --where="backup_timestamp > '2023-09-22 13:16:28'" > backup/differential_backup.sql`

#### Full - 544 kb
#### Differential - 57 kb --> growth with time. The day before full backup could be the biggest size

## For incremental. I tried to do with logs strategy, but for complicated to do, because only latest debian works https://stackoverflow.com/questions/73288860/can-not-find-mysqlbinlog-command-in-docker 


`mysqldump -u root -p dbdb > /backup/backup.sql`
`mysqlbinlog /backup/log-bin.00000* > /backup/backup_incremental-1.sql`

### or with timestamp for every change

`mysqldump -u root -p --no-create-info dbdb --where="backup_timestamp > '2023-09-23 14:43:02'" > backup/backup_incremental-date-****.sql`

#### File size the same as for differential. But for everyday it will be almost the same



### Continious data protection 
Seems the same as for replication. All changes are written as bin format and stored in another server for saving data. The most flexible way to make a backup

### Reverse Delta
Implementation is simmilar to incremental. But it calculates the difference between the full backup and the changes that have been made.
Used very rare!
