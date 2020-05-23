<template>
    <div class="w-full min-h-screen flex flex-col justify-center items-center bg-gray-100 text-sans px-4 sm:px-0">
        <h1 class="text-2xl sm:text-3xl mb-12 text-gray-700">UK Coronavirus Deaths</h1>
        <div class="w-full max-w-2xl p-4 sm:p-8 shadow-2xl rounded-lg bg-white">
            <calendar-heatmap
              :values="values"
              :end-date="endDate"
              tooltip-unit="Deaths"
              :range-color="colorRange"
            />
        </div>
        <p class="mt-12 text-gray-500 text-xs">
            <a href="https://datavis.co" target="_blank" class="underline hover:text-yellow-500">#100DaysOfDataVis</a>
            <span> by </span>
            <a href="https://twitter.com/madebycrevans" target="_blank" class="underline hover:text-blue-400">@MadeByCrevans</a>
        </p>
    </div>
</template>

<script lang="ts">
import Vue from "vue";
import * as d3 from "d3";

export default Vue.extend({
    data() {
        return {
            datasetUrl: 'https://c19downloads.azureedge.net/downloads/json/coronavirus-deaths_latest.json',
            dataset: {},
            values: [],
            endDate: null,
            colorA: "#FFE373",
            colorB: "#FD5E53",
            colorRange: []
        };
    },
    methods: {
        getDataSet() {
            fetch(this.datasetUrl).then((response) => {
                return response.json();
            }).then((data) => {
                this.dataset = data;
                this.process();
            }).catch((err) => {
                console.warn('Something went wrong.', err);
            });
        },
        process() {
            this.values = this.getValues();
            this.endDate = this.getEndDate();
        },
        getValues() {
            return this.dataset.overview.map(dv => ({date: new Date(dv.reportingDate), count: Number(dv.dailyChangeInDeaths)}));
        },
        getEndDate() {
            return d3.max(this.values.map(d => d.date));
        },
        getColorRange() {
            // Create range
            let clr = d3.scaleLinear().range([this.colorA, this.colorB]).domain([0, 4]);
            let colorRange = d3.range(5).map((d) => clr(d));

            // Set null color
            colorRange.unshift("rgba(247, 250, 252)");

            // Return
            this.colorRange = colorRange;
        }
    },
    created() {
        this.getColorRange();
        this.getDataSet();
    }
});
</script>
