<template>
	<article id="index">
		<div class="balance">{{user.balance}}</div>
			<form action="" id="withdrawal-of-money">
				<input type="text" v-model='user.withdrawal.sum' placeholder='Введите сумму'>
				<input type="text" v-model='user.withdrawal.card.number'>
				<input type="text" v-model='user.withdrawal.card.fullName'>
				<input type="text" v-model='user.withdrawal.card.date'>
				<input type="text" v-model='user.withdrawal.card.cvv'>
				<Input placeholder='Введите сумму' :data='profile.name' :error-text='profile_changed.name.errorText' :success='profile_changed.name.success'  :error='profile_changed.name.error'  @input="onInput($event, 'name')"/>
			</form>
	</article>
</template>

<script>
import Input from "@/components/Input.vue";

export default {
  name: 'Index',
  data() {
	  return {
		  user: {
			  'balance' : 0,
			  'withdrawal' : {
				  'sum' : 0,
				  'card' : {
					  'number' : '',
					  'fullName' : '',
					  'date' : '',
					  'cvv' : '',
 				  },
			  },
			  'history' : [],
		  },
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
			}
		}).catch(err => {
			alert(err);
		});
	},
	//обработка ввода
	onInput(text, name){
		const t = this;

		t.profile_changed[name].input_text = text;
		t.profile_changed[name].success =  false;
		t.profile_changed[name].error =  false;
	},
  },
}
</script>

<style scoped lang="scss">
</style>
