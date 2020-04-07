<template>
    <view class="todo_wrapper">
        <uni-nav-bar :shadow="false">
            <view slot="default" class="nav__title">
                <text>已完成</text>
            </view>
        </uni-nav-bar>
        <uni-swipe-action class="todo__list" 
            v-for="(item, index) in doingDataList"
            :key="index">
            <uni-swipe-action-item :options="options"
                @click="handleTodoList($event, index)"
                :autoClose="true">
                <view @tap="showContent(item)"
                    class="todo__list-cell">
                    <view class="todo__list__header">
                        <view class="todo__list-title">
                            {{ item.title }}
                        </view>
                        <view class="todo__list-important">
                            <image :src="showImportant(item.important)"
                                class="todo__list-important-icon" >
                            </image>
                        </view>
                    </view>
                    <view class="todo__list-start-time">
                        创建于{{ new Date(item.startTime).toLocaleString() }}
                    </view>
                </view>
            </uni-swipe-action-item>
        </uni-swipe-action>
        <uni-popup ref="deleteDialog"
            :maskClick="false">
            <view class="todo__list-delete-dialog__wrapper">
                <view class="todo__list-delete-dialog__body">
                    确定要删除吗？
                </view>
                <view class="todo__list-delete-dialog__bottom">
                    <text class="todo__list-delete-dialog__bottom-btn border-right"
                        @tap="$refs.deleteDialog.close()">
                        取消
                    </text>
                    <text class="todo__list-delete-dialog__bottom-btn warn-opt"
                        @tap="deleteDoingList">
                        删除
                    </text>
                </view>
            </view>
        </uni-popup>
    </view>
</template>

<script>
const operation = {
    'TODO': '标记为待办',
    'DELETE': '删除'
};
import doingData from '../../../data/doingData';
import todoData from '../../../data/todoData';
import {uniSwipeAction, uniSwipeActionItem, uniPopup, uniNavBar} from '@dcloudio/uni-ui';
export default {
    data () {
        return {
            doingDataList: doingData,
            contentVisible: false,
            options: [
                {   
                    type: '',
                    text: operation.TODO,
                    style: {
                        backgroundColor: '#ccc'
                    }
                },
                {
                    text: operation.DELETE,
                    style: {
                        backgroundColor: '#dd524d'
                    }
                },
            ],
            deleteObj: 0
        }
    },
    components: {
        uniSwipeAction,
        uniSwipeActionItem,
        uniPopup,
        uniNavBar
    },
    computed: {
        showImportant (important) {
            return (important) => {
                return `../../static/${important}.svg`;
            };
        }
    },  
    onLoad: () => {
        
    },
    methods: {
        handleTodoList (e, index) {
            let opt = e.content.text;
            if (opt === operation.DELETE) {
                this.$refs.deleteDialog.open();
                this.deleteObj = index;
            } else {
                this.markTodo(this.doingDataList[index]);
                this.doingDataList.splice(index, 1);
            }
        },
        deleteDoingList () {
            this.doingDataList.splice(this.deleteObj, 1);
            this.$refs.deleteDialog.close();
        },
        markTodo (item) {
            todoData.push(item);
        }
    }
}
</script>

<style>
.todo__list {
    flex-direction: column;
}
.todo__list-cell {
    width: 100%;
    padding: 20rpx 20rpx 10rpx;
    border-bottom: 1rpx solid #eee;
}
.todo__list__header {
    flex-direction: row;
}
.todo__list-title {
    font-size: 30rpx;
    flex-grow: 1;
}
.todo__list-important-icon {
    width: 20rpx;
    height: 20rpx;
    position: relative;
    top: 5px;
}
.todo__list-start-time {
    font-size: 16rpx;
    color: #ccc;
    margin-top: 5rpx;
}
.todo__list-content {
    font-size: 25rpx;
    margin-top: 20rpx;
}
.todo__list-delete-dialog__wrapper {
    background: #fff;
    width: 620rpx;
    border-radius: 19rpx;
}
.todo__list-delete-dialog__body {
    font-size: 16px;
    color: #14161A;
    text-align: center;
    line-height: 20px;
    margin: 68rpx 0;
}
.todo__list-delete-dialog__bottom {
    height: 96rpx;
    display: flex;
    flex-direction: row;
    border-top: 2rpx solid #EBEDF0;
}
.todo__list-delete-dialog__bottom-btn {
    flex-grow: 1;
    text-align: center;
    line-height: 96rpx;
    font-size: 16px;
    color: #333333;
}
.border-right {
    border-right: 1rpx solid #EBEDF0;
}
.warn-opt {
    color: #DE1B1B;
}
.nav__title {
    flex-basis: 100%;
    text-align: center;
}
</style>