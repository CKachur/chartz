<template>
    <div>
        <div v-if="dataset != null">
            <h2>{{ dataset.name }}</h2>
            <p v-for="dataItem in dataset.structure">{{ dataItem.dataName }} - {{ dataItem.dataType }}</p>
            <button v-on:click="deleteDataset">Delete</button>
        </div>
        <h2 v-else>Could not retrieve '{{ datasetName }}' dataset</h2>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'Dataset',
    props: {
        datasetName: String,
        backToHome: Function
    },
    watch: {
        datasetName() {
            this.fetchDataset()
        }
    },
    data() {
        return {
            dataset: null
        }
    },
    created() {
        this.fetchDataset()
    },
    methods: {
        fetchDataset() {
            axios.get('http://localhost:3000/datasets')
                .then((datasets) => {
                    for (let i = 0; i < datasets.data.datasets.length; i++) {
                        if (datasets.data.datasets[i].name == this.datasetName) {
                            this.dataset = datasets.data.datasets[i]
                            break
                        }
                    }
                })
                .catch((error) => {
                    this.dataset = null
                })
        },
        deleteDataset() {
            axios.delete('http://localhost:3000/dataset', {
                    data: {
                        name: this.datasetName
                    }
                })
                .then((response) => {
                    this.backToHome()
                })
                .catch((error) => {
                    console.log(error)
                })
        }
    }
}
</script>
