<template>
	<div v-cloak ref='inputWrap' class='input-wrap' :class='{success: success, error: error, focus: isFocus}'>
		<input v-if='mask.length == 0' :class='{full: text.length != 0}' :type="type" v-model.trim='text' @input='onInput' :placeholder='placeholder_real'/>
		<div class="placeholder" @click='focusInput'>{{placeholder}}</div>
		<div v-if='text.length != 0 && !success' class="btn-delete" @click='deleteText'></div>
		<div class="error-text">{{errorText}}</div>
		<DatePicker v-if='datePicker' :date='data' @date-pick='datePick'/>
	</div>
</template>

<script>

	import DatePicker from "@/components/DatePicker.vue";

	export default {
		name: "Input",
		components: {
			DatePicker,
		},
		props: {
			placeholder: {
				type: String,
				default: '',
			},
			placeholder_real: {
				type: String,
				default: '',
			},
			success: {
				type: Boolean,
				default: false,
			},
			error: {
				type: Boolean,
				default: false,
			},
			errorText: {
				type: String,
				default: 'Ошибка',
			},
			type: {
				type: String,
				default: 'text',
			},
			data: {
				default: '',
			},
			datePicker: {
				type: Boolean,
				default: false,
			},
		},
		data() {
			return {
				text: '',
				isFocus: false,
			}
		},
		created(){
			const t = this;

			if(t.data.length > 0){
				t.text = t.data;
			}

			setTimeout(function(){
				$(t.$refs.inputWrap).find('input').on('focus', function(){
					t.isFocus = true;
				});

				$(t.$refs.inputWrap).find('input').on('blur', function(){
					t.isFocus = false;
				});
			},100);
		},
		methods: {
			//поставить фокусировку на инпут при нажатии на .placeholder
			focusInput(){
				const t = this;

				$(t.$refs.inputWrap).find('input').trigger('focus');
			},
			onInput(){
				const t = this;

				t.$emit('input', t.text);
			},
			deleteText(){
				const t = this;

				t.text = ""
				t.onInput();
			},
			//выбор даты через date-picker
			datePick(val){
				const t = this;

				t.text = val;
				t.onInput();
			},
		},
	}
</script>

