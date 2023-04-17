<script setup>
import { FilterMatchMode } from 'primevue/api';
import { ref, onMounted, onBeforeMount } from 'vue';
import ProductService from '@/service/ProductService';
import { useToast } from 'primevue/usetoast';
import { useLayout } from '@/layout/composables/layout';
// 776fpl
const toast = useToast();
const { contextPath } = useLayout();
const errorvalidacion=ref([]);
const products = ref(null);
const productDialog = ref(false);
const deleteProductDialog = ref(false);
const deleteProductsDialog = ref(false);
const product = ref({});
const nombreForm = ref('Nuevo Abogado')
const selectedProducts = ref(null);
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
    product.value = {};
    nombreForm.value = "Nuevo Abogado";
    submitted.value = false;
    productDialog.value = true;
};

const hideDialog = () => {
    productDialog.value = false;
    submitted.value = false;
    errorvalidacion.value=[]
    product.value = {};
};

const cargarAbogados=()=>{
    fetch('http://localhost:80/apiEstudiojuridico/public/api/abogados/retornarabogados',{
        method:'POST',                
        headers: {
        "Content-Type": "application/json",                               
        },
    })
    .then((response) => {
        return response.json();
    })
    .then(res=>{
        products.value = res.abogados
    })
    .catch((e)=>{
        console.log(e)
    })
}

