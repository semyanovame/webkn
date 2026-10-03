<template>

</template>

<script setup lang="ts">
    import { computed, ref } from 'vue';
    import ProductCard from '@/components/ProductCard.vue';
    import type { Product } from '@/types'; 
    interface Props{
        products: Product[],
        cartIds: number[],
    }
    const props = withDefaults(defineProps<Props>(),{ // если никто не лежит в корзине будет пустой массив
        cartIds: () => [],
    })
    const emit = defineEmits<{
        add: [product: Product];
        remove: [id: number];
    }>();
    const search = ref('');
    const sortOrder = ref<'default' | 'asc' | 'desc'>('default');
</script>

<style scoped>

</style>