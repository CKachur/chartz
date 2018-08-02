<template>
    <div>
        <form id="addDataset" v-on:submit="(e) => e.preventDefault()">
            <label>Dataset Name: </label>
            <input type="text" v-model="datasetName"></br>
            <button v-on:click="addData">Add Data</button></br>
            <div v-for="(data, index) in datasetStructure">
                <label>Data Name: </label>
                <input type="text" v-model="data.dataName">
                <label>Data Type: </label>
                <select v-model="data.dataType">
                    <option>number</option>
                    <option>string</option>
                </select>
                <button v-on:click="removeData(index)">Remove</button>
            </div>
            <button v-on:click="submitDataset">Create Dataset</button>
        </form>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'AddDataset',
    props: {
        backToHome: Function
    },
    data() {
        return {
            datasetName: null,
            datasetStructure: []
        }
    },
    methods: {
        addData() {
            this.datasetStructure.push({ dataName: "", dataType: "" })
        },
        removeData(index) {
            this.datasetStructure.splice(index, 1)
        },
        submitDataset(e) {
            if (e) e.preventDefault()
            axios.post('http://localhost:3000/dataset', {
                    name: this.datasetName,
                    structure: this.datasetStructure
                })
                .then((response) => {
                    this.datasetName = null
                    this.datasetStructure = []
                    this.backToHome()
                })
                .catch((error) => {
                    console.log(error)
                })
        }
    }
}
</script>