const saveProduct = () => {
    submitted.value = true;
    if ( product.value.nombre && product.value.nombre.trim() && product.value.carnet && product.value.celular &&
        (product.value.appaterno || product.value.apmaterno) && product.value.direccion && product.value.fechanac &&
        product.value.genero && product.value.estadocivil && product.value.codigo ) 
        {
            let fecha =new Date(product.value.fechanac);
            let fecha_nac=fecha.getFullYear()+'-'+(fecha.getMonth()+1)+'-'+fecha.getDate();
            if (product.value.id) {
                fetch('http://localhost:81/apiEstudiojuridico/public/api/abogados/actualizar',{
                    method:'POST',
                    body:JSON.stringify({
                    'nombre':product.value.nombre,
                    'apellido1':product.value.appaterno,
                    'apellido2':product.value.apmaterno,
                    'cedula':product.value.carnet,
                    'celular':product.value.celular,
                    'direccion':product.value.direccion,
                    'fechaNacimiento':fecha_nac,
                    'genero': product.value.genero.value,
                    'estadocivil': product.value.estadocivil.value,
                    'tipopersona':null,
                    'codigo':product.value.codigo,
                    'id':product.value.id,
                    'idpersona':product.value.idpersona
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
                            toast.add({ severity: 'success', summary: 'Successful', detail: 'Abogado actualizado correctamente ..!', life: 3000 });
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
                 
            } else {
                fetch('http://localhost:81/apiEstudiojuridico/public/api/abogados/agregar',{
                    method:'POST',
                    body:JSON.stringify({
                        'nombre':product.value.nombre,
                        'apellido1':product.value.appaterno,
                        'apellido2':product.value.apmaterno,
                        'cedula':product.value.carnet,
                        'celular':product.value.celular,
                        'direccion':product.value.direccion,
                        'fechaNacimiento':fecha_nac,
                        'genero': product.value.genero.value,
                        'estadocivil': product.value.estadocivil.value,
                        'tipopersona':null,
                        'codigo':product.value.codigo
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
                            toast.add({ severity: 'success', summary: 'Successful', detail: 'Abogado registrado correctamente ..!', life: 3000 });
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
    product.value = { ...editProduct };
    nombreForm.value ="Actualizar Abogado"
    console.log(product);
    productDialog.value = true;
};

const confirmDeleteProduct = (editProduct) => {
    product.value = editProduct;
    deleteProductDialog.value = true;
};

const deleteProduct = () => {
    fetch('http://localhost:81/apiEstudiojuridico/public/api/abogados/eliminar',{
        method:'POST',
        body:JSON.stringify({
            'idAbogado':product.value.id,
            'idPersona':product.value.idpersona
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
            toast.add({ severity: 'success', summary: 'Successful', detail: 'Abogado eliminado correctamente', life: 3000 });
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
                            <Button label="Agregar nuevo" icon="pi pi-plus" class="p-button-success mr-2" 
                            @click="openNew" />
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
                            <h5 class="m-0">Gestion de Abogados</h5>
                            <span class="block mt-2 md:mt-0 p-input-icon-left">
                                <i class="pi pi-search" />
                                <InputText v-model="filters['global'].value" placeholder="Buscar por cualquier palabra" />
                            </span>
                        </div>
                    </template>
                    
                    <Column field="nombre" header="Nombre completo" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Nombre completo</span>
                            Lic. {{ slotProps.data.nombre }} {{ slotProps.data.appaterno }} {{ slotProps.data.apmaterno }}
                        </template>
                    </Column>                    
                    <Column field="carnet" header="Carnet" :sortable="true" headerStyle="width:14%; min-width:8rem;">
                        <template #body="slotProps">
                            <span class="p-column-title">Carnet</span>
                            {{ slotProps.data.carnet }}
                        </template>
                    </Column>
                    <Column field="celular" header="Celular/Telefono" :sortable="true" headerStyle="width:14%; min-width:8rem;">
                        <template #body="slotProps">
                            <span class="p-column-title">Celular/Telefono</span>
                            {{ slotProps.data.celular }}
                        </template>
                    </Column>
                    <Column field="estadocivil" header="Estado civil" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Estado civil</span>
                            {{ slotProps.data.estadocivil }}
                        </template>
                    </Column>    
                    <Column field="fechanac" header="Fecha Nacimiento" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                        <template #body="slotProps"> 
                            <span class="p-column-title">Fecha nacimiento</span>
                            {{ slotProps.data.fechanac }}
                        </template>
                    </Column>                       
                    <Column field="estado" header="Codigo de Sistema" :sortable="true" headerStyle="width:14%; min-width:10rem;">
                        <template #body="slotProps">
                            <span class="p-column-title">Codigo en el sistema</span>
                            <span :class="'product-badge status-INSTOCK'">{{ slotProps.data.codigo}}</span>
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
                        <InputText id="name" v-model.trim="product.nombre" :required="true" autofocus :class="{ 'p-invalid': submitted && !product.nombre }" />
                        <small class="p-invalid" v-if="submitted && !product.nombre">Debe de ingresar un nombre especifico.</small>
                    </div>
                    <div class="formgrid grid">
                        <div class="field col">
                            <label for="price">Ap. Paterno</label>
                            <InputText id="price" v-model="product.appaterno"   :required="true" />                            
                        </div>
                        <div class="field col">
                            <label for="quantity">Ap. Materno</label>
                            <InputText id="quantity" v-model="product.apmaterno" :required="true" integeronly   />
                        </div>
                    </div>
                    <div class="formgrid grid">
                        <div class="field col">
                            <label for="price">C.I.</label>
                            <InputNumber id="price" v-model="product.carnet"   :class="{ 'p-invalid': submitted && !product.carnet }" :required="true" />
                            <small class="p-invalid" v-if="submitted && !product.carnet">Debe de ingresar el C.I. de la persona.</small>
                        </div>
                        <div class="field col">
                            <label for="quantity">Telefono/Celular</label>
                            <InputNumber id="quantity" v-model="product.celular" :class="{ 'p-invalid': submitted && !product.celular }"  :required="true" integeronly />
                            <small class="p-invalid" v-if="submitted && !product.celular">Debe de ingresar el número de telefono de la persona.</small>
                        </div>
                    </div>

                    <div class="field">
                        <label for="description">Direccion </label>
                        <InputText id="description" v-model="product.direccion" :class="{ 'p-invalid': submitted && !product.direccion }" :required="true" rows="3" cols="20" />
                        <small class="p-invalid" v-if="submitted && !product.direccion">Debe de ingresar la direccion del domicilio de la persona.</small>
                    </div>

                    <div class="field">
                        <label for="description">Fecha Nacimiento</label>
                        <Calendar :showIcon="true" :showButtonBar="true" v-model="product.fechanac" :class="{ 'p-invalid': submitted && !product.fechanac }" :required="true"></Calendar>
                        <small class="p-invalid" v-if="submitted && !product.fechanac">La fecha de nacimiento no puede ser vacia.</small>
                    </div>                   

                    <div class="formgrid grid">
                        <div class="field col">
                            <label for="price">Genero</label>
                            <Dropdown id="inventoryStatus" v-model="product.genero" :options="generos" :class="{ 'p-invalid': submitted && !product.genero }" :required="true" optionLabel="label" placeholder="Elige una opcion">
                            <template #value="slotProps">
                                <div v-if="slotProps.value && slotProps.value.value">
                                    <span :class="'product-badge status-' + slotProps.value.value">{{ slotProps.value.label }}</span>
                                </div>
                                <div v-else-if="slotProps.value && !slotProps.value.value">
                                    <span :class="'product-badge status-' + slotProps.value">{{ slotProps.value }}</span>
                                </div>
                                <span v-else>
                                    {{ slotProps.placeholder }}
                                </span>                                
                            </template>
                            </Dropdown>
                            <small class="p-invalid" v-if="submitted && !product.genero">Debe seleccionar una de las opciones disponibles.</small>
                        </div>  
                        <div class="field col">
                            <label for="quantity">Estado Civil</label>
                            <Dropdown id="inventoryStatus" v-model="product.estadocivil" :options="estadosciviles" :class="{ 'p-invalid': submitted && !product.estadocivil }" :required="true"  optionLabel="label" placeholder="Elige una opcion">
                            <template #value="slotProps">
                                <div v-if="slotProps.value && slotProps.value.value">
                                    <span :class="'product-badge status-' + slotProps.value.value">{{ slotProps.value.label }}</span>
                                </div>
                                <div v-else-if="slotProps.value && !slotProps.value.value">
                                    <span :class="'product-badge status-' + slotProps.value">{{ slotProps.value }}</span>
                                </div>
                                <span v-else>
                                    {{ slotProps.placeholder }}
                                </span>
                            </template>
                        </Dropdown>
                        <small class="p-invalid" v-if="submitted && !product.estadocivil">Debe seleccionar una de las opciones disponibles.</small>
                        </div> 
                    </div>        
                    <div class="field">
                        <label class="mb-3">Codigo en el sistema</label>                        
                        <InputText id="description" v-model="product.codigo" :class="{ 'p-invalid': submitted && !product.codigo }" :required="true" rows="3" cols="20" />              
                        <small class="p-invalid" v-if="submitted && !product.codigo">Ingrese un codigo de ingreso para  el sistema.</small>
                    </div> 
                    <div>
                        <ul>
                            <li   v-for="(error , i) in errorvalidacion" v-bind:key="i" class="text-red-500">{{ error }}</li>
                        </ul>
                    </div>
                    <template #footer>
                        <Button label="Cancel" icon="pi pi-times" class="p-button-text bg-info" @click="hideDialog" />
                        <Button label="Save" icon="pi pi-check" class="p-button-text" @click="saveProduct" />
                    </template>
                </Dialog>

                <Dialog v-model:visible="deleteProductDialog" :style="{ width: '450px' }" header="Confirm" :modal="true">
                    <div class="flex align-items-center justify-content-center">
                        <i class="pi pi-exclamation-triangle mr-3" style="font-size: 2rem" />
                        <span v-if="product"
                            >Are you sure you want to delete <b>{{ product.nombre }}</b
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