<style lang="scss" scoped>
	.input-wrap{
		position:relative; margin-bottom: 16px;
		input[type=number], input[type=text], input[type=password], input[type=tel], input[type=date], textarea, select {
			width: 100%; height: 48px; font-style: normal; font-size: 15px; line-height: 100%;  border: 1px solid #EBEBEB; border-radius: 4px; padding-left: 16px; padding-right:48px; padding-top: 12px;
			&::placeholder{
				position:absolute; left: 16px; top: 17px; font-size: 15px; line-height: 100%; color: #1C1C1E; transition: .15s ease-in-out;
			}
			&.masked{
				+.placeholder{
					opacity:0;
				}
			}
			&:focus{
				border: 1px solid $primary;  line-height: 100%;
				+.placeholder{
					top: 10px; font-size: 13px; line-height: 90%; color: $gray; opacity: 1;
				}
			}
			&.full{
				+.placeholder{
					top: 10px; font-size: 13px; line-height: 90%; color: $gray; opacity: 1;
				}
			}
		}
		&.success{
			input{
				border: 1px solid #28BC00;
				&:focus{
					border: 1px solid #28BC00;
				}
			}
			&::after{
				content: ''; position: absolute; display: block; width: 48px; height: 48px; top:0; right:0; background: url('data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMTYiIGhlaWdodD0iMTQiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZmlsbC1ydWxlPSJldmVub2RkIiBjbGlwLXJ1bGU9ImV2ZW5vZGQiIGQ9Ik01LjA5IDEwLjU0NkwxLjI3NCA2LjcyNyAwIDhsNS4wOSA1LjA5TDE2IDIuMTgzIDE0LjcyNy45MDlsLTkuNjM2IDkuNjM2eiIgZmlsbD0iIzI4QkMwMCIvPjwvc3ZnPg==') no-repeat center center;
			}
		}
		&.error{
			input{
				border: 1px solid #E40033;
			}
			.btn-delete{
				background: url('data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMjAiIGhlaWdodD0iMjAiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZD0iTTExLjAyOS4wMTZjMS4wOTIuMDc2IDIuMTcuMzggMy4yMzMuOTFhMTAuMjk1IDEwLjI5NSAwIDAxMi43MzIgMS45MzZjMS44OTggMi4wNSAyLjg0NyA0LjQ3IDIuODQ3IDcuMjY0YTkuMjIgOS4yMiAwIDAxLS42MTUgMy4zMDEgMTEuMDcgMTEuMDcgMCAwMS0xLjY2MiAyLjk2IDkuOTA2IDkuOTA2IDAgMDEtMi41MjggMi4xODYgOS41MzIgOS41MzIgMCAwMS0zLjE2NSAxLjIzYy0xLjE2OS4yMjgtMi4yOTIuMjU4LTMuMzcuMDkxYTkuMDg0IDkuMDg0IDAgMDEtMy4xNjUtMS4wNyAxMS4xNTYgMTEuMTU2IDAgMDEtMi42NjQtMi4xNCAxMC4zMjcgMTAuMzI3IDAgMDEtMS43NzYtMi44NyAxMC40MDIgMTAuNDAyIDAgMDEtLjcyOS0zLjM0NyA5LjI4NCA5LjI4NCAwIDAxLjQ1Ni0zLjMyNGMuNzQ0LTIuMzY4IDIuMDcyLTQuMTc1IDMuOTg0LTUuNDJBOS42MTQgOS42MTQgMCAwMTcuNjM2LjM1OGExMS4wNzggMTEuMDc4IDAgMDEzLjM5My0uMzQxem0uNzA2IDE4LjM3NWE5LjIzMyA5LjIzMyAwIDAwMi42MTgtMS4wNyA4LjQxNyA4LjQxNyAwIDAwMi4yMDktMS45MzVjLjYwNy0uODIgMS4wNTUtMS42NjMgMS4zNDMtMi41MjguMzE5LS45NTYuNDQtMS45MTMuMzY0LTIuODY5IDAtMS4xNTQtLjIxMi0yLjI3LS42MzctMy4zNDdhNy43OSA3Ljc5IDAgMDAtMS43NzYtMi43NzhjLTEuNDczLTEuNDcyLTMuMTgtMi4yNzctNS4xMjMtMi40MTRhOC42NSA4LjY1IDAgMDAtMi44MDEuMjI4IDcuNDk3IDcuNDk3IDAgMDAtMi42MTkgMS4xODRDMy42OSA0LjA0NiAyLjYwMyA1LjYxNyAyLjA1NyA3LjU3NWE4LjA2IDguMDYgMCAwMC0uMzg3IDIuODQ3Yy4wMyAxLjAwMi4yNSAxLjk1LjY2IDIuODQ2Ljg2NiAxLjg1MiAyLjE0OCAzLjI3MSAzLjg0OSA0LjI1OC44NS41IDEuNzUzLjgyNyAyLjcxLjk4YTcuODA2IDcuODA2IDAgMDAyLjg0Ni0uMTE1em0tMS43MDgtOS4xMDhsMy40MTUtMy41NzUgMS4wMDIgMS4wMDItMy40MTUgMy41NTIgMy40MTUgMy41NzUtMS4wMDIuOTgtMy40MTUtMy41NTMtMy40MTYgMy41NTItMS4wMDItLjk3OSAzLjQxNi0zLjU3NUw1LjYwOSA2LjcxbDEuMDAyLTEuMDAyIDMuNDE2IDMuNTc1eiIgZmlsbD0iI0U0MDAzMyIvPjwvc3ZnPg==') no-repeat center center;
			}
			.error-text{
				display:block;
			}
		}
		&.focus{
			.btn-delete{
				background: url('data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMjQiIGhlaWdodD0iMjQiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZD0iTTEzLjAyOSAyLjAxNmMxLjA5My4wNzYgMi4xNy4zOCAzLjIzMy45MWExMC4yOTggMTAuMjk4IDAgMDEyLjczMyAxLjkzNmMxLjg5NyAyLjA1IDIuODQ2IDQuNDcgMi44NDYgNy4yNjRhOS4yMTggOS4yMTggMCAwMS0uNjE1IDMuMzAxIDExLjA2OCAxMS4wNjggMCAwMS0xLjY2MiAyLjk2IDkuOTAzIDkuOTAzIDAgMDEtMi41MjggMi4xODYgOS41MyA5LjUzIDAgMDEtMy4xNjUgMS4yM2MtMS4xNjguMjI4LTIuMjkyLjI1OC0zLjM3LjA5MWE5LjA4NCA5LjA4NCAwIDAxLTMuMTY1LTEuMDcgMTEuMTU2IDExLjE1NiAwIDAxLTIuNjY0LTIuMTQgMTAuMzI3IDEwLjMyNyAwIDAxLTEuNzc2LTIuODcgMTAuNDAyIDEwLjQwMiAwIDAxLS43MjgtMy4zNDcgOS4yODQgOS4yODQgMCAwMS40NTUtMy4zMjRjLjc0NC0yLjM2OCAyLjA3Mi00LjE3NSAzLjk4NS01LjQyYTkuNjE0IDkuNjE0IDAgMDEzLjAyOC0xLjM2NiAxMS4wNzggMTEuMDc4IDAgMDEzLjM5My0uMzQxem0uNzA2IDE4LjM3NWE5LjIzMSA5LjIzMSAwIDAwMi42MTgtMS4wNyA4LjQxNSA4LjQxNSAwIDAwMi4yMDktMS45MzVjLjYwNy0uODIgMS4wNTUtMS42NjMgMS4zNDMtMi41MjguMzItLjk1Ni40NC0xLjkxMy4zNjUtMi44NjkgMC0xLjE1NC0uMjEzLTIuMjctLjYzOC0zLjM0N2E3Ljc5IDcuNzkgMCAwMC0xLjc3Ni0yLjc3OGMtMS40NzItMS40NzItMy4xOC0yLjI3Ny01LjEyMy0yLjQxNGE4LjY1IDguNjUgMCAwMC0yLjguMjI4IDcuNDk3IDcuNDk3IDAgMDAtMi42MiAxLjE4NEM1LjY5IDYuMDQ2IDQuNjA0IDcuNjE3IDQuMDU4IDkuNTc1YTguMDYgOC4wNiAwIDAwLS4zODcgMi44NDdjLjAzIDEuMDAyLjI1IDEuOTUuNjYgMi44NDYuODY2IDEuODUyIDIuMTQ5IDMuMjcxIDMuODQ5IDQuMjU4Ljg1LjUgMS43NTMuODI3IDIuNzEuOThhNy44MDYgNy44MDYgMCAwMDIuODQ2LS4xMTV6bS0xLjcwOC05LjEwOGwzLjQxNS0zLjU3NSAxLjAwMiAxLjAwMi0zLjQxNSAzLjU1MiAzLjQxNSAzLjU3NS0xLjAwMi45OC0zLjQxNS0zLjU1My0zLjQxNiAzLjU1Mi0xLjAwMS0uOTc5IDMuNDE1LTMuNTc1TDcuNjEgOC43MSA4LjYxIDcuNzA4bDMuNDE2IDMuNTc1eiIgZmlsbD0iIzA2RiIvPjwvc3ZnPg==') no-repeat center center;
			}
			.error-text{
				display:none;
			}
		}
		.placeholder{
			position:absolute; left: 16px; top: 17px; font-size: 15px; line-height: 100%; color: #1C1C1E; transition: .15s ease-in-out;
		}
		.btn-delete{
			position:absolute; top: 0; right: 0; display:block; width: 48px; height: 48px; background: url('data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMjAiIGhlaWdodD0iMjAiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZD0iTTExLjAyOS4wMTZjMS4wOTMuMDc2IDIuMTcuMzggMy4yMzMuOTFhMTAuMjk4IDEwLjI5OCAwIDAxMi43MzMgMS45MzZjMS44OTcgMi4wNSAyLjg0NiA0LjQ3IDIuODQ2IDcuMjY0YTkuMjE4IDkuMjE4IDAgMDEtLjYxNSAzLjMwMSAxMS4wNjggMTEuMDY4IDAgMDEtMS42NjIgMi45NiA5LjkwMyA5LjkwMyAwIDAxLTIuNTI4IDIuMTg2IDkuNTMgOS41MyAwIDAxLTMuMTY1IDEuMjNjLTEuMTY4LjIyOC0yLjI5Mi4yNTgtMy4zNy4wOTFhOS4wODQgOS4wODQgMCAwMS0zLjE2NS0xLjA3IDExLjE1NiAxMS4xNTYgMCAwMS0yLjY2NC0yLjE0IDEwLjMyNyAxMC4zMjcgMCAwMS0xLjc3Ni0yLjg3IDEwLjQwMiAxMC40MDIgMCAwMS0uNzI4LTMuMzQ3IDkuMjg0IDkuMjg0IDAgMDEuNDU1LTMuMzI0Yy43NDQtMi4zNjggMi4wNzItNC4xNzUgMy45ODUtNS40MkE5LjYxNCA5LjYxNCAwIDAxNy42MzYuMzU4IDExLjA3OCAxMS4wNzggMCAwMTExLjAzLjAxNnptLjcwNiAxOC4zNzVhOS4yMzEgOS4yMzEgMCAwMDIuNjE4LTEuMDcgOC40MTUgOC40MTUgMCAwMDIuMjA5LTEuOTM1Yy42MDctLjgyIDEuMDU1LTEuNjYzIDEuMzQzLTIuNTI4LjMyLS45NTYuNDQtMS45MTMuMzY1LTIuODY5IDAtMS4xNTQtLjIxMy0yLjI3LS42MzgtMy4zNDdhNy43OSA3Ljc5IDAgMDAtMS43NzYtMi43NzhjLTEuNDcyLTEuNDcyLTMuMTgtMi4yNzctNS4xMjMtMi40MTRhOC42NSA4LjY1IDAgMDAtMi44LjIyOCA3LjQ5NyA3LjQ5NyAwIDAwLTIuNjIgMS4xODRDMy42OSA0LjA0NiAyLjYwNCA1LjYxNyAyLjA1OCA3LjU3NWE4LjA2IDguMDYgMCAwMC0uMzg3IDIuODQ3Yy4wMyAxLjAwMi4yNSAxLjk1LjY2IDIuODQ2Ljg2NiAxLjg1MiAyLjE0OSAzLjI3MSAzLjg0OSA0LjI1OC44NS41IDEuNzUzLjgyNyAyLjcxLjk4YTcuODA2IDcuODA2IDAgMDAyLjg0Ni0uMTE1em0tMS43MDgtOS4xMDhsMy40MTUtMy41NzUgMS4wMDIgMS4wMDItMy40MTUgMy41NTIgMy40MTUgMy41NzUtMS4wMDIuOTgtMy40MTUtMy41NTMtMy40MTYgMy41NTItMS4wMDEtLjk3OSAzLjQxNS0zLjU3NUw1LjYxIDYuNzEgNi42MSA1LjcwOGwzLjQxNiAzLjU3NXoiIGZpbGw9IiNEMUQxRDYiLz48L3N2Zz4=') no-repeat center center;
		}
		.error-text{
			display: none; position:absolute; font-size: 13px; line-height: 90%; color: #E40033; bottom: -13px; left: 16px;
		}
	}
</style>
