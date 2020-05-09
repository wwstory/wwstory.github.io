---
layout: article
titles:
  # @start locale config
  en      : &EN       About
  en-GB   : *EN
  en-US   : *EN
  en-CA   : *EN
  en-AU   : *EN
  zh-Hans : &ZH_HANS  关于
  zh      : *ZH_HANS
  zh-CN   : *ZH_HANS
  zh-SG   : *ZH_HANS
  zh-Hant : &ZH_HANT  關於
  zh-TW   : *ZH_HANT
  zh-HK   : *ZH_HANT
  # @end locale config
key: page-about
---


- 姓名：dejiangsong
- 邮件：1018472917@qq.com
- Github：wwstory.github.io

> 喜欢码代码，会搞点小板子，磨点小榔头。

[leetcode](https://github.com/wwstory/leetcode)

# 技能

<script src="https://cdn.bootcss.com/echarts/4.3.0/echarts.min.js"></script>
<div id="chart" style="width:100%; height:400px;">
</div>
<script type="text/javascript">
    var myChart = echarts.init(document.getElementById('chart'));
    var option = 
{
    title: [{
        text: '语言',
        x: '10%',
        textAlign: 'center'
    },
    {
        text: '算法',
        x: '35%',
        textAlign: 'center'
    },
    {
        text: '基础',
        x: '60%',
        textAlign: 'center'
    },
    {
        text: '底层',
        x: '85%',
        textAlign: 'center'
    },
    {
        text: '机器学习',
        x: '10%',
        y: '50%',
        textAlign: 'center'
    },
    {
        text: '深度学习',
        x: '35%',
        y: '50%',
        textAlign: 'center'
    },
    {
        text: 'web开发',
        x: '60%',
        y: '50%',
        textAlign: 'center'
    },
    {
        text: 'other',
        x: '85%',
        y: '50%',
        textAlign: 'center'
    },],
    tooltip: {
        trigger: 'axis'
    },
    // legend: {
    //     x: 'center',
    //     data: [
    //         '语言', 
    //         '算法', 
    //         '基础', 
    //         '底层', 
    //         '机器学习', '线性模型', '神经网络', '决策树', '支持向量机', '聚类', '贝叶斯分类器', '概率图模型', '特征选择与稀疏学习', '降维与度量学习', '集成学习', '半监督学习', '强化学习',
    //         '深度学习', 'CNN', 'RNN', 'GAN', 'optim', 'object detection', 'driverless', 'face',
    //         'web开发', 'jvm', '设计模式', 'spring', 'mysql', 'mybatis', 'redis', 'mongodb', 'mq', 'docker', '微服务', 'nginx', 'vue',
    //         'other', 'arduino', '51', 'STM32', 'nvidia xavier', '机械', 'ros', 'qt', 'flask',
    //         ]
    // },
    // 0：基本不知, 1：入门, 2：进阶, 3：应用, 4：熟练, 5：掌握
    radar: [
        {
            indicator: [
                { text: 'python', max: 5 },
                { text: 'java', max: 5 },
                { text: 'c/c++', max: 5 },
                { text: 'go', max: 5 },
                { text: 'c#', max: 5 },
            ],
            center: ['15%', '30%'],
            radius: 50,
            title: {
                text: 'abc'
            }
        },
        {
            indicator: [
                { text: '数学', max: 5 },
                { text: '表', max: 5 },
                { text: '树', max: 5 },
                { text: '图', max: 5 },
                { text: '分冶', max: 5 },
                { text: '回溯', max: 5 },
                { text: '动态规划', max: 5 },
                { text: '贪心', max: 5 },
            ],
            center: ['35%', '30%'],
            radius: 50,
        },
        {
            indicator: [
                { text: '数据结构', max: 5 },
                { text: '计算机组成原理', max: 5 },
                { text: '操作系统', max: 5 },
                { text: '计算机网络', max: 5 },
                { text: 'linux', max: 5 },
                { text: '微机原理', max: 5 },
                { text: '编译原理', max: 5 },
            ],
            center: ['60%', '30%'],
            radius: 50
        },
        {
            indicator: [
                { text: 'c/c++库', max: 5 },
                { text: '编译器', max: 5 },
                { text: 'linux内核', max: 5 },
                { text: '机器学习库', max: 5 },
                { text: '深度学习库', max: 5 },
                { text: 'jvm', max: 5 },
                { text: 'python vm', max: 5 },
            ],
            center: ['85%', '30%'],
            radius: 50
        },
        {
            indicator: [
                { text: '广度', max: 5 },
                { text: '深度', max: 5 },
                { text: '理解', max: 5 },
                { text: '原理', max: 5 },
                { text: '代码', max: 5 },
                { text: '应用', max: 5 },
            ],
            center: ['15%', '80%'],
            radius: 50
        },
        {
            indicator: [
                { text: '广度', max: 5 },
                { text: '深度', max: 5 },
                { text: '理解', max: 5 },
                { text: '原理', max: 5 },
                { text: '代码', max: 5 },
                { text: '应用', max: 5 },
            ],
            center: ['35%', '80%'],
            radius: 50
        },
        {
            indicator: [
                { text: '广度', max: 5 },
                { text: '深度', max: 5 },
                { text: '理解', max: 5 },
                { text: '原理', max: 5 },
                { text: '代码', max: 5 },
                { text: '应用', max: 5 },
            ],
            center: ['60%', '80%'],
            radius: 50
        },
        {
            indicator: [
                { text: '广度', max: 5 },
                { text: '深度', max: 5 },
                { text: '理解', max: 5 },
                { text: '原理', max: 5 },
                { text: '代码', max: 5 },
                { text: '应用', max: 5 },
            ],
            center: ['85%', '80%'],
            radius: 50
        },
    ],
    series: [
        {
            type: 'radar',
            tooltip: {
                trigger: 'item'
            },
            itemStyle: { normal: { areaStyle: { type: 'default' } } },
            data: [
                {
                    name: '语言',
                    value: [4, 3, 2, 1, 2]
                }
            ]
        },
        {
            type: 'radar',
            radarIndex: 1,
            tooltip: {
                trigger: 'item'
            },
            itemStyle: { normal: { areaStyle: { type: 'default' } } },
            data: [
                {
                    name: '算法',
                    value: [0, 3, 2, 2, 2, 2, 2, 1]
                }
            ]
        },
        {
            type: 'radar',
            radarIndex: 2,
            tooltip: {
                trigger: 'item'
            },
            itemStyle: { normal: { areaStyle: { type: 'default' } } },
            data: [
                {
                    name: '基础',
                    value: [4, 0, 0, 0, 4, 3, 0]
                }
            ]
        },
        {
            type: 'radar',
            radarIndex: 3,
            tooltip: {
                trigger: 'item'
            },
            itemStyle: { normal: { areaStyle: { type: 'default' } } },
            data: [
                {
                    name: '底层',
                    value: [0, 0, 1, 2, 4, 2, 0],
                }
            ]
        },
        {
            type: 'radar',
            radarIndex: 4,
            tooltip: {
                trigger: 'item'
            },
            data: [
                {
                    name: '线性模型',
                    value: [4, 4, 4, 4, 4, 5],
                },
                {
                    name: '神经网络',
                    value: [4, 4, 4, 4, 4, 5],
                },
                {
                    name: '决策树',
                    value: [3, 3, 4, 3, 0, 0],
                },
                {
                    name: '支持向量机',
                    value: [3, 2, 2, 0, 0, 0],
                },
                {
                    name: '聚类',
                    value: [3, 2, 2, 1, 0, 0],
                },
                {
                    name: '贝叶斯分类器',
                    value: [2, 2, 2, 1, 0, 0],
                },
                {
                    name: '概率图模型',
                    value: [2, 1, 1, 0, 0, 0],
                },
                {
                    name: '特征选择与稀疏学习',
                    value: [3, 2, 2, 2, 1, 0],
                },
                {
                    name: '降维与度量学习',
                    value: [3, 2, 2, 1, 0, 0],
                },
                {
                    name: '集成学习',
                    value: [3, 2, 2, 2, 0, 0],
                },
                {
                    name: '半监督学习',
                    value: [3, 2, 2, 1, 0, 0],
                },
                {
                    name: '强化学习',
                    value: [3, 2, 2, 1, 0, 0],
                },
            ]
        },
        {
            type: 'radar',
            radarIndex: 5,
            tooltip: {
                trigger: 'item'
            },
            data: [
                {
                    name: 'CNN',
                    value: [4, 3, 3, 3, 4, 4],
                },
                {
                    name: 'RNN',
                    value: [2, 2, 3, 1, 0, 1],
                },
                {
                    name: 'GAN',
                    value: [4, 3, 3, 3, 0, 2],
                },
                {
                    name: 'optim优化',
                    value: [3, 2, 2, 2, 2, 2],
                },
                {
                    name: 'object detection',
                    value: [2, 2, 2, 2, 0, 0],
                },
                {
                    name: 'driverless',
                    value: [2, 1, 1, 0, 0, 0],
                },
                {
                    name: 'face',
                    value: [3, 2, 3, 2, 3, 2],
                },
            ]
        },
        {
            type: 'radar',
            radarIndex: 6,
            tooltip: {
                trigger: 'item'
            },
            data: [
                {
                    name: 'jvm',
                    value: [2, 1, 1, 1, 0, 0],
                },
                {
                    name: '设计模式',
                    value: [2, 1, 1, 1, 0, 0],
                },
                {
                    name: 'spring',
                    value: [3, 3, 3, 2, 1, 1],
                },
                {
                    name: 'mysql',
                    value: [3, 2, 2, 1, 3, 3],
                },
                {
                    name: 'mybatis',
                    value: [2, 1, 2, 1, 0, 0],
                },
                {
                    name: 'redis',
                    value: [3, 3, 3, 2, 2, 0],
                },
                {
                    name: 'mongodb',
                    value: [1, 1, 1, 1, 1, 0],
                },
                {
                    name: 'mq',
                    value: [2, 1, 1, 1, 1, 0],
                },
                {
                    name: 'docker',
                    value: [1, 1, 1, 0, 0, 0],
                },
                {
                    name: '微服务',
                    value: [0, 0, 0, 0, 0, 0],
                },
                {
                    name: 'nginx',
                    value: [0, 0, 0, 0, 0, 0],
                },
                {
                    name: 'vue',
                    value: [2, 1, 2, 1, 1, 1],
                },
            ]
        },
        {
            type: 'radar',
            radarIndex: 7,
            tooltip: {
                trigger: 'item'
            },
            data: [
                {
                    name: 'arduino',
                    value: [5, 5, 4, 4, 5, 5],
                },
                {
                    name: '51',
                    value: [4, 3, 4, 3, 3, 3],
                },
                {
                    name: 'STM32',
                    value: [2, 1, 1, 1, 1, 1],
                },
                {
                    name: 'nvidia xavier',
                    value: [2, 2, 2, 0, 1, 1],
                },
                {
                    name: '机械',
                    value: [3, 2, 2, 2, 0, 1],
                },
                {
                    name: 'ros',
                    value: [2, 2, 1, 1, 1, 1],
                },
                {
                    name: 'qt',
                    value: [2, 2, 2, 1, 1, 1],
                },
                {
                    name: 'flask',
                    value: [2, 2, 2, 1, 2, 1],
                },
            ]
        },
    ]
};
    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(option);
</script>


# 简述

以前学机械，参加过许多创新创业类竞赛，做过板子，码过代码。

现今学计算机，搞搞深度学习，做自动驾驶。

[书单(已读)](/note/2019/08/01/book-list-readed)
