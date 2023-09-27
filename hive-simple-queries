CREATE TABLE IF NOT EXISTS test_table(col1 int, col2 string)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE;

INSERT INTO default.test_table values(1, 'Some test value 1');
INSERT INTO test_table values(2, 'Some test value 2');
SELECT * FROM test_table;
