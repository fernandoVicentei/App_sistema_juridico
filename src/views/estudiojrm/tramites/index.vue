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
const ruta = ref('http://localhost:80/apiEstudiojuridico/public/api/')

const filters = ref({});
const tramite = ref(null)

onBeforeMount(() => {
    initFilters();
});

onMounted(() => {
   cargarTramites()
});

const cargarTramites=()=>{
    fetch( ruta.value+ 'tramite/listar',{
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

  router.push('/estudio/tramites/registrar_tramites');
  //router.back()
  //this.$router.push('/estudio/tramites/registrar_tramites')
};

const initFilters = () => {
    filters.value = {
        global: { value: null, matchMode: FilterMatchMode.CONTAINS }
    };
};

const editarTramite=(e)=>{

    ///tramite/buscar
    let id = e.id
    fetch( ruta.value+ 'tramite/buscar',{
        method:'POST',                
        body:JSON.stringify({
            idTramite:id
        }),
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{
        if(res.status == 200){
           tramite.value = res.tramite
          
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
    .finally((e)=>{
       // router.push({ path: '/estudio/tramites/editar_tramites', params: { tramite } });
        router.push({ path: '/estudio/tramites/editar_tramites', query: { tramite: JSON.stringify(tramite.value) } });
    })
     
    
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
                            <h3 class="text-right">Gestion de tramites</h3>
                            <Button label="Agregar nuevo" icon="pi pi-plus" class="p-button-success mr-2" @click="openNew" />
                        </div>
                    </template>                     
                </Toolbar>       

                <DataTable
                    ref="dt"
                    :value="tramites"
                    v-model:selection="selectedProducts"
                    dataKey="id"
                    :paginator="true"
                    :rows="10"
                    :filters="filters"
                    paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                    :rowsPerPageOptions="[5, 10, 25]"
                    currentPageReportTemplate="Mostrando {first} a {last} de {totalRecords} tramites"
                    responsiveLayout="scroll"
                >
                    <template #header>
                        <div class="flex flex-column md:flex-row md:justify-content-between md:align-items-center">                          
                            <span class="block mt-2 md:mt-0 p-input-icon-left">
                                <i class="pi pi-search" />                                
                                <InputText v-model="filters['global'].value" placeholder="Buscar por cualquier palabra" />
                            </span>
                        </div>
                    </template>
                    
                    <Column field="abogado" header="Abogado Encargado" :sortable="true" headerStyle="width:50%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Abogado Encargado </span>
                            <span v-if="slotProps.data.abogado == null">
                                Pendiente
                            </span>
                            <span  v-else>
                                {{ slotProps.data.abogado }}
                            </span> 
                              
                        </template>
                    </Column>                           
                    <Column field="cliente" header="Cliente" :sortable="true" headerStyle="width:50%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Cliente</span>
                            <span v-if="slotProps.data.cliente == null">
                                Pendiente
                            </span>
                            <span  v-else>
                                {{ slotProps.data.cliente }}
                            </span> 
                        </template>
                    </Column>  
                    <Column field="fecha" header="Fecha Iniciada" :sortable="true" headerStyle="width:50%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Fecha Iniciada </span>
                              {{ slotProps.data.fecha }} 
                        </template>
                    </Column>  
                    <Column field="hechosOcurridos" header="Descripcion" :sortable="true" headerStyle="width:50%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Decripcion</span>
                              {{ slotProps.data.hechosOcurridos }} 
                        </template>
                    </Column> 
                    <Column field="estado" header="Estado" :sortable="true" headerStyle="width:50%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Estado</span>
                            <span v-if="slotProps.data.estado==0" >
                                <span :class="'customer-badge status-unqualified'" >INACTIVO</span>
                            </span>
                            <span v-else>
                                <span :class="'customer-badge status-qualified'" >ACTIVO</span>
                            </span>
                           
                        </template>
                    </Column>  
                    
                    <Column headerStyle="min-width:10rem;">
                        <template #body="slotProps">
                            <Button icon="pi pi-pencil" class="p-button-rounded p-button-warning mr-2" @click="editarTramite(slotProps.data)" />
                            <Button icon="pi pi-trash" class="p-button-rounded p-button-danger mt-2" @click="confirmDeleteProduct(slotProps.data)" />
                        </template>
                    </Column>

                </DataTable>        

            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
@import '@/assets/demo/styles/badges.scss';
</style>
