<template>
    <div class="col">
        <div class="card shadow">
            <div class="card-header border-0">
                <div class="text-lg-right">
                    <router-link class="btn btn-primary text-lg-right" :to="{path: '/place/create'}">
                        <span class="btn-inner--icon"><i class="ni ni-fat-add"></i></span>Agregar
                    </router-link>
                </div>
                <div class="form-group align-content-end">
                    <input type="text" id="search" name="search" class="form-control col-md-6" placeholder="buscar..."
                           v-model="search"
                           v-on:keyup="onSearch">
                </div>
                <h3 class="mb-0">Lugares registrados</h3>
            </div>
            <div class="table-responsive">
                <table class="table align-items-center table-flush">
                    <thead class="thead-light">
                    <tr>
                        <th scope="col">Lugar</th>
                        <th scope="col">Nombre</th>
                        <th scope="col">Descripción</th>
                        <th scope="col">Encargado</th>
                        <th scope="col">Extensión</th>
                        <th scope="col"></th>
                    </tr>
                    </thead>
                    <tbody v-if="places.data.length !== 0">
                    <tr v-for="place in places.data" :key="place.id">
                        <th scope="row">
                            <div class="media align-items-center">
                                <a href="#" class="avatar rounded-circle mr-3">
                                    <img alt="Image placeholder" :src="'/images/places/'+place.foto">
                                </a>
                                <div class="media-body">
                                    <span class="mb-0 text-sm"></span>
                                </div>
                            </div>
                        </th>
                        <td>{{place.nombre}}</td>
                        <td>{{place.descripcion}}</td>
                        <td>{{place.encargado}}</td>
                        <td>{{place.extension}}</td>
                        <td class="text-right">
                            <div class="dropdown">
                                <a class="btn btn-sm btn-icon-only text-light" href="#" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false">
                                    <i class="fas fa-ellipsis-v"></i>
                                </a>
                                <div class="dropdown-menu dropdown-menu-right dropdown-menu-arrow">
                                    <router-link class="dropdown-item text-success" v-bind:to="{name: 'place-edit', params:{id: place.id}}">Editar</router-link>
                                    <a class="dropdown-item text-danger" v-on:click="onDelete(place.id, place)">Eliminar</a>
                                </div>
                            </div>
                        </td>
                    </tr>
                    </tbody>
                    <tr v-else>
                        <td><strong>No se encontrarón resultados</strong></td>
                    </tr>
                </table>
            </div>
            <div class="card-footer py-4">
                <div class="pagination justify-content-center">
                    <pagination :data="places" @pagination-change-page="getResults"></pagination>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
    export default {
        data(){
          return {
              places: {},
              search: ''
          }
        },
        created() {
            this.getResults();
        },
        methods: {

            onDelete(id, element){
                this.$swal(swalConfirmDelete).then(result =>{
                    if (result.value){
                        this.$axios.delete(`/place/${id}`).then(()=>{
                            this.$swal(toastDelete);
                            this.places.data.splice(this.places.data.indexOf(element), 1);
                        })
                    }
                });
            },

            getResults(page = 1){
                this.$axios.get(`/place?page=${page}`).then(response => {
                    this.places = response.data;
                });
            },

            onSearch(){
                if (this.search !=='')
                    this.$axios.get(`/place/search/${this.search}`).then(response => { this.places = response.data; });
                else
                    this.getResults();
            },

        }
    }
</script>

