<template>
    <div id="app">
        <LineChart
            :data="lineData"
            :options="lineOptions"
            :width="600"
            :height="400" />
        <BarChart
            :data="barData"
            :options="barOptions"
            :width="600"
            :height="400" />
    </div>
</template>

<script>
import LineChart from './charts/LineChart'
import BarChart from './charts/BarChart'

export default {
    name: 'app',
    components: {
        LineChart,
        BarChart
    },
    data: function() {
        return {
            barData: {
                labels: ['0-5', '6-10', '11-15', '16-20', '21+'],
                datasets: [
                    {
                        label: 'Session Time (in minutes)',
                        backgroundColor: ['#3e95cd', '#8e5ea2', '#3cba9f', '#e8c3b9', '#c45850'],
                        data: [10, 8, 5, 1, 0]
                    }
                ]
            },
            barOptions: {
                responsive: false,
                maintainAspectRatio: false,
                scales: {
                    yAxes: [{
                        beginAtZero: true
                    }]
                },
                legend: { display: false },
                title: {
                    display: true,
                    text: 'Manticore Session Time Frequency (in minutes)'
                }
            },
            lineData: {
                datasets: [
                    {
                        label: 'Requests',
                        borderColor: '#ff0000',
                        pointBackgroundColor: '#ff0000',
                        pointBorderColor: '#ff0000',
                        fill: false,
                        data: [
                            { x: Date.now(), y: 0 },
                            { x: Date.now() + (1 * 60000), y: 1 },
                            { x: Date.now() + (2 * 60000), y: 1 },
                            { x: Date.now() + (3 * 60000), y: 2 },
                            { x: Date.now() + (4 * 60000), y: 3 },
                            { x: Date.now() + (5 * 60000), y: 7 },
                            { x: Date.now() + (6 * 60000), y: 6 },
                        ]
                    },
                    {
                        label: 'Allocations',
                        borderColor: '#00ff00',
                        pointBackgroundColor: '#00ff00',
                        pointBorderColor: '#00ff00',
                        fill: false,
                        data: [
                            { x: Date.now(), y: 0 },
                            { x: Date.now() + (1 * 60000), y: 0 },
                            { x: Date.now() + (2 * 60000), y: 1 },
                            { x: Date.now() + (3 * 60000), y: 1 },
                            { x: Date.now() + (4 * 60000), y: 3 },
                            { x: Date.now() + (5 * 60000), y: 5 },
                            { x: Date.now() + (6 * 60000), y: 6 },
                        ]
                    }
                ]
            },
            lineOptions: {
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
                    text: 'Manticore Requests/Allocations'
                }
            }
        }
    }
}
</script>

<style>
#app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
}
</style>
