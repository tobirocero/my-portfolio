<script setup>

	import {Notyf} from 'notyf';

	import {ref, onMounted, onBeforeUnmount} from 'vue';

	const notyf = new Notyf();

	const name = ref("");
	const email = ref("");
	const message = ref("");

	const isLoading = ref(false);

	// Web3Forms Access Key used to authenticate form submissions
	const WEB3FORMS_ACCESS_KEY = "5b874251-1109-4df8-9581-6f518d93a5d0";


	// Email subject that will appear when a form submission is received
	const subject = "New message from Portfolio Contact Form";

	// The submitForm() function handles the contact form submission
	const submitForm = async () => {

		// Ensure the user completes the reCAPTCHA challenge before submitting the form.
        // Check if a reCAPTCHA token exists
        // recaptchaToken.value - stores the verification token returned by Google reCAPTCHA
		if(!recaptchaToken.value) {
			notyf.error('Please verify that you are not a robot');
			return;
		}

		// While the email is being sent, disable the button and change it text to "Sending..."
		isLoading.value = true;

		try {

			const response = await fetch("https://api.web3forms.com/submit", {
				method: "POST",
				headers: {
					"Content-Type": "application/json",
					Accept: "application/json"
				},
				body: JSON.stringify({
					access_key: WEB3FORMS_ACCESS_KEY,
					subject: subject,
					name: name.value,
					email: email.value,
					message: message.value
				})
			})

			const result = await response.json();

				if (result.success) {
					console.log(result);
					isLoading.value = false;
					notyf.success("Message Sent!");
				}
		} catch (error) {
			console.log(error);
			isLoading.value = false;
			noty.error("Failed to send message.")
		} finally {
			resetRecaptcha();
		}
	}
	
	/*reCAPTCHA Integration*/

    const SITE_KEY = '6LfxSActAAAAAM-ahW_mzytvKqQOEgXjMtEQDRZH';  // Replace with your site key
    const recaptchaContainer = ref(null);
    const recaptchaWidgetId = ref(null);
    const recaptchaToken = ref('');

    function onRecaptchaSuccess(token) {
        recaptchaToken.value = token;
    }

    function onRecaptchaExpired() {
        recaptchaToken.value = '';
    }

    function renderRecaptcha() {
        if (!window.grecaptcha) {
            console.error('reCAPTCHA not loaded');
            return;
        }

        recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
            sitekey: SITE_KEY,
            size: 'normal',
            callback: onRecaptchaSuccess,
            'expired-callback': onRecaptchaExpired,
        });
    }

    function resetRecaptcha() {
        if (recaptchaWidgetId.value !== null) {
        	window.grecaptcha.reset(recaptchaWidgetId.value);
        	recaptchaToken.value = '';
        }
    }

    onMounted(() => {
    const interval = setInterval(() => {
        if (window.grecaptcha && window.grecaptcha.render) {
            renderRecaptcha();
            clearInterval(interval);
        }
    }, 100);

      onBeforeUnmount(() => {
          clearInterval(interval);
      });
  });	




</script>

<template>
	<!-- Start of Contact -->
	<h1 class="text-center my-4 pt-5" id="contact">Contact</h1>
	<div class="contact-section">
	    <div class="row align-items-center mt-4">
	        <div class="col-md-6 map-container">
	            <iframe id="gmap_canvas" src="https://maps.google.com/maps?q=centro%20escolar%20university%20manila&t=&z=13&ie=UTF8&iwloc=&output=embed" frameborder="0" scrolling="no" marginheight="0" marginwidth="0"></iframe>
	        </div>
	        <div class="col-md-6">
	            <form @submit.prevent="submitForm">
	                <div class="mb-3">
	                    <input type="text" v-model="name" class="form-control contact-form-control" placeholder="First Name M.I. Last Name">
	                </div>
	                <div class="mb-3">
	                    <input type="email" v-model="email" class="form-control contact-form-control" placeholder="Email">
	                </div>
	                <div class="mb-3">
	                    <textarea class="form-control contact-form-control" v-model="message" rows="6" placeholder="Message"></textarea>
	                </div>
	                <div class="form-footer">
	                    <div class="social-icons">
	                        <a href="https://www.linkedin.com/in/charles-babbage-8291a6211/" id="linkedin"><i class="fab fa-linkedin"></i></a>
	                        <a href="https://gitlab.com/cbabbage0991" id="gitlab"><i class="fab fa-gitlab"></i></a>
	                        <a href="https://github.com/cbabbage0991" id="github"><i class="fab fa-github"></i></a>
	                    </div>
	                    <button type="submit" class="submit-btn pl-5 pr-5" :disabled="isLoading">{{isLoading ? "Sending..." : "Submit"}}</button>
	                </div>

	                <div class="d-flex justify-content-end mt-2">
	                	<div ref="recaptchaContainer"></div>

	                </div>
	            </form>
	            
	        </div>
	    </div>
	</div>
	<!-- End of Contact -->
</template>

<style scoped>
	
</style>