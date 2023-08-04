<template>

    <main class="content container">
        <div class="content__top">
            <ul class="breadcrumbs">
                <li class="breadcrumbs__item">
                <a class="breadcrumbs__link" href="index.html">
                    Каталог
                </a>
                </li>
                <li class="breadcrumbs__item">
                <a class="breadcrumbs__link" href="cart.html">
                    Корзина
                </a>
                </li>
                <li class="breadcrumbs__item">
                <a class="breadcrumbs__link">
                    Оформление заказа
                </a>
                </li>
            </ul>

            <h1 class="content__title">
                Корзина
            </h1>
            <span class="content__info">
                {{ products.length }} товара
            </span>
        </div>

        <section class="cart">
            <form class="cart__form form" action="#" method="POST" @submit.prevent="order">
                <div class="cart__field">
                <div class="cart__data">

                    <BaseFromText title="ФИО" placeholder="Введите ваше полное имя" v-model="formData.name" :error="formError.name"/>
                    <BaseFromText title="Адрес доставки" placeholder="Введите ваш адрес" v-model="formData.address" :error="formError.address"/>
                    <BaseFromText title="Телефон" placeholder="Введите ваш телефон" v-model="formData.phone" :error="formError.phone"/>
                    <BaseFromText title="Email" placeholder="Введи ваш Email" v-model="formData.email" :error="formError.email"/>

                    <BaseFromTextarea title="Комментарий к заказу" placeholder="Ваши пожелания" v-model="formData.comment" :error="formError.comment"/>

                </div>

                <div class="cart__options">
                    <h3 class="cart__title">Доставка</h3>
                    <ul class="cart__options options">
                    <li class="options__item">
                        <label class="options__label">
                        <input class="options__radio sr-only" type="radio" name="delivery" value="0" checked="">
                        <span class="options__value">
                            Самовывоз <b>бесплатно</b>
                        </span>
                        </label>
                    </li>
                    <li class="options__item">
                        <label class="options__label">
                        <input class="options__radio sr-only" type="radio" name="delivery" value="500">
                        <span class="options__value">
                            Курьером <b>500 ₽</b>
                        </span>
                        </label>
                    </li>
                    </ul>

                    <h3 class="cart__title">Оплата</h3>
                    <ul class="cart__options options">
                    <li class="options__item">
                        <label class="options__label">
                        <input class="options__radio sr-only" type="radio" name="pay" value="card">
                        <span class="options__value">
                            Картой при получении
                        </span>
                        </label>
                    </li>
                    <li class="options__item">
                        <label class="options__label">
                        <input class="options__radio sr-only" type="radio" name="pay" value="cash">
                        <span class="options__value">
                            Наличными при получении
                        </span>
                        </label>
                    </li>
                    </ul>
                </div>
                </div>

                <div class="cart__block">
                    <CartInfo :products="products" :totalPrice="totalPrice"/>

                <button class="cart__button button button--primery" type="submit">
                    Оформить заказ
                </button>
                </div>
                <div class="cart__error form__error-block" v-if="formErrorMessage">
                    <h4>Заявка не отправлена!</h4>
                    <p>
                        {{ formErrorMessage }}
                    </p>
                </div>
            </form>
        </section>
  </main>

</template>


<script>
    import BaseFromText from '@/components/BaseFormText.vue';
    import BaseFromTextarea from '@/components/BaseFormTextarea.vue';
    import CartInfo from '@/components/CartInfo.vue';
    import { API_BASE_URL } from '@/config';
    import axios from 'axios';
    import {mapGetters} from 'vuex';

    export default{
        components: { BaseFromText, BaseFromTextarea, CartInfo },
        data(){
            return {
                formData: {},
                formError: {},
                formErrorMessage: ''
            }
        },
        computed: {
        ...mapGetters({ products: "cartDetailProducts", totalPrice: "cartTotalPrice" }),
        },
        methods: {
            order(){

                this.formError = {}
                this.formErrorMessage = ''

                axios.post(API_BASE_URL + '/api/orders', {
                    ...this.formData
                }, {
                    params: {
                        userAccessKey: this.$store.state.userAccessKey
                    }
                })
                .then(response => {
                    this.$store.commit('resetCart')
                    this.$store.commit('updateOrderInfo', response.data)
                    this.$router.push({name: 'orderInfo', params:{id: response.data.id}})
                })
                .catch(error => {
                    this.formError = error.response.data.error.request || {};
                    this.formErrorMessage = error.response.data.error.message
                })
            }
        }
    } 

</script>