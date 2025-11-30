#5 - CLOUDERA PIG HIVE

nano wc.pig

#file 
data = LOAD '/input/in.txt' AS (line:chararray);
words = FOREACH data GENERATE FLATTEN(TOKENIZE(line)) AS word;
groups = GROUP words BY word;
count = FOREACH groups GENERATE group, COUNT(words);
DUMP count;

#BT terminal
echo "hadoop pig count" > in.txt
echo "count pig cloudera" >> in.txt
hdfs dfs -mkdir /input
hdfs dfs -put in.txt /input

pig wc.pig
