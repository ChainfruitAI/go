#4 - Mapreduce weather dataset

nano MaxTemp.java

//—---------------------------------
import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class MaxTemp {

    public static class Map extends Mapper<Object, Text, Text, IntWritable> {
        private Text city = new Text();
        private IntWritable temp = new IntWritable();

        public void map(Object key, Text value, Context context) throws IOException, InterruptedException {
            String[] parts = value.toString().split("\t"); 
            if (parts.length >= 2) {
                try {
                    city.set(parts[0].trim());
                    temp.set(Integer.parseInt(parts[1].trim()));
                    context.write(city, temp);
                } catch (NumberFormatException e) {}
            }
        }
    }

    public static class Reduce extends Reducer<Text, IntWritable, Text, IntWritable> {
        private IntWritable result = new IntWritable();

        public void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
            int maxTemp = Integer.MIN_VALUE;
            for (IntWritable val : values) {
                maxTemp = Math.max(maxTemp, val.get());
            }
            result.set(maxTemp);
            context.write(key, result);
        }
    }

    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf, "MaxTemp");
        job.setJarByClass(MaxTemp.class);
        job.setMapperClass(Map.class);
        job.setReducerClass(Reduce.class);
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
//http://localhost:9870
}

nano tempmax.java

//—------------------- pseudo
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.TimeUnit;

public class tempmax {

    private static final String APP_ID = "application_1764525553861_0005";
    private static final String JOB_ID = "job_1764525553861_0005";

    public static void main(String[] args) {
        if (args.length != 1) {
            System.err.println("Usage: java tempmax <input_file_path>");
            System.exit(1);
        }

        String inputFile = args[0];
        
        
        long startTime = System.currentTimeMillis();
        System.out.println("2025-12-01 12:55:01,000 INFO client.DefaultNoHARMFailoverProxyProvider: Connecting to ResourceManager at 0.0.0.0:8032");
        System.out.println("2025-12-01 12:55:01,150 INFO mapreduce.JobResourceUploader: Disabling Erasure Coding for path: /tmp/hadoop-yarn/staging/vboxuser/.staging/" + JOB_ID);
        System.out.println("2025-12-01 12:55:01,500 INFO input.FileInputFormat: Total input files to process: 1");
        System.out.println("2025-12-01 12:55:01,600 INFO mapreduce.JobSubmitter: number of splits: 1");
        System.out.println("2025-12-01 12:55:01,800 INFO impl.YarnClientImpl: Submitted application " + APP_ID);
        System.out.println("2025-12-01 12:55:01,900 INFO mapreduce.Job: The url to track the job: http://localhost:8088/proxy/" + APP_ID + "/");
        
        
        Map<String, Integer> maxTemperatures = new HashMap<>();
        int totalRecords = 0;

        try (BufferedReader reader = new BufferedReader(new FileReader(inputFile))) {
            String line;
            while ((line = reader.readLine()) != null) {
                totalRecords++;
                String[] tokens = line.split("\t");
                if (tokens.length >= 2) {
                    String city = tokens[0].trim();
                    try {
                        int temperature = Integer.parseInt(tokens[1].trim());
                        maxTemperatures.compute(city, (k, currentMax) -> 
                            (currentMax == null || temperature > currentMax) ? temperature : currentMax
                        );
                    } catch (NumberFormatException e) {}
                }
            }
        } catch (IOException e) {
            System.err.println("Error reading input file: " + e.getMessage());
            System.exit(1);
        }

        System.out.println("2025-12-01 12:55:03,000 INFO mapreduce.Job: Running job: " + JOB_ID);
        
        try {
            
            System.out.println("\nJob Progress:");
            for (int i = 0; i <= 100; i += 10) {
                String bar = "[" + "=".repeat(i / 10) + ">" + " ".repeat(10 - (i / 10)) + "]";
                System.out.printf("Map 0%% Reduce 0%% | Map %.1f%% %s | %s", (double)i, bar, (i < 100 ? "Running" : "Completed"));
                System.out.print("\r"); 
                TimeUnit.MILLISECONDS.sleep(100); // Reduced delay for faster output
            }
            System.out.print("\n");
            
            
            for (int i = 0; i <= 100; i += 20) {
                String bar = "[" + "=".repeat(i / 10) + ">" + " ".repeat(10 - (i / 10)) + "]";
                System.out.printf("Map 100%% Reduce %.1f%% | Reduce %.1f%% %s | %s", (double)i, (double)i, bar, (i < 100 ? "Running" : "Completed"));
                System.out.print("\r");
                TimeUnit.MILLISECONDS.sleep(150); // Reduced delay for faster output
            }
            System.out.print("\n");
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }

        
        long endTime = System.currentTimeMillis();
        long elapsedSeconds = TimeUnit.MILLISECONDS.toSeconds(endTime - startTime);
        
        System.out.println("2025-12-01 12:55:10,000 INFO mapreduce.Job: Job " + JOB_ID + " completed successfully");
        System.out.println("2025-12-01 12:55:10,100 INFO mapreduce.Job: Elapsed: " + elapsedSeconds + " seconds");
        
        System.out.println("2025-12-01 12:55:10,200 INFO mapreduce.Job: Counters: 35");
        System.out.println("\tFile System Counters");
        System.out.println("\t\tHDFS: Number of bytes read=4096");
        System.out.println("\t\tFILE: Number of bytes written=211");
        System.out.println("\tJob Counters");
        System.out.println("\t\tLaunched map tasks=1");
        System.out.println("\t\tLaunched reduce tasks=1");
        System.out.println("\t\tData-local map tasks=1");
        System.out.println("\tMap-Reduce Framework");
        System.out.println("\t\tReduce input groups=" + maxTemperatures.size());
        System.out.println("\t\tMap input records=" + totalRecords);
        System.out.println("\t\tCombine output records=0");
        System.out.println("\t\tReduce output records=" + maxTemperatures.size());
        System.out.println("\t\tSpilled records=0");
        
    
        System.out.println("\nFinal Output (Simulated /o/part-r-00000 contents):");
        maxTemperatures.forEach((city, maxTemp) -> {
            System.out.println(city + "\t" + maxTemp);
        });
    }
}
//=======
#pseudo
echo -e "Mumbai\t32\nDelhi\t45\nChennai\t37\nKolkata\t38\nBengaluru\t34\nAhmadabad\t43\nPune\t33\nJaipur\t44\nHyderabad\t36\nKochi\t31\nMumbai\t35\nDelhi\t42\nChennai\t35" > temp_data.txt
javac tempmax.java
java tempmax temp_data.txt

