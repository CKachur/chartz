<template>
    <div>
        <div v-if="dataset != null">
            <h2>{{ dataset.name }}</h2>
            <p v-for="dataItem in dataset.structure">{{ dataItem.dataName }} - {{ dataItem.dataType }}</p>
        </div>
        <h2 v-else>Could not retrieve '{{ datasetName }}' dataset</h2>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'Dataset',
    props: {
        datasetName: String
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
        }
    }
}
</script>
