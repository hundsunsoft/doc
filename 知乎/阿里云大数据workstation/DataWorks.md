# 阿里云DataWorks

## 数据开发

新建表，支持BIGINT,DECIMAL,STRING,DOUBLE,DATETIME,BOOLEAN类型

导入本地数据，支持分隔符格式

### DDL语句

Union All可以把多个Select操作返回的结果，联合成一个数据集。它会返回所有的结果，但是不会执行去重。MaxCompute不支持直接对顶级的两个查询结果进行Union操作，需要写成子查询的形式。

join on

子查询改Join
例如有一个子查询，如下所示。
SELECT * FROM table_a a WHERE a.col1 IN (SELECT col1 FROM table_b b WHERE xxx);
此时您可以使用Join语句来代替
SELECT a.* FROM table_a a JOIN (SELECT DISTINCT col1 FROM table_b b WHERE xxx) c ON (a.col1 = c.col1)

## java upload and pom

Uploadsmple.java

    package com.aliyun.odps.tunnel.example;
    import java.io.IOException;
    import java.util.Date;
    
    import com.aliyun.odps.Column;
    import com.aliyun.odps.Odps;
    import com.aliyun.odps.PartitionSpec;
    import com.aliyun.odps.TableSchema;
    import com.aliyun.odps.account.Account;
    import com.aliyun.odps.account.AliyunAccount;
    import com.aliyun.odps.data.Record;
    import com.aliyun.odps.data.RecordWriter;
    import com.aliyun.odps.tunnel.TableTunnel;
    import com.aliyun.odps.tunnel.TunnelException;
    import com.aliyun.odps.tunnel.TableTunnel.UploadSession;
    
    public class UploadSample {
      private static String accessId = "####";
      private static String accessKey = "####";
      private static String tunnelUrl = "http://dt.odps.aliyun.com";
    
      private static String odpsUrl = "http://service.odps.aliyun.com/api";
    
      private static String project = "odps_public_dev";
      private static String table = "tunnel_sample_test";
      private static String partition = "pt=20150801,dt=hangzhou";
    public static void main(String args[]) {
      Account account = new AliyunAccount(accessId, accessKey);
      Odps odps = new Odps(account);
      odps.setEndpoint(odpsUrl);
      odps.setDefaultProject(project);
      try {
            TableTunnel tunnel = new TableTunnel(odps);
            tunnel.setEndpoint(tunnelUrl);
            PartitionSpec partitionSpec = new PartitionSpec(partition);
            UploadSession uploadSession = tunnel.createUploadSession(project,
            table, partitionSpec);
            System.out.println("Session Status is : "
            + uploadSession.getStatus().toString());
            TableSchema schema = uploadSession.getSchema();
            RecordWriter recordWriter = uploadSession.openRecordWriter(0);
            Record record = uploadSession.newRecord();
              for (int i = 0; i < schema.getColumns().size(); i++) {
                Column column = schema.getColumn(i);
                switch (column.getType()) {
                    case BIGINT:
                record.setBigint(i, 1L);
                break;
                case BOOLEAN:
                record.setBoolean(i, true);
                break;
                case DATETIME:
                record.setDatetime(i, new Date());
                break;
                case DOUBLE:
                record.setDouble(i, 0.0);
                break;
                case STRING:
                record.setString(i, "sample");
                break;
                default:
                throw new RuntimeException("Unknown column type: "
                + column.getType());
                }
              }
              for (int i = 0; i < 10; i++) {
                recordWriter.write(record);
              }
                recordWriter.close();
                uploadSession.commit(new Long[]{0L});
                System.out.println("upload success!");
          } catch (TunnelException e) {
              e.printStackTrace();
          } catch (IOException e) {
              e.printStackTrace();
        }
      }
    }


pom.xml

    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.aliyun.odps.tunnel.example</groupId>
    <artifactId>UploadSample</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
      <dependency>
        <groupId>com.aliyun.odps</groupId>
        <artifactId>odps-sdk-core</artifactId>
        <version>0.20.7-public</version>
      </dependency>
    </dependencies>
    <repositories>
      <repository>
      <id>alibaba</id>
      <name>alibaba Repository</name>
      <url>http://mvnrepo.alibaba-inc.com/nexus/content/groups/public/</url>
      </repository>
    </repositories>
    </project>


## 编写MapReduce
