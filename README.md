#1.不同内存配置的平均价格
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体和高清显示
plt.rcParams.update({
    'font.sans-serif': ['SimHei', 'WenQuanYi Micro Hei', 'Heiti TC'],
    'figure.dpi': 300
})

# 读取数据并绘图
df = pd.read_excel('C:\\Users\\Lenovo\\Desktop\\不同内存配置的平均价格.xlsx')
ax = df.plot(kind='bar',
             x='内存配置',
             y='平均价格（元）',
             title='不同内存配置的平均价格',
             rot=45,
             legend=False)

# 设置标签和优化布局
ax.set_xlabel('内存配置')
ax.set_ylabel('平均价格（元）')
plt.tight_layout()
plt.show()


#2.不同屏幕尺寸的平均价格
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体和高清显示
plt.rcParams.update({
    'font.sans-serif': ['SimHei', 'WenQuanYi Micro Hei', 'Heiti TC'],
    'figure.dpi': 300,
    'font.size': 8  # 全局字体大小
})
# 读取数据
df = pd.read_excel('C:\\Users\\Lenovo\\Desktop\\不同屏幕尺寸的平均价格.xlsx')
# 创建图形
plt.figure(figsize=(8, 5))  # 使用较小的图形尺寸
# 创建柱状图
ax = df.plot(kind='bar',
             x=df.columns[0],
             y=df.columns[1],
             title='不同屏幕尺寸的平均价格',
             rot=30,  # 使用30度旋转，比45度更平缓
             legend=False)
# 设置标签
ax.set_xlabel(df.columns[0], fontsize=9)
ax.set_ylabel(df.columns[1], fontsize=9)
ax.set_title('不同屏幕尺寸的平均价格', fontsize=10)
# 调整x轴标签位置
plt.subplots_adjust(bottom=0.15)  # 微调底部边距
# 添加数据标签
ax.bar_label(ax.containers[0], fontsize=7)
# 优化布局
plt.tight_layout(pad=1.5)  # 设置边距填充
plt.show()


#3.各品牌不同价格段分布
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# 设置中文字体和高清显示
plt.rcParams.update({
    'font.family': ['SimHei', 'WenQuanYi Micro Hei', 'Heiti TC'],
    'figure.dpi': 300,
    'font.size': 5,  # 减小字体
    'figure.figsize': [5, 4]  # 减小默认图形尺寸
})
# 读取数据
df = pd.read_excel("C:\\Users\\Lenovo\\Desktop\\各品牌不同价格段分布.xlsx")
# 创建图形
plt.figure()
brands = df.iloc[:, 0]
price_ranges = df.columns[1:]
num_ranges = len(price_ranges)
bar_width = 0.8 / num_ranges
positions = np.arange(len(brands))
# 绘制不同价格段的水平条形图
for i, price_range in enumerate(price_ranges):
    plt.barh(positions + i * bar_width, df[price_range],
             height=bar_width, label=price_range)

plt.yticks(positions + bar_width * (num_ranges - 1) / 2, brands)
plt.xlabel('占比 (%)')
plt.title('各品牌不同价格段分布')
# 调整图例位置和大小
plt.legend(title='价格段', bbox_to_anchor=(1.05, 0.5),
           loc='center left', fontsize=4, title_fontsize=5)
# 调整边距
plt.subplots_adjust(left=0.3, right=0.7, top=0.9, bottom=0.1)
plt.show()


#4.不同电池容量的平均价格
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体和高清显示
plt.rcParams.update({
    'font.family': ['SimHei', 'WenQuanYi Micro Hei', 'Heiti TC'],
    'figure.dpi': 300
})

# 读取数据
df = pd.read_excel("C:\\Users\\Lenovo\\Desktop\\不同电池容量的平均价格.xlsx")

# 创建图表
plt.figure(figsize=(5, 4))
plt.bar(df['电池容量(mAh)'], df['平均价格(元)'], color='skyblue')
plt.title('不同电池容量的平均价格')
plt.xlabel('电池容量(mAh)')
plt.ylabel('平均价格(元)')
plt.xticks(rotation=45)

# 自动调整布局
plt.tight_layout()
plt.show()


#5.好评率
import pandas as pd
import matplotlib.pyplot as plt

# 设置中文字体（适配更多环境）
plt.rcParams['font.sans-serif'] = ['SimHei', 'Microsoft YaHei', 'PingFang SC']
plt.rcParams['axes.unicode_minus'] = False  # 解决负号显示异常
plt.rcParams['figure.dpi'] = 300  # 高清显示

# 1. 读取数据（明确指定列，避免冗余数据干扰）
file_path = "C:\\Users\\Lenovo\\Desktop\\好评率top10.xlsx"
try:
    # 只读取需要的列，假设“机型”是 x 轴，“好评率”是 y 轴
    df = pd.read_excel(file_path, usecols=['机型', '好评率'])
    print("数据读取成功，前 5 行数据：")
    print(df.head())
except Exception as e:
    print(f"读取文件失败：{e}")
    print("请检查文件路径、Sheet 名称、列名是否正确")
    exit()

# 2. 数据校验（核心！解决“都是 10%”的问题）
if df.empty:
    print("数据为空，请检查 Excel 文件内容")
    exit()
