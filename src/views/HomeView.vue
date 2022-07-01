<template>
	<div>
		<hello-world />
		<echarts />
	</div>
</template>

<script>
import HelloWorld from '../components/HelloWorld';
import Echarts from '../components/Echarts.vue';

export default {
	name: 'Home',

	components: {
		HelloWorld,
		Echarts
	}
};
</script>
