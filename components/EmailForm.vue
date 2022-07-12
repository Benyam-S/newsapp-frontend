<template>
    <div class="email-form pt-5 pb-5 z-depth-1">
        <h3 class="font-weight-bold text-center mb-3">Email Us</h3>

        <p class="text-muted text-center">
            Send us your favorite category and we will update you with the latest newest!
        </p>

        <div v-if="!requestSuccess">
            <div class="md-form md-outline mt-3 from-input-container">
                <label for="form-name">Name</label>
                <input type="text" class="form-control" v-model="name">
                <div class="form-input-error">
                    <span color="error">{{ nameError }}</span>
                </div>
            </div>

            <div class="md-form md-outline mt-3 from-input-container">
                <label for="form-email">E-mail</label>
                <input type="email" class="form-control" v-model="email">
                <div class="form-input-error">
                    <span color="error">{{ emailError }}</span>
                </div>
            </div>

            <div class="md-form md-outline mt-3 from-input-container">
                <label for="form-category">Favorite Category</label>
                <select class="form-select pt-2 pb-2 categories" v-model="category" aria-label="category">
                    <option value="all">All</option>
                    <option :key="categoryValue" v-for="categoryValue in categories" :value="categoryValue">{{
                            categoryValue
                    }}
                    </option>
                </select>
            </div>

            <div class="mt-4 text-center">
                <div class="form-input-error mb-2">
                    <span class="d-block m-auto text-center" color="error">{{ sendEmailResponse }}</span>
                </div>
                <button type="button" class="btn btn-primary btn-sm ml-0 pt-2 pb-2 ps-5 pe-5" @click="sendEmail">
                    <div v-if="status === 'loading'">
                        <div class="spinner-border spinner-border-sm" role="status">
                            <span class="sr-only">Loading...</span>
                        </div>
                    </div>
                    <div v-else>Submit</div>
                </button>
            </div>
        </div>

        <div v-else class="d-flex flex-column justify-content-center align-items-center mt-5 mb-5">
            <fa class="text-success" :style="{ 'font-size': '50px' }" icon="circle-check"></fa>
            <p class="text-muted text-center mt-3">Operation successful please check your email!</p>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import config from '../config/config';

export default {
    name: "EmailForm",
    data() {
        return {
            name: "",
            email: "",
            nameError: "",
            emailError: "",
            requestSuccess: false,
            sendEmailResponse: "",
            categories: [],
            category: "all",
            status: "active"
        };
    },

    created() {
        // Getting all the categories
        var url = `${config.apiBaseURL}/categories`;
        axios.get(url).then((result) => {
            if (result.data && result.data.status) {
                this.categories = result.data.categories;
            }
        }).catch((err) => {
            console.log(err);
        });
    },

    methods: {
        validateInput(inputs) {
            const { name, email } = inputs;
            const isValidEmail =
                /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                    email
                );
            const isValidName = name.length > 2;
            return {
                name: isValidName,
                email: isValidEmail,
            };
        },

        // Validating the input value
        handleValidateInput(props, event) {
            const newState = { "name": this.name, "email": this.email, };

            let isFormValid = true;
            this.nameError = "";
            this.emailError = "";
            this.sendEmailResponse = "";

            Object.entries(this.validateInput(newState)).forEach(([key, value]) => {
                if (!value) {
                    isFormValid = false; // Setting the form is invalid if there is an error input

                    // Setting the error message for the corresponding inputs
                    if (key == "name") {
                        this.nameError = "Please enter valid name.";
                    } else if (key == "email") {
                        this.emailError = "Please enter valid email address.";
                    }
                }
            });

            return isFormValid;
        },

        sendEmail(e) {
            if (this.handleValidateInput()) {
                this.status = "loading";

                var url = `${config.apiBaseURL}/service/send_mail`;
                const response = axios.post(url, {
                    name: this.name,
                    email: this.email,
                    category: this.category,
                });
                response.then((result) => {
                    this.status = "active";
                    this.requestSuccess = true
                }).catch((result) => {
                    this.status = "active";

                    if (result.response.data) {
                        let response = result.response.data;
                        if (response.error) {
                            if (response.error.name) {
                                this.nameError = response.error.name;
                            }
                            if (response.error.email) {
                                this.emailError = response.error.email;
                            }
                        } else {
                            this.sendEmailResponse = "Unable to perform operation please try agin!";
                        }
                    }
                });
            }
        }
    }
}
</script>

<style scoped>
.email-form {
    max-width: 500px;
    margin: 2rem auto;
    overflow: hidden;
    padding: 1rem 2rem;
    background: #fff;
}

label {
    font-size: medium;
    margin-bottom: 5px;
}

.from-input-container {
    margin-bottom: 30px;
}

.form-input-error {
    display: flex;
    padding: 3px 0 0 0;
    margin: 0rem;
}

.form-input-error span {
    color: rgb(204, 50, 63);
    margin: 0px;
    position: relative;
    font-weight: 400 !important;
    font-size: 0.8rem !important;
    line-height: 1.5 !important;
}
</style>
