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

## for differential

`mysqldump -u root -p dbdb > backup/backup.sql`
`mysqldump -u root -p --no-create-info dbdb --where="backup_timestamp > '2023-09-22 13:16:28'" > backup/differential_backup.sql`

## for incremental only latest debian https://stackoverflow.com/questions/73288860/can-not-find-mysqlbinlog-command-in-docker

`mysqldump -u root -p dbdb > backup/backup.sql`
`mysqldump -u root -p --no-create-info dbdb --where="backup_timestamp > '2023-09-22 13:16:28'" > backup/differential_backup.sql`


`mysqldump -u root -p dbdb > /backup/backup.sql`
`mysqlbinlog /backup/log-bin.00000* > /backup/backup_incremental-1.sql`

### or with timestamp for every change

`mysqldump -u root -p --no-create-info dbdb --where="backup_timestamp > '2023-09-23 14:43:02'" > backup/backup_incremental-date-****.sql`

