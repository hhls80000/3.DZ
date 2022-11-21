<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>折线</title>
    <!-- 引入样式 -->
    <link rel="stylesheet" href="https://unpkg.com/element-ui/lib/theme-chalk/index.css">

    <style>
        * {
            padding: 0;
            margin: 0;
        }

        * html,
        * body {
            width: 100%;
        }

        * html #app,
        * body #app {
            width: 1100px;
            margin: 0 auto;
            margin-top: 20px;
        }

        * html #app .header,
        * body #app .header {
            display: flex;
        }

        * html #app .header .button,
        * body #app .header .button {
            margin-left: 20px;
        }
    </style>
</head>

<body>
    <div id="app">
        <header class="header">
            <div class="time">
                <el-time-select v-model="timeValue" :picker-options="{
                  start: '00:00',
                  step: '00:30',
                  end: '24:00'
                }" placeholder="选择时间">
                </el-time-select>
            </div>
            <el-button class="button" type="primary" :loading=loading plain @click="handleClick">查询</el-button>
            <!-- <el-button class="button" type="warning" plain @click="handleClear">清除</el-button> -->
            <!-- <el-button class="button" type="primary" plain @click="handleNext">查询下一条</el-button> -->
        </header>
        <p style="color: red;">目前只有 3.15---9.30 有数据</p>
        <el-divider></el-divider>
        <main>
            <!-- <section class="one">
                <h3>No.1， </h3>
                <p>实际预测全天均方根误差：{{meanDiffAll}}</p>
                <p>实际预测短期均方根误差：{{meanShortDiff}}</p>
                <p>短期预测1,均方根误差：{{meanDiffOne}}</p>
                <p>短期预测2,均方根误差：{{meanDiffTwo}}</p>
                <p>短期预测3,均方根误差：{{meanDiffThree}}</p>
                <div id="oneMain">
                </div>
            </section> -->

            <div id="oneMain">
            </div>
            <el-divider></el-divider>
            <div id="twoMain">
            </div>
        </main>
    </div>
