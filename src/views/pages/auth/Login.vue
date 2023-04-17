<script setup>
import { useLayout } from '@/layout/composables/layout';
import { ref, computed,onMounted } from 'vue';
import AppConfig from '@/layout/AppConfig.vue';
import { useRouter } from 'vue-router';
import { useToast } from 'primevue/usetoast';
const toast = useToast();
//776fpl
const { layoutConfig, contextPath } = useLayout();
const email = ref('');
const password = ref('');
const checked = ref(false);
const router = useRouter();
const submitted = ref(false);
const errorvalidacion=ref([]);
const error = ref(false)

const logoUrl = computed(() => {
    return `${contextPath}layout/images/${layoutConfig.darkTheme.value ? 'logo-white' : 'logo-dark'}.svg`;
});

const verificarCredencial=()=>{
    submitted.value = true;
    error.value=false;
    if(email.value && email.value.length>5){
        fetch('http://localhost:81/apiEstudiojuridico/public/api/login',{
            method:'POST',
            body:JSON.stringify({
                'codigo':email.value                
            }),
            headers: {
            "Content-Type": "application/json",                               
            },
        })
        .then((response) => {
            return response.json();
        })
        .then((res)=>{
            console.log(res)
            if(res.status == 422){
                    toast.add({ severity: 'warn', summary: 'Error Inesperado', detail: 'Los campos no cumplen con las requisitos para ser registrados. Intente nuevamente', life: 3000 });
                    errorvalidacion.value=res.errors;                   
            }else{
                if(res.success){
                    toast.add({ severity: 'success', summary: 'Informacion', detail: 'Bienvenido al Sistema', life: 3000 });
                    router.push('/');
                }else{
                    error.value=true;
                    toast.add({ severity: 'warn', summary: 'Error Inesperado', detail: 'Hubo problemas al realizar los cambios. Intentelo nuevamente', life: 3000 });
                }
            }        
        })
        .catch((ee)=>{
            error.value=true;
        })
        
    }else{
        error.value=true
    }
    
    
}

</script>

<template>
    <div class="surface-ground flex align-items-center justify-content-center min-h-screen min-w-screen overflow-hidden">
        <div class="flex flex-column align-items-center justify-content-center">
           
            <div style="border-radius: 56px; padding: 0.3rem; background: linear-gradient(180deg, var(--primary-color) 10%, rgba(33, 150, 243, 0) 30%)">
                <div class="w-full surface-card py-8 px-5 sm:px-8" style="border-radius: 53px">
                    <div class="text-center mb-5">
                        <img src="/demo/images/login/estudiojrm.png" alt="Image" height="50" class="mb-3" />
                        <div class="text-900 text-3xl font-medium mb-3">ESTUDIO JURIDICO "JRM"    </div>
                        <span class="text-900 font-medium text-2xl">Iniciar sesion</span>
                    </div>
                    <div  class="field">
                        <label for="email1" class="block text-900 text-xl font-medium mb-2">Codigo de Usuario</label>
                        <InputText id="email1" type="text" :required="true" autofocus :class="{ 'p-invalid': submitted && !email }" placeholder="Ingresa el codigo de usuario" class="w-full md:w-30rem mb-5" style="padding: 1rem" v-model="email" />
                        <br>
                        <strong class="p-invalid text-orange-500" v-if="submitted && !email  ">El codigo de sesion debe ser mayor a 6 caracteres, intentelo nuevamente.</strong>
                        <br><strong class="p-invalid text-orange-500" v-if="error">El codigo ingresado es incorrecto. Intentelo nuevamente con un codigo valido..!</strong>
                        <div>
                            <ul>
                                <li   v-for="(error , i) in errorvalidacion" v-bind:key="i" class="text-red-500">{{ error }}</li>
                            </ul>
                        </div>

                    </div>
                    <Button label="Ingresar" class="w-full p-3 text-xl p-button-info"  @click="verificarCredencial()"  ></Button>
                </div>
            </div>
        </div>
    </div>
    <AppConfig simple />
</template>

<style scoped>
.pi-eye {
    transform: scale(1.6);
    margin-right: 1rem;
}

.pi-eye-slash {
    transform: scale(1.6);
    margin-right: 1rem;
}
</style>
