<script setup>
import { ref } from 'vue';

import AppMenuItem from './AppMenuItem.vue';

const model = ref([
  
    {
        label: 'Administracion',
        items: [
            { label: 'Gestion de clientes', icon: 'pi pi-fw pi-users', to: '/estudio/clientes' },
            { label: 'Gestion de Abogados', icon: 'pi pi-fw pi-user', to: '/estudio/abogados' },                 
            { label: 'Tipos de tramites', icon: 'pi pi-fw pi-align-justify', to: '/estudio/tipotramites' },   
        ]
    },   
    {
        label: 'Procesos',
        items: [
            {
                label: 'Gestion de Procesos / Tramites',
                icon: 'pi pi-fw pi-bookmark',
                items: [                    
                        { label: 'Iniciar Tramite', icon: 'pi pi-fw pi-book',to:'/estudio/tramites/mis_tramites' },                           
                ]
            },
            { label: 'Gestion de Juzgados', icon: 'pi pi-fw pi-home',  },
            
        ]
    }, 
    
]);
</script>

<template>
    <ul class="layout-menu">
        <template v-for="(item, i) in model" :key="item">
            <app-menu-item v-if="!item.separator" :item="item" :index="i"></app-menu-item>
            <li v-if="item.separator" class="menu-separator"></li>
        </template>
        
    </ul>
</template>

<style lang="scss" scoped></style>
