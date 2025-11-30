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
//—-------------------
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

public class LocalMaxTemp {

    public static void main(String[] args) {
        if (args.length != 1) {
            System.err.println("Usage: java LocalMaxTemp <input_file_path>");
            System.exit(1);
        }

        String inputFile = args[0];
        // Map to store the maximum temperature found so far for each city
        Map<String, Integer> maxTemperatures = new HashMap<>();

        try (BufferedReader reader = new BufferedReader(new FileReader(inputFile))) {
            String line;
            while ((line = reader.readLine()) != null) {
                // Split assuming CityName\tTemperature
                String[] tokens = line.split("\t");

                if (tokens.length >= 2) {
                    String city = tokens[0].trim();
                    
                    try {
                        int temperature = Integer.parseInt(tokens[1].trim());

                        // Check if the current temperature is the highest seen for this city
                        maxTemperatures.compute(city, (k, currentMax) -> {
                            if (currentMax == null || temperature > currentMax) {
                                return temperature;
                            }
                            return currentMax;
                        });
                        
                    } catch (NumberFormatException e) {
                        // Ignore lines where temperature is not a valid number
                    }
                }
            }
        } catch (IOException e) {
            System.err.println("Error reading input file: " + e.getMessage());
            System.exit(1);
        }

        System.out.println("--- Max Temperatures Per City (Local Check) ---");
        // Print the final calculated maximums
        maxTemperatures.forEach((city, maxTemp) -> {
            System.out.println(city + "\t" + maxTemp);
        });
    }
}
//=======
#pseudo
echo -e "DALLAS\t35\nNYC\t28\nDALLAS\t42\nLA\t30\nNYC\t31" > temp_data.txt
javac LocalMaxTemp.java
java LocalMaxTemp temp_data.txt

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


