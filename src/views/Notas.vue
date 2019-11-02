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
            }
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
        countDownChanged(dismissCountDown) {
            this.dismissCountDown = dismissCountDown
        },
        showAlert() {
            this.dismissCountDown = this.dismissSecs
        }
    },
}
</script>