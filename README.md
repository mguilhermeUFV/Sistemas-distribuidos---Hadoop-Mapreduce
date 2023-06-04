# Processamento Distribuído de Big Data com Hadoop

A aplicação desenvolvida é um exemplo de processamento distribuído de Big Data utilizando o framework Hadoop. O código implementa um programa MapReduce para contar a ocorrência de palavras em um conjunto de documentos de texto. O Hadoop permite processar grandes volumes de dados de forma distribuída, dividindo a tarefa em partes menores executadas em paralelo nos nós do cluster. Isso proporciona uma abordagem escalável e eficiente para lidar com grandes conjuntos de dados, acelerando o processamento e possibilitando a extração de informações relevantes.

# Configuração do Cluster Hadoop

Este documento fornece um guia passo a passo para configurar um cluster Hadoop utilizando a framework MapReduce. O cluster é composto por uma máquina mestre e duas máquinas slave.

## Pré-requisitos

Antes de começar a configuração, verifique se você atende aos seguintes pré-requisitos:

- Java Development Kit (JDK)
- Hadoop 
- Ubuntu

## Instalação do Java

Faça o download e instale o JDK em todas as máquinas do cluster. Você pode baixar o JDK no site oficial da Oracle.

Configure a variável de ambiente JAVA_HOME para apontar para o diretório de instalação do JDK. Isso pode ser feito adicionando a seguinte linha ao arquivo `.bashrc` ou `.bash_profile`:

```ruby
export JAVA_HOME=/caminho/para/o/JDK
export PATH=$JAVA_HOME/bin:$PATH
```
Certifique-se de substituir "/caminho/para/o/JDK" pelo diretório correto onde o JDK está instalado.

## Instalação do Hadoop
Faça o download do arquivo de instalação do Hadoop no site oficial do Apache Hadoop.

Extraia o arquivo em um diretório de sua escolha. Por exemplo, você pode criar um diretório chamado /opt/hadoop e extrair o Hadoop lá.

## Configuração das Máquinas
Edite o arquivo `/etc/hosts` em todas as máquinas do cluster e adicione as seguintes entradas:
```ruby
IP_MÁQUINA_MESTRE  nome_máquina_mestre
IP_MÁQUINA_SLAVE1 nome_máquina_slave1
IP_MÁQUINA_SLAVE2 nome_máquina_slave2
```
Certifique-se de substituir IP_MÁQUINA_MESTRE, IP_MÁQUINA_SLAVE1 e IP_MÁQUINA_SLAVE2 pelos endereços IP corretos das respectivas máquinas e nome_máquina_mestre, nome_máquina_slave1 e nome_máquina_slave2 pelos nomes de cada máquina.

Isso permitirá que as máquinas se comuniquem entre si pelo nome, em vez de usar apenas o endereço IP.

Após adicionar as entradas, salve o arquivo.
### Máquina Mestre
Acesse o diretório de instalação do Hadoop na máquina mestre.

Edite o arquivo `etc/hadoop/core-site.xml` e defina as seguintes configurações:
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://master:9000</value>
  </property>
</configuration>
```
Certifique-se de substituir "master" pelo nome ou endereço IP da máquina mestre.

Edite o arquivo `etc/hadoop/hadoop-env.sh` e defina a variável JAVA_HOME para o diretório de instalação do JDK:

```ruby
export JAVA_HOME=/caminho/para/o/JDK
```

### Máquinas Slaves
Acesse o diretório de instalação do Hadoop nas máquinas slaves.

Edite o arquivo `etc/hadoop/core-site.xml` e defina as seguintes configurações:
```xml
<configuration>
  <property>
    <name>fs.defaultFS</name>
    <value>hdfs://master:9000</value>
  </property>
</configuration>
```

Certifique-se de substituir "master" pelo nome ou endereço IP da máquina mestre.

Edite o arquivo `etc/hadoop/hadoop-env.sh` e defina a variável JAVA_HOME para o diretório de instalação do JDK:
```ruby
export JAVA_HOME=/caminho/para/o/JDK
```
## Configuração do HDFS
Edite o arquivo `etc/hadoop/hdfs-site.xml` na máquina mestre e defina as seguintes configurações:
```xml
<configuration>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>/caminho/para/diretorio/name</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>/caminho/para/diretorio/data</value>
  </property>
