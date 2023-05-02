<script setup>
import { FilterMatchMode } from 'primevue/api';
import { ref, onMounted, onBeforeMount ,  } from 'vue';
import ProductService from '@/service/ProductService';
import { useToast } from 'primevue/usetoast';
import { useLayout } from '@/layout/composables/layout';
import { useRouter } from 'vue-router';
const toast = useToast();
const router = useRouter();
const tramites = ref(null);
const submitted = ref(false);

const fecha = ref('')
const abogado  = ref('')
const abogados = ref([])
const cliente =  ref('')
const clientes =  ref([])
const juzgado = ref('')
const juzgados =  ref([])
const tipotramite = ref('')
const tipotramites = ref([])
const presupuesto = ref(0)
const descripcionpresupuesto= ref('')
const declaracioncliente = ref('')
const declaraciondemandado= ref('')
const tipopretenciones = ref([])
const tipopretencion = ref('')
const pretencion_otro = ref('')
const valormedida = ref('')
const pretencioncliente = ref('')
const pretenciondemandado = ref('')
const hechosocurridos = ref('')
const ruta =ref('http://localhost:80/apiEstudiojuridico/public/api/')

const filters = ref({});

onBeforeMount(() => {
    initFilters();
     
});

onMounted(() => {
   cargarClientes()
   cargarAbogados()
   cargarJuzgado()
   cargarTipoTramites()
   cargarTipoPretenciones()
});

const mostrarPrueba=()=>{
  console.log( abogados.value[0] )
}

