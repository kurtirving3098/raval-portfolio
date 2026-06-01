<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';

// Form Data binding
const name = ref("");
const email = ref("");
const message = ref("");
const isLoading = ref(false);

// 🔑 REPLACE THESE WITH YOUR ACTUAL KEYS
const WEB3FORMS_ACCESS_KEY = "e24f9995-dea1-4f09-a131-7f3edd8e02c7";
const SITE_KEY = "6Le3aActAAAAAJGhc_7YDwrN1SsfM12kvIwEFxj4";

const subject = "New message from Portfolio Contact Form";

// reCAPTCHA State
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
        console.error('reCAPTCHA script has not loaded yet.');
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

// 🚀 Integrated Form Submission Logic
const submitForm = async () => {
    if (!recaptchaToken.value) {
        alert('Please complete the reCAPTCHA verification.');
        return; 
    }

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
                message: message.value,
            })
        });

        const result = await response.json();

        if (result.success) {
            alert("Message Sent successfully!");
            // Clear fields on success
            name.value = "";
            email.value = "";
            message.value = "";
        } 
        
    } catch (error) { 
        console.error("Form submission error:", error);
        alert("An error occurred. Please try again.");
    } finally {
        isLoading.value = false;
        resetRecaptcha(); 
    }
};

// Polling for global script availability
let scriptCheckInterval = null;

onMounted(() => {
    scriptCheckInterval = setInterval(() => {
        if (window.grecaptcha && window.grecaptcha.render) {
            renderRecaptcha();
            clearInterval(scriptCheckInterval);
        }
    }, 100);
});

onBeforeUnmount(() => {
    if (scriptCheckInterval) clearInterval(scriptCheckInterval);
});
</script>

<template>
	<div id="contact" class="container-fluid section-pad">
	  <h2 class="section-title text-center">Contact</h2>
	  <p class="section-subtitle text-center">Have any questions? Don't hesitate to reach out!</p>
	  <div class="row justify-content-center mt-4">
	    <div class="col-md-6">
	    	<form id="contact-form" @submit.prevent="submitForm">
	      		<input 
                    type="text" 
                    v-model="name" 
                    class="form-control mb-3" 
                    placeholder="First Name MI. Last Name" 
                    required
                >
	      		<input 
                    type="email" 
                    v-model="email" 
                    class="form-control mb-3" 
                    placeholder="Email" 
                    required
                >
	      		<textarea 
                    class="form-control mb-3" 
                    v-model="message" 
                    rows="5" 
                    placeholder="Message" 
                    required
                ></textarea>

                <div class="d-flex justify-content-center mb-3">
                    <div ref="recaptchaContainer"></div>
                </div>

	      		<div class="d-flex justify-content-between align-items-center">
	        		<div>
                        <a href="https://www.linkedin.com" target="_blank">
                            <i class="devicon-linkedin-plain colored contact-icon"></i>
                        </a>
                        <a href="https://www.gitlab.com" target="_blank">
                            <i class="devicon-gitlab-plain colored contact-icon"></i>
                        </a>
                        <a href="https://www.github.com" target="_blank">
                            <i class="devicon-github-original contact-icon"></i>
                        </a>
	        		</div>
	        		<button class="btn btn-submit" type="submit" :disabled="isLoading">
                        {{ isLoading ? 'Sending...' : 'Submit' }}
                    </button>
	      		</div>
	      </form>
	    </div>
	  </div>
	</div>
	</template>

<style scoped>
/* Your existing styles */
</style>