</configuration>
```
Certifique-se de substituir "/caminho/para/diretorio/name" e "/caminho/para/diretorio/data" pelos diretórios onde o NameNode e o DataNode armazenarão os dados, respectivamente.

## Configuração do MapReduce
Edite o arquivo `etc/hadoop/mapred-site.xml` na máquina mestre e defina as seguintes configurações:

```xml
<configuration>
  <property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
    <name>mapreduce.application.classpath</name>
    <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
  </property>
  <property>
    <name>yarn.app.mapreduce.am.env</name>
    <value>HADOOP_MAPRED_HOME=/caminho/para/hadoop</value>
  </property>
  <property>
    <name>mapreduce.map.env</name>
    <value>HADOOP_MAPRED_HOME=/caminho/para/hadoop</value>
  </property>
  <property>
    <name>mapreduce.reduce.env</name>
    <value>HADOOP_MAPRED_HOME=/caminho/para/hadoop</value>
  </property>
</configuration>
```
Certifique-se de substituir "/caminho/para/hadoop" pelo diretório de instalação do Hadoop.

Edite o arquivo `etc/hadoop/yarn-site.xml` na máquina mestre e defina as seguintes configurações:

```xml
<configuration>
  <property>
    <name>yarn.nodemanager.aux-services</name>
    <value>mapreduce_shuffle</value>
  </property>
  <property>
    <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
    <value>org.apache.hadoop.mapred.ShuffleHandler</value>
  </property>
</configuration>
```
## Iniciando o Cluster
Inicie o NameNode na máquina mestre executando o seguinte comando:

```ruby
$ hdfs namenode -format
$ hdfs --daemon start namenode
$ hdfs --daemon start datanode
```
> **Nota:** Se você encontrar um erro relacionado ao diretório temporário (por exemplo, "/tmp/hadoop-${user.name}"), certifique-se de que o diretório esteja criado e tenha permissões adequadas. Se o diretório não existir, você pode criá-lo manualmente usando o comando `mkdir /tmp/hadoop-${user.name}`.

Inicie o ResourceManager na máquina mestre executando o seguinte comando:

```ruby
$ yarn --daemon start resourcemanager
$ yarn --daemon start nodemanager
```
Verifique se o cluster foi iniciado corretamente executando o seguinte comando:

```ruby
$ jps
```
Você deverá ver processos como NameNode, DataNode, ResourceManager e NodeManager sendo executados.

Exiba informações detalhadas sobre o status de cada nó no cluster, incluindo o número de nós ativos, capacidade de armazenamento, utilização, entre outros, executando esse comando:

```ruby
$ hdfs dfsadmin -report
```

## Executando o MapReduce
Agora que você configurou corretamente o Hadoop e o cluster, pode executar um exemplo simples de aplicação MapReduce. Vamos usar o exemplo clássico de contagem de palavras.

```java
import java.io.IOException;
import java.util.StringTokenizer;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class WordCount {

  public static class TokenizerMapper extends Mapper<Object, Text, Text, IntWritable> {
    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context)
        throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      }
    }
  }

  public static class IntSumReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values, Context context)
        throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
    }
  }

  public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    conf.set("HADOOP_HOME", "/caminho/para/o/hadoop");
    Job job = Job.getInstance(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(args[0]));
    FileOutputFormat.setOutputPath(job, new Path(args[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  }
}
```
### Para executar o MapReduce, siga estas etapas:

- Compile o código Java para gerar um arquivo JAR.
- Copie o arquivo JAR para o diretório do Hadoop em sua máquina mestre.

Crie um diretório de entrada no HDFS e coloque seus arquivos de entrada nesse diretório. Envie o arquivo de entrada para o HDFS:
```ruby
$ hdfs dfs -put /caminho/para/o/arquivo/de/entrada /input
```
Execute o seguinte comando para iniciar o job MapReduce:
```ruby
$ hadoop jar /caminho/para/o/hadoop/wordcount.jar WordCount /caminho/para/o/input /caminho/para/o/output
```
Certifique-se de substituir "/caminho/para/o/hadoop" pelo diretório correto do Hadoop em sua máquina mestre, "/caminho/para/o/input" pelo diretório de entrada no HDFS e "/caminho/para/o/output" pelo diretório de saída no HDFS.

Verifique os resultados executando o seguinte comando:

```ruby
$ hdfs dfs -cat /output/part-r-00000
```
Os resultados do processo MapReduce serão exibidos.


#### Fim