</body>
<!-- import Vue before Element -->
<script src="https://unpkg.com/vue@2/dist/vue.js"></script>
<!-- <script src="https://cdn.jsdelivr.net/npm/axios/di  st/axios.min.js"></script> -->
<!-- import JavaScript -->
<script src="https://unpkg.com/element-ui/lib/index.js"></script>
<script src="https://cdn.jsdelivr.net/npm/echarts@5.4.0/dist/echarts.min.js"></script>
<script>
    new Vue({
        el: '#app',
        data: function () {
            return {
                dataValue: '',
                timeValue: '',
                thisStableDate: [],
                thisRelativeDate: [],
                thisAllDate: [],
                dataTime: '',
                loading: false,
                data: {
                    title: {
                        text: '相对差'
                    },
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {
                            type: 'shadow'
                        }
                    },
                    legend: {},
                    grid: {
                        left: '3%',
                        right: '4%',
                        bottom: '3%',
                        containLabel: true
                    },
                    xAxis: {
                        type: 'category',
                        data: [0, 0.01, 0.02, 0.03, 0.04, 0.05, 0.06, 0.07, 0.08, 0.09, 0.1, 0.11, 0.12, 0.13, 0.14, 0.15, 0.16, 0.17, 0.18, 0.19, 0.2, 0.21, 0.22, 0.23, 0.24, 0.25, 0.26, 0.27, 0.28, 0.29, 0.3, 0.31, 0.32, 0.34, 0.35, 0.36, 0.37, 0.38, 0.39, 0.4, 0.41, 0.42, 0.43, 0.44, 0.45, 0.46, 0.47, 0.48, 0.49, 0.5, 0.51, 0.52, 0.53, 0.54, 0.55, 0.56, 0.57, 0.58, 0.59, 0.6, 0.61, 0.62, 0.63, 0.64, 0.65, 0.66, 0.67, 0.68, 0.69, 0.7]
                    },
                    yAxis: {
                        type: 'value',

                    },
                    series: [
                        {
                            name: '相对差',
                            type: 'bar',
                            data: []
                        }
                    ]
                },
                dataTwo: {
                    title: {
                        text: '平稳度'
                    },
                    tooltip: {
                        trigger: 'axis',
                        axisPointer: {
                            type: 'shadow'
                        }
                    },
                    legend: {},
                    grid: {
                        left: '3%',
                        right: '4%',
                        bottom: '3%',
                        containLabel: true
                    },
                    xAxis: {
                        type: 'category',
                        data: [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1]
                    },
                    yAxis: {
                        type: 'value',
                    },
                    series: [
                        {
                            name: '平稳度',
                            type: 'bar',
                            data: []
                        }
                    ]
                },
            }
        },
        created() {
        },
        mounted() {

        },
        methods: {
            //搜索数据
            handleClick() {

                this.getAllActua()
                this.myChartOne()
                this.myChartTwo()
                setTimeout(() => {
                    // this.getAllActua()
                    this.myChartOne()
                    this.myChartTwo()
                }, 200);

            },
            // handleNext() {
            //     if (this.dataValue && this.timeValue) {
            //         this.timeValue = this.getAllTime[this.getAllTime.indexOf(this.timeValue) + 1]
            //         this.handleClick()
            //     }
            // },
            myChartOne() {
                var myChart = echarts.init(document.getElementById('oneMain'), null, { with: 1000, height: 400 })
                // 绘制图表
                // this.da.series[0].data = this.thisDate
                // this.da.series[1].data = this.thisDiffDate
                // this.da.series[2].data = this.thisAllDate
                // this.da.series[3].data = this.getOne
                // this.da.series[4].data = this.getTwo
                this.data.series.data = this.thisStableDate
                console.log(this.thisStableDate);
                myChart.setOption(this.data);
            },
            myChartTwo() {
                let myChart = echarts.init(document.getElementById('twoMain'), null, { with: 1000, height: 400 })
                // 绘制图表
                this.dataTwo.series.data = []
                this.dataTwo.series.data = this.thisRelativeDate
                console.log(this.thisRelativeDate,00000);
                myChart.setOption(this.dataTwo);
            },
            async getAllActua() {
                if (this.timeValue) {
                    var data = '2022-3-30' + ' ' + this.timeValue + ':' + '00'
                    let res = await fetch(`http://10.84.63.112:8080/Forecast/forecast/statistics?date=${data}`)
                        .then(response => {
                            return response.json()
                        })
                        .then(data => {
                            return data
                        })
                    if (res.code == 200) {
                        this.thisAllDate = res.res
                        this.getRelative()
                        this.getStable()
                        // console.log(this.thisRelativeDate);
                    }
                }else{
                    let res = await fetch(`http://10.84.63.112:8080/Forecast/forecast/statistics`)
                        .then(response => {
                            return response.json()
                        })
                        .then(data => {
                            return data
                        })
                    if (res.code == 200) {
                        this.thisAllDate = res.res
                        this.getRelative()
                        this.getStable()
                        console.log(this.thisRelativeDate);
                        console.log(this.thisStableDate);
                    }
                }

            },
            //获取平稳度
            getStable() {
                this.thisRelativeDate = []
                this.thisRelativeDate.push(this.thisAllDate['s0_01'])
                this.thisRelativeDate.push(this.thisAllDate['s01_02'])
                this.thisRelativeDate.push(this.thisAllDate['s02_03'])
                this.thisRelativeDate.push(this.thisAllDate['s03_04'])
                this.thisRelativeDate.push(this.thisAllDate['s04_05'])
                this.thisRelativeDate.push(this.thisAllDate['s05_06'])
                this.thisRelativeDate.push(this.thisAllDate['s06_07'])
                this.thisRelativeDate.push(this.thisAllDate['s07_08'])
                this.thisRelativeDate.push(this.thisAllDate['s08_09'])
                this.thisRelativeDate.push(this.thisAllDate['s09_1'])

            },
            //获取相对差
            getRelative() {
                this.thisStableDate = []
                this.thisStableDate.push(this.thisAllDate['r0_001'])
                this.thisStableDate.push(this.thisAllDate['r001_002'])
                this.thisStableDate.push(this.thisAllDate['r002_003'])
                this.thisStableDate.push(this.thisAllDate['r003_004'])
                this.thisStableDate.push(this.thisAllDate['r004_005'])
                this.thisStableDate.push(this.thisAllDate['r005_006'])
                this.thisStableDate.push(this.thisAllDate['r006_007'])
                this.thisStableDate.push(this.thisAllDate['r007_008'])
                this.thisStableDate.push(this.thisAllDate['r008_009'])
                this.thisStableDate.push(this.thisAllDate['r009_01'])
                this.thisStableDate.push(this.thisAllDate['r011_012'])
                this.thisStableDate.push(this.thisAllDate['r012_013'])
                this.thisStableDate.push(this.thisAllDate['r013_014'])
                this.thisStableDate.push(this.thisAllDate['r014_015'])
                this.thisStableDate.push(this.thisAllDate['r015_016'])
                this.thisStableDate.push(this.thisAllDate['r016_017'])
                this.thisStableDate.push(this.thisAllDate['r018_019'])
                this.thisStableDate.push(this.thisAllDate['r019_02'])
                this.thisStableDate.push(this.thisAllDate['r02_021'])
                this.thisStableDate.push(this.thisAllDate['r021_022'])
                this.thisStableDate.push(this.thisAllDate['r022_023'])
                this.thisStableDate.push(this.thisAllDate['r023_024'])
                this.thisStableDate.push(this.thisAllDate['r024_025'])
                this.thisStableDate.push(this.thisAllDate['r026_027'])
                this.thisStableDate.push(this.thisAllDate['r027_028'])
                this.thisStableDate.push(this.thisAllDate['r028_029'])
                this.thisStableDate.push(this.thisAllDate['r029_03'])
                this.thisStableDate.push(this.thisAllDate['r031_032'])
                this.thisStableDate.push(this.thisAllDate['r032_033'])
                this.thisStableDate.push(this.thisAllDate['r033_034'])
                this.thisStableDate.push(this.thisAllDate['r034_035'])
                this.thisStableDate.push(this.thisAllDate['r035_036'])
                this.thisStableDate.push(this.thisAllDate['r036_037'])
                this.thisStableDate.push(this.thisAllDate['r037_038'])
                this.thisStableDate.push(this.thisAllDate['r038_039'])
                this.thisStableDate.push(this.thisAllDate['r04_041'])
                this.thisStableDate.push(this.thisAllDate['r041_042'])
                this.thisStableDate.push(this.thisAllDate['r041_042'])
                this.thisStableDate.push(this.thisAllDate['r042_043'])
                this.thisStableDate.push(this.thisAllDate['r043_044'])
                this.thisStableDate.push(this.thisAllDate['r044_045'])
                this.thisStableDate.push(this.thisAllDate['r045_046'])
                this.thisStableDate.push(this.thisAllDate['r046_047'])
                this.thisStableDate.push(this.thisAllDate['r047_048'])
                this.thisStableDate.push(this.thisAllDate['r048_049'])
                this.thisStableDate.push(this.thisAllDate['r05_051'])
                this.thisStableDate.push(this.thisAllDate['r052_053'])
                this.thisStableDate.push(this.thisAllDate['r053_054'])
                this.thisStableDate.push(this.thisAllDate['r054_055'])
                this.thisStableDate.push(this.thisAllDate['r055_056'])
                this.thisStableDate.push(this.thisAllDate['r056_057'])
                this.thisStableDate.push(this.thisAllDate['r057_058'])
                this.thisStableDate.push(this.thisAllDate['r058_059'])
                this.thisStableDate.push(this.thisAllDate['r059_06'])
                this.thisStableDate.push(this.thisAllDate['r06_061'])
                this.thisStableDate.push(this.thisAllDate['r061_062'])
                this.thisStableDate.push(this.thisAllDate['r062_063'])
                this.thisStableDate.push(this.thisAllDate['r063_064'])
                this.thisStableDate.push(this.thisAllDate['r064_065'])
                // this.thisStableDate.push(this.thisAllDate['r065_066'])
                // this.thisStableDate.push(this.thisAllDate['r066_067'])
                // this.thisStableDate.push(this.thisAllDate['r067_068'])
                // this.thisStableDate.push(this.thisAllDate['r068_069'])
                // this.thisStableDate.push(this.thisAllDate['r069_07'])
            },
            getShortData(data) {
                this.getAllTime.forEach((item, index) => {
                    if (item === this.timeValue) {
                        this.thisDate = this.thisDate.slice(0, index + 1)
                    }
                })
            }
        }
    })
</script>


</html>
