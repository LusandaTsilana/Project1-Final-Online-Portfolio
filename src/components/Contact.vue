

<template>
    <h1 class="display-4">Contact</h1>

    <div id="main" class="container">

        <div id="contact-modes" class="column shadow p-3 mb-5 bg-body-tertiary rounded">
            <h5>Talk to me</h5>

            <div class="col shadow-sm p-3 mb-5 bg-body-tertiary rounded">
                <p><a href=""><i class="bi bi-envelope-at"></i></a>sanda.tsilana@gmail.com</p>
            </div>
            <div class="col shadow-sm p-3 mb-5 bg-body-tertiary rounded">
                <p><a href="https://www.linkedin.com/in/lusanda-tsilana31" target="_blank" @click="openLink()"><i class="bi bi-linkedin"></i></a>Linkedin</p>
            </div>
            <div class="col shadow-sm p-3 mb-5 bg-body-tertiary rounded">
                <p><a href="https://github.com/LusandaTsilana" target="_blank" @click="openLink()"><i class="bi bi-github"></i></a>Github</p>
            </div>

        </div>

        <div id="form-box" class="column shadow p-3 mb-5 bg-body-tertiary rounded">
            <h5>Write me your project</h5>



            <form @submit.prevent="sendForm" ref="myForm" >
              
                <div class="mb-3">
                    <label for="InputName" class="form-label">Full Name</label>
                    <input type="name" class="form-control" id="InputName" v-model="state.fullname"/>
                    <span v-if="v$.fullname.$error">
                    {{ v$.fullname.$errors[0].$message }}</span>
                   
                </div>

                <div class="mb-3">
                    <label for="InputNumber" class="form-label">Cellphone (optional)</label>
                    <input type="cellphone" class="form-control" id="InputNumber" v-model="state.cellphone"/>
                    <span v-if="v$.cellphone.$error">
                    {{ v$.cellphone.$errors[0].$message }}</span>
                   
                </div>

              
                <div class="mb-3">
                    <label for="InputEmail" class="form-label">Email</label>
                    <input  type="text" class="form-control" id="InputEmail" v-model="state.email"/>
                    <span v-if="v$.email.$error">
                    {{ v$.email.$errors[0].$message }}</span>
                </div>

               
                <div class="mb-3">
                    <label for="InputMessage" class="form-label">Message</label>
                    <input  type="text" class="form-control pb-5" id="InputMessage" cols="30" rows= "10" v-model="state.messagetext"/>
                    <span v-if="v$.messagetext.$error">
                    {{ v$.messagetext.$errors[0].$message }}</span>

                </div>

                <div class="mb-3">
                    <!--<vue-recaptcha sitekey="6LfvMBwoAAAAAHBRBl_2OCBMvgygQgeOhT-IBTjk"></vue-recaptcha>-->
                        <div class="g-recaptcha" :data-sitekey="state.recaptchaSiteKey" :data-callback="onRecaptchaClick"></div>
                </div>

                
              
                <button type="submit" class="btn btn-outline" id="submit-button">Submit</button>
            </form>
        </div>

    </div>
</template>

<style scoped>
/*--headings styling--*/
h1 {
    text-align: center;
    text-decoration: underline;
    padding-top: 50px;
    padding-bottom: 70px;
}

h5 {
    text-align: center;
    margin: 10px 0px 30px 0px;
    font-weight: bold;
}

#main {
    display: flex;
    justify-content: space-evenly;
    padding-bottom: 50px;
}

/*---contact modes styling--*/
i {
    padding: 20px 10px 20px 10px;
    font-size: 2rem;
}

div a {
    text-decoration: none;
    color: black;
}

#form-box {
    padding: 100px;
    width: 50%;
    height: 100%;
}
#contact-modes{
    padding: 100px;
    width: 30%;
    height: 100%;
}

.g-recaptcha{
    display: block;
    min-height: 125px;
    min-width: 20px;
}

.col {
    text-align: center;
    padding: 30px 0px 30px 0px;

}

/*---form box styling--- fix button placements*/
#submit-button {
    background-color: rgba(202, 220, 199, 1);
}

span{
    color: red;
    font-size: 13px;
}

/*---responsiveness of column components---*/
@media (max-width: 992px) {
    #main {
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;

    }

    #contact-modes,
    #form-box {
        padding: 100px;
        width: 60%;
        
    }

}
</style>


<script>

import { useVuelidate } from '@vuelidate/core'
import { required, alpha, numeric, email, minLength, maxLength } from '@vuelidate/validators'
import { reactive, computed, ref } from 'vue'
import emailjs from '@emailjs/browser';
//import VueRecaptcha from 'vue-recaptcha'



export default {

   // components: {
       // VueRecaptcha,
    //},

    setup() {

        const recaptchaResponse = ref(null);
        
        const state = reactive({
            fullname: '',
            cellphone: '',
            email: '',
            messagetext: '',
           recaptchaSiteKey: '6LfvMBwoAAAAAHBRBl_2OCBMvgygQgeOhT-IBTjk',
           

        });

        const rules = computed(() => {
            return {
                fullname: { required, alpha },
                cellphone: { numeric,
                    minLength: minLength(10),
                    maxLength: maxLength(10) },
                email: { required, email },
                messagetext: {
                    required
                },
               
            };
        });


        const v$ = useVuelidate(rules, state);
        return {
            state,
            v$,
            recaptchaResponse,
            
           // VueRecaptcha,
           
            
           
        };
    },
    minLength(min) {
        return {
            $property: "cellphone",
            $validator: minLength(min),
            $message: ({ $params }) => `A cellphone number should have ${$params.min} digits.`,
            $params: { min }
        };
    },
    maxLength(min) {
        return {
            $property: "cellphone",
            $validator: maxLength(min),
            $message: ({ $params }) => `A cellphone number should have ${$params.min} digits.`,
            $params: { min }
        };
    },
    methods: {
        
        async sendForm() {
            //to validate form fields using vuelidate
            this.v$.$validate();
           
           //to validate recaptcha to see if it has been clicked
           if (recaptchaResponse.value) {
            if (!this.v$.$error) {
                emailjs
                    .sendForm('service_ouebe0d', 'template_6yxd1di', this.$refs.myForm, 'n3c3fJnlqx0Zw7gBF')
                    .then((response) => {
                    console.log('Email sent successfully', response);
                    //will send form to server/email.js here
                })
                    .catch((errors) => {
                    console.error('Email sending failed', errors);
                });
            } else {
                //display error message if recaptcha is not clicked
                console.error('recaptcha not clicked!')
            }
           }

            
           
        },
        openLink() {
            //to open to new window when clicking on view for school website
        },

        onRecaptchaClick(response) {
  // This method will be called when reCAPTCHA is clicked.
  recaptchaResponse.value = response;

},

    },

   
   
}





</script>


