<template>

<div class="card">
    <div class="card-body">
        <div class="d-flex justify-content-between pb-2 mb-2">
            <h5 class="card-title">Tout les produits</h5>
            <div>
                <button class="btn btn-success" type="button" @click="this.$router.push('/novalager/add')">Ajouter un produit</button>
            </div>
        </div>
       
        <table class="table table-hover table-sm">
            <thead class="bg-dark text-light">
                <tr>
                    <th width="50" class="text-center">#</th>
                    <th>Titre</th>
                    <th>Description</th>
                    <th>Url</th>
                    <th>PDF</th>
                    <th class="text-center" width="120">Image</th>
                    <th class="text-center" width="200">Actions</th>
                </tr>
            </thead>
            <tbody>
               
                <tr v-for="(product, index) in products" :key="product.id">
                    <td class="text-center">{{index+1}}.</td>
                    <td>{{product.title}}</td>
                    <td>{{product.description}}</td>
                    <td>
                        <a href="#" @click="gotoAmazon()">{{product.url_amazon}}</a>
                    </td>
                    <td>{{product.filePdf}}</td>
                    <td class="text-center">
                        <div v-if="product.image">
                            <img alt="product-img" width="100" v-bind:src="'/images/' +product.image">
                        </div>
                    </td>
                    <td class="text-center">
                        <router-link :to="{name:'editproduct', params: {id:product.id}}" class="btn btn-warning">Modifier</router-link>
                        <button class="btn btn-danger" @click="deleteproduct(product.id)">Supprimer</button>
                        <router-link :to="{name:'showproduct', params: {id:product.id}}" class="btn btn-warning">Voir</router-link>
                    </td>
                </tr>
            </tbody>
        </table>


    </div>
</div>


</template>

<script>


    export default {
 
        data() {
            return {
                products: [],
                strSuccess: '',
                strError: '',
                
            }
        },
        created() {
            this.$axios.get('/sanctum/csrf-cookie').then(response => {
                this.$axios.get('/api/products')
                .then(response => {
                    this.products = response.data;
                })
                .catch(function(error) {
                    console.log(error);
                });
            });
        },
        methods: {
            deleteproduct(id) {
                this.$axios.get('/sanctum/csrf-cookie').then(response => {
                    let existingObj = this;

                    if(confirm("Voulez-vous vraiment supprimer ce produit ?")) {
                        this.$axios.delete(`/api/products/delete/${id}`)
                        .then(response => {

                            let i = this.products.map(item => item.id).indexOf(id); // find index of your object
                            this.products.splice(i, 1);
                            existingObj.strError = "";
                            existingObj.strSuccess = response.data.success;

                        })
                        .catch(function(error) {
                            existingObj.strError = "";
                            existingObj.strSuccess = error.response.data.message;
                        });
                    }
                });
            },
            gotoAmazon() {
                fetch('https://api.ipregistry.co/?key=kon72k7l4497ihrg')
                    .then(function (response) {
                        return response.json();
                    })
                    .then(function (payload) {
                        console.log(payload.location.country.name);
                        let country = payload.location.country.name
                        if (country === 'Canada') {
                            window.open("https://amazon.ca/", "_blank");
                        } else if (country === 'USA'){
                            window.open("https://amazon.com/", "_blank");
                        }
                });
                
                
            },
           
        },
        beforeRouteEnter(to, from, next) {
            if (!window.Laravel.isLoggedin) {
                window.location.href = "/";
            }
            next();
        }
    }

</script>