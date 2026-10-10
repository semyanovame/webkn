<template>
  <div class="app">
    <h1>Магазин</h1>

    <!-- Поиск и сортировка -->
    <div class="controls">
      <input v-model="search" type="text" placeholder="Поиск..." />
      <select v-model="sortOrder">
        <option value="default">По умолчанию</option>
        <option value="asc">Цена: по возрастанию</option>
        <option value="desc">Цена: по убыванию</option>
      </select>
    </div>

    <!-- Каталог -->
    <div class="catalog">
      <div v-for="product in filteredProducts" :key="product.id" class="card">
        <div class="card__image">{{ product.image }}</div>
        <h3>{{ product.name }}</h3>
        <p class="card__category">{{ product.category }}</p>
        <p class="card__price">{{ product.price }} ₽</p>

        <button v-if="!isInCart(product.id)" @click="addToCart(product)">
          В корзину
        </button>
        <button v-else @click="removeFromCart(product.id)">
          Удалить
        </button>
      </div>
    </div>

    <!-- Корзина -->
    <div class="cart">
      <div class="cart__header">
        <h2>Корзина</h2>
        <button v-if="cart.length" @click="clearCart">Очистить</button>
      </div>

      <p v-if="!cart.length">Корзина пуста</p>

      <ul v-else class="cart__list">
        <li v-for="item in cart" :key="item.id" class="cart__item">
          <span>{{ item.image }}</span>
          <span class="cart__name">{{ item.name }}</span>
          <span>{{ priceWithDiscount(item.price) }} ₽</span>
          <button @click="removeFromCart(item.id)">−</button>
          <span>{{ item.amount }}</span>
          <button @click="addToCart(item)">+</button>
        </li>
      </ul>

      <p v-if="cart.length" class="cart__total">
        Итого: {{ totalPrice }} ₽
      </p>

      <div v-if="cart.length" class="cart__promo">
        <input v-model="promoCode" type="text" placeholder="Промокод" />
      </div>
      <p v-if="discount > 0" class="cart__promo-ok">Скидка 10% применена</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, watch } from 'vue';

interface Product {
  id: number;
  name: string;
  price: number;
  category: string;
  image: string;
}

interface CartItem extends Product {
  amount: number;
}

const products: Product[] = [
  { id: 1, name: 'Ноутбук Pro 14', price: 89990, category: 'Электроника', image: '💻' },
  { id: 2, name: 'Механическая клавиатура', price: 7500, category: 'Электроника', image: '⌨️' },
  { id: 3, name: 'Кружка разработчика(бесценно)', price: 99999999, category: 'Аксессуары', image: '☕' },
  { id: 4, name: 'Худи "Vue Master"', price: 3200, category: 'Одежда', image: '👕' },
  { id: 5, name: 'Мышь беспроводная', price: 2400, category: 'Электроника', image: '🖱️' },
  { id: 6, name: 'Стикерпак с логотипом', price: 150, category: 'Аксессуары', image: '🏷️' },
  { id: 7, name: 'Монитор 27"', price: 24990, category: 'Электроника', image: '🖥️' },
  { id: 8, name: 'Кепка "Frontend"', price: 1100, category: 'Одежда', image: '🧢' },
];


const cart = ref<CartItem[]>(JSON.parse(localStorage.getItem('cart') || '[]'));

watch(cart, () => {
  localStorage.setItem('cart', JSON.stringify(cart.value));
}, { deep: true });

const discount = ref<number>(Number(localStorage.getItem('discount')) || 0);

watch(discount, () => {
  localStorage.setItem('discount', String(discount.value));
});

const search = ref('');
const sortOrder = ref('default');
const promoCode = ref(localStorage.getItem('discount') === '10' ? 'WEB' : '');

watch(promoCode, (value) => {
  if (value.trim().toUpperCase() === 'WEB') {
    discount.value = 10;
  } else {
    discount.value = 0;
  }
});

function isInCart(id: number) {
  return cart.value.some(item => item.id === id);
}

const filteredProducts = computed(() => {
  let result = products;

  const query = search.value.trim().toLowerCase();
  if (query) {
    result = result.filter(p => p.name.toLowerCase().includes(query));
  }

  if (sortOrder.value === 'asc') {
    result = [...result].sort((a, b) => a.price - b.price);
  } else if (sortOrder.value === 'desc') {
    result = [...result].sort((a, b) => b.price - a.price);
  }

  return result;
});

function priceWithDiscount(price: number) {
  return Math.round(price * (1 - discount.value / 100));
}

const totalPrice = computed(() => {
  return cart.value.reduce(
    (sum, item) => sum + priceWithDiscount(item.price) * item.amount,
    0
  );
});

