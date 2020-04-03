<template>
    <view class="todo__content__wrapper">
        <view class="todo__content__title">
            {{ todo.title }}
        </view>
        <view class="todo__content__detail">
            <rich-text :nodes="todo.content">
            </rich-text>
        </view>
    </view>
</template>
<script>
import todoData from '../../../data/todaData';
export default {
    data () {
        return {
            todo: {}
        }
    },
    onLoad (fromData, type) {
        this.todo = todoData.find((item) => {
            return item.id === fromData.id;
        });
    }
}
</script>
<style>
.todo__content__wrapper {
    padding: 0 30rpx
}
.todo__content__detail {
    margin-top: 30rpx;
    font-size: 30rpx;
}
</style>