<template>
    <div>
        <div v-if="chart != null">
            <LineChart v-if="lineOptions && lineData"
                :data="lineData"
                :options="lineOptions" />
            <BarChart v-if="barOptions && barData"
                :data="barData"
                :options="barOptions" />
        </div>
        <h2 v-else>Could not retrieve '{{ chartName }}' chart</h2>
    </div>
</template>

<script>
import axios from 'axios'
import BarChart from '../charts/BarChart'
import LineChart from '../charts/LineChart'

export default {
    name: 'Chart',
    components: {
        LineChart,
        BarChart
    },
    props: {
        chartName: String
    },
    data() {
        return {
            chart: null,
            data: [],
            lineData: null,
            lineOptions: null,
            barData: null,
            barOptions: null,
            colors: ['#3e95cd', '#8e5ea2', '#3cba9f', '#e8c3b9', '#c45850']
        }
    },
    created() {
        this.fetchChart()
    },
    methods: {
        fetchChart() {
            axios.get('http://localhost:3000/charts')
                .then((charts) => {
                    for (let i = 0; i < charts.data.charts.length; i++) {
                        if (charts.data.charts[i].name == this.chartName) {
                            this.chart = charts.data.charts[i]
                            break
                        }
                    }

                    if (this.chart != null) {
                        axios.get('http://localhost:3000/dataset', {
                                params: {
                                    name: this.chart.dataset
                                }
                            })
                            .then((data) => {
                                this.data = data.data.data
                                this.setupChart()
                            })
                    }
                })
                .catch((error) => {
                    this.chart = null
                })
        },
        setupChart() {
            if (this.chart.type == 'barGraph') {
                if (this.chart.xAxis.buckets != undefined) {
                    var labels = []
                    for (let i = 0; i < this.chart.xAxis.buckets.length; i++) {
                        labels.push(this.chart.xAxis.buckets[i].bottomBound + '-' + this.chart.xAxis.buckets[i].topBound)
                    }

                    var datasets = []
                    for (let i = 0; i < this.chart.yAxis.length; i++) {
                        var buckets = new Array(this.chart.xAxis.buckets.length)
                        buckets.fill(0)

                        for (let j = 0; j < this.data.length; j++) {
                            for (let k = 0; k < this.chart.xAxis.buckets.length; k++) {
                                if (this.data[j][this.chart.yAxis[i].dataName] >= this.chart.xAxis.buckets[k].bottomBound
                                    && this.data[j][this.chart.yAxis[i].dataName] <= this.chart.xAxis.buckets[k].topBound) {
                                    buckets[k]++
                                }
                            }
                        }

                        datasets.push({ data: buckets })
                    }
                    this.barData = { labels: labels, datasets: datasets }

                    this.barOptions = {
                        responsive: false,
                        maintainAspectRatio: false,
                        scales: {
                            yAxes: [{
                                ticks: {
                                    beginAtZero: true
                                }
                            }]
                        },
                        legend: { display: false },
                        title: {
                            display: true,
                            text: this.chart.name
                        }
                    }
                }
            } else if (this.chart.type == 'lineGraph') {

            } else if (this.chart.type == 'lineGraphTime') {
                var datasets = []
                for (let i = 0; i < this.chart.yAxis.length; i++) {
                    var data = []
                    for (let j = 0; j < this.data.length; j++) {
                        data.push({ x: this.data[j].timestamp, y: this.data[j][this.chart.yAxis[i]] })
                    }
                    datasets.push({
                        label: this.chart.yAxis[i],
                        borderColor: this.colors[i % this.colors.length],
                        pointBackgroundColor: this.colors[i % this.colors.length],
                        pointBorderColor: this.colors[i % this.colors.length],
                        fill: false,
                        data: data
                    })
                }
                this.lineData = { datasets: datasets }

                this.lineOptions = {
                    responsive: false,
                    maintainAspectRatio: false,
                    scales: {
                        yAxes: [{
                            ticks: {
                                beginAtZero: true
                            }
                        }],
                        xAxes: [{
                            type: 'time',
                            time: {
                                unit: 'minute',
                                displayFormats: {
                                    minute: 'h:mm a'
                                }
                            }
                        }]
                    },
                    elements: {
                        line: {
                            tension: 0
                        }
                    },
                    title: {
                        display: true,
                        text: this.chart.name
                    }
                }
            }
        }
    }
}
</script>
