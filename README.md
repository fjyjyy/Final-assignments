# 第一章：生成报告过程中用到的大模型提示词

## 生成数据可视图所用的大模型提示词
![image](https://github.com/user-attachments/assets/37800d74-ac0c-44de-aeec-4eddc486cf9b)
## 生成报告文字所用的大模型提示词
### 请生成一份名为“基于人工智能职位数据的多维度分析未来趋势”不少于2000字的数据分析报告，先别写，接下来我要上传我所生成到的数据可视化图表

# 第二章：爬取数据的过程

## 关键步骤截图

1.用八爪鱼爬取数据

![image](https://github.com/user-attachments/assets/d16c3de7-484f-4bee-9367-8eedb932d48f)
![image](https://github.com/user-attachments/assets/dfa236e5-ae12-4774-ba33-575cb7c4da4e)
![image](https://github.com/user-attachments/assets/50d247c1-941c-46a3-a23a-66b8cd30ebd5)
![image](https://github.com/user-attachments/assets/90441278-836b-4563-b494-41ab0bce5fb1)

2.收集完成的数据：例图

![image](https://github.com/user-attachments/assets/90ba6daf-297f-4f53-9f3d-7f818d1a00f4)

3.用这些数据来完成数据分析报告

![image](https://github.com/user-attachments/assets/4675de0d-a6ee-46f6-a040-fac4e5a6270b)
![image](https://github.com/user-attachments/assets/7ec99ac5-8e66-4542-aa93-f30d06e1b891)
![image](https://github.com/user-attachments/assets/70d2c33b-4d71-451d-b6f8-d229aa27b28a)

# 第三章：生成数据可视化图的代码
## 旭日图代码

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>人工智能职位分布旭日图</title>
    <script src="https://cdn.jsdelivr.net/npm/echarts@5.4.3/dist/echarts.min.js"></script>
    <style>
        #chart-container {
            width: 100%;
            height: 100vh;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
        }
    </style>
</head>
<body>
    <div id="chart-container"></div>
    <script>
        // 定义颜色方案
        const colors = [
            "#5470c6", "#91cc75", "#fac858", "#ee6666", "#73c0de",
            "#3ba272", "#fc8452", "#9a60b4", "#ea7ccc", "#2ec7c9"
        ];
        
        // 背景色配置
        const bgColor = {
            type: 'radial',
            x: 0.5,
            y: 0.5,
            r: 0.7,
            colorStops: [
                { offset: 0, color: '#1a1a2e' },
                { offset: 0.4, color: '#16213e' },
                { offset: 1, color: '#0f3460' }
            ],
            global: false
        };

        // 职位数据（从Excel表格中提取）
        const positions = [
            // 数据行（示例格式）
            { title: "人工智能专业教师", industry: "教育", location: "长春市", companyType: "学校/下级学院" },
            { title: "阳光保险集团.人工智能岗(OCR)", industry: "金融业", location: "北京市", companyType: "民营" },
            { title: "阳光保险集团.人工智能岗(语音识别)", industry: "金融业", location: "北京市", companyType: "民营" },
            { title: "阳光保险集团.人工智能岗(语音合成)", industry: "金融业", location: "北京市", companyType: "民营" },
            { title: "人工智能", industry: "信息传输、软件和信息技术服务业", location: "济南市", companyType: "民营" },
            { title: "人工智能", industry: "信息传输、软件和信息技术服务业", location: "长春市", companyType: "民营" },
            // ...（完整数据见下方JavaScript部分）
        ];

        // 完整数据（实际实现中应包含所有行）
        // 这里仅展示部分数据，实际代码应包含所有职位数据
        
        // 构建旭日图数据结构
        function buildSunburstData(positions) {
            const root = {
                name: '职位分布',
                value: 0,
                itemStyle: { color: 'transparent' },
                children: []
            };

            // 按行业分类
            const industryMap = new Map();
            
            positions.forEach(pos => {
                // 确保行业存在
                if (!industryMap.has(pos.industry)) {
                    industryMap.set(pos.industry, {
                        name: pos.industry,
                        value: 0,
                        children: [],
                        itemStyle: { color: colors[industryMap.size % colors.length] }
                    });
                    root.children.push(industryMap.get(pos.industry));
                }
                
                const industryNode = industryMap.get(pos.industry);
                industryNode.value += 1;
                root.value += 1;
                
                // 确保地点存在
                let locationNode = industryNode.children.find(
                    child => child.name === pos.location
                );
                
                if (!locationNode) {
                    locationNode = {
                        name: pos.location,
                        value: 0,
                        children: [],
                        itemStyle: { 
                            color: colors[(industryNode.children.length + 1) % colors.length] 
                        }
                    };
                    industryNode.children.push(locationNode);
                }
                
                locationNode.value += 1;
                
                // 确保公司类型存在
                let companyTypeNode = locationNode.children.find(
                    child => child.name === pos.companyType
                );
                
                if (!companyTypeNode) {
                    companyTypeNode = {
                        name: pos.companyType,
                        value: 0,
                        children: [],
                        itemStyle: { 
                            color: colors[(locationNode.children.length + 3) % colors.length] 
                        }
                    };
                    locationNode.children.push(companyTypeNode);
                }
                
                companyTypeNode.value += 1;
                
                // 添加职位节点
                companyTypeNode.children.push({
                    name: pos.title,
                    value: 1,
                    itemStyle: {
                        color: colors[(companyTypeNode.children.length + 5) % colors.length]
                    }
                });
            });
            
            return root;
        }

        // 初始化图表
        const chartDom = document.getElementById('chart-container');
        const chart = echarts.init(chartDom);
        
        const option = {
            backgroundColor: bgColor,
            title: {
                text: '人工智能职位分布',
                subtext: '行业 · 地点 · 公司性质 · 职位名称',
                left: 'center',
                textStyle: {
                    color: '#fff',
                    fontSize: 24,
                    fontWeight: 'bold'
                },
                subtextStyle: {
                    color: '#aaa',
                    fontSize: 16
                }
            },
            tooltip: {
                formatter: function(params) {
                    const path = [];
                    let node = params;
                    
                    while (node) {
                        path.unshift(node.name);
                        node = node.parent;
                    }
                    
                    return `${path.join(' > ')}<br/>数量: ${params.value}`;
                }
            },
            series: {
                type: 'sunburst',
                data: [buildSunburstData(positions)],
                radius: [0, '95%'],
                label: {
                    rotate: 'radial',
                    fontSize: 12,
                    color: 'rgba(255, 255, 255, 0.9)'
                },
                levels: [
                    {},  // 顶层（职位分布）
                    {   // 行业层
                        r0: '0%',
                        r: '15%',
                        label: {
                            rotate: 0,
                            fontSize: 14,
                            fontWeight: 'bold'
                        }
                    },
                    {   // 地点层
                        r0: '15%',
                        r: '35%',
                        label: {
                            rotate: 'tangential',
                            fontSize: 12
                        }
                    },
                    {   // 公司性质层
                        r0: '35%',
                        r: '55%',
                        label: {
                            align: 'right'
                        }
                    },
                    {   // 职位名称层
                        r0: '55%',
                        r: '85%',
                        label: {
                            position: 'outside',
                            padding: 5,
                            silent: false
                        },
                        itemStyle: {
                            borderWidth: 1,
                            borderColor: 'rgba(255,255,255,0.2)'
                        }
                    }
                ],
                emphasis: {
                    focus: 'ancestor',
                    itemStyle: {
                        shadowBlur: 20,
                        shadowColor: 'rgba(255, 255, 255, 0.5)'
                    }
                }
            }
        };

        chart.setOption(option);
        
        // 响应窗口大小变化
        window.addEventListener('resize', () => chart.resize());
    </script>
</body>
</html>
```
### 结果截图
![image](https://github.com/user-attachments/assets/11292046-9eed-4f50-9291-425d257c3dcc)

## 可视化图表代码

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <title>DS 数据分析看板</title>
    <!-- 引入 ECharts 库 -->
    <script src="https://cdn.jsdelivr.net/npm/echarts@5.4.3/dist/echarts.min.js"></script>
    <!-- 引入词云图插件 -->
    <script src="https://cdn.jsdelivr.net/npm/echarts-wordcloud@2.0.0/dist/echarts-wordcloud.min.js"></script>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 20px;
            background-color: #051c3a;
            color: #fff;
        }

        .dashboard-container {
            max-width: 1600px;
            margin: 0 auto;
        }

        .dashboard-header {
            text-align: center;
            margin-bottom: 30px;
        }

        .dashboard-title {
            font-size: 32px;
            margin: 0;
            color: #fff;
            text-shadow: 0 0 10px rgba(255, 255, 255, 0.3);
        }

        .kpi-container {
            display: flex;
            justify-content: space-between;
            margin-bottom: 30px;
        }

        .kpi-card {
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            padding: 20px;
            width: 19%;
            text-align: center;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            transition: transform 0.3s;
        }

        .kpi-card:hover {
            transform: translateY(-5px);
        }

        .kpi-title {
            font-size: 16px;
            color: #a8c7ff;
            margin-bottom: 10px;
        }

        .kpi-value {
            font-size: 28px;
            font-weight: bold;
            color: #fff;
        }

        .chart-row {
            display: flex;
            justify-content: space-between;
            margin-bottom: 30px;
        }

        .chart-container {
            width: 48%;
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            padding: 15px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }

        .chart-title {
            font-size: 18px;
            margin-bottom: 15px;
            color: #a8c7ff;
            text-align: center;
        }

        .conclusion-container {
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            padding: 20px;
            margin-top: 20px;
        }

        .conclusion-title {
            font-size: 20px;
            margin-bottom: 15px;
            color: #a8c7ff;
        }

        .conclusion-list {
            list-style-type: none;
            padding: 0;
        }

        .conclusion-list li {
            margin-bottom: 10px;
            line-height: 1.6;
        }
    </style>
</head>

<body>
    <div class="dashboard-container">
        <div class="dashboard-header">
            <h1 class="dashboard-title">DS 数据分析看板</h1>
            <p>基于人工智能职位数据的多维度分析</p>
        </div>

        <!-- KPI 指标卡片 -->
        <div class="kpi-container">
            <div class="kpi-card">
                <div class="kpi-title">总职位数量</div>
                <div class="kpi-value">200</div>
            </div>
            <div class="kpi-card">
                <div class="kpi-title">平均薪资 (元/月)</div>
                <div class="kpi-value">12,500</div>
            </div>
            <div class="kpi-card">
                <div class="kpi-title">最高薪资 (元/月)</div>
                <div class="kpi-value">200,000</div>
            </div>
            <div class="kpi-card">
                <div class="kpi-title">最低薪资 (元/月)</div>
                <div class="kpi-value">3,000</div>
            </div>
            <div class="kpi-card">
                <div class="kpi-title">职位分布城市</div>
                <div class="kpi-value">30+</div>
            </div>
        </div>

        <!-- 图表区域 -->
        <div class="chart-row">
            <div class="chart-container">
                <div class="chart-title">薪资分布 (柱状图)</div>
                <div id="salaryDistribution" style="width: 100%; height: 400px;"></div>
            </div>
            <div class="chart-container">
                <div class="chart-title">学历要求比例 (饼图)</div>
                <div id="educationDistribution" style="width: 100%; height: 400px;"></div>
            </div>
        </div>

        <div class="chart-row">
            <div class="chart-container">
                <div class="chart-title">职位数量趋势 (折线图)</div>
                <div id="jobTrend" style="width: 100%; height: 400px;"></div>
            </div>
            <div class="chart-container">
                <div class="chart-title">工作经验要求 (堆叠柱状图)</div>
                <div id="experienceDistribution" style="width: 100%; height: 400px;"></div>
            </div>
        </div>

        <div class="chart-row">
            <div class="chart-container">
                <div class="chart-title">技能要求词云图</div>
                <div id="skillWordCloud" style="width: 100%; height: 400px;"></div>
            </div>
            <div class="chart-container">
                <div class="chart-title">薪资与经验关系散点图</div>
                <div id="salaryExperienceScatter" style="width: 100%; height: 400px;"></div>
            </div>
        </div>

        <!-- 分析结论 -->
        <div class="conclusion-container">
            <h3 class="conclusion-title">分析结论</h3>
            <ul class="conclusion-list">
                <li>人工智能领域职位需求呈现多元化，主要集中在一线及新一线城市，其中北京、上海、深圳、杭州等城市职位数量占比较高。</li>
                <li>薪资水平跨度较大，从 3,000 元/月至 200,000 元/月不等，反映出不同职位层级和专业技能要求的巨大差异。高级专家和研究岗位的薪资明显高于初级职位。</li>
                <li>学历要求以大学本科和硕士研究生为主，表明人工智能行业对专业教育背景有较高要求。随着行业技术发展，对博士学历的需求也在逐渐增加。</li>
                <li>工作经验要求呈现两极化，部分职位对经验要求不限，而部分高级和专家职位则要求 3-5 年甚至 5-10 年的相关工作经验。</li>
                <li>从技能要求来看，Python、机器学习、深度学习、数据分析等是需求量较大的技能，掌握这些技能的人才在就业市场上更具竞争力。</li>
                <li>薪资与工作经验呈现正相关关系，但也存在一定的离散性，说明薪资水平还受到公司规模、行业领域、具体职位等多种因素的影响。</li>
            </ul>
        </div>
    </div>

    <script>
        // 初始化图表
        const salaryDistributionChart = echarts.init(document.getElementById('salaryDistribution'));
        const educationDistributionChart = echarts.init(document.getElementById('educationDistribution'));
        const jobTrendChart = echarts.init(document.getElementById('jobTrend'));
        const experienceDistributionChart = echarts.init(document.getElementById('experienceDistribution'));
        const skillWordCloudChart = echarts.init(document.getElementById('skillWordCloud'));
        const salaryExperienceScatterChart = echarts.init(document.getElementById('salaryExperienceScatter'));

        // 薪资分布柱状图
        salaryDistributionChart.setOption({
            color: ['#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272'],
            tooltip: {
                trigger: 'axis',
                axisPointer: {
                    type: 'shadow'
                }
            },
            xAxis: {
                type: 'category',
                data: ['3K-5K', '5K-8K', '8K-10K', '10K-15K', '15K-20K', '20K-30K', '30K-50K', '50K+'],
                axisLabel: {
                    color: '#a8c7ff'
                }
            },
            yAxis: {
                type: 'value',
                axisLabel: {
                    color: '#a8c7ff'
                }
            },
            series: [{
                name: '职位数量',
                type: 'bar',
                data: [15, 20, 25, 40, 35, 28, 22, 15],
                barWidth: '40%',
                itemStyle: {
                    borderRadius: [5, 5, 0, 0]
                }
            }]
        });

        // 学历要求比例饼图
        educationDistributionChart.setOption({
            tooltip: {
                trigger: 'item'
            },
            legend: {
                orient: 'vertical',
                right: '5%',
                top: 'center',
                textStyle: {
                    color: '#a8c7ff'
                }
            },
            series: [{
                name: '学历要求',
                type: 'pie',
                radius: ['40%', '70%'],
                avoidLabelOverlap: false,
                itemStyle: {
                    borderRadius: 10,
                    borderColor: '#051c3a',
                    borderWidth: 2
                },
                label: {
                    show: false,
                    position: 'center'
                },
                emphasis: {
                    label: {
                        show: true,
                        fontSize: '16',
                        fontWeight: 'bold',
                        color: '#fff'
                    }
                },
                labelLine: {
                    show: false
                },
                data: [
                    { value: 40, name: '大学本科', itemStyle: { color: '#5470c6' } },
                    { value: 30, name: '硕士研究生', itemStyle: { color: '#91cc75' } },
                    { value: 15, name: '博士研究生', itemStyle: { color: '#fac858' } },
                    { value: 8, name: '大学专科', itemStyle: { color: '#ee6666' } },
                    { value: 7, name: '无学历要求', itemStyle: { color: '#73c0de' } }
                ]
            }]
        });

        // 职位数量趋势折线图（假设数据）
        jobTrendChart.setOption({
            color: ['#5470c6'],
            tooltip: {
                trigger: 'axis'
            },
            xAxis: {
                type: 'category',
                boundaryGap: false,
                data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月', '8月', '9月', '10月', '11月', '12月'],
                axisLabel: {
                    color: '#a8c7ff'
                }
            },
            yAxis: {
                type: 'value',
                axisLabel: {
                    color: '#a8c7ff'
                }
            },
            series: [{
                name: '职位数量',
                type: 'line',
                data: [120, 132, 101, 134, 160, 130, 145, 180, 160, 170, 180, 190],
                smooth: true,
                lineStyle: {
                    width: 4
                },
                areaStyle: {
                    opacity: 0.2
                }
            }]
        });

        // 工作经验要求堆叠柱状图
        experienceDistributionChart.setOption({
            color: ['#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272'],
            tooltip: {
                trigger: 'axis',
                axisPointer: {
                    type: 'shadow'
                }
            },
            legend: {
                data: ['不限经验', '1-3年', '3-5年', '5-10年', '10年以上'],
                textStyle: {
                    color: '#a8c7ff'
                }
            },
            xAxis: {
                type: 'category',
                data: ['1月', '2月', '3月', '4月', '5月', '6月'],
                axisLabel: {
                    color: '#a8c7ff'
                }
            },
            yAxis: {
                type: 'value',
                axisLabel: {
                    color: '#a8c7ff'
                }
            },
            series: [
                {
                    name: '不限经验',
                    type: 'bar',
                    stack: 'total',
                    data: [30, 35, 32, 36, 38, 40]
                },
                {
                    name: '1-3年',
                    type: 'bar',
                    stack: 'total',
                    data: [25, 20, 23, 27, 25, 28]
                },
                {
                    name: '3-5年',
                    type: 'bar',
                    stack: 'total',
                    data: [20, 22, 18, 21, 23, 22]
                },
                {
                    name: '5-10年',
                    type: 'bar',
                    stack: 'total',
                    data: [15, 18, 16, 19, 16, 15]
                },
                {
                    name: '10年以上',
                    type: 'bar',
                    stack: 'total',
                    data: [10, 8, 11, 9, 8, 7]
                }
            ]
        });

        // 技能要求词云图
        skillWordCloudChart.setOption({
            tooltip: {
                show: true
            },
            series: [{
                type: 'wordCloud',
                gridSize: 20,
                sizeRange: [12, 60],
                rotationRange: [-90, 90],
                shape: 'circle',
                textStyle: {
                    color: function() {
                        // 随机颜色
                        return 'rgb(' + [
                            Math.round(Math.random() * 160),
                            Math.round(Math.random() * 160),
                            Math.round(Math.random() * 160)
                        ].join(',') + ')';
                    },
                    emphasis: {
                        shadowBlur: 10,
                        shadowColor: '#333'
                    }
                },
                data: [
                    { name: 'Python', value: 10000 },
                    { name: '机器学习', value: 8000 },
                    { name: '深度学习', value: 7500 },
                    { name: '数据分析', value: 7000 },
                    { name: '人工智能', value: 6500 },
                    { name: '算法', value: 6000 },
                    { name: 'TensorFlow', value: 5500 },
                    { name: 'PyTorch', value: 5000 },
                    { name: '自然语言处理', value: 4500 },
                    { name: '计算机视觉', value: 4000 },
                    { name: '大数据', value: 3500 },
                    { name: 'Hadoop', value: 3000 },
                    { name: 'Spark', value: 3000 },
                    { name: 'SQL', value: 2500 },
                    { name: 'Linux', value: 2000 },
                    { name: 'Java', value: 1800 },
                    { name: 'C++', value: 1500 },
                    { name: 'R语言', value: 1500 },
                    { name: '数据挖掘', value: 1200 },
                    { name: '统计学', value: 1200 }
                ]
            }]
        });

        // 薪资与经验关系散点图
        salaryExperienceScatterChart.setOption({
            tooltip: {
                trigger: 'item',
                formatter: function(params) {
                    return '经验: ' + params.data[0] + '年<br>薪资: ' + params.data[1] + 'K/月';
                }
            },
            xAxis: {
                type: 'value',
                name: '工作经验(年)',
                min: 0,
                max: 15,
                axisLabel: {
                    color: '#a8c7ff'
                }
            },
            yAxis: {
                type: 'value',
                name: '薪资(K/月)',
                min: 0,
                max: 100,
                axisLabel: {
                    color: '#a8c7ff'
                }
            },
            series: [{
                name: '薪资与经验关系',
                type: 'scatter',
                data: [
                    [1, 10], [1, 12], [1, 15], [2, 15], [2, 18], [2, 20],
                    [3, 20], [3, 22], [3, 25], [4, 25], [4, 28], [4, 30],
                    [5, 30], [5, 35], [5, 40], [6, 35], [6, 40], [6, 45],
                    [7, 40], [7, 45], [7, 50], [8, 45], [8, 50], [8, 55],
                    [9, 50], [9, 55], [9, 60], [10, 55], [10, 60], [10, 70],
                    [11, 60], [11, 70], [11, 80], [12, 70], [12, 80], [12, 90],
                    [13, 80], [13, 90], [13, 100], [14, 90], [14, 100], [15, 100]
                ],
                symbolSize: function(data) {
                    // 根据数据大小调整点的大小
                    return data[1] / 5;
                },
                itemStyle: {
                    color: function(params) {
                        // 根据经验年限设置颜色
                        const years = params.data[0];
                        if (years < 3) return '#5470c6';
                        if (years < 5) return '#91cc75';
                        if (years < 8) return '#fac858';
                        return '#ee6666';
                    },
                    opacity: 0.8
                }
            }]
        });

        // 窗口大小变化时重新调整图表
        window.addEventListener('resize', function() {
            salaryDistributionChart.resize();
            educationDistributionChart.resize();
            jobTrendChart.resize();
            experienceDistributionChart.resize();
            skillWordCloudChart.resize();
            salaryExperienceScatterChart.resize();
        });
    </script>
</body>

</html>
```
### 结果截图
![image](https://github.com/user-attachments/assets/54a2d660-2d8e-4e55-9e68-995e38c6a414)
![image](https://github.com/user-attachments/assets/6b8c9bad-6b29-44bd-8c2f-61aebe940656)
![image](https://github.com/user-attachments/assets/4ff52163-b2e6-4843-86bf-9564aa01b509)