function addToCart(product: Product) {
  const found = cart.value.find(item => item.id === product.id);
  if (found) {
    found.amount++;
  } else {
    cart.value.push({ ...product, amount: 1 });
  }
}

function removeFromCart(id: number) {
  const found = cart.value.find(item => item.id === id);
  if (!found) return;
  if (found.amount > 1) {
    found.amount--;
  } else {
    cart.value = cart.value.filter(item => item.id !== id);
  }
}

function clearCart() {
  cart.value = [];
  discount.value = 0;
  promoCode.value = '';
}
</script>

<style scoped>
* { box-sizing: border-box; }

.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
  font-family: system-ui, sans-serif;
  min-height: 100vh;
  background: #fff5f8;
  color: #6b3a4e;
}

h1 {
  color: #d4788f;
  font-weight: 500;
  letter-spacing: 1px;
  margin-bottom: 24px;
}

.controls {
  display: flex;
  gap: 10px;
  margin-bottom: 24px;
}

.controls input,
.controls select {
  padding: 10px 14px;
  border: 1px solid #f5c6d3;
  border-radius: 20px;
  font-size: 14px;
  background: #fff;
  color: #6b3a4e;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
}

.controls input:focus,
.controls select:focus {
  border-color: #e8a2b6;
  box-shadow: 0 0 0 3px rgba(232, 162, 182, 0.2);
}

.controls input { flex: 1; }

.catalog {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 16px;
  margin-bottom: 32px;
}

.card {
  background: #fff;
  border: 1px solid #fce4ec;
  border-radius: 16px;
  padding: 16px;
  transition: box-shadow 0.2s, transform 0.2s;
}

.card:hover {
  box-shadow: 0 8px 24px rgba(232, 162, 182, 0.15);
  transform: translateY(-2px);
}

.card__image { font-size: 40px; text-align: center; }
.card h3 { margin: 10px 0 6px; font-size: 15px; color: #6b3a4e; font-weight: 600; }
.card__category { color: #c98aa0; font-size: 12px; margin: 4px 0; text-transform: uppercase; letter-spacing: 0.5px; }
.card__price { font-weight: 700; font-size: 17px; margin: 10px 0; color: #d4788f; }

.card button {
  width: 100%;
  padding: 9px;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  background: #f8b8cc;
  color: #fff;
  font-weight: 600;
  font-size: 14px;
  transition: background 0.2s, transform 0.1s;
}

.card button:hover {
  background: #f39cb5;
}

.card button:active {
  transform: scale(0.97);
}

.cart {
  background: #fff;
  border: 1px solid #fce4ec;
  border-radius: 16px;
  padding: 20px;
  box-shadow: 0 4px 16px rgba(232, 162, 182, 0.08);
}

.cart__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 14px;
  padding-bottom: 10px;
  border-bottom: 1px solid #fce4ec;
}

.cart__header h2 {
  margin: 0;
  font-size: 18px;
  color: #d4788f;
  font-weight: 600;
}

.cart__header button {
  background: transparent;
  border: 1px solid #f5c6d3;
  color: #d4788f;
  padding: 5px 14px;
  border-radius: 20px;
  cursor: pointer;
  font-size: 13px;
  transition: background 0.2s;
}

.cart__header button:hover {
  background: #fff0f4;
}

.cart__list {
  list-style: none;
  padding: 0;
  margin: 0;
}

.cart__item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px 0;
  border-bottom: 1px dashed #fce4ec;
  font-size: 14px;
}

.cart__item:last-child {
  border-bottom: none;
}

.cart__name {
  flex: 1;
  color: #6b3a4e;
}

.cart__item button {
  width: 28px;
  height: 28px;
  border: 1px solid #f5c6d3;
  background: #fff;
  color: #d4788f;
  border-radius: 50%;
  cursor: pointer;
  font-weight: 700;
  transition: background 0.2s;
}

.cart__item button:hover {
  background: #fff0f4;
}

.cart__total {
  font-size: 20px;
  font-weight: 700;
  margin-top: 16px;
  padding-top: 12px;
  border-top: 2px solid #fce4ec;
  color: #d4788f;
  text-align: right;
}

.cart__promo {
  margin-top: 14px;
}

.cart__promo input {
  padding: 10px 14px;
  border: 1px solid #f5c6d3;
  border-radius: 20px;
  width: 200px;
  background: #fff;
  color: #6b3a4e;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s;
}

.cart__promo input:focus {
  border-color: #e8a2b6;
  box-shadow: 0 0 0 3px rgba(232, 162, 182, 0.2);
}

.cart__promo-ok {
  color: #d4788f;
  font-weight: 600;
  margin-top: 8px;
  font-size: 14px;
}
</style>