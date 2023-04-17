<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue';
import { useLayout } from '@/layout/composables/layout';
import { useRouter } from 'vue-router';
import { useToast } from 'primevue/usetoast';
import { useConfirm } from 'primevue/useconfirm';
const { layoutConfig, onMenuToggle, contextPath } = useLayout();
const menu = ref(null);
const outsideClickListener = ref(null);
const topbarMenuActive = ref(false);
const router = useRouter();

const display = ref(false);
const displayConfirmation = ref(false);

const toast = useToast();
const confirmPopup = useConfirm();

onMounted(() => {
    bindOutsideClickListener();
});

onBeforeUnmount(() => {
    unbindOutsideClickListener();
});

const logoUrl = computed(() => {
    return `${contextPath}layout/images/${layoutConfig.darkTheme.value ? 'logo-white' : 'logo-dark'}.svg`;
});

const onTopBarMenuButton = () => {
    topbarMenuActive.value = !topbarMenuActive.value;
};
const onSettingsClick = () => {
    topbarMenuActive.value = false;
    router.push('/documentation');
};
const topbarMenuClasses = computed(() => {
    return {
        'layout-topbar-menu-mobile-active': topbarMenuActive.value
    };
});

const bindOutsideClickListener = () => {
    if (!outsideClickListener.value) {
        outsideClickListener.value = (event) => {
            if (isOutsideClicked(event)) {
                topbarMenuActive.value = false;
            }
        };
        document.addEventListener('click', outsideClickListener.value);
    }
};
const unbindOutsideClickListener = () => {
    if (outsideClickListener.value) {
        document.removeEventListener('click', outsideClickListener);
        outsideClickListener.value = null;
    }
};
const isOutsideClicked = (event) => {
    if (!topbarMenuActive.value) return;

    const sidebarEl = document.querySelector('.layout-topbar-menu');
    const topbarEl = document.querySelector('.layout-topbar-menu-button');

    return !(sidebarEl.isSameNode(event.target) || sidebarEl.contains(event.target) || topbarEl.isSameNode(event.target) || topbarEl.contains(event.target));
};

const toggleMenu = () => {
    displayConfirmation.value = true;
};

const closeConfirmation = () => {
    displayConfirmation.value = false;
};

const salir=()=>{    
    router.replace('/auth/login')
}
</script>

<template>
    <div class="layout-topbar">
        <router-link to="/" class="layout-topbar-logo">
            <img src="/demo/images/login/estudiojrm.png" alt="logo" />
            <span>Estudio Juridico JRM</span>
        </router-link>

        <button class="p-link layout-menu-button layout-topbar-button" @click="onMenuToggle()">
            <i class="pi pi-bars"></i>
        </button>

        <button class="p-link layout-topbar-menu-button layout-topbar-button" @click="onTopBarMenuButton()">
            <i class="pi pi-ellipsis-v"></i>
        </button>

        <div class="layout-topbar-menu" :class="topbarMenuClasses">
            <button @click="onTopBarMenuButton()" class="p-link layout-topbar-button">
                <i class="pi pi-calendar"></i>
                <span>Calendar</span>
            </button>
             
            <button @click="toggleMenu"    class="p-link layout-topbar-button" title="Cerrar Sesion" >
                <i class="pi pi-user-minus"></i>
            </button>                      
        </div>

        <Dialog header="SISTEMA JURIDICO  JRM " v-model:visible="displayConfirmation" :style="{ width: '350px' }" :modal="true">
            <div class="flex align-items-center justify-content-center">
                <i class="pi pi-exclamation-triangle mr-3" style="font-size: 2rem" />
                <span>Esta seguro de terminar la sesion?</span>
            </div>
            <template #footer>
                <Button label="Cancelar" icon="pi pi-times" @click="closeConfirmation" class="p-button-text" />
                <Button label="SI, cerrar sesion" icon="pi pi-check" @click="salir" class="p-button-text" autofocus />
            </template>
        </Dialog>

    </div>
</template>

<style lang="scss" scoped></style>
