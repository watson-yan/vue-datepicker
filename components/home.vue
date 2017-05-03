<template>
	<div>
		<h4>基于Vue.2X的日期选择器</h4>
		<div style="width: 502px;">
			<datepicker v-on:picked="picked"></datepicker>
		</div>
	</div>
</template>
<script>
	import datepicker from './datepicker.vue'
	export default {
		data() {
			return {}
		},
		methods: {
			picked(year, month, date) {
				console.warn(`你选择了${year}年${month}月${date}日`)
			}
		},
		components: {
			datepicker
		}
	}
</script>