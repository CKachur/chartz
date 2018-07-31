<template>
    <div id="app">
        <div class="chart-content">
            <div class="chart-content-list">
                <h2>Datasets</h2>
                <button v-on:click="fetchDatasets">Refresh</button>
                <p v-if="datasetError">Cannot retrieve datasets</p>
                <div v-for="dataset in datasets" v-on:click="displayDataset(dataset.name)">
                    <p>{{ dataset.name }}</p>
                </div>

                <h2>Charts</h2>
                <button v-on:click="fetchCharts">Refresh</button>
                <p v-if="chartError">Cannot retrieve charts</p>
                <div v-for="chart in charts" v-on:click="displayChart(chart.name)">
                    <p>{{ chart.name }}</p>
                </div>
            </div>
            <div class="chart-content-display">
                <Dataset v-if="visibleType == 'dataset'" v-bind:datasetName="visibleDataset" />
                <Chart v-else-if="visibleType == 'chart'" v-bind:chartName="visibleChart"/>
                <h2 v-else>Click on a dataset or chart to display it</h2>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import Dataset from './components/Dataset'
import Chart from './components/Chart'

export default {
    name: 'app',
    components: {
        Dataset,
        Chart
    },
    data() {
        return {
            datasets: [],
            datasetError: null,
            charts: [],
            chartError: null,
            visibleType: null,
            visibleChart: null,
            visibleDataset: null
        }
    },
    created() {
        this.fetchDatasets()
        this.fetchCharts()
    },
    methods: {
        fetchDatasets() {
            axios.get('http://localhost:3000/datasets')
                .then((datasets) => {
                    this.datasets = datasets.data.datasets.sort((d1, d2) => {
                        if (d1.name < d2.name) return -1
                        if (d1.name > d2.name) return 1
                        return 0
                    })
                    this.datasetError = null
                })
                .catch((error) => {
                    this.datasetError = error
                    this.datasets = []
                })
        },
        fetchCharts() {
            axios.get('http://localhost:3000/charts')
                .then((charts) => {
                    this.charts = charts.data.charts.sort((c1, c2) => {
                        if (c1.name < c2.name) return -1
                        if (c1.name > c2.name) return 1
                        return 0
                    })
                    this.chartError = null
                })
                .catch((error) => {
                    this.chartError = error
                    this.charts = []
                })
        },
        displayDataset(datasetName) {
            this.visibleDataset = datasetName
            this.visibleType = 'dataset'
        },
        displayChart(chartName) {
            this.visibleChart = chartName
            this.visibleType = 'chart'
        }
    }
}
</script>

<style>
body {
    margin: 0;
    padding: 0;
}

#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
}

.chart-content {
    margin: auto;
    padding: 0;
    display: flex;
    flex-direction: row;
}

.chart-content-list {
    margin: 30px 15px 30px 30px;
}

.chart-content-display {
    margin: 30px 30px 30px 15px;
    flex-grow: 1;
    display: flex;
    align-items: center;
    justify-content: center;
}
</style>
