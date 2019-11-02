<template>
    <div class="container">
        <h1>Notas</h1>

        <b-alert
            :show="dismissCountDown"
            dismissible
            :variant="mensaje.color"
            @dismissed="dismissCountDown=0"
            @dismiss-count-down="countDownChanged"
        >
            {{mensaje.texto}}
        </b-alert>

        <form @submit.prevent="agregarNota(nota)" v-if="agregar">
            <h3 class="text-center">Agregar nueva Nota</h3>
            <input type="text" placeholder="Ingrese un Nombre" class="form-control my-2" v-model="nota.nombre">
            <input type="text" placeholder="Ingrese una descripcion" class="form-control my-2" v-model="nota.descripcion">
            <b-button class="btn-sm btn-block btn-success" type="submit">Agregar</b-button>
        </form>

        <table class="table">
            <thead>
                <tr>
                <th scope="col">#</th>
                <th scope="col">Nombre</th>
                <th scope="col">Descripción</th>
                <th scope="col">Fecha</th>
                <th scope="col">Acciones</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(item, index) in notas" :key="index">
                <th scope="row">{{item._id}}</th>
                <td>{{item.nombre}}</td>
                <td>{{item.descripcion}}</td>
                <td>{{item.date}}</td>
                <td>
                    <b-button class="btn-warning btn-sm mx-2" @click="activarEdicion(item._id)">Actualizar</b-button>
                    <b-button class="btn-danger btn-sm mx-2" @click="eliminarNota(item._id)">Eliminar</b-button>
                    <b-button @click="alerta()"> Acción</b-button>
                </td>
                </tr>
            </tbody>
        </table>
    </div>
</template>

<script>
export default {
    data(){
        return{
            notas:[],
            dismissSecs: 5,
            dismissCountDown: 0,
            mensaje:{
                color: '',
                texto: ''
            },
            nota:{
                nombre:'',
                descripcion:''
            },
            agregar: true,
        }
    },
    created(){
        this.listarNotas();
    },
    methods: {
        alerta(){
            this.mensaje.color = 'danger',
            this.mensaje.texto = 'Probando alerta',
            this.showAlert();
        },
        listarNotas(){
            this.axios.get('/notas')
                .then(res =>{
                    console.log(res.data);
                    this.notas = res.data;
                })
                .catch(e =>{
                    console.error(e.response);
                })
        },
        agregarNota(){
            this.axios.post('/nueva-nota', this.nota)
                .then(res =>{
                    this.notas.push(res.data)
                    this.nota.nombre = '';
                    this.nota.descripcion = '';

                    this.mensaje.color = 'success',
                    this.mensaje.texto = 'Nota agregada',
                    this.showAlert();
                })
                .catch(e =>{
                    console.error(e.response);
                    if(e.response.data.error.errors.nombre.message){
                        this.mensaje.texto = e.response.data.error.errors.nombre.message;
                    }else{
                        this.mensaje.texto = 'Error del sistema';
                    }
                    this.mensaje.color = 'danger';
                    this.showAlert();
                })
        },
        countDownChanged(dismissCountDown) {
            this.dismissCountDown = dismissCountDown
        },
        showAlert() {
            this.dismissCountDown = this.dismissSecs
        }
    },
}
</script>