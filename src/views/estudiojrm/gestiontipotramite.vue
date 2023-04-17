<script setup>
import { FilterMatchMode } from 'primevue/api';
import { ref, onMounted, onBeforeMount } from 'vue';
import ProductService from '@/service/ProductService';
import { useToast } from 'primevue/usetoast';
import { useLayout } from '@/layout/composables/layout';

const toast = useToast();
const { contextPath } = useLayout();

const products = ref(null);
const productDialog = ref(false);
const deleteProductDialog = ref(false);
const deleteProductsDialog = ref(false);
const product = ref({});
const nombreForm = ref('Nuevo Tipo Tramite')
const selectedProducts = ref(null);
const errorvalidacion=ref([]);
const dt = ref(null);
const filters = ref({});
const submitted = ref(false);
const estadosciviles = ref([
{ label: 'CASADO', value: '1' },
{ label: 'SOLTERO', value: '2' },
{ label: 'DIVORCIADO', value: '3' },
{ label: 'VIUDO', value: '4' },
])
const generos = ref([
{ label: 'HOMBRE', value: '1' },
{ label: 'MUJER', value: '0' },
])

const productService = new ProductService();

onBeforeMount(() => {
    initFilters();
});
onMounted(() => {
    cargarAbogados();
});
const formatCurrency = (value) => {
    return value.toLocaleString('en-US', { style: 'currency', currency: 'USD' });
};

const openNew = () => {
    nombreForm.value="Nuevo Tipo Tramite"
    product.value = {};
    submitted.value = false;
    productDialog.value = true;
};

const hideDialog = () => {
    productDialog.value = false;
    errorvalidacion.value=[]
    submitted.value = false;
    product.value = {};
};

