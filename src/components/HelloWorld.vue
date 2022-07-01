<template>
	<v-container>
		<v-row class="text-center">
			<v-col cols="12">
				<!-- <v-simple-table>
					<template v-slot:default>
						<thead>
							<tr>
								<th class="text-center" width="200px"></th>
								<th class="text-center" colspan="2">Station A</th>
								<th class="text-center" colspan="2">Station B</th>
							</tr>
							<tr>
								<th class="text-center">Time</th>
								<th class="text-center">PQ A1</th>
								<th class="text-center">PQ A2</th>
								<th class="text-center">PQ B1</th>
								<th class="text-center">PQ B2</th>
							</tr>
						</thead>
						<tbody>
							<tr>
								<td>2022-06-08 08:30:15</td>
								<td>A1 T1</td>
								<td>A2 T1</td>
								<td>B1 T1</td>
								<td>B2 T1</td>
							</tr>
							<tr>
								<td>2022-06-08 08:31:15</td>
								<td>A1 T2</td>
								<td>A2 T2</td>
								<td>B1 T2</td>
								<td>B2 T2</td>
							</tr>
						</tbody>
					</template>
				</v-simple-table> -->

				<v-simple-table class="mt-4">
					<template v-slot:default>
						<thead>
							<tr>
								<th class="text-center" width="200px" style="border-bottom: 1px solid #dcdcdc">PQ</th>
								<th
									v-for="station in stations"
									:key="station.id"
									class="text-center"
									:colspan="station.pqs.length"
									style="border-bottom: 1px solid #dcdcdc; border-left: 1px solid #dcdcdc"
								>
									{{ station.name }}
								</th>
							</tr>
							<tr>
								<th class="text-center">Time</th>
								<template v-for="station in stations">
									<th v-for="pq in station.pqs" :key="`${station.id}_${pq.id}`" class="text-center">{{ pq.name }}</th>
								</template>
							</tr>
						</thead>
						<tbody>
							<!-- <tr v-for="(time, timeIndex) in timeStamps" :key="timeIndex">
								<td>{{ time }}</td>
								<template v-for="(station, stationIndex) in stations">
									<template v-for="(pq, pqIndex) in station.pqs">
										<template v-for="(timeSeriesData, tsIndex) in pq.timeSeries">
											<td v-if="tsIndex === timeIndex" :key="`${stationIndex}_${pqIndex}_${tsIndex}`">
												{{ timeSeriesData.value }}
											</td>
										</template>
									</template>
								</template>
							</tr> -->

							<tr v-for="(time, timeIndex) in timeStamps" :key="timeIndex">
								<td>{{ time }}</td>
								<td v-for="n in amountOfPq" :key="`${timeIndex}_${n}`">
									{{ pqValues[amountOfPq * timeIndex + n - 1].value }}
								</td>
							</tr>
						</tbody>
					</template>
				</v-simple-table>
			</v-col>
		</v-row>
	</v-container>
</template>

<script>
export default {
	name: 'HelloWorld',

	data: () => ({
		stations: [
			{
				id: '1',
				name: 'Staiton A',
				pqs: [
					{
						id: 'a1',
						name: 'PQ A1',
						unit: '公尺',
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
						unit: '公尺',
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
						unit: '公尺',
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
						unit: '公尺',
						chartType: 'lineChart',
						timeSeries: [
							{
								value: 14.6,
								timeStamp: '2022-06-07 08:30:15'
							},
							{
								value: 19.4,
								timeStamp: '2022-06-07 08:31:15'
							},
							{
								value: 15.1,
								timeStamp: '2022-06-07 08:32:15'
							}
						]
					},
					{
						id: 'b2',
						name: 'PQ B2',
						unit: '公尺',
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
		],

		pqs: [
			{
				id: 'a1',
				name: 'PQ A1',
				stationInfo: {
					id: 'a1 station',
					name: 'Station A'
				},
				timeSeries: [
					{
						value: 'A1 T1',
						timeStamp: '2022-06-07 08:30:15'
					},
					{
						value: 'A1 T2',
						timeStamp: '2022-06-07 08:31:15'
					},
					{
						value: 'A1 T3',
						timeStamp: '2022-06-07 08:32:15'
					}
				]
			},
			{
				id: 'a2',
				name: 'PQ A2',
				timeSeries: [
					{
						value: 'A2 T1',
						timeStamp: '2022-06-07 08:30:15'
					},
					{
						value: 'A2 T2',
						timeStamp: '2022-06-07 08:31:15'
					},
					{
						value: 'A2 T3',
						timeStamp: '2022-06-07 08:32:15'
					}
				]
			},
			{
				id: 'a3',
				name: 'PQ A3',
				timeSeries: [
					{
						value: 'A3 T1',
						timeStamp: '2022-06-07 08:30:15'
					},
					{
						value: 'A3 T2',
						timeStamp: '2022-06-07 08:31:15'
					},
					{
						value: 'A3 T3',
						timeStamp: '2022-06-07 08:32:15'
					}
				]
			},
			{
				id: 'b1',
				name: 'PQ B1',
				timeSeries: [
					{
						value: 'B1 T1',
						timeStamp: '2022-06-07 08:30:15'
					},
					{
						value: 'B1 T2',
						timeStamp: '2022-06-07 08:31:15'
					},
					{
						value: 'B1 T3',
						timeStamp: '2022-06-07 08:32:15'
					}
				]
			},
			{
				id: 'b2',
				name: 'PQ B2',
				timeSeries: [
					{
						value: 'B2 T1',
						timeStamp: '2022-06-07 08:30:15'
					},
					{
						value: 'B2 T2',
						timeStamp: '2022-06-07 08:31:15'
					},
					{
						value: 'B2 T3',
						timeStamp: '2022-06-07 08:32:15'
					}
				]
			}
		]
	}),

	computed: {
		timeStamps() {
			return this.stations ? this.stations[0].pqs[0].timeSeries.map((item) => item.timeStamp) : [];
		},

		amountOfPq() {
			let pqCount = 0;
			this.stations.forEach((item) => (pqCount = pqCount + item.pqs.length));
			return pqCount;
		},

		pqValues() {
			const temp = [];
			for (let i = 0; i < this.timeStamps.length; i++) {
				this.stations.forEach(function (station) {
					station.pqs.forEach((item) => temp.push(item.timeSeries[i]));
				});
			}
			return temp;
		}
	},

	created() {},

	methods: {
		// pqValues() {
		// 	const temp = this.stations
		// 		.map(function (station) {
		// 			return station.pqs.map((pq) => pq.timeSeries);
		// 		})
		// 		.reduce(function (pre, cur) {
		// 			return pre.concat(cur);
		// 		})
		// 		.flat(1);
		// 	console.log(temp);
		//
		// 	/* --- */
		//
		// 	const temp = [];
		// 	for (let i = 0; i < this.timeStamps.length; i++) {
		// 		this.stations.forEach(function (station) {
		// 			station.pqs.forEach((item) => temp.push(item.timeSeries[i]));
		// 		});
		// 	}
		// 	console.log(temp);
		// }
	}
};
</script>

<style lang="scss" scoped>
td {
	border-left: 1px solid #dcdcdc;
}

td:first-child {
	border-left: 0;
}
</style>
