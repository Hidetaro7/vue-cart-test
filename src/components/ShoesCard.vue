<template>
    <div class="card">
        <img
         class="thumbnail"
         :src="'/img/items/' + item.photoFileName"
         :alt="item.name"
        />
        <div class="item-info">
            <div class="item-title">{{ item.name }}</div>
            <div class="item-description">{{ item.description }}</div>
            <div class="item-price">&yen;{{ priceFormat(item.price) }}</div>
        </div>
        <button @click="addCart">カートに追加</button>
    </div>
</template>

<script>
import { ref } from "vue";
export default {
    props:{
        shoesData: Object,
    },
    setup(props,context){
        const item = ref(props.shoesData);
        const priceFormat = (num) => {
            return num.toLocaleString();
        };
        const addCart = () => {
            context.emit("added-cart", item);
        };
        return{
            item,
            priceFormat,
            addCart,
        };
    },  
};
</script>