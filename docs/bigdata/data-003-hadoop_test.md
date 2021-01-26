---
layout: default
title: 하둡 Hadoop 테스트 (Java)
parent: 빅데이터
---



{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>



# 하둡 Hadoop 테스트 (Java)

작업환경

Windows 10 Home Edition

Eclipse IDE Version: 2019-12 (4.14.0)



하둡 테스트를 위해 자바로 단어 갯수 세는 간단한 프로그램을 만든다.

이클립스(Eclipse IDE)에 Maven 프로젝트를 만든다.

하둡 라이브러리 다운로드를 위해 첨부 pom.xml를 받아 파일을 대체해 설치한다.



WordCount.java

```java
package sample;

import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.input.TextInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat;

/**
 * 파일을 읽어서 단어의 수를 계산하여 그 결과를 파일에 저장
*/

public class WordCount {

	public static class MyMapper 
	 extends Mapper<LongWritable, Text, Text, LongWritable>{
		private final static LongWritable one = new LongWritable(1);
		private Text word = new Text();
		
		@Override
		protected void map(LongWritable key, Text value, Mapper<LongWritable, Text, Text, LongWritable>.Context context)
				throws IOException, InterruptedException {
			
			String line = value.toString();
			StringTokenizer itr = new StringTokenizer(line,"\t\r\n\f :;,.()<>");
			while(itr.hasMoreTokens()) {
				word.set(itr.nextToken());
				context.write(word, one);
			}
		}
	}
	
	public static class MyReducer 
	 extends Reducer<Text, LongWritable, Text, LongWritable>{
		
		private LongWritable result = new LongWritable();

		@Override
		protected void reduce(Text key, Iterable<LongWritable> values,
				Reducer<Text, LongWritable, Text, LongWritable>.Context context) throws IOException, InterruptedException {
			
			int sum = 0;
			for (LongWritable val : values) {
				sum += val.get();
			}
			result.set(sum);
			context.write(key, result);
		}
	}
	

	public static void main(String[] args) throws Exception{
		
		
		Configuration conf = new Configuration();
		if (args.length != 2) {
			System.err.println("Usage: WordCount <input> <output>");
			System.exit(2);
		}
		
		
		Job job = Job.getInstance(conf, "WordCount");

		
		job.setJarByClass(WordCount.class); // 나중에 jar로 실행해야 함
		job.setMapperClass(MyMapper.class); 
		job.setReducerClass(MyReducer.class);

		
		job.setOutputKeyClass(Text.class);
		job.setOutputValueClass(LongWritable.class);
		
		
		job.setInputFormatClass(TextInputFormat.class);
		job.setOutputFormatClass(TextOutputFormat.class);

		
		FileInputFormat.addInputPath(job, new Path(args[0]));
		FileOutputFormat.setOutputPath(job, new Path(args[1]));

		
		job.waitForCompletion(true);
	}
}
```



프로젝트를 하나의 jar 파일로 만든다.

1. 프로젝트 우클릭 > Run AS > Maven Install

2. BUILD SUCCESS 메시지 확인 후 target 폴더에 lab - 으로 시작하는 jar 파일 생성 확인



WinSCP로 jar 파일을 /home/hadoop/source 폴더에 복사한다.

복사가 정상적으로 됐는지 확인한다.

```bash
$ ls /home/hadoop/source
```

확인 대상 파일을 /home/hadoop/data 폴더에 만든다.

```bash
$ vi mydata.txt
```

```
good morning
i love coffee
coffee is good
people loves coffee	 
```

하둡 명령어 hdfs dfs로 폴더가 있는지 확인한다.

```bash
$ hdfs dfs -ls /input/data
```

폴더를 생성한다.

```bash
$ hdfs dfs -mkdir -p /input/data
```

파일을 하둡 hdfs에 복사한다.

```bash
$ hdfs dfs -put ~/data/mydata.txt /input/data
```

하둡에 정상 복사됐는지 확인한다.

```bash
$ hdfs dfs -ls /input/data
```



준비가 끝났다. 실행한다.



yarn jar jar파일명.jar 패키지명.클래스명 소스(경로) 결과물(경로)

```bash
$ yarn jar /home/hadoop/source/lab1.jar sample.WordCount /input/data /output/wordcount
```

*주의: 존재하는 폴더일 경우 실행하지 않는다.

**만일 eclipse pom.xml에 maven-jar-plugin 설치했다면 위 명령어에서 클래스 이름 (sample.wordCount)을 빼고 실행한다.

실행으로 생성된 파일, 폴더를 확인한다.

```bash
$ hdfs dfs -ls /output/wordcount
```

위 명령어로 파일이 존재하는지 확인한다. 하둡으로 실행한 파일은 HDFS 저장소에 별도로 저장되므로 /output/wordcount를 리눅스 명령어(ls -la 등)로 찾아도 폴더나 파일이 나오지 않는다.

실행 결과를 확인한다.

```bash
$ hdfs dfs -cat /output/wordcount/part-r-00000
```



터미널 종료 시 반드시 하둡 종료 명령어를 실행한다.

```bash
$ stop-all.sh
```











