<template>
	<date-pick v-model="date_format" format='DD.MM.YYYY'  :selectableYearRange="{from: 1920, to: 2020}" :startPeriod="start_period" :weekdays="['Пн', 'Вт', 'Ср', 'Чт', 'Пт', 'Сб', 'Вс']" :months="['Январь', 'Февраль', 'Март', 'Апрель', 'Май', 'Июнь', 'Июль', 'Август', 'Сентябрь', 'Октябрь', 'Ноябрь', 'Декабрь']" >
        <template v-slot:default="{toggle}">
            <div @click="toggle" class='date-pick'></div>
        </template>
    </date-pick>
</template>

<script>
	import DatePick from 'vue-date-pick';
	import 'vue-date-pick/dist/vueDatePick.css';

	export default {
		name: 'DatePicker',
		components: {DatePick},
		props: {
			date: {
				type: String,
				default: '01.01.1980',
			},
		},
		data() {
			return {
				date_format: this.date,
			}
		},
		watch:{
			date_format(val){
				const t = this;

				t.$emit('date-pick', val);
			},
		},
		computed: {
			start_period(){
				const t  = this;
				let start_period = {};

				if(t.date == ''){
					start_period = {month: 0, year: 1980};
				}else{
					let date = t.date;
					date = date.split('.');

					let month = parseInt(date[1]) - 1;
					let year = parseInt(date[2]);

					start_period = {month: month, year: year};
				}

				return start_period;
			},
		}
	};
</script>

<style lang="scss" scoped>
	.vdpComponent, .date-pick{
		position:absolute; width: 100%; height: 48px; bottom: 0; left:0; background: transparent;
	}
</style>