const cargarAbogados=()=>{
    fetch('http://localhost:80/apiEstudiojuridico/public/api/tipotramites/retornartipotramite',{
        method:'POST',                
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{
        products.value = res.tipotramites
    })
    .catch((e)=>{
        console.log(e)
    })
}


const saveProduct = () => {
    submitted.value = true;
    if ( product.value.proceso && product.value.proceso.trim()  && product.value.descripcion ) 
        {           
            if (product.value.id) {
                fetch('http://localhost:81/apiEstudiojuridico/public/api/tipotramite/actualizar',{
                    method:'POST',
                    body:JSON.stringify({
                        'nombre':product.value.proceso,
                        'descripcion':product.value.descripcion,      
                        'id':product.value.id
                    }),
                    headers: {
                    "Content-Type": "application/json",                               
                    },
                })
                .then((response) => {
                    return response.json();
                })
                .then(res=>{
                    if(res.status == 422){
                        toast.add({ severity: 'warn', summary: 'Error Inesperado', detail: 'Los campos no cumplen con las requisitos para ser registrados. Intente nuevamente', life: 3000 });
                        errorvalidacion.value=res.errors;                   
                    }else{
                        if(res.success){
                            productDialog.value = false;
                            cargarAbogados();
                            toast.add({ severity: 'success', summary: 'Successful', detail: 'Tipo de tramite actualizado correctamente ..!', life: 3000 });
                            
                            product.value = {};
                            errorvalidacion.value=[];    
                        }else{
                            toast.add({ severity: 'warn', summary: 'Error Inesperado', detail: 'Hubo problemas al realizar los cambios. Intentelo nuevamente', life: 3000 });
                            console.log(res)
                        }    
                    }
                    
                })
                .catch((e)=>{
                    toast.add({ severity: 'error', summary: 'Error', detail: 'Problemas al acceder al servidor. Compruebe los datos e intentelo nuevamente', life: 3000 });
                    console.log(e)
                })    
                 
            } else {
                fetch('http://localhost:81/apiEstudiojuridico/public/api/tipotramite/agregar',{
                    method:'POST',
                    body:JSON.stringify({
                        'nombre':product.value.proceso,
                        'descripcion':product.value.descripcion,                                              
                    }),
                    headers: {
                    "Content-Type": "application/json",                               
                    },
                })
                .then((response) => {
                    return response.json();
                })
                .then(res=>{
                    if(res.status == 422){
                        toast.add({ severity: 'warn', summary: 'Error Inesperado', detail: 'Los campos no cumplen con las requisitos para ser registrados. Intente nuevamente', life: 3000 });
                        errorvalidacion.value=res.errors;                   
                    }else{
                        if(res.success){
                            cargarAbogados();
                            toast.add({ severity: 'success', summary: 'Successful', detail: 'Tipo tramite registrado correctamente ..!', life: 3000 });
                            productDialog.value = false;
                            product.value = {};
                            errorvalidacion.value=[];
                        }else{
                            toast.add({ severity: 'warn', summary: 'Error Inesperado', detail: 'Hubo problemas al realizar los cambios. Intentelo nuevamente', life: 3000 });
                            console.log(res)
                        }
                    }
                    
                })
                .catch((e)=>{
                    toast.add({ severity: 'error', summary: 'Error', detail: 'Problemas al acceder al servidor. Compruebe los datos e intentelo nuevamente', life: 3000 });
                    console.log(e)
                })                
            }             
    }
};

const editProduct = (editProduct) => {
    nombreForm.value="Actualizar Tipo Tramite"
    product.value = { ...editProduct };
    console.log(product);
    productDialog.value = true;
};

const confirmDeleteProduct = (editProduct) => {
    product.value = editProduct;
    deleteProductDialog.value = true;
};

const deleteProduct = () => {
    fetch('http://localhost:81/apiEstudiojuridico/public/api/tipotramite/eliminar',{
        method:'POST',
        body:JSON.stringify({
            'id':product.value.id,            
        }),
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{
        if(res.success){
            cargarAbogados();
            toast.add({ severity: 'success', summary: 'Successful', detail: 'Tipo tramite eliminado correctamente', life: 3000 });
        }else{
            console.log(res)
            toast.add({ severity: 'warn', summary: 'Error Inesperado', detail: 'Problemas al acceder al servidor. Compruebe los datos e intentelo nuevamente', life: 3000 });
 
        }
    })
    .catch((e)=>{
        console.log(e)
        toast.add({ severity: 'error', summary: 'Error', detail: 'Problemas al acceder al servidor. Compruebe los datos e intentelo nuevamente', life: 3000 });

    })
    deleteProductDialog.value = false;
};

const findIndexById = (id) => {
    let index = -1;
    for (let i = 0; i < products.value.length; i++) {
        if (products.value[i].id === id) {
            index = i;
            break;
        }
    }
    return index;
};

const createId = () => {
    let id = '';
    const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
    for (let i = 0; i < 5; i++) {
        id += chars.charAt(Math.floor(Math.random() * chars.length));
    }
    return id;
};

const exportCSV = () => {
    dt.value.exportCSV();
};

const confirmDeleteSelected = () => {
    deleteProductsDialog.value = true;
};


const initFilters = () => {
    filters.value = {
        global: { value: null, matchMode: FilterMatchMode.CONTAINS }
    };
};
</script>

<template>
    <div class="grid">
        <div class="col-12">
            <div class="card">
                <Toast />
                <Toolbar class="mb-4">
                    <template v-slot:start>
                        <div class="my-2">
                            <Button label="Agregar nuevo" icon="pi pi-plus" class="p-button-success mr-2" @click="openNew" />
                            
                        </div>
                    </template>
                     
                </Toolbar>

                <DataTable
                    ref="dt"
                    :value="products"
                    v-model:selection="selectedProducts"
                    dataKey="id"
                    :paginator="true"
                    :rows="10"
                    :filters="filters"
                    paginatorTemplate="FirstPageLink PrevPageLink PageLinks NextPageLink LastPageLink CurrentPageReport RowsPerPageDropdown"
                    :rowsPerPageOptions="[5, 10, 25]"
                    currentPageReportTemplate="Showing {first} to {last} of {totalRecords} products"
                    responsiveLayout="scroll"
                >
                    <template #header>
                        <div class="flex flex-column md:flex-row md:justify-content-between md:align-items-center">
                            <h5 class="m-0">Gestion de tipo de tramites</h5>
                            <span class="block mt-2 md:mt-0 p-input-icon-left">
                                <i class="pi pi-search" />
                                <InputText v-model="filters['global'].value" placeholder="Buscar por cualquier palabra" />
                            </span>
                        </div>
                    </template>
                    
                    <Column field="proceso" header="Nombre  " :sortable="true" headerStyle="width:50%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Nombre  </span>
                             {{ slotProps.data.proceso }} 
                        </template>
                    </Column>                    
                                      
                    <Column field="descripcion" header="Descripcion" :sortable="true" headerStyle="width:50%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Decripcion</span>
                              {{ slotProps.data.descripcion }} 
                        </template>
                    </Column>  

                    <Column headerStyle="min-width:10rem;">
                        <template #body="slotProps">
                            <Button icon="pi pi-pencil" class="p-button-rounded p-button-warning mr-2" @click="editProduct(slotProps.data)" />
                            <Button icon="pi pi-trash" class="p-button-rounded p-button-danger mt-2" @click="confirmDeleteProduct(slotProps.data)" />
                        </template>
                    </Column>

                </DataTable>

                <Dialog v-model:visible="productDialog" :style="{ width: '450px' }" :header="nombreForm" :modal="true" class="p-fluid">
                    <div class="field">
                        <label for="name">Nombre</label>
                        <InputText id="name" v-model.trim="product.proceso" :required="true" autofocus :class="{ 'p-invalid': submitted && !product.proceso }" />
                        <small class="p-invalid" v-if="submitted && !product.proceso">Debe de ingresar el nombre del tramite.</small>
                    </div>                

                    <div class="field">
                        <label for="description">Descripcion </label>
                        <InputText id="description" v-model="product.descripcion" :class="{ 'p-invalid': submitted && !product.descripcion }" :required="true" rows="3" cols="20" />
                        <small class="p-invalid" v-if="submitted && !product.descripcion">Debe de ingresar la descripcion del tipo de tramite.</small>
                    </div>   
                    <div>
                        <ul>
                            <li   v-for="(error , i) in errorvalidacion" v-bind:key="i" class="text-red-500">{{ error }}</li>
                        </ul>
                    </div>                                 

                    <template #footer>
                        <Button label="Cancel" icon="pi pi-times" class="p-button-text" @click="hideDialog" />
                        <Button label="Save" icon="pi pi-check" class="p-button-text" @click="saveProduct" />
                    </template>
                </Dialog>

                <Dialog v-model:visible="deleteProductDialog" :style="{ width: '450px' }" header="Confirm" :modal="true">
                    <div class="flex align-items-center justify-content-center">
                        <i class="pi pi-exclamation-triangle mr-3" style="font-size: 2rem" />
                        <span v-if="product"
                            >Are you sure you want to delete <b>{{ product.proceso }}</b
                            >?</span
                        >
                    </div>
                    <template #footer>
                        <Button label="No" icon="pi pi-times" class="p-button-text" @click="deleteProductDialog = false" />
                        <Button label="Yes" icon="pi pi-check" class="p-button-text" @click="deleteProduct" />
                    </template>
                </Dialog>

               
            </div>
        </div>
    </div>
</template>

<style scoped lang="scss">
@import '@/assets/demo/styles/badges.scss';
</style>
