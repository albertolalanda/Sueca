<template>
    <div class="page">
        <navbar-component-game :user="user"></navbar-component-game>

        <div class="page-content d-flex align-items-stretch">
            


            <div class="content-inner-game">
                <!-- Page Header-->
                <header class="page-header">
                    <div class="container-fluid">
                        <h2 class="no-margin-bottom">Playing Area</h2>
                    </div>
                </header>
                <!-- Dashboard Counts Section-->
                <section class="tables">
                    <div class="container-fluid">
                        <div class="row">
                            <div class="col-lg-12">
                                <div class="card">
                                    <game-lobby :user="user"></game-lobby>
                                    

                                </div>
                            </div>
                        </div>
                    </div>
                </section>
                
                <!-- Dashboard Header Section    -->



                <!-- Page Footer-->
                <footer class="main-footer">
                    <div class="container-fluid">
                        <div class="row">
                            <div class="col-sm-6">
                                <p>Recurso Sueca | DAD</p>
                            </div>
                            <div class="col-sm-6 text-right">
                                <p>Alberto Lalanda | Bruno Pereira | Fábio Lage</p>
                            </div>
                        </div>
                    </div>
                </footer>
            </div>
        </div>
    </div>
</template>
<script type="text/javascript">

    export default {
        data: function(){

            return {
                user:'',
                

            }
        },
        methods: {
            getUser: function () {
                axios.get('/api/user')
                    .then((response) => {
                        this.user = response.data;

                    })
                    .catch((error) => {

                    });
            },
        },
        computed: {

        },
        components: {

        },
        created: function(){
            this.getUser();
        }


    }
</script>