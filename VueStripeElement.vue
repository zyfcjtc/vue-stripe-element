<template>
	<div>
		<form action="/charge" method="post" id="payment-form">
			<div class="form-row">
				<label for="card-element">
				  	Credit or debit card
				</label>
				<div id="card-element">
				  	<!-- a Stripe Element will be inserted here. -->
				</div>

				<!-- Used to display form errors -->
				<div id="card-errors"></div>
			</div>

			<button>Submit Payment</button>
		</form>
	</div>
</template>

<script>
	export default {


		mounted() {
			this.loadStripeElement();
		},
		method: {
            loadStripeElement: function(public_key){
            	var vm = this;
				// Create a Stripe client
				var stripe = Stripe('pk_test_6pRNASCoBOKtIshFeQd4XMUh');

				// Create an instance of Elements
				var elements = stripe.elements();

				// Custom styling can be passed to options when creating an Element.
				// (Note that this demo uses a wider set of styles than the guide below.)
				var style = {
						base: {
							color: '#32325d',
							lineHeight: '24px',
							fontFamily: '"Helvetica Neue", Helvetica, sans-serif',
							fontSmoothing: 'antialiased',
							fontSize: '16px',
							'::placeholder': {
								color: '#aab7c4'
							}
						},
						invalid: {
							color: '#fa755a',
							iconColor: '#fa755a'
						}
				};

				// Create an instance of the card Element
				var card = elements.create('card', {style: style});

				// Add an instance of the card Element into the `card-element` <div>
				card.mount('#card-element');

				// Handle real-time validation errors from the card Element.
				card.addEventListener('change', function(event) {
					var displayError = document.getElementById('card-errors');
					if (event.error) {
						displayError.textContent = event.error.message;
					} else {
						displayError.textContent = '';
					}
				});

				// Handle form submission
				var form = document.getElementById('payment-form');
				form.addEventListener('submit', function(event) {
					event.preventDefault();

					stripe.createToken(card).then(function(result) {
						if (result.error) {
							// Inform the user if there was an error
							var errorElement = document.getElementById('card-errors');
							errorElement.textContent = result.error.message;
						} else {
							// Send the token to your server
							console.log(result.token);
							vm.token = result.token;
						}
					});
				});
            },
		}
	}
</script>
<style scoped>
.StripeElement {
	background-color: white;
	padding: 8px 12px;
	border-radius: 4px;
	border: 1px solid transparent;
	box-shadow: 0 1px 3px 0 #e6ebf1;
	-webkit-transition: box-shadow 150ms ease;
	transition: box-shadow 150ms ease;
}

.StripeElement--focus {
	box-shadow: 0 1px 3px 0 #cfd7df;
}

.StripeElement--invalid {
	border-color: #fa755a;
}

.StripeElement--webkit-autofill {
	background-color: #fefde5 !important;
}
</style>