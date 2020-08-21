<template>
	<article class="index">
		<h1>Мои финансы</h1>
		<h2>Текущий баланс</h2>
		<div class="balance">{{user.balance}} pуб.</div>
		<h2>Произвести транзакцию</h2>
		<form action="" class="withdrawal-of-money">
			<input type="text" autocomplete='off' v-model='user.withdrawal.sum' name='sum' placeholder='Введите сумму' @input='validate'>
			<div class="error" v-if='error'>На Вашем счете недостаточно средств.</div>
			<div class="title"></div>
			<input type="text" autocomplete='off' v-model='user.withdrawal.card.number' name='card_number' placeholder='Номер' @input='validate'>
			<input type="text" autocomplete='off' v-model='user.withdrawal.card.fullName' name='card_name' placeholder='Имя держателя' @input='validate'>
			<input type="text" autocomplete='off' v-model='user.withdrawal.card.date' name='card_date' placeholder='Дата' @input='validate'>
			<input type="text" autocomplete='off' v-model='user.withdrawal.card.cvv' name='card_cvv' placeholder='CVV' @input='validate'>
			<div class='checkbox-wrap'>
				<input type="checkbox" id="rules" name="rules" v-model='user.withdrawal.agreement' @input='validate'>
				<label for="rules">Я согласен с правилами сервиса</label>
			</div>
			<button :class='{active: validation}' @click.prevent='makeTransaction'>Вывести</button>
		</form>
		<div class="history">
			<h2>История операций</h2>
			<div class="text" v-if='user.history.length == 0'>История операций пуста.</div>
			<div v-else>
				<div class="flex" >
					<div class="title">Дата</div>
					<div class="title">Сумма</div>
				</div>
				<div class="pagination" v-for='(page, index_p_item) in user.history_chunk' :key='index_p_item' >
					<div v-if='active_page == index_p_item'>
						<div class="flex" v-for='(operation, index_op) in page' :key='index_op'>
							<div class="date">{{operation.date}}</div>
							<div class="sum">{{operation.sum}}</div>
						</div>
					</div>
				</div>
				<div class="pages flex">
					<div class="page" v-for='(page, index_p) in user.history_chunk' :key='index_p' @click='active_page = index_p'>{{index_p + 1}}</div>
				</div>
			</div>
		</div>
	</article>
</template>

<script>

export default {
  name: 'Index',
  data() {
	  return {
		  user: {
			 	'balance' : 0,
			  	'withdrawal' : {
				  'sum' : '',
				  'card' : {
					  'number' : '',
					  'fullName' : '',
					  'date' : '',
					  'cvv' : '',
 					},
				  'agreement' : false,
				},
			'history' : [],
			'history_chunk': [],
		  },
		  error: false,
		  validation: false,
		  active_page: 0,
	  }
  },
  created(){
	  const t = this;

	  t.getBalance();
  },
  methods: {
	//получить баланс
	getBalance(){
		const t = this;

		this.$axios.get( './balance.json').then(function ( res ) {
			if( res.data != undefined ) {

				t.user.balance = res.data.balance;
				t.user.balance = t.user.balance.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1 ');
			}
		}).catch(err => {
			alert(err);
		});
	},
	//провести транзакцию
	makeTransaction(){
		const t = this;

		if(t.validation){
			t.sendRequest();
		}
	},
	//заглушка ajax на сервер с данными формы
	sendRequest(){
		const t = this;

		this.$axios.get( './transaction.json').then(function ( res ) {
			if( res.data != undefined ) {
				if(res.data.status == 'ok'){
					let date = new Date(),
						day = date.getDate(),
						month = date.getMonth() + 1,
						year = date.getFullYear(),
						hour = date.getHours(),
						min =  date.getMinutes();

					if(month < 10){
						month = '0' + month;
					}
					if(day < 10){
						day = '0' + day;
					}
					if(hour < 10){
						hour = '0' + hour;
					}
					if(min < 10){
						min = '0' + min;
					}

					let date_format = day + '.' + month + '.' + year + '  ' + hour + ':' + min;

					let obj = {
						'date' : date_format,
						'sum' : t.user.withdrawal.sum,
					};

					t.user.history.unshift(obj);
				}
			}
		}).catch(err => {
			alert(err);
		});
	},
	//валидция формы
	validate(){
		const t = this;

		if( +t.user.withdrawal.sum > t.user.balance){
			t.error = true;
			t.validation = false;
			return false;
		}else{
			t.error = false;
			if(+t.user.withdrawal.sum > 0
				&& +t.user.withdrawal.card.number > 0
				&& t.user.withdrawal.card.fullName.length > 0
				&& t.user.withdrawal.card.date.length > 0
				&& +t.user.withdrawal.card.cvv > 0
				&& t.user.withdrawal.agreement){

			t.validation = true;
			}
		}
	},
	//Split an array into chunks
	arrayChunk( input, size ) {

		for(var x, i = 0, c = -1, l = input.length, n = []; i < l; i++){
			(x = i % size) ? n[c][x] = input[i] : n[++c] = [input[i]];
		}
		return n;
	},
  },
  watch: {
	   'user.history' : {
			handler(val){
				const t = this;

				t.user.history_chunk = t.arrayChunk(val, 10);
			},
			deep: true,
		},
  },
}
</script>

<style lang="scss">
	body{
		background: #A0C1B5;
	}
	h1{
		font-size: 60px;
	}
	.index{
		.balance{
			font-size: 40px;
			font-weight: bold;
			padding: 24px;
		}
		form{
			padding: 24px;
			input{
				display: block;
				height: 24px;
				line-height: 24px;
				border-radius: 8px;
				outline: none;
				border: none;
				padding-left: 12px;
			}
		}
	}
</style>
