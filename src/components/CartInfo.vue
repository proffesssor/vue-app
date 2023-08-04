<template>

    <div>
        <ul class="cart__orders">
            <li class="cart__order" v-for="item in products" :key="item.product.id">
            <h3>{{ item.product.title }}</h3>
            <b>{{ item.product.price | numberFormat }} ₽</b>
            <span>Артикул: {{ item.product.id }}</span>
            </li>
        </ul>
        
        <div class="cart__total">
            <p>Доставка: <b>500 ₽</b></p>
            <p>Итого: <b>{{ products.length }}</b> товара на сумму <b>{{ totalPrice | numberFormat }} ₽</b></p>
        </div>
    </div>


</template>


<script>
    import numberFormat from '@/helpers/numberFormat';


    export default {
        props: ['products', 'totalPrice'],
        filters: {numberFormat}
    }

</script>