<template>
<div class = "card">
    <div class="card__image">
        {{ product.image }}
    </div>
    <h3 class="card__title">
        {{ product.name }}
    </h3>
    <p class="card__category">
        {{ product.category }}
    </p>
    <p class="card__price">
        {{ product.price }} ₽
    </p>
    <button v-if="!isInCart"
        class="btn btn--add"
        @click="emit('add', product)">
        Добавить в корзину
    </button>
    <button v-else
        class="btn btn--remove"
        click="emit('remove', product.id)">
        Удалить из корзины
    </button>
</div>
</template>

<script setup lang="ts">
    import type { Product } from '@/types'; //т.е. здесь мы берем тот шаблон продукта
    interface Props{         //  описание того что компонент принимает снаружи
        product: Product, //сам продукт
        isInCart?: boolean, //дополнительное булево значение
    }
    const props = defineProps<Props>();    // здесь хранятся данные от родителя
    const emit = defineEmits<{ // события сообщения "родителю" 
        add: [product: Product]; // событие добавить продукт
        remove: [id: number]; // убрать его по айди
    }>();
    
</script>

<style scoped>
.card {
  background: #fff;
  border: 1px solid #eee;
  border-radius: 12px;
  padding: 16px;
  display: flex;
  flex-direction: column;
  gap: 8px;
  transition: box-shadow 0.2s;
}

.card:hover {
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.08);
}

.card__image {
  font-size: 48px;
  text-align: center;
}

.card__title {
  font-size: 16px;
  margin: 0;
}

.card__category {
  color: #888;
  font-size: 13px;
  margin: 0;
}

.card__price {
  font-weight: 600;
  font-size: 18px;
  margin: 4px 0 12px;
}

.btn {
  padding: 8px 14px;
  border-radius: 8px;
  border: none;
  cursor: pointer;
  font-weight: 600;
}

.btn--add {
  background: #42b883;
  color: white;
}

.btn--add:hover {
  background: #369e70;
}

.btn--remove {
  background: #fee;
  color: #c33;
}

.btn--remove:hover {
  background: #fdd;
}
</style>