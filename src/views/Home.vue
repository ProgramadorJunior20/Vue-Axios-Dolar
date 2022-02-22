<template>
  <div>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-date-picker 
            v-model="fecha" 
            elevation="15"
            full-width
            locale="ES-CO"
            :min="minimo"
            :max="maximo"
            @change="getDolar(fecha)"
          >

          </v-date-picker>
        </v-card>
        <v-card  color="error" dark>
          <v-card-text class="subtitle-1 text-center">
            {{valor}}
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
  import axios from "axios"
  import { mapMutations } from 'vuex'
  export default {
    name: 'Home',
    data(){
      return{
        fecha: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
        minimo: '1984',
        maximo: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
        valor: null
      }
    },
    methods:{
      ...mapMutations(['mostrarLoading', 'ocultarLoading']),
      async getDolar(dia){
        //invirtiendo orden de fecha
        let arrayFecha = dia.split(['-'])
        let ddmmyy= arrayFecha[2]+'-'+arrayFecha[1]+'-'+arrayFecha[0]

        try {
          //mostrando loading
          this.mostrarLoading({titulo: 'Accediendo a información', color: 'secondary'})

          //almacenamos la api en la confuncion getDolar, en una variable
          let datos = await axios.get(`https://mindicador.cl/api/dolar/${ddmmyy}`);
          
          //solo se pinta el valor cuando traemos algo de la api
          if(datos.data.serie.length > 0){
            this.valor = await datos.data.serie[0].valor
          }else{
            this.valor = 'Sin resultados'
          }


        } catch (error) {
          //console.log(error)
        }
        finally{
          //ocultando loading
          this.ocultarLoading()
        }

      }
    },
    created(){
      //llamamos el metodo getDolar() para mostrarlo por consola
      this.getDolar(this.fecha)
    }

  }
</script>
