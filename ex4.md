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

start-dfs.sh
start-yarn.sh
jps
echo -e "DALLAS\t35\nNYC\t28\nDALLAS\t42\nLA\t30\nNYC\t31" > i.txt
hdfs dfs -rm -r -skipTrash /i /o
hdfs dfs -mkdir /i
hdfs dfs -put i.txt /i
javac -classpath $(hadoop classpath) -d . MaxTemp.java
jar -cvf max.jar *.class
hadoop jar max.jar MaxTemp /i /o
hdfs dfs -cat /o/part-r-00000
cat part-r-00000 