# 检查好评率数值范围，正常应该是 0-100（百分比）或 0-1（小数）
rate_min = df['好评率'].min()
rate_max = df['好评率'].max()
if not (0 <= rate_min <= rate_max <= 100 or 0 <= rate_min <= rate_max <= 1):
    print(f"好评率数据异常！当前范围：{rate_min} ~ {rate_max}")
    print("请检查 Excel 中‘好评率’列的数值，应该是百分比（如 98.5）或小数（如 0.985）")
    exit()
# 统一转换为百分比（如果是小数的话）
if rate_max <= 1:
    df['好评率'] = df['好评率'] * 100
    print("已自动将小数转换为百分比（如 0.985 → 98.5%）")

# 3. 绘制折线图（优化字体、布局、标签）
plt.figure(figsize=(8, 4))  # 缩小画布，适配数据量
plt.plot(df['机型'], df['好评率'], marker='o', color='skyblue', linewidth=2)

# 4. 优化图表细节（解决“字体太大”问题）
plt.title('不同机型好评率分布', fontsize=10)  # 标题字体
plt.xlabel('机型', fontsize=9)                 # x 轴标签
plt.ylabel('好评率(%)', fontsize=9)            # y 轴标签
plt.xticks(rotation=45, ha='right', fontsize=8)  # x 轴文字旋转、字号
plt.yticks(fontsize=8)                           # y 轴字号

# 5. 添加数据标签（更清晰展示数值）
for x, y in zip(df['机型'], df['好评率']):
    plt.text(x, y, f'{y:.1f}%', ha='center', va='bottom', fontsize=7)

plt.tight_layout()  # 自动优化布局
plt.show()


#6.各品牌不同价格段分布
import pandas as pd
import matplotlib.pyplot as plt
import numpy as np

# 读取文件
excel_file = pd.ExcelFile('C:\\Users\\Lenovo\\Desktop\\各品牌不同价格段分布.xlsx')
# 获取指定工作表中的数据
df = excel_file.parse('Sheet1')
# 设置图片清晰度
plt.rcParams['figure.dpi'] = 300
# 设置中文字体
plt.rcParams['font.sans-serif'] = ['WenQuanYi Zen Hei', 'SimHei', 'WenQuanYi Zen Hei', 'WenQuanYi Zen Hei']
# 定义品牌名称
brands = df['品牌'].tolist()
# 定义价格段
price_ranges = df.columns[1:].tolist()
# 提取各品牌在不同价格段的占比数据
data = df[price_ranges].values
# 设置柱状图的宽度
width = 0.35
# 生成 x 轴位置
x = np.arange(len(brands))
# 绘制堆叠柱状图
bottom = np.zeros(len(brands))
for i, price_range in enumerate(price_ranges):
    plt.bar(x, data[:, i], width, label=price_range, bottom=bottom)
    bottom += data[:, i]
# 设置图表标题和坐标轴标签
plt.title('各品牌不同价格段分布')
plt.xlabel('品牌')
plt.ylabel('占比')
# 设置 x 轴刻度和标签
plt.xticks(x, brands)
# 显示图例
plt.legend()
# 显示图形
plt.show()


#7.网页可视化
import dash
from dash import html, dcc, Input, Output
import pandas as pd
import plotly.express as px
import plotly.graph_objects as go

# 读取文件
df_memory_price = pd.read_excel("C:\\Users\\Lenovo\\Desktop\\不同内存配置的平均价格.xlsx")
df_screen_price = pd.read_excel("C:\\Users\\Lenovo\\Desktop\\不同屏幕尺寸的平均价格.xlsx")
df_brand_price = pd.read_excel("C:\\Users\\Lenovo\\Desktop\\不同品牌手机的平均价格.xlsx")
df_battery_price = pd.read_excel("C:\\Users\\Lenovo\\Desktop\\不同电池容量的平均价格.xlsx")
df_top10 = pd.read_excel("C:\\Users\\Lenovo\\Desktop\\好评率top10.xlsx")
df_price_distribution = pd.read_excel("C:\\Users\\Lenovo\\Desktop\\各品牌不同价格段分布.xlsx")

# 创建 Dash 应用
app = dash.Dash(__name__)

# 外部样式表
app.layout = html.Div([
    html.Div([
        html.H1("京东手机大数据分析平台", style={'textAlign': 'center','margin': '20px 0'})
    ], className='header'),

    html.Div([
        # 内存配置价格图表
        html.Div([
            dcc.Graph(
                id='memory-price-chart',
                figure=px.bar(
                    df_memory_price,
                    x='内存配置',
                    y='平均价格（元）',
                    title='不同内存配置的平均价格（元）',
                    color='平均价格（元）',
                    color_continuous_scale='Viridis'
                )
            )
        ], className='card'),

        # 屏幕尺寸价格图表
        html.Div([
            dcc.Graph(
                id='screen-price-chart',
                figure=px.scatter(
                    df_screen_price,
                    x='屏幕尺寸(英寸)',
                    y='平均价格(元)',
                    title='不同屏幕尺寸的平均价格（元）',
                    size='平均价格(元)',
                    color='屏幕尺寸(英寸)',
                    trendline='ols'
                )
            )
        ], className='card'),

        # 品牌价格图表
        html.Div([
            dcc.Graph(
                id='brand-price-chart',
                figure=px.bar(
                    df_brand_price,
                    x='品牌',
                    y='平均价格(元)',
                    title='不同品牌手机的平均价格（元）',
                    color='平均价格(元)',
                    