const cargarClientes=()=>{
    fetch(ruta.value+'clientes/retornarclientesbasico',{
        method:'POST',                
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{
    
        if(res.status == 200 ){
             clientes.value = res.clientes
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

const cargarAbogados=()=>{
    fetch(ruta.value+'abogados/retornarabogadosbasico',{
        method:'POST',                
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{        
        if(res.status == 200 ){
             abogados.value = res.abogados
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

const cargarJuzgado=()=>{
    fetch(ruta.value+'juzgado/retornarjuzgadobasico',{
        method:'POST',                
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{        
        if(res.status == 200 ){
             juzgados.value = res.juzgados
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

const cargarTipoTramites=()=>{// tipotramites/retornartipotramite
    fetch(ruta.value+'tipotramites/retornartipotramite',{
        method:'POST',                
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{        
        if(res.status == 200 ){
             tipotramites.value = res.tipotramites
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

const cargarTipoPretenciones=()=>{// 
    fetch(ruta.value+'pretenciones/retornartipopretenciones',{
        method:'POST',                
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{        
        if(res.status == 200 ){
             tipopretenciones.value = res.pretenciones
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

const initFilters = () => {
    filters.value = {
        global: { value: null, matchMode: FilterMatchMode.CONTAINS }
    };
};

const volver=()=>{
  router.back()
}

const tipotramiteseleccionado=(e)=>{
  console.log(e)
  presupuesto.value = e.value.precioinicial
}

const guardar=()=>{
  submitted.value = true;  
  if ( fecha.value && abogado.value.id && cliente.value.id && juzgado.value.id && hechosocurridos.value &&
          tipotramite.value.id && declaracioncliente.value && tipopretencion.value.id && valormedida.value &&
          pretencioncliente.value ) 
      {
        let fecha_ =new Date(fecha.value);
        let fecha_reg=fecha_.getFullYear()+'-'+(fecha_.getMonth()+1)+'-'+fecha_.getDate();

        fetch( ruta.value+'tramite/agregar',{
            method : 'POST', 
            body:JSON.stringify({
              fecha:fecha_reg,
              estado :1,
              hechosOcurridos:hechosocurridos.value,
              abogado_id :abogado.value.id,
              cliente_id : cliente.value.id ,
              tipoproceso_id: tipotramite.value.id ,
              valor_medida: valormedida.value ,
              tipopretencion_id : tipopretencion.value.id ,
              detallePretencionCliente: pretencioncliente.value   ,
              declaracionCliente : declaracioncliente.value,
              declaracionDemandado : declaraciondemandado.value,
              monto: presupuesto.value ,
              asunto : descripcionpresupuesto.value,
              detallePretencionDemandado : pretenciondemandado.value
            }),               
            headers: {
              "Content-Type": "application/json",                               
            },
        })
        .then((response) => {
            return response.json();
        })
        .then(res=>{    
            console.log(res)
            if(res.status == 200 ){
              toast.add({ 
                severity: 'success', 
                summary: 'Registrado', 
                detail: 'El registro del nuevo tramite se guardo correctamente.', 
                life: 3000 });  
            
              setTimeout(function() {
                  router.back()
              },3000);
            
            }else{
                toast.add({
                severity: 'warning',
                summary: 'Error',
                detail: 'Hubo un error al guardar el tramite', 
                life: 3000
              });
            }   
        })
        .catch((e)=>{
          toast.add({
                severity: 'error',
                summary: 'Error',
                detail: 'Problemas al conectarse al servidor. Inntentelo nuevamente', 
                life: 3000
          });

        })
      } 


}

</script>

<template>
    <div class="grid">
      <div class="col-12">
        <div class="card">
          <Toast />
          <form>
          <Toolbar class="mb-4">
            <template v-slot:start>
              <div class="my-2">
                <h3 class="text-left">Editar Tramite</h3> 
               
                    <div class="p-fluid formgrid grid">

                        <div class="field col-12 md:col-4">
                          <label for="fecha_nacimiento">Fecha de Inscripcion</label>
                          <Calendar id="fecha_nacimiento" v-model="fecha" 
                            :class="{ 'p-invalid': submitted && !fecha }" :required="true" />
                          <small class="p-invalid" v-if="submitted && !fecha">Debe ingresar una fecha </small>
                        </div>   
                        
                        <div class="field col-12 md:col-4">
                          <label for="abogados">Abogados</label>
                          <Dropdown 
                            id="abogados"
                            v-model="abogado" 
                            :options="abogados"                            
                            filter placeholder="Seleccione una opcion"
                            optionLabel="nombre_completo"                             
                          />
                        </div> 

                        <div class="field col-12 md:col-4">
                          <label for="abogados">Clientes</label>
                          <Dropdown 
                            id="abogados"
                            v-model="cliente" 
                            :options="clientes" 
                            filter placeholder="Seleccione una opcion"
                            optionLabel="nombre_completo"
                            :class="{ 'p-invalid': submitted && !cliente }" 
                            :required="true"                            
                          />
                          <small class="p-invalid" v-if="submitted && !cliente">Debe seleccionar un cliente </small>
                        </div>

                        <div class="field col-12 md:col-4">
                          <label for="juzgados">Juzgado</label>
                          <Dropdown 
                            id="juzgados"
                            v-model="juzgado" 
                            :options="juzgados" 
                            filter placeholder="Seleccione una opcion"
                            optionLabel="nombre"
                            :class="{ 'p-invalid': submitted && !juzgado }" 
                          />
                          <small class="p-invalid" v-if="submitted && !juzgado">Debe seleccionar una opcion </small>
                        </div>
                                 
                    </div>

                    <br>

                    <div class="p-fluid formgrid grid" >
                      <div class="field col-12 md:col-12">
                        <label for="address">Hechos ocurridos</label>
                        <Textarea id="address" rows="2"  v-model="hechosocurridos" 
                        :class="{ 'p-invalid': submitted && !hechosocurridos }" 
                        />
                        <small class="p-invalid" v-if="submitted && !hechosocurridos">Debe ingresar los hechos para iniciar el tramite </small>
                      </div>
                    </div>  
                
              </div>
            </template>
          </Toolbar>

          <Toolbar class="mb-4">
            <template v-slot:start>
              <div class="my-2">
                <h3 class="text-left">Detalles Especificos</h3> 
                
                    <div class="p-fluid formgrid grid">

                        <div class="field col-12 md:col-6">
                          <label for="tipotramite">Tipo de tramite</label> 
                          <Dropdown 
                            id="tipotramite"
                            v-model="tipotramite" 
                            :options="tipotramites" 
                            filter placeholder="Seleccione un tipo de tramite"
                            optionLabel="proceso"
                            @change="tipotramiteseleccionado"
                            :class="{ 'p-invalid': submitted && !tipotramite }" 
                          />
                          <small class="p-invalid" v-if="submitted && !tipotramite">Debe seleccionar el tipo de tramite al que corresponde el registro </small>
                        </div>

                        <div class="field col-12 md:col-6"></div>

                        <div class="field col-12 md:col-6">
                            <label for="quantity">Presupuesto Inicial</label>
                            <InputNumber id="quantity"  v-model="presupuesto" 
                             :required="true" integeronly readonly 
                              />                            
                        </div>

                        <div class="field col-12 md:col-6">
                            <label for="quantity">Descripcion del presupuesto (Opcional)</label>
                            <InputText id="asunto_presupuesto"  v-model="descripcionpresupuesto"    />                                                         
                        </div>
                        
                        <div class="field col-12 md:col-6">
                            <label for="quantity">Declaracion del Cliente</label>
                            <Textarea id="address" rows="4"  v-model="declaracioncliente" 
                              :class="{ 'p-invalid': submitted && !declaracioncliente }" :required="true" />
                            <small class="p-invalid" v-if="submitted && !declaracioncliente">Debe de ingresar la declaracion del cliente.</small>
                        </div>
                        <div class="field col-12 md:col-6">
                            <label for="quantity">Declaracion del Demandado (Opcional)</label>
                            <Textarea id="address" rows="4"  v-model="declaraciondemandado" />                            
                        </div>
                                           

                    </div>       
               
              </div>              
            </template>             
          </Toolbar>

          <Toolbar class="mb-4">
            <template v-slot:start>
              <div class="my-2">
                <h3 class="text-left">Pretensiones</h3>  
              
                  <div class="p-fluid formgrid grid">

                    <div class="field col-12 md:col-6">
                          <label for="tipretencion">Tipo de Pretencion</label>
                          <Dropdown 
                            id="tipretencion"
                            v-model="tipopretencion" 
                            :options="tipopretenciones" 
                            filter placeholder="Seleccione una opcion"
                            optionLabel="pretencion"
                            :class="{ 'p-invalid': submitted && !tipopretencion }" :required="true"
                            />
                            <small class="p-invalid" v-if="submitted && !tipopretencion">Debe de seleccionar el tipo de pretencion del cliente. </small>
                    </div>

                    <div class="field col-12 md:col-6">
                        <label for="quantity">Otro (Opcional) </label>
                        <InputText  v-model="pretencion_otro"     />                                                     
                    </div>

                    <div class="field col-12 md:col-12">
                        <label for="quantity">Valor medida </label>
                        <InputText id="asunto_presupuesto"   v-model="valormedida" 
                        :class="{ 'p-invalid': submitted && !valormedida }" :required="true"
                        />                             
                        <small class="p-invalid" v-if="submitted && !valormedida">Debe de ingresar el valor representativo para iniciar el tramite </small>
                    </div>


                    <div class="field col-12 md:col-6">
                            <label for="quantity">Pretencion del Cliente</label>
                            <Textarea id="address" rows="4" v-model="pretencioncliente"
                            :class="{ 'p-invalid': submitted && !pretencioncliente }" :required="true"
                            />
                            <small class="p-invalid" v-if="submitted && !pretencioncliente">Debe de ingresar la pretencion del cliente </small>
                        </div>

                    <div class="field col-12 md:col-6">
                        <label for="quantity">Pretencion del Demandado (Opcional)</label>
                        <Textarea id="address" rows="4" v-model="pretenciondemandado" />                        
                    </div>

                    <div class="field col-12 md:col-4">
                        <Button label="Cancelar" class="p-button-secondary col-12   md:col-4 p-3" @click="volver" /> 
                    </div>
                    <div class="field col-12 md:col-4">
                        <Button label="Guardar" class="p-button-primary col-12  md:col-4 p-3" @click="guardar"  /> 
                    </div>    

                  </div>              

               
                </div>
            </template>
          </Toolbar>
          </form>
        </div>
      </div>
    </div>
</template>

<style scoped lang="scss">

@import '@/assets/demo/styles/badges.scss';

</style>
