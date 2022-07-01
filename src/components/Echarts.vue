<template>
	<div>
		<v-chart v-if="isRender" ref="vChart" class="chart" :option="option" autoresize />
		<v-btn @click="isGroupedByUnit = !isGroupedByUnit">change GroupedByUnit: {{ isGroupedByUnit }}</v-btn>
		<v-btn @click="test()">test</v-btn>
	</div>
</template>

<script>
import { use } from 'echarts/core';
import { SVGRenderer } from 'echarts/renderers';
import { LineChart, BarChart } from 'echarts/charts';
import { TitleComponent, TooltipComponent, LegendComponent, GridComponent, DataZoomComponent, DatasetComponent } from 'echarts/components';
import VChart from 'vue-echarts';

use([
	GridComponent,
	SVGRenderer,
	LineChart,
	BarChart,
	TitleComponent,
	TooltipComponent,
	LegendComponent,
	DataZoomComponent,
	DatasetComponent
]);

export default {
	name: 'LineChart',
	components: {
		VChart
	},
	provide: {},

	data() {
		return {
			option: {
				color: ['#5470c6', '#91cc75', '#fac858', '#ee6666', '#73c0de', '#3ba272', '#fc8452', '#9a60b4', '#ea7ccc'],
				legend: {
					data: [],
					orient: 'horizontal',
					top: '5%',
					textStyle: { fontSize: '16' }
				},
				dataZoom: [
					{
						type: 'inside',
						start: 0,
						end: 100,
						filterMode: 'none'
					},
					{
						start: 0,
						end: 100,
						bottom: '8%'
					}
				],
				grid: {
					top: '15%',
					left: 60,
					right: 100,
					bottom: '20%',
					containLabel: true
				},
				tooltip: {
					trigger: 'axis',
					axisPointer: {
						type: 'cross'
					}
				},
				xAxis: {
					type: 'category',
					axisLabel: {
						fontSize: 14
					}
				},
				yAxis: [
					{
						type: 'value',
						axisLabel: {
							fontSize: 16
						}
					}
				],
				series: []
			},

			stations: [],
			isRender: false,
			chartTypeEnum: {
				lineChart: 'line',
				barChart: 'bar',
				areaChart: 'line'
			},
			isGroupedByUnit: true
		};
	},

	computed: {
		timeStamps() {
			return this.stations.length ? this.stations[0].pqs[0].timeSeries.map((item) => item.timeStamp) : [];
		},

		pqValues() {
			const temp = [];
			for (let i = 0; i < this.timeStamps.length; i++) {
				this.stations.forEach(function (station) {
					station.pqs.forEach((item) => temp.push(item.timeSeries[i]));
				});
			}
			return temp;
		},

		pqs() {
			if (this.stations.length) {
				const temp = this.stations
					.map(function (station) {
						return station.pqs;
					})
					.reduce(function (pre, cur) {
						return pre.concat(cur);
					})
					.flat(1);
				return temp;
			} else {
				return [];
			}
		}
	},

	watch: {
		stations: {
			handler(val) {
				if (val) {
					this.isRender = true;
					this.setOptions(true);
				}
			},
			immediate: true
		},

		isGroupedByUnit: {
			handler() {
				this.setOptions();
			}
		}
	},

	created() {
		this.stations = [
			{
				id: '1',
				name: 'Staiton A',
				pqs: [
					{
						id: 'a1',
						name: 'PQ A1',
						unit: 'cm',
						chartType: 'lineChart',
						timeSeries: [
							{
								value: 120.1,
								timeStamp: '2022-06-07 08:30:15'
							},
							{
								value: 130.5,
								timeStamp: '2022-06-07 08:31:15'
							},
							{
								value: 110.9,
								timeStamp: '2022-06-07 08:32:15'
							}
						]
					},
					{
						id: 'a2',
						name: 'PQ A2',
						unit: 'm',
						chartType: 'lineChart',
						timeSeries: [
							{
								value: 25,
								timeStamp: '2022-06-07 08:30:15'
							},
							{
								value: 24,
								timeStamp: '2022-06-07 08:31:15'
							},
							{
								value: 11,
								timeStamp: '2022-06-07 08:32:15'
							}
						]
					},
					{
						id: 'a3',
						name: 'PQ A3',
						unit: 'm',
						chartType: 'lineChart',
						timeSeries: [
							{
								value: 5.1,
								timeStamp: '2022-06-07 08:30:15'
							},
							{
								value: 7.3,
								timeStamp: '2022-06-07 08:31:15'
							},
							{
								value: 15.9,
								timeStamp: '2022-06-07 08:32:15'
							}
						]
					}
				]
			},
			{
				id: '2',
				name: 'Staiton B',
				pqs: [
					{
						id: 'b1',
						name: 'PQ B1',
						unit: 'cm',
						chartType: 'lineChart',
						timeSeries: [
							{
								value: 54.6,
								timeStamp: '2022-06-07 08:30:15'
							},
							{
								value: 69.4,
								timeStamp: '2022-06-07 08:31:15'
							},
							{
								value: 65.1,
								timeStamp: '2022-06-07 08:32:15'
							}
						]
					},
					{
						id: 'b2',
						name: 'PQ B2',
						unit: 'm',
						chartType: 'lineChart',
						timeSeries: [
							{
								value: 9.7,
								timeStamp: '2022-06-07 08:30:15'
							},
							{
								value: 9.4,
								timeStamp: '2022-06-07 08:31:15'
							},
							{
								value: 11.5,
								timeStamp: '2022-06-07 08:32:15'
							}
						]
					}
				]
			}
		];

		this.getGroupedProcessedAxisY();

		this.getGroupedProcessedSeries();
	},

	mounted() {},

	methods: {
		resetOption() {
			this.$refs.vChart.setOption({}, true);
		},

		setOptions(isInit) {
			if (!isInit) this.resetOption();

			if (this.isGroupedByUnit) {
				this.option.series = this.getGroupedProcessedSeries();
				this.option.yAxis = this.getGroupedProcessedAxisY();
			} else {
				this.option.series = this.getProcessedSeries();
				this.option.yAxis = this.getProcessedAxisY();
			}

			this.option.grid.left = 60 + this.option.yAxis.length * 30;
			this.option.xAxis.data = this.timeStamps;
		},

		getProcessedSeries() {
			const vm = this;
			const processedSeries = [];
			this.pqs.forEach(function (pq, pqIndex) {
				const timeSeriesValue = pq.timeSeries.map((item) => item.value);

				const tempProcessedSeries = {
					name: pq.name,
					type: vm.chartTypeEnum[pq.chartType],
					yAxisIndex: pqIndex,
					data: timeSeriesValue,
					tooltip: {
						valueFormatter: (value) => `${value} ${pq.unit}`
					}
				};

				if (pq.chartType === 'areaChart') tempProcessedSeries.areaStyle = {};

				processedSeries.push(tempProcessedSeries);
			});

			return processedSeries;
		},

		getGroupedProcessedSeries() {
			const vm = this;
			const processedSeries = [];
			const groupedData = this.getGroupDataByUnit();

			Object.keys(groupedData).forEach(function (objectKey, groupIndex) {
				groupedData[objectKey].forEach(function (pq) {
					const timeSeriesValue = pq.timeSeries.map((item) => item.value);
					const tempProcessedSeries = {
						name: pq.name,
						type: vm.chartTypeEnum[pq.chartType],
						yAxisIndex: groupIndex,
						data: timeSeriesValue,
						tooltip: {
							valueFormatter: (value) => `${value} ${pq.unit}`
						}
					};

					if (pq.chartType === 'areaChart') tempProcessedSeries.areaStyle = {};

					processedSeries.push(tempProcessedSeries);
				});
			});

			console.log('GroupedProcessedSeries', processedSeries);
			return processedSeries;
		},

		getProcessedAxisY() {
			const vm = this;
			const processedAxisY = [];
			let circularIndex = 0;
			const maxColorIndex = this.option.color.length - 1;

			this.pqs.forEach(function (pq, pqIndex) {
				processedAxisY.push({
					type: 'value',
					name: pq.name,
					position: 'left',
					offset: 60 * pqIndex,
					alignTicks: true,
					axisLine: {
						show: true,
						lineStyle: {
							color: vm.option.color[circularIndex]
						}
					},
					axisLabel: {
						// formatter: '{value} ml',
						fontSize: 14
					}
				});
				if (circularIndex++ >= maxColorIndex) circularIndex = 0;
			});

			return processedAxisY;
		},

		getGroupedProcessedAxisY() {
			const processedAxisY = [];
			const groupedData = this.getGroupDataByUnit();

			Object.keys(groupedData).forEach(function (objectKey, index) {
				// const reduceArr = groupedData[objectKey]
				// 	.reduce((accumulator, currentValue) => {
				// 		return accumulator.concat(currentValue.timeSeries);
				// 	}, [])
				// 	.map((item) => item.value);

				// console.log('objectKey, reduceArr', objectKey, reduceArr);

				processedAxisY.push({
					type: 'value',
					name: objectKey,
					position: 'left',
					offset: 60 * index,
					// max: Math.max(...reduceArr),
					// min: Math.min(...reduceArr),
					axisLine: {
						show: true
					},
					axisLabel: {
						fontSize: 14
					}
				});
			});

			console.log('GroupedProcessedAxisY', processedAxisY);
			return processedAxisY;
		},

		getGroupDataByUnit() {
			const groupKey = 'unit';
			const groupedData = this.pqs.reduce(function (groups, item) {
				const val = item[groupKey];
				groups[val] = groups[val] || [];
				groups[val].push(item);
				return groups;
			}, {});

			return groupedData;
		}
	}
};
</script>

<style scoped>
.chart {
	min-height: 500px;
}
</style>
