<script setup>
import { FilterMatchMode } from 'primevue/api';
import { ref, onMounted, onBeforeMount } from 'vue';
import ProductService from '@/service/ProductService';
import { useToast } from 'primevue/usetoast';
import { useLayout } from '@/layout/composables/layout';
import { useRouter } from 'vue-router';
const toast = useToast();
const router = useRouter();
const tramites = ref(null);

const filters = ref({});

onBeforeMount(() => {
    initFilters();
});

onMounted(() => {
   //cargarTramites()
});
const opcionseleccionada1 = ref(null)
const countries = ref(
  [
        { label: 'Estados Unidos', value: 'us' },
        { label: 'México', value: 'mx' },
        { label: 'Canadá', value: 'ca' },
        { label: 'España', value: 'es' },
        { label: 'Argentina', value: 'ar' },
        { label: 'Colombia', value: 'co' }
  ]
)


const cargarTramites=()=>{
    fetch('http://localhost:80/apiEstudiojuridico/public/api/tramite/listar',{
        method:'POST',                
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{
        if(res.success){
            tramites.value = res.tramites
        }else{
            toast.add({
             severity: 'warning',
             summary: 'Error',
             detail: 'Problemas al obtener los datos.', 
             life: 3000
        });
        }        
    })
    .catch((e)=>{
        toast.add({
             severity: 'error',
             summary: 'Error',
             detail: 'Problemas con obtener la informacion. Inntentelo nuevamente', 
             life: 3000
        });
    })
    
}

const openNew = () => {
    console.log('aca')
   // this.$router.push('/estudio/tramites/registrar_tramites')
};

const initFilters = () => {
    filters.value = {
        global: { value: null, matchMode: FilterMatchMode.CONTAINS }
    };
};

const volver=()=>{
  router.back()
}

</script>

<template>
    <div class="grid">
      <div class="col-12">
        <div class="card">
          <Toast />
          <Toolbar class="mb-4">
            <template v-slot:start>
              <div class="my-2">
                <h3 class="text-left">Registrar Tramite</h3> 
                <form class="">
                    
                    <div class="p-fluid formgrid grid">

                        <div class="field col-12 md:col-4">
                          <label for="fecha_nacimiento">Fecha de nacimiento</label>
                          <Calendar id="fecha_nacimiento" v-model="fechaNacimiento" />
                        </div>   
                        
                        <div class="field col-12 md:col-4">
                          <label for="abogados">Abogados</label>
                          <Dropdown 
                            id="abogados"
                            v-model="opcionseleccionada1" 
                            :options="countries" 
                            filter placeholder="Seleccione un país"
                            optionLabel="label"
                            />
                        </div> 

                        <div class="field col-12 md:col-4">
                          <label for="abogados">Clientes</label>
                          <Dropdown 
                            id="abogados"
                            v-model="opcionseleccionada1" 
                            :options="countries" 
                            filter placeholder="Seleccione un país"
                            optionLabel="label"
                            />
                        </div>
                        <div class="field col-12 md:col-4">
                          <label for="abogados">Juzgado</label>
                          <Dropdown 
                            id="abogados"
                            v-model="opcionseleccionada1" 
                            :options="countries" 
                            filter placeholder="Seleccione un país"
                            optionLabel="label"
                            />
                        </div>
                                 
                    </div>

                    <br>

                    <div class="p-fluid formgrid grid" >
                      <div class="field col-12 md:col-12">
                        <label for="address">Hechos ocurridos</label>
                        <Textarea id="address" rows="2" />
                      </div>
                    </div>  

                </form>
              </div>
            </template>
          </Toolbar>

          <Toolbar class="mb-4">
            <template v-slot:start>
              <div class="my-2">
                <h3 class="text-left">Detalles Especificos</h3> 
                <form class="">
                    <div class="p-fluid formgrid grid">
                        <div class="field col-12 md:col-6">
                          <label for="abogados">Tipo de tramite</label>
                          <Dropdown 
                            id="abogados"
                            v-model="opcionseleccionada1" 
                            :options="countries" 
                            filter placeholder="Seleccione un país"
                            optionLabel="label"
                            />
                        </div>
                        <div class="field col-12 md:col-6">
                            <label for="quantity">Presupuesto Inicial</label>
                            <InputNumber id="quantity"    :required="true" integeronly />
                            <small class="p-invalid" v-if="submitted && !product.celular">Debe de ingresar el número de telefono de la persona.</small>
                        </div>
                        
                        <div class="field col-12 md:col-6">
                            <label for="quantity">Declaracion del Cliente</label>
                            <Textarea id="address" rows="4" />
                            <small class="p-invalid" v-if="submitted && !product.celular">Debe de ingresar el número de telefono de la persona.</small>
                        </div>
                        <div class="field col-12 md:col-6">
                            <label for="quantity">Declaracion del Demandado (Opcional)</label>
                            <Textarea id="address" rows="4" />
                            <small class="p-invalid" v-if="submitted && !product.celular">Debe de ingresar el número de telefono de la persona.</small>
                        </div>

                        <div class="field col-12 md:col-4">
                          <Button label="Cancelar" class="p-button-secondary col-12   md:col-4 p-3" @click="volver" /> 
                        </div>
                        <div class="field col-12 md:col-4">
                          <Button label="Guardar" class="p-button-primary col-12  md:col-4 p-3" /> 
                        </div>                       

                    </div>       
                </form>
              </div>              
            </template>             
          </Toolbar>
          
        </div>
      </div>
    </div>
</template>

<style scoped lang="scss">

@import '@/assets/demo/styles/badges.scss';

</style>
