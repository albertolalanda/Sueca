<template>
    <section class="forms">
        <div class="container-fluid">
            <div class="row">
                <div class="col-lg-6">
                    <div class="card">
                        <div class="card-header d-flex align-items-center ">
                            <h3 class="h4 text-dark">Platform Email Settings</h3>
                        </div>
                        <div class="card-body">
                            <form id="formEmailsSetting" class="" method="post" v-on:submit.prevent="validateForm">
                                <div class="form-group" v-bind:class="{ 'has-error': missingEmail || invalidEmail  }">
                                    <label class="control-label" for="plat_email">Email <span class="required">*</span>
                                    </label>
                                    <div class="">
                                        <input  type="text" name="plat_email" id="plat_email" v-model="settings.email" class="form-control">
                                        <span class="alert alert-danger" v-if="missingEmail">Fill Email</span>
                                        <span  class="alert alert-danger" v-if="invalidEmail">Invalid Email</span>
                                    </div>
                                </div>

                                <div class="form-group" v-bind:class="{ 'has-error': missingPassword  }" >
                                    <label class="control-label" for="plat_password">Password <span class="required">*</span>
                                    </label>
                                    <div class="">
                                        <input type="password" name="plat_password" id="plat_password" v-model="settings.password" class="form-control">
                                        <span  class="alert alert-danger" v-if="missingPassword">Fill Password</span>
                                    </div>
                                </div>

                                <div class="form-group" v-bind:class="{ 'has-error': missingHost  }">
                                    <label class="control-label" for="plat_host">Host <span class="required">*</span>
                                    </label>
                                    <div class="">
                                        <input type="text" name="plat_host" id="plat_host" v-model="settings.host"  class="form-control">
                                        <span  class="alert alert-danger" v-if="missingHost">Fill Host</span>
                                    </div>
                                </div>

                                <div class="form-group" v-bind:class="{ 'has-error': missingPort || invalidPort }">
                                    <label class="control-label" for="plat_port">Port <span class="required">*</span>
                                    </label>
                                    <div class="">
                                        <input type="number" name="plat_port" id="plat_port" v-model="settings.port" class="form-control">
                                        <span  class="alert alert-danger" v-if="missingPort">Fill Port</span>
                                        <span  class="alert alert-danger" v-if="invalidPort">Enter a valid Port Number </span>
                                    </div>
                                </div>
                                <div class="form-group">
                                    <label class="control-label" for="plat_encryption">Encryption</label>
                                    <div class="">
                                        <select v-model="settings.encryption"name="plat_encryption" id="plat_encryption" class="form-control">
                                            <option v-for="option in options" v-bind:value="option.value">
                                                {{ option.text }}
                                            </option>
                                        </select>
                                    </div>
                                </div>


                                <div class="form-group">
                                    <input type="submit" value="Save" class="btn btn-dark float-right">
                                    <span  class="alert alert-danger" v-if="serverError">{{serverErrorMessage}}</span>
                                </div>
                            </form>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </section>
</template>



<script type="text/javascript">
    export default {
        data: function(){
            return {
                settings: {
                    email: '',
                    password: '',
                    host: '',
                    port: '',
                    encryption: '',
                },
                attemptSubmit: false,
                serverError: false,
                serverErrorMessage: '',
                success: false,
                options: [
                    { text: 'SSL', value: 'ssl' },
                    { text: 'TLS', value: 'tls' },
                    { text: 'Nenhuma', value: '' }
                ]
            }
        },
        computed: {
            missingEmail: function () {
                return this.settings.email.trim() === '' && !this.hasServerError && this.attemptSubmit;
            },
            invalidEmail: function () {
                return !this.missingEmail && !this.validateEmail(this.settings.email.trim()) && !this.hasServerError && this.attemptSubmit;
            },
            missingPassword: function () {
                return this.settings.password.trim() === '' && !this.hasServerError && this.attemptSubmit;
            },
            missingHost: function () {
                return this.settings.host.trim() === '' && !this.hasServerError && this.attemptSubmit;
            },
            missingPort: function () {
                return this.settings.port === '' && !this.hasServerError && this.attemptSubmit;
            },
            invalidPort: function () {
                return !this.missingPort && !this.validateIntegerBiggerThan0(this.settings.port) && !this.hasServerError && this.attemptSubmit;
            },
            hasClientError: function () {
                return (this.missingEmail || this.invalidEmail || this.missingPassword || this.missingHost || this.missingPort || this.invalidPort);
            },
            hasServerError: function () {
                return this.serverError;
            },
            isFormInvalid: function () {
                return (this.hasClientError || this.hasServerError) && this.attemptSubmit;
            },
        },
        methods: {
            validateEmail: function (email) {
                var re = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
                return re.test(email);
            },
            validateIntegerBiggerThan0: function (number) {
                var re = /^[1-9][0-9]*$/;
                return re.test(number);
            },
            validateForm: function () {
                //CLEARS SERVER ERROR'S
                this.serverError = false;
                this.success = false;

                //PREVENT FORM
                event.preventDefault();

                //FORM SUBMITED
                this.attemptSubmit = true;

                //IF FORM IS VALID MAKE API REQUEST FOR LOGIN
                if (!this.isFormInvalid) {
                    const data = {
                        host:  this.settings.host,
                        port:  this.settings.port,
                        password:  this.settings.password,
                        encryption:  this.settings.encryption,
                        email: this.settings.email,
                    };
                    axios.post('/api/settings/update', data)
                        .then((response) => {
                            this.success = true;
                            this.attemptSubmit = false;
                            this.serverError = true;
                            this.serverErrorMessage = 'Data updated!';
                        })
                        .catch((error) => {
                            this.serverError = true;
                            this.serverErrorMessage = error.response.data.msg;
                        });
                }
            },
            getPlatformData: function () {
                axios.get('/api/settings')
                    .then(response=>{
                        this.settings = response.data.data;
                    });
            },
            cancel: function () {
                this.attemptSubmit = false;
                this.getPlatformData();
            }
        },
        created: function () {
            this.getPlatformData();
        }
    }
</script>