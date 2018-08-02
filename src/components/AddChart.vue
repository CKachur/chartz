<template>
    <div>
        <form id="addChart" v-on:submit="(e) => e.preventDefault()">
            <label>Chart Name: </label>
            <input type="text" v-model="chartName"></br>

            <label>Dataset: </label>
            <select v-model="datasetName">
                <option v-for="dataset in datasets">{{ dataset.name }}</option>
            </select></br>

            <label>Chart Type: </label>
            <select v-model="chartType">
                <option>Bar</option>
                <option>Line</option>
                <option>Line (Time)</option>
            </select></br>

            <div v-if="chartType != 'Line (Time)' && chartType != null">
                <label>X Axis: </label>
                <select v-model="xAxis">
                    <option v-for="(data, index) in chosenDataset.structure">
                        {{ data.dataName }}
                    </option>
                </select></br>

                <div v-if="chosenX != null && chosenX.dataType == 'number' && chartType == 'Bar'">
                    <label>X Axis Buckets: </label><button v-on:click="addBucket">Add Bucket</button></br>
                    <div v-for="(bucket, index) in buckets">
                        <label>Bottom Bound: </label><input type="number" v-model="bucket.bottomBound">
                        <label>Top Bound: </label><input type="number" v-model="bucket.topBound">
                        <button v-on:click="removeBucket(index)">Remove</button>
                    </div>
                </div>
            </div>

            <div v-if="chartType != null">
                <label>Y Axis: </label>
                <button v-on:click="addYAxis">Add Y Axis Data</button></br>
                <div v-for="(data, index) in yAxis">
                    <label>Y Axis Data: </label>
                    <select v-model="data.dataName">
                        <option v-for="dataX in chosenDataset.structure">{{ dataX.dataName }}</option>
                    </select>
                    <label v-if="chosenX != null && chosenX.dataType == 'string'">Aggregate By: </label>
                    <select v-if="chosenX != null && chosenX.dataType == 'string'" v-model="data.aggregateBy">
                        <option>sum</option>
                        <option>count</option>
                    </select>
                    <button v-on:click="removeYAxis(index)">Remove</button></br>
                </div>
            </div>

            <button v-on:click="submitChart">Create Chart</button>
        </form>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'AddChart',
    props: {
        backToHome: Function
    },
    data() {
        return {
            chartName: null,
            chartType: null,
            datasetName: null,
            xAxis: null,
            buckets: [],
            yAxis: [],

            datasets: []
        }
    },
    computed: {
        chosenDataset: function() {
            return this.datasets.find((e) => { return e.name == this.datasetName })
        },
        chosenX: function() {
            return this.chosenDataset.structure.find((e) => { return e.dataName == this.xAxis })
        }
    },
    created() {
        this.fetchDatasets()
    },
    methods: {
        submitChart(e) {
            if (e) e.preventDefault()

            var newChart = {
                name: this.chartName,
                dataset: this.datasetName,
                xAxis: {
                    dataName: this.xAxis,
                    buckets: this.buckets
                }
            }

            var yAxisPlain = []
            for (let i = 0; i < this.yAxis.length; i++) {
                yAxisPlain.push(this.yAxis[i].dataName)
            }

            if (this.chartType == 'Bar') {
                newChart['type'] = 'barGraph'
                newChart['yAxis'] = this.yAxis
            } else if (this.chartType == 'Line') {
                newChart['type'] = 'lineGraph'
                newChart['yAxis'] = yAxisPlain
            } else if (this.chartType == 'Line (Time)') {
                newChart['type'] = 'lineGraphTime'
                newChart['yAxis'] = yAxisPlain
            }

            axios.post('http://localhost:3000/chart', newChart)
                .then((response) => {
                    this.backToHome()
                })
                .catch((error) => {
                    console.log(error)
                })
        },
        fetchDatasets() {
            axios.get('http://localhost:3000/datasets')
                .then((datasets) => {
                    this.datasets = datasets.data.datasets
                })
                .catch((error) => {
                    this.dataset = null
                })
        },
        addBucket() {
            this.buckets.push({ bottomBound: 0, topBound: 0 })
        },
        removeBucket(index) {
            this.buckets.splice(index, 1)
        },
        addYAxis() {
            this.yAxis.push({ dataName: "", aggregateBy: "" })
        },
        removeYAxis(index) {
            this.yAxis.splice(index, 1)
        }
    }
}
</script>