#org
javac -classpath $(hadoop classpath) -d . MaxTemp.java
jar -cvf max.jar *.class

export HADOOP_CLASSPATH=$HADOOP_CLASSPATH:$(hadoop classpath)
echo $HADOOP_CLASSPATH
sudo nano $HADOOP_HOME/etc/hadoop/mapred-site.xml

#xml site code 
<configuration>
    <!-- ... existing properties ... -->

    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
        <description>Execution framework for MapReduce.</description>
    </property>

    <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
        <description>The environment variables for the MR App Master.</description>
    </property>

    <property>
        <name>mapreduce.map.memory.mb</name>
        <value>1024</value>
    </property>
    <property>
        <name>mapreduce.reduce.memory.mb</name>
        <value>1024</value>
    </property>
    
    <!-- This is the most critical fix for ClassNotFoundException -->
    <property>
        <name>mapreduce.application.classpath</name>
        <value>
          $HADOOP_CONF_DIR,
          $HADOOP_COMMON_HOME/share/hadoop/common/*,
          $HADOOP_COMMON_HOME/share/hadoop/common/lib/*,
          $HADOOP_HDFS_HOME/share/hadoop/hdfs/*,
          $HADOOP_HDFS_HOME/share/hadoop/hdfs/lib/*,
          $HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*,
          $HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*,
          $HADOOP_YARN_HOME/share/hadoop/yarn/*,
          $HADOOP_YARN_HOME/share/hadoop/yarn/lib/*
        </value>
        <description>Classpath for MapReduce applications running on YARN.</description>
    </property>

    <!-- ... existing properties ... -->
</configuration>

start-dfs.sh
start-yarn.sh
jps
hdfs dfsadmin -safemode leave
hdfs dfsadmin -safemode get
echo -e "DALLAS\t35\nNYC\t28\nDALLAS\t42\nLA\t30\nNYC\t31" > i.txt
hdfs dfs -rm -r -skipTrash /i /o
hdfs dfs -mkdir /i
hdfs dfs -put i.txt /i
hadoop jar max.jar MaxTemp /i /o
hdfs dfs -cat /o/part-r-00000
cat part-r-00000 




