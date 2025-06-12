import sys
from pyspark.sql import SparkSession
from pyspark.sql.functions import col, trim, length, isnan, when, count, udf
from pyspark.sql.types import DoubleType, StringType


def main():
    try:
        # 初始化SparkSession并启用Hive支持
        spark = SparkSession.builder \
            .appName("JD_Products_Data_Processing") \
            .enableHiveSupport() \
            .getOrCreate()

        spark.sparkContext.setLogLevel("INFO")
        print("SparkSession初始化成功")

        # 读取实际采集的数据文件（修改为page_5.csv）
        df = spark.read.csv(
            "hdfs://node1.itcast.cn:8020/user/YT/jd_products_page_30.csv",
            header=True,
            inferSchema=True,
            sep=","
        )

        # 打印数据基本信息（符合实训报告数据预处理要求）
        print("===== 数据基本信息 =====")
        df.printSchema()
        print(f"原始数据列数: {len(df.columns)}")
        print(f"数据行数: {df.count()}")

        # 数据清洗 - 过滤空行
        if len(df.columns) > 0:
            first_column = df.columns[0]
            df = df.filter(length(trim(col(first_column))) > 0)
        else:
            print("错误: 数据无有效列")
            sys.exit(1)

        # 列名校验与重命名（根据实际数据列调整）
        actual_columns = df.columns
        expected_columns = [
            "page", "title", "price", "color", "ram",
            "battery", "screen_size", "model", "好评率", "link"
        ]

        if actual_columns != expected_columns:
            print(f"警告: 列名不匹配，原始列名: {actual_columns}")
            print(f"       期望列名: {expected_columns}")
            # 若列名顺序一致但大小写不同，可强制转换
            df = df.toDF(*expected_columns)
        else:
            print("列名匹配，无需重命名")

        # 数据类型转换（根据实际数据调整）
        try:
            df = df.withColumn("price", col("price").cast("double"))
            # 处理好评率字段（包含"万+"文字，需要特殊处理）
            df = df.withColumn("好评率",
                               when(col("好评率").contains("万+"),
                                    col("好评率").cast("double") * 10000)
                               .when(col("好评率").contains("千+"),
                                     col("好评率").cast("double") * 1000)
                               .otherwise(col("好评率").cast("double"))
                               )
        except Exception as e:
            print(f"数据类型转换错误: {str(e)}")
            print("尝试显示部分数据以排查问题:")
            df.show(5, truncate=False)
            sys.exit(1)

        # 数据质量检查（符合实训报告数据预处理要求）
        print("===== 数据质量检查（NULL/NaN统计） =====")
        null_counts = df.select([
            count(when(isnan(c) | col(c).isNull(), c)).alias(f"{c}_null_count")
            for c in df.columns
        ])
        null_counts.show()

        # 描述性统计（新增，符合实训报告描述性分析要求）
        print("===== 数据描述性统计 =====")
        df.describe().show()

        # 写入Hive表（符合实训报告数据存储要求）
        table_name = "jd_products"
        df.write.mode("overwrite").saveAsTable(table_name)
        print(f"数据已成功写入Hive表: {table_name}")
        print(f"表结构:")
        spark.sql(f"DESCRIBE {table_name}").show()

    except Exception as e:
        print(f"程序执行出错: {str(e)}")
        import traceback
        print(f"堆栈信息:\n{traceback.format_exc()}")
        sys.exit(1)
    finally:
        if 'spark' in locals():
            spark.stop()
            print("SparkSession已关闭")


if __name__ == "__main__":
    main